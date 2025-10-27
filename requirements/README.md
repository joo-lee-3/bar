# Story Mapping API

A RESTful API for managing user stories, activities, and story maps in an agile development workflow.

## Overview

The Story Mapping API provides a hierarchical structure for organizing user stories with AI-powered enhancement capabilities:
- **StoryMaps** contain multiple **Activities**
- **Activities** contain multiple **Stories**  
- **Stories** represent individual user stories with personas, actions, and metadata
- **AI Processing** enables conversational querying and content enhancement

## Quick Start

### Authentication
All endpoints require JWT authentication via the `Authorization: Bearer <token>` header.

### Base URL
```
https://api.example.com/v1
```

### Create a Story Map from Requirements
```bash
curl -X POST https://api.example.com/v1/storymaps \
  -H "Authorization: Bearer <token>" \
  -H "Content-Type: application/json" \
  -d '{
    "requirements": "Build an e-commerce platform where users can browse products, add to cart, and checkout",
    "name": "E-commerce Platform",
    "description": "Complete user journey for online shopping"
  }'
```

## Core Concepts

### Entities

#### StoryMap
Represents a complete user journey or product feature set. Created from requirements text or files and contains all related activities.

#### Activity  
Represents a major step or phase in the user journey. Groups together related user stories that accomplish a common goal.

#### Story
Represents a single user story with a specific persona, action, and outcome. The smallest unit of work in the story mapping hierarchy.

### External Metadata
Stories support external metadata through a separate endpoint system:
- **Purpose**: Allow external systems to attach custom data without affecting core story structure
- **Namespacing**: Each consumer uses their own key (e.g., `composer-v3`, `jira-sync`)
- **Access**: Via dedicated `/stories/{uuid}/metadata` endpoints

## API Structure

### StoryMaps
```
GET    /storymaps                     # List all story maps (with filtering)
POST   /storymaps                     # Create story map from requirements
GET    /storymaps/{uuid}              # Get specific story map
POST   /storymaps/{uuid}              # Process story map with AI
PUT    /storymaps/{uuid}              # Update story map
DELETE /storymaps/{uuid}              # Delete story map
PUT    /storymaps/{uuid}/activities   # Manage activities within story map
```

### Activities
```
GET    /activities/{uuid}             # Get specific activity
PUT    /activities/{uuid}             # Update activity
DELETE /activities/{uuid}             # Delete activity
PUT    /activities/{uuid}/stories     # Manage stories within activity
```

### Stories
```
GET    /stories                       # List all stories (with filtering)
GET    /stories/{uuid}                # Get specific story
PUT    /stories/{uuid}                # Update story
DELETE /stories/{uuid}                # Delete story
```

### Story Metadata
```
GET    /stories/{uuid}/metadata                    # Get all metadata
GET    /stories/{uuid}/metadata/{consumerKey}      # Get consumer-specific metadata
PUT    /stories/{uuid}/metadata/{consumerKey}      # Update consumer metadata
DELETE /stories/{uuid}/metadata/{consumerKey}      # Delete consumer metadata
```

## AI-Powered Story Map Processing

The API includes AI capabilities for enhancing and querying story maps through conversational interactions.

### AI Operations
```
POST   /storymaps/{uuid}              # Process story map with AI
```

#### Operation Types
- **`query`** - Ask questions about the story map (read-only)
- **`update`** - Request modifications to the story map content

#### Basic Usage
```bash
# Query story map information
POST /storymaps/{uuid}
{
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "What are the main user personas in this story map?"
        }
      ]
    }
  ],
  "operation_type": "query"
}

# Update story map content
POST /storymaps/{uuid}
{
  "messages": [
    {
      "role": "user", 
      "content": [
        {
          "type": "text",
          "text": "Add acceptance criteria to all user stories and ensure they follow the standard format"
        }
      ]
    }
  ],
  "operation_type": "update"
}
```

#### Response Format
All AI operations return a response with content and operation type:
```json
{
  "content": "This story map contains 4 main user personas: New Customer, Returning Customer, Premium Member, and Guest User...",
  "operation_type": "query"
}
```

