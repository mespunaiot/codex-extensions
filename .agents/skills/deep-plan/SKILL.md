---
name: deep-plan
description: 'Create detailed implementation plan based on comprehensive analysis'
---


You are Codex, an expert project planner with deep technical knowledge. Create comprehensive, actionable implementation plans by following these steps exactly:

## Step 1: Understand Request and Define Task
- User prompt provided: `$ARGUMENTS`
- If no prompt is provided, ask the user to describe what task they want to plan
- Read and understand the user's request thoroughly
- Based on the user's prompt, determine an appropriate task identifier that will be used as the filename
  - The task should be clear, concise, and in kebab-case (e.g., "implement-auth-system", "migrate-to-graphql", "optimize-api-performance")
  - Examples:
    - Prompt: "plan how to implement user authentication with JWT" → Task: "implement-jwt-auth"
    - Prompt: "create a plan to migrate from REST to GraphQL" → Task: "migrate-to-graphql"
    - Prompt: "plan the optimization of our slow API endpoints" → Task: "optimize-api-performance"
- Inform the user of the chosen task identifier

## Step 2: Perform Deep Analysis First
**CRITICAL**: Before planning, you MUST gather comprehensive information about the current state.

- Create an analysis subject based on the task
  - Example: Task "implement-jwt-auth" → Analysis subject: "auth-system-analysis"
- Use the `/deep-analisys` command with an appropriate prompt to analyze the relevant codebase areas
  - Example: `/deep-analisys "analyze the current authentication system, identify all components, dependencies, and areas that need modification for JWT implementation"`
- Wait for the analysis to complete and review the generated report in `.agents/analisys/`
- Use the analysis findings as the foundation for your planning

**If analysis already exists:**
- Check if a relevant analysis report already exists in `.agents/analisys/`
- If found, ask the user if they want to use the existing analysis or create a new one
- Read and incorporate the existing analysis into your planning

## Step 3: Setup Directory Structure
- Create directory if needed: `.agents/plans/`
- Verify the directory is accessible and writable
- The final plan will be saved to: `.agents/plans/<task>.md`

## Step 4: Develop Implementation Strategy
Based on the deep analysis report, develop a comprehensive implementation strategy:

### Planning Considerations
- **Current State**: What exists now (from analysis report)
- **Desired State**: What needs to be built/changed
- **Gap Analysis**: What's missing, what needs modification
- **Dependencies**: What must be done first, what can be parallel
- **Risks**: What could go wrong, mitigation strategies
- **Complexity**: Estimate difficulty and effort for each component
- **Testing Strategy**: How to verify each step works
- **Rollback Plan**: How to undo changes if needed

### Break Down the Task
- Decompose the task into logical phases
- Identify atomic, testable work units
- Order tasks by dependency and priority
- Consider incremental delivery opportunities
- Plan for validation at each step

## Step 5: Generate Detailed Plan
Create a comprehensive, actionable plan at `.agents/plans/<task>.md` using this template:

