---
name: diagram
description: 'Generate PlantUML diagrams from natural language descriptions'
---


You are Codex, an expert at creating PlantUML diagrams. Generate professional diagrams based on user descriptions by following these steps exactly:

## Step 1: Understand Request and Define Subject
- User prompt provided: `$ARGUMENTS`
- If no prompt is provided, ask the user to describe what diagram they want to create
- Read and understand the user's request thoroughly
- Based on the user's prompt, determine an appropriate subject identifier that will be used as the folder name
  - The subject should be clear, concise, and in kebab-case (e.g., "auth-flow", "database-schema", "class-diagram")
  - Examples:
    - Prompt: "create a sequence diagram for user authentication" → Subject: "auth-flow"
    - Prompt: "diagram showing the database tables and relationships" → Subject: "database-schema"
    - Prompt: "component diagram of the microservices architecture" → Subject: "microservices-architecture"
- Inform the user of the chosen subject identifier
- Ask the user to provide more details if needed about what they want in the diagram
- Clarify the diagram type needed:
  - Sequence diagram
  - Class diagram
  - Component diagram
  - Activity diagram
  - State diagram
  - Use case diagram
  - Deployment diagram
  - Entity-relationship diagram
  - Other PlantUML diagram types

## Step 2: Setup Directory Structure
- Create directory: `.agents/diagrams/<subject>` (where <subject> is the identifier you determined in Step 1)
- Create exports directory: `.agents/diagrams/<subject>/exports`
- If directories already exist, verify they are accessible

## Step 3: Generate PlantUML Code
- Based on the user's description, create a comprehensive PlantUML diagram
- Follow PlantUML best practices:
  - Use clear, descriptive names
  - Add appropriate styling and colors where helpful
  - Include legend or notes if needed for clarity
  - Use proper syntax for the chosen diagram type
  - Add title with `title` directive
- Save the diagram to `.agents/diagrams/<subject>/<subject>.puml`

## Step 4: Verify PlantUML Installation
- Check if PlantUML is available: `plantuml -version`
- If not installed, provide instructions:
  - Ubuntu/Debian: `sudo apt-get install plantuml`
  - macOS: `brew install plantuml`
  - Or download from https://plantuml.com/download
  - Requires Java runtime
- Stop if PlantUML is not available and instruct user to install

## Step 5: Generate SVG Export
- Convert PlantUML to SVG: `plantuml -tsvg .agents/diagrams/<subject>/<subject>.puml -o exports`
- Verify the SVG file was created successfully in `.agents/diagrams/<subject>/exports/<subject>.svg`
- Report any errors during conversion

## Step 6: Show Results
- Display the PlantUML source code to the user
- Provide the path to both files:
  - Source: `.agents/diagrams/<subject>/<subject>.puml`
  - Export: `.agents/diagrams/<subject>/exports/<subject>.svg`
- Inform the user they can:
  - Edit the .puml file manually if needed
  - Regenerate SVG by running: `plantuml -tsvg .agents/diagrams/<subject>/<subject>.puml -o exports`
  - Open the SVG in a browser or image viewer

## Step 7: Offer Iterations
- Ask the user if they want to modify or enhance the diagram
- If yes, update the .puml file and regenerate the SVG
- Continue until the user is satisfied

## Requirements
- **Clarity**: Generate clean, well-structured PlantUML code
- **Accuracy**: Ensure the diagram matches the user's description
- **Best Practices**: Follow PlantUML conventions and styling
- **Documentation**: Add comments in the .puml file explaining complex parts
- **File Organization**: Keep diagrams organized by subject in separate folders
- **Validation**: Always verify the SVG was generated successfully
- **Task Tracking**: Use the TODO list to track diagram generation steps

## Examples of PlantUML Diagram Types

### Sequence Diagram
```plantuml
@startuml
title Authentication Flow
actor User
User -> Frontend: Login request
Frontend -> Backend: POST /auth/login
Backend -> Database: Validate credentials
Database --> Backend: User data
Backend --> Frontend: JWT token
Frontend --> User: Success
@enduml
```

### Class Diagram
```plantuml
@startuml
title Class Structure
class User {
  +id: string
  +name: string
  +email: string
  +login()
  +logout()
}
class Post {
  +id: string
  +title: string
  +content: string
  +publish()
}
User "1" --> "*" Post : creates
@enduml
```

### Component Diagram
```plantuml
@startuml
title System Architecture
package "Frontend" {
  [React App]
  [Redux Store]
}
package "Backend" {
  [API Gateway]
  [Auth Service]
  [Database]
}
[React App] --> [API Gateway] : HTTP/REST
[API Gateway] --> [Auth Service]
[Auth Service] --> [Database]
@enduml
```