**Note**: The `operation_type` in the response will always be "query" for query requests, but can be either "query" or "update" for update requests depending on what the AI determined was needed.

#### Conversational Context
You can include conversation history for multi-turn interactions:
```bash
POST /storymaps/{uuid}
{
  "messages": [
    {
      "role": "user",
      "content": [{"type": "text", "text": "Show me the activities in this story map"}]
    },
    {
      "role": "assistant", 
      "content": [{"type": "text", "text": "The story map contains 5 activities: Registration, Discovery, Cart, Checkout, Support"}]
    },
    {
      "role": "user",
      "content": [{"type": "text", "text": "Which activity needs the most work?"}]
    }
  ],
  "operation_type": "query"
}
```

## Common Operations

### 1. Requirements to Story Map
Convert text requirements into a structured story map:

```bash
POST /storymaps
{
  "requirements": "Users need to register, browse products, compare items, add to cart, and checkout",
  "name": "E-commerce Flow"
}
```

### 2. Add Activity to Story Map
Insert a new activity at a specific position:

```bash
PUT /storymaps/{uuid}/activities
{
  "operation": "add",
  "activity": {
    "name": "User Registration",
    "description": "Account creation and onboarding"
  },
  "position": 0
}
```

### 3. Add Story to Activity
Insert a new story at a specific position:

```bash
PUT /activities/{uuid}/stories
{
  "operation": "add",
  "story": {
    "persona": "New Customer",
    "actionType": "Registration Task",
    "name": "Create account with email",
    "description": "<p>As a new customer, I want to create an account using my email address so that I can save my preferences</p>",
    "status": "NOT_STARTED"
  },
  "position": 0
}
```

### 4. Reorder Items
Change the sequence of activities or stories:

```bash
PUT /storymaps/{uuid}/activities
{
  "operation": "reorder",
  "activityUuids": [
    "aa1b2c3d-4e5f-6789-0abc-def123456789",
    "bb2a1c3d-45e6-78f9-0123-456789abcdef",
    "cc3b2d4e-56f7-89a0-1234-56789abcdef0"
  ]
}
```

### 5. AI-Enhanced Story Analysis
Query and enhance story maps using AI:

```bash
# Analyze story map structure
POST /storymaps/{uuid}
{
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "text", 
          "text": "Analyze this story map and identify any gaps in the user journey"
        }
      ]
    }
  ],
  "operation_type": "query"
}

# Enhance stories with acceptance criteria
POST /storymaps/{uuid}
{
  "messages": [
    {
      "role": "user",
      "content": [
        {
          "type": "text",
          "text": "Add detailed acceptance criteria to all user stories and ensure they follow the 'As a [persona], I want [goal] so that [benefit]' format"
        }
      ]
    }
  ],
  "operation_type": "update"
}
```

### 6. Manage External Metadata
Store consumer-specific metadata:

```bash
# Update metadata for a specific consumer
PUT /stories/{uuid}/metadata/composer-v3
{
  "priority": "high",
  "estimatedHours": 8,
  "assignedTeam": "frontend"
}

# Get only your consumer's metadata
GET /stories/{uuid}/metadata/composer-v3
```

## Filtering and Querying

### List Story Maps
```bash
GET /storymaps?filter=e-commerce&sortBy=created&sortOrder=desc&limit=10
```

### List Stories
```bash
# Filter by story map
GET /stories?storymapUuid=aa1b2c3d-4e5f-6789-0abc-def123456789

# Filter by activity
GET /stories?activityUuid=bb2a1c3d-45e6-78f9-0123-456789abcdef

# Filter by persona and status
GET /stories?persona=Shopper&status=NOT_STARTED

# Text search
GET /stories?filter=payment&sortBy=persona
```

## Data Models

### Story Status Values
- `NOT_STARTED` - Story not yet begun
- `IN_PROGRESS` - Story currently being worked on
- `COMPLETED` - Story finished and accepted
- `BLOCKED` - Story cannot proceed due to dependencies

### Design Objects
Stories can reference Appian design objects:
```json
{
  "designObjects": [
    {
      "uuid": "SYSTEM_RECORD_TYPE_USER",
      "typeQname": "type!{http://www.appian.com/ae/types/2009}RecordTypeId"
    }
  ]
}
```