```markdown
# Implementation Plan: <Task Title>

**Plan Date**: <current-date>
**Planned By**: Codex AI (<model-name>)
**Task Identifier**: <task-identifier>
**Related Analysis**: `.agents/analisys/<analysis-subject>.md`

---

## Executive Summary

### Objective
Clear statement of what this plan aims to achieve.

### Approach
High-level strategy and methodology.

### Timeline Estimate
Rough effort estimation (in story points or hours, not calendar time).

### Key Success Criteria
- Criterion 1: How to measure success
- Criterion 2: What "done" looks like
- Criterion 3: Quality gates to pass

---

## Current State Analysis

### Summary of Findings
Key insights from the deep analysis report (reference specific sections).

### Baseline Assessment
- **Architecture**: Current system design
- **Components**: Existing modules/files involved
- **Dependencies**: Libraries and frameworks in use
- **Technical Debt**: Known issues that impact this task
- **Test Coverage**: Current testing situation

### Critical Observations
Highlight from analysis report the most important factors that influence planning:
1. **Observation 1**: Impact on planning
2. **Observation 2**: Impact on planning

---

## Requirements and Scope

### Functional Requirements
1. **Requirement 1**: Detailed description
2. **Requirement 2**: Detailed description

### Non-Functional Requirements
- **Performance**: Expected performance characteristics
- **Security**: Security requirements and considerations
- **Scalability**: Scale expectations
- **Maintainability**: Code quality standards
- **Compatibility**: Backward compatibility needs

### In Scope
- [ ] Item 1
- [ ] Item 2

### Out of Scope
- [ ] Item 1 (rationale: why not now)
- [ ] Item 2 (rationale: why not now)

### Assumptions
1. **Assumption 1**: What we're assuming to be true
2. **Assumption 2**: What we're assuming to be true

### Constraints
1. **Constraint 1**: Limitation that affects implementation
2. **Constraint 2**: Limitation that affects implementation

---

## Architecture and Design

### Proposed Architecture
Description of the architectural approach with diagrams if needed.

```
[Pseudo-diagram or description of components and their relationships]
```

### Design Decisions

#### Decision 1: <Decision Title>
- **Context**: Why this decision is needed
- **Options Considered**:
  1. Option A: Pros/Cons
  2. Option B: Pros/Cons
- **Chosen Approach**: Option X
- **Rationale**: Why this option is best
- **Trade-offs**: What we're giving up

#### Decision 2: <Decision Title>
<!-- Repeat structure -->

### Component Breakdown

#### Component 1: <Name>
- **Purpose**: What it does
- **Location**: Where it will be implemented (file paths)
- **Dependencies**: What it needs
- **Interfaces**: How it connects to other components
- **Complexity**: High/Medium/Low

#### Component 2: <Name>
<!-- Repeat structure -->

### Data Model Changes
- New tables/collections needed
- Schema modifications
- Migration strategy
- Data seeding requirements

### API Design
- New endpoints or changes to existing ones
- Request/response schemas
- Authentication/authorization changes
- Error handling approach

---

## Implementation Phases

### Phase 1: Foundation (Week 1)
**Goal**: Set up infrastructure and prepare the groundwork

#### Tasks
1. **Task 1.1**: <Description>
   - **File(s)**: `path/to/file.ts`
   - **Action**: What to do
   - **Validation**: How to verify it works
   - **Dependencies**: None
   - **Effort**: XS/S/M/L/XL
   - **Risk**: Low/Medium/High

2. **Task 1.2**: <Description>
   - **File(s)**: `path/to/file.ts`
   - **Action**: What to do
   - **Validation**: How to verify it works
   - **Dependencies**: Task 1.1
   - **Effort**: XS/S/M/L/XL
   - **Risk**: Low/Medium/High

#### Phase 1 Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2
- [ ] All tests pass
- [ ] Code review completed

---

### Phase 2: Core Implementation (Week 2-3)
**Goal**: Implement the main functionality

#### Tasks
1. **Task 2.1**: <Description>
   - **File(s)**: `path/to/file.ts`
   - **Action**: What to do
   - **Validation**: How to verify it works
   - **Dependencies**: Phase 1 complete
   - **Effort**: XS/S/M/L/XL
   - **Risk**: Low/Medium/High

<!-- More tasks -->

#### Phase 2 Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

---

### Phase 3: Integration and Testing (Week 4)
**Goal**: Connect everything and ensure quality

#### Tasks
1. **Task 3.1**: <Description>
   - **File(s)**: `path/to/file.ts`
   - **Action**: What to do
   - **Validation**: How to verify it works
   - **Dependencies**: Phase 2 complete
   - **Effort**: XS/S/M/L/XL
   - **Risk**: Low/Medium/High

<!-- More tasks -->

#### Phase 3 Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

---

### Phase 4: Deployment and Monitoring (Week 5)
**Goal**: Release to production and ensure stability

#### Tasks
1. **Task 4.1**: <Description>
   - **File(s)**: `path/to/file.ts`
   - **Action**: What to do
   - **Validation**: How to verify it works
   - **Dependencies**: Phase 3 complete
   - **Effort**: XS/S/M/L/XL
   - **Risk**: Low/Medium/High

<!-- More tasks -->

#### Phase 4 Acceptance Criteria
- [ ] Criterion 1
- [ ] Criterion 2

---

## Dependency Graph

```
Phase 1: Foundation
  ├─ Task 1.1 (no deps)
  ├─ Task 1.2 (depends on 1.1)
  └─ Task 1.3 (depends on 1.1)

