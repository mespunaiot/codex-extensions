---
name: go-cactus-standard
description: Go coding standards and conventions for Cactus Go codebases. Use only when reviewing code, writing code, or checking PR compliance for Go projects, or when a repository explicitly asks for this Go standard. Do not use for C, C++, Python, or other non-Go codebases. Covers package structure, interface design, manager pattern, mock generation, testing standards, naming conventions, configuration, logging, documentation, and terminology.
---

# Cactus Go Coding Standards

This document outlines the coding conventions and standards for Go projects at Cactus. Use it only for Go codebases; it does not apply to C, C++, Python, or other non-Go repositories. Following these guidelines ensures consistency across the codebase and facilitates code reviews.

## Table of Contents

- [Package Structure](#package-structure)
- [Interface Design](#interface-design)
- [Manager Pattern](#manager-pattern)
- [Mock Generation](#mock-generation)
- [Testing Standards](#testing-standards)
- [Naming Conventions](#naming-conventions)
- [Configuration](#configuration)
- [Logging](#logging)
- [Documentation](#documentation)
- [Terminology](#terminology)

---

## Package Structure

Organize code following the standard layered architecture:

```
internal/
├── entities/                    # Domain entities and protocol definitions
│   ├── peripheral_protocol_layer/
│   ├── fae/
│   └── pedal/
├── managers/                    # Business logic managers
│   ├── data_link_manager/
│   └── application_layer_manager/
├── services/                    # Application services
├── repositories/                # Data access layer
└── drivers/                     # Hardware/external system drivers
```

---

## Interface Design

### Public vs Private Interfaces

**Rule**: Interfaces consumed only within a package should be **private** (lowercase).

```go
// CORRECT: Private interface for internal use only
type serialPort interface {
    Connect() error
    Disconnect() error
    Write(data []byte) error
    ReadWithTimeout(maxBytes int, timeout time.Duration) ([]byte, error)
}

// INCORRECT: Public interface only used internally
type SerialPort interface {
    // ...
}
```

### When to Export Interfaces

Export an interface when:
- It defines the contract for a manager/service that other packages will consume
- It's part of the public API of your package

### Interface Organization Comments

Use `// Producing` and `// Consuming` section comments to organize interfaces in `interfaces.go`. This makes the dependency direction immediately clear at a glance.

```go
// Producing

// MyManager defines the public contract for this package.
// Other packages consume this interface.
type MyManager interface {
    Init() error
    Run(ctx context.Context)
}

// Consuming

// dependency is the interface for an external dependency.
// This package consumes implementations provided by others.
type dependency interface {
    SomeMethod() error
}

// anotherDependency is another consumed interface.
type anotherDependency interface {
    OtherMethod() error
}
```

**Rules:**
- `// Producing` marks interfaces this package **exports** for other packages to consume
- `// Consuming` marks interfaces this package **depends on** (implemented elsewhere)
- Place the section comment on its own line with a blank line before and after
- Group all producing interfaces first, then all consuming interfaces

---

## Manager Pattern

Follow the **"Expose Interface, Hide Implementation"** pattern.

### Structure

1. **Define a public interface** in `interfaces.go`
2. **Implement with a private struct**
3. **`New()` returns the interface**, not the concrete type

### Example

```go
// interfaces.go
package my_manager

// Producing

// MyManager defines the public contract - EXPORTED
type MyManager interface {
    Init() error
    Run(ctx context.Context)
    DoSomething() error
}

// Consuming

// dependency is only used internally - NOT exported
type dependency interface {
    SomeMethod() error
}
```

```go
// my_manager.go
package my_manager

// manager is the private implementation - NOT exported
type manager struct {
    dep dependency
    // other fields...
}

// New returns the interface, not *manager
func New(dep dependency) MyManager {
    return &manager{
        dep: dep,
    }
}

func (manager *manager) Init() error {
    // implementation
}

func (manager *manager) Run(ctx context.Context) {
    // implementation
}

func (manager *manager) DoSomething() error {
    // implementation
}
```

### Benefits

- Consumers depend on abstractions, not implementations
- Easy to mock in tests
- Implementation details are hidden
- Enforces proper encapsulation

---

## Mock Generation

### Mockgen Directive

Place the `go:generate` directive at the top of `interfaces.go`:

```go
//go:generate mockgen -source=./interfaces.go -destination=./mocks.go -package=my_manager -exclude_interfaces=MyManager
```

### Key Rules

1. **Exclude the main manager interface** from mock generation
   - We mock dependencies, not the manager itself
   - Use `-exclude_interfaces=MyManager`

2. **Mock dependencies**, not the service under test
   - If `MyManager` depends on `serialPort`, mock `serialPort`
   - Tests should exercise the real `MyManager` implementation

### Example

```go
//go:generate mockgen -source=./interfaces.go -destination=./mocks.go -package=data_link_peripherals_manager -exclude_interfaces=DataLinkPeripheralsManager

package data_link_peripherals_manager

// Producing

// DataLinkPeripheralsManager - public interface, EXCLUDED from mocks
type DataLinkPeripheralsManager interface {
    Init() error
    Run(ctx context.Context)
}

// Consuming

// serialPort - private dependency, INCLUDED in mocks
type serialPort interface {
    Connect() error
    Disconnect() error
}
```

---

## Testing Standards

### Test Naming Convention

All test case names **MUST** follow the format:

```
"Should <expected behavior> when <condition>"
```

### Examples

```go
// CORRECT
t.Run("Should create frame channel with correct buffer size when initialized", func(t *testing.T) {
    // ...
})

t.Run("Should return error when connection fails", func(t *testing.T) {
    // ...
})

t.Run("Should discard frame when BCC mismatches", func(t *testing.T) {
    // ...
})

// INCORRECT
t.Run("creates frame channel with correct buffer size", func(t *testing.T) {
    // ...
})

t.Run("connection failure", func(t *testing.T) {
    // ...
})

t.Run("BCC mismatch discards frame", func(t *testing.T) {
    // ...
})
```

### Test Structure

Use table-driven tests when appropriate, and always include:

1. **Arrange**: Set up test fixtures and mocks
2. **Act**: Execute the code under test
3. **Assert**: Verify the results

```go
func TestMyManager_DoSomething(t *testing.T) {
    t.Run("Should succeed when dependency returns no error", func(t *testing.T) {
        // Arrange
        ctrl := gomock.NewController(t)
        defer ctrl.Finish()

        mockDep := NewMockdependency(ctrl)
        mockDep.EXPECT().SomeMethod().Return(nil)

        mgr := New(mockDep)

        // Act
        err := mgr.DoSomething()

        // Assert
        if err != nil {
            t.Errorf("expected no error, got %v", err)
        }
    })
}
```

### Accessing Private Fields in Tests

When testing internal state, use type assertions:

```go
func TestNew(t *testing.T) {
    t.Run("Should store dependency when created", func(t *testing.T) {
        mockDep := NewMockdependency(ctrl)

        mgr := New(mockDep)
        m := mgr.(*manager)  // Type assertion to access private fields

        if m.dep != mockDep {
            t.Error("expected dependency to be stored")
        }
    })
}
```

### Assertion Helpers

Use the `testify` library for assertions instead of manual `if/t.Errorf()` patterns.

#### Import

```go
import (
    "github.com/stretchr/testify/assert"
    "github.com/stretchr/testify/require"
)
```

#### When to Use `assert` vs `require`

| Package | Behavior | Use When |
|---------|----------|----------|
| `assert` | Reports failure, continues test | Most assertions; want to see all failures |
| `require` | Reports failure, stops test | Preconditions; failure would cause panic |

#### Common Patterns

```go
// Value equality
assert.Equal(t, expected, actual)
assert.Equal(t, expected, actual, "optional message")

// Error handling
assert.NoError(t, err)                    // No error expected
assert.Error(t, err)                      // Error expected
assert.ErrorIs(t, err, expectedErr)       // Error wraps specific error

// Precondition checks (use require)
result, err := SomeFunction()
require.NoError(t, err)  // Stop if error - result may be invalid
require.Len(t, slice, 5) // Stop if wrong length - would panic on access

// Boolean checks
assert.True(t, condition, "message")
assert.False(t, condition, "message")

// Nil checks
assert.Nil(t, value)
assert.NotNil(t, value)

// Collection checks
assert.Len(t, collection, expectedLength)
assert.Contains(t, collection, element)

// Pointer/reference equality
assert.Same(t, ptr1, ptr2)
```

#### Async/Concurrent Tests

```go
select {
case result := <-ch:
    require.Len(t, result, expectedLen)  // Precondition
    assert.Equal(t, expected, result)    // Verification
case <-time.After(100 * time.Millisecond):
    require.Fail(t, "timeout waiting for result")
}
```

#### Assertions in Mock Callbacks

```go
mockDep.EXPECT().Method(gomock.Any()).DoAndReturn(func(data []byte) error {
    require.GreaterOrEqual(t, len(data), 4, "data too short")  // Precondition
    assert.Equal(t, expectedByte, data[0])                      // Verification
    return nil
})
```

---

## Naming Conventions

### Files

| Type | Convention | Example |
|------|------------|---------|
| Main implementation | `<package_name>.go` | `data_link_peripherals_manager.go` |
| Interfaces | `interfaces.go` | `interfaces.go` |
| Tests | `<package_name>_test.go` | `data_link_peripherals_manager_test.go` |
| Mocks | `mocks.go` | `mocks.go` |

### Variables and Functions

| Type | Convention | Example |
|------|------------|---------|
| Public interface | PascalCase | `DataLinkPeripheralsManager` |
| Private interface | camelCase | `serialPort` |
| Private struct | camelCase | `manager` |
| Constructor | `New` or `New<Type>` | `New(dep dependency)` |
| Method receiver | Descriptive, matches struct name | `(manager *manager)` |

### Method Receiver Names

**Rule**: Use descriptive receiver names that match the struct type name, NOT single-letter abbreviations.

```go
// CORRECT: Descriptive receiver name
func (manager *manager) Init() error {
    return manager.port.Connect()
}

func (manager *manager) Run(ctx context.Context) {
    defer manager.close()
    // ...
}

// INCORRECT: Single-letter abbreviation
func (m *manager) Init() error {
    return m.port.Connect()
}
```

This improves code readability and makes it immediately clear what type the receiver is, especially in longer methods.

### Constants

```go
// Group related constants
const (
    DLE = 0x10 // Data Link Escape
    STX = 0x02 // Start of Text
    ETX = 0x03 // End of Text
)

// Use descriptive names for configuration
const (
    DefaultReadTimeout        = 100 * time.Millisecond
    DefaultReadBufferSize     = 256
    DefaultFrameChannelBuffer = 16
)
```

---

## Configuration

Create a `config.go` file with a `Config` struct for packages requiring extensive configuration. The struct must support automated loading/unloading (JSON, YAML, CBOR, etc.).

### Rules

- Tags must use snake_case matching the variable name
- Use basic types (avoid Go-specific types like `time.Duration`)
- Be descriptive with time variables: append `Milliseconds`, `Seconds`, etc.

| Variable Name | Tag Equivalence |
|---------------|-----------------|
| VariableNumberOne | `variable_number_one` |
| TimeoutMilliseconds | `timeout_milliseconds` |

```go
type Config struct {
    Field1              string `json:"field_1"`                              // Optional validation
    TimeoutMilliseconds int    `json:"timeout_milliseconds" validate:"required"`
}
```

---

## Logging

Use the logger package from [go_share_lib](https://github.com/cactusiot/go_share_lib) (GSL).

### Usage

```go
// Default development logger (no setup required)
logger.Info("This is an Info log")

// Custom configuration (at app initialization)
err := logger.Configure(logger.Config{})

// Custom logger instance
newLogger, err := logger.NewLoggerWithConfig(logger.Config{})
```

### Log Levels

**Error** - Unrecoverable failures. Log only at the top-level manager where the error terminates.

```
App -> Manager -> Service -> Driver
         |           |          |
         |        doTask     doTask
         |           |<--------|  (return error)
         |<----------|  (return error)
      log error
         X
```

**Warn** - Recoverable errors that don't affect execution flow. Use when the application can continue.

**Info** - Application status for non-developers. Examples: "Application started", "Server running on :443"

**Debug** - Developer information. Use extensively in development, disable in production.

---

## Documentation

Based on **godoc** conventions. Comments should be concise while maintaining purpose and readability.

### Guidelines

- Comments for exported identifiers must begin with the identifier name
- Focus on explaining "why", not "what"
- Use full sentences with proper grammar

### Examples

```go
// Package calculator provides basic arithmetic operations.
package calculator

// Add returns the sum of two integers.
//
// Params:
//   a: first operand
//   b: second operand
//
// Returns:
//   int: the sum of two integers
func Add(a, b int) int {
    return a + b
}

const (
    // Pi is an approximation of the mathematical constant π.
    Pi = 3.1416
)

// Calculator represents a simple calculator capable of basic arithmetic operations.
type Calculator struct {
    // Precision determines the number of decimal places used in calculations.
    Precision int
}
```

### README Requirements

Every package should have a README with:
- **Title**: Package name
- **Description**: Brief explanation
- **Example**: Copy-paste ready `.go` code (include package main, imports)

Optional: **References** (external docs), **Dependencies** (with versions)

---

## Terminology

Cactus idiomatic naming for variables and functions. For unlisted cases, refer to [Go Style](https://google.github.io/styleguide/go/).

### Accepted Acronyms

Only these abbreviations are accepted. Other abbreviations require team approval.

| Acronym | Meaning |
|---------|---------|
| ctx | context |
| ch | channel |
| mutex | mutual exclusion |
| wg | wait group |
| tmp | temporary |
| config | configuration |
| init | initialize |
| deinit | de-initialize |
| fn | function |
| cb | callback |
| req | request |
| res | response |

### Standard Function Names

| Name | Meaning |
|------|---------|
| New[WithSomething] | Creates instance; **never** returns error |
| Init | Initializes struct; returns error if failed |
| DeInit | De-initializes struct |
| Config | Configures with parameters (accepts config struct); returns error |
| Run | Runs in own goroutine; accepts **only** context; **no** error return |
| Start | Starts background task; non-blocking; can return error |
| Stop | Used after Start; can return error |
| Open | Opens resource (file, DB, stream); can return error |
| Close | Used after Open; can return error |
| ensure[Action] | Panics on failure; **only** use at app initialization |

---

## Checklist for PRs

Before submitting a PR, ensure:

**Code Structure**
- [ ] Interfaces consumed only within the package are private (lowercase)
- [ ] Manager pattern follows "expose interface, hide implementation"
- [ ] `New()` returns interface, not concrete type
- [ ] Method receivers use descriptive names (e.g., `manager` not `m`)
- [ ] `interfaces.go` uses `// Producing` and `// Consuming` section comments

**Testing**
- [ ] Mockgen excludes main manager interface (`-exclude_interfaces=...`)
- [ ] All test names follow "Should xxx when yyy" format
- [ ] Tests use testify assertion helpers (`assert`/`require`)
- [ ] Mocks are regenerated (`go generate ./...`)
- [ ] All tests pass (`go test ./...`)

**Configuration**
- [ ] Config structs use snake_case tags matching variable names
- [ ] Time-related config fields are descriptive (e.g., `TimeoutMilliseconds`)

**Logging**
- [ ] Appropriate log levels used (Error, Warn, Info, Debug)
- [ ] Error logs only at top-level handlers
- [ ] Info logs are non-technical and user-friendly

**Documentation**
- [ ] Exported identifiers have godoc comments starting with the identifier name
- [ ] Package has a README with Description and Example sections

**Terminology**
- [ ] Only accepted acronyms are used (ctx, ch, wg, tmp, etc.)
- [ ] Standard function names follow conventions (New, Init, Run, Start, Stop, etc.)

**Build**
- [ ] Code builds without errors (`go build ./...`)

---