### External Metadata Structure
```json
{
  "ext-metadata": {
    "composer-v3": {
      "priority": "high",
      "estimatedHours": 8,
      "assignedTeam": "frontend"
    },
    "jira-sync": {
      "ticketId": "PROJ-123",
      "lastSync": "2024-01-15T10:30:00Z"
    }
  }
}
```

## Response Formats

### Success Responses
Most PUT operations return simple success confirmations:
```json
{
  "success": true,
  "message": "Operation completed successfully"
}
```

### Success with UUID
Operations that create new entities return the UUID:
```json
{
  "success": true,
  "message": "Story added successfully",
  "uuid": "ee7e8d9a-09c0-45cf-9d63-4482fc0a4bd5"
}
```

### Error Responses
All errors follow a consistent format:
```json
{
  "error": {
    "type": "validation_error",
    "message": "The persona field is required for all stories",
    "code": "missing_required_field",
    "param": "persona"
  }
}
```

### Paginated Lists
List endpoints return paginated results:
```json
{
  "stories": [...],
  "total": 25,
  "limit": 20,
  "offset": 0
}
```

## Best Practices

### 1. AI Operations
- Use `query` operations for read-only analysis and information retrieval
- Use `update` operations when you want to modify story map content
- Provide clear, specific instructions in your messages
- Include conversation context for multi-turn interactions
- Be specific about the format or structure you want for updates

### 2. Metadata Management
- Always namespace metadata with your consumer key
- Use consistent field names within your namespace
- Keep metadata focused on your specific use case
- Clean up metadata when removing integrations

### 3. Performance
- Use filtering parameters to reduce response sizes
- Access metadata only when needed via dedicated endpoints
- Use pagination for large result sets
- Cache story map structures when possible
- Use AI operations judiciously as they may have higher latency

## Error Handling

TBD

## File Upload Constraints

When creating story maps from file attachments:
- **Maximum file size**: TBD
- **Supported formats**: TBD
- **Content**: Requirements documents, user research, specifications

## API Design Trade-offs

### Pros

**Clear Structure with Flexible Querying**
- StoryMap structure remains obvious to consumers through standard CRUD endpoints
- External metadata architecture keeps core data model clean while enabling rich integrations
- AI endpoint provides flexible natural language querying without changing the core structure
- Multiple consumers can attach domain-specific data (JIRA tickets, project metadata) without conflicts
- Clients can use traditional REST patterns for direct manipulation or conversational AI for exploration

**Simplified Integration**
- Only two parameters required: `messages` and `operation_type`
- No AI expertise needed - server handles model selection and parameters
- Single endpoint for both queries and updates

**Future-Proof Abstraction**
- AI improvements happen server-side without client changes
- Can switch AI providers transparently
- Core API remains stable while allowing rich integrations (JIRA, project management tools, etc.)

### Cons

**Reduced Control**
- No model selection or parameter tuning
- Natural language responses may vary in format
- Less predictable than direct CRUD operations

**Performance Trade-offs**
- AI processing adds latency compared to direct API calls
- Higher computational cost per request##
 TODO

### 1. Security Architecture
- **KAS Integration**: Implement Key Admin Service (KAS) for authentication to this service
- **AI Platform Authentication**: Define how this service authenticates to the underlying AI platform
- **Token Management**: Establish token lifecycle and refresh patterns
- **Authorization Model**: Define fine-grained permissions for story map operations

### 2. Streaming Support
- **Real-time Responses**: Add streaming support for AI operations to improve user experience
- **Server-Sent Events**: Implement SSE protocol for incremental content delivery
- **Stream Parameter**: Add optional `stream` parameter to AI request schema
- **Partial Results**: Define how to handle partial responses and error recovery

### 3. Concurrent Editing Guarantees
- **Optimistic Locking**: Implement version-based conflict detection for story map updates
- **Merge Strategies**: Define how to handle conflicting edits to the same story map
- **Real-time Sync**: Consider WebSocket support for live collaboration
- **Consistency Model**: Establish guarantees around data consistency during concurrent operations
- **Conflict Resolution**: Define user experience for handling edit conflicts