Phase 2: Core Implementation
  ├─ Task 2.1 (depends on Phase 1)
  ├─ Task 2.2 (depends on 2.1)
  └─ Task 2.3 (depends on 2.1, can parallel with 2.2)

Phase 3: Integration
  └─ (depends on Phase 2)

Phase 4: Deployment
  └─ (depends on Phase 3)
```

---

## Testing Strategy

### Unit Testing
- **Scope**: What to unit test
- **Tools**: Testing frameworks to use
- **Coverage Target**: Minimum % coverage
- **Key Test Cases**:
  1. Test case 1: What to verify
  2. Test case 2: What to verify

### Integration Testing
- **Scope**: What integrations to test
- **Approach**: How to test them
- **Key Test Cases**:
  1. Test case 1: What to verify
  2. Test case 2: What to verify

### End-to-End Testing
- **Scope**: User flows to test
- **Tools**: E2E testing tools
- **Key Scenarios**:
  1. Scenario 1: User action flow
  2. Scenario 2: User action flow

### Performance Testing
- **Metrics**: What to measure
- **Benchmarks**: Expected performance
- **Load Testing**: How to stress test

### Security Testing
- **Vulnerabilities to Check**: OWASP Top 10 relevant items
- **Tools**: Security scanning tools
- **Penetration Testing**: If needed

---

## Risk Management

### Risk Assessment

| Risk | Likelihood | Impact | Severity | Mitigation Strategy | Contingency Plan |
|------|------------|--------|----------|---------------------|------------------|
| <Risk description> | High/Med/Low | High/Med/Low | Critical/High/Med/Low | <How to prevent> | <What to do if it happens> |

### Critical Risks (High Priority)

#### Risk 1: <Risk Title>
- **Description**: What could go wrong
- **Probability**: High/Medium/Low
- **Impact**: What happens if it occurs
- **Early Warning Signs**: How to detect it early
- **Prevention**: Steps to avoid
- **Mitigation**: Steps to minimize impact
- **Contingency**: What to do if it happens

---

## Rollback Strategy

### Rollback Triggers
When to consider rolling back:
- Trigger 1: Condition that warrants rollback
- Trigger 2: Condition that warrants rollback

### Rollback Procedure
Step-by-step process to revert changes:

1. **Step 1**: Action to take
   - Commands: `git revert ...` or specific rollback commands
   - Validation: How to verify rollback success

2. **Step 2**: Action to take
   - Commands: Database migrations rollback, etc.
   - Validation: How to verify rollback success

### Data Recovery
- Backup strategy before changes
- How to restore data if needed
- Testing the rollback procedure

---

## Monitoring and Observability

### Metrics to Track
1. **Metric 1**: What to measure, why it matters
2. **Metric 2**: What to measure, why it matters

### Logging Strategy
- What events to log
- Log levels to use
- Log aggregation approach

### Alerting
- Alert conditions
- Who to notify
- Response procedures

### Health Checks
- Endpoint health checks
- Service health indicators
- Automated monitoring setup

---

## Documentation Requirements

### Code Documentation
- [ ] Inline comments for complex logic
- [ ] Function/method documentation
- [ ] Type definitions/interfaces
- [ ] README updates

### API Documentation
- [ ] Endpoint documentation
- [ ] Request/response examples
- [ ] Authentication documentation
- [ ] Error codes and handling

### User Documentation
- [ ] User-facing feature docs
- [ ] Configuration guides
- [ ] Migration guides (if applicable)
- [ ] FAQ updates

### Technical Documentation
- [ ] Architecture decision records (ADRs)
- [ ] Deployment procedures
- [ ] Troubleshooting guides
- [ ] Runbook updates

---

## Definition of Done

### Code Quality Checklist
- [ ] All functionality implemented per requirements
- [ ] Code follows project style guidelines
- [ ] No console.log or debug code remaining
- [ ] Error handling implemented
- [ ] Input validation in place
- [ ] Security best practices followed

### Testing Checklist
- [ ] Unit tests written and passing
- [ ] Integration tests written and passing
- [ ] E2E tests written and passing
- [ ] Test coverage meets target (X%)
- [ ] Manual testing completed
- [ ] Edge cases tested

### Review Checklist
- [ ] Code review completed
- [ ] Security review completed (if needed)
- [ ] Performance review completed (if needed)
- [ ] Documentation review completed

### Deployment Checklist
- [ ] Staging deployment successful
- [ ] Production deployment plan reviewed
- [ ] Rollback procedure tested
- [ ] Monitoring and alerts configured
- [ ] Team trained on new features

---

## Open Questions

### Questions Requiring Resolution
1. **Question 1**: What needs to be decided?
   - **Impact**: Why this matters
   - **Options**: Possible answers
   - **Recommendation**: Suggested approach
   - **Decision Maker**: Who should decide

2. **Question 2**: What needs to be decided?
   <!-- Repeat structure -->

---

## Appendices

### Appendix A: Code Examples

**Example 1: <Component> Implementation**
```typescript
// Example code showing key implementation pattern
```

**Example 2: <API> Usage**
```typescript
// Example code showing how to use new API
```

### Appendix B: Related Resources
- Link to analysis report: `.agents/analisys/<subject>.md`
- Related documentation URLs
- Reference implementations
- Helpful articles or guides

### Appendix C: Glossary
- **Term 1**: Definition
- **Term 2**: Definition

### Appendix D: Effort Estimation Guide
- **XS**: < 1 hour
- **S**: 1-4 hours
- **M**: 4-8 hours (1 day)
- **L**: 1-3 days
- **XL**: 3-5 days

---

**Plan Version**: 1.0
**Last Updated**: <timestamp>
**Status**: Draft/Ready for Review/Approved
```

## Step 6: Quality Assurance
- Review the plan for completeness and actionability
- Ensure all tasks have clear acceptance criteria
- Verify dependencies are correctly identified
- Check that risks have mitigation strategies
- Validate that the plan is based on the analysis findings
- Ensure every task includes file paths and specific actions

## Step 7: Present Plan
- Display a summary of the plan structure to the user
- Provide the full path to the plan: `.agents/plans/<task>.md`
- Highlight:
  - Total number of phases and tasks
  - Critical risks identified
  - Key dependencies
  - Estimated total effort
- Offer to:
  - Clarify any part of the plan
  - Add more detail to specific areas
  - Create diagrams for architecture
  - Start implementation of Phase 1
  - Generate a review of the plan using `/deep-plan-review`

## Requirements

### Planning Quality
- **Actionable**: Every task must have clear, implementable steps
- **Specific**: Include exact file paths, function names, approach details
- **Testable**: Every task must have validation criteria
- **Realistic**: Based on actual analysis, not assumptions
- **Complete**: Cover all aspects from code to deployment
- **Risk-Aware**: Identify and plan for potential problems

### Analysis Integration
- **Reference Analysis**: Cite specific findings from the analysis report
- **Build on Facts**: Use analysis data, not assumptions
- **Address Findings**: Plan should address issues found in analysis
- **Validate Feasibility**: Ensure plan aligns with current architecture

### Task Structure
- **Granular**: Tasks should be 1-8 hours of work
- **Dependent**: Clearly mark dependencies
- **Ordered**: Logical sequence of execution
- **Parallel**: Identify tasks that can be done concurrently
- **Verifiable**: Each task has clear success criteria

### Professional Standards
- **No Vagueness**: Be specific in every instruction
- **No Assumptions**: Verify everything through analysis
- **No Gaps**: Cover the entire implementation journey
- **No Handwaving**: Provide concrete steps, not general advice

## Tips for Effective Planning

### Start with Why
- Understand the business value
- Know the user impact
- Identify the problem being solved

### Think Incrementally
- Plan for iterative delivery
- Identify MVP vs. nice-to-have
- Create checkpoints for validation

### Consider the Team
- Plan for code review time
- Include knowledge sharing
- Account for testing time

### Plan for Failure
- Every task needs a rollback
- Identify risks early
- Have contingencies ready

### Be Pragmatic
- Don't over-engineer
- Use existing patterns
- Keep it simple

---

**Remember**: A great plan is specific enough to follow, flexible enough to adapt, and grounded in reality through thorough analysis. The goal is to provide a clear roadmap that any developer can follow to successfully implement the task.
