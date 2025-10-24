# Story Mapping API

A RESTful API for managing user stories, activities, and story maps in an agile development workflow.

## Overview

The Story Mapping API provides a hierarchical structure for organizing user stories:
- **StoryMaps** contain multiple **Activities**
- **Activities** contain multiple **Stories**
- **Stories** represent individual user stories with personas, actions, and metadata

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

### 5. Manage External Metadata
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

### 1. Metadata Management
- Always namespace metadata with your consumer key
- Use consistent field names within your namespace
- Keep metadata focused on your specific use case
- Clean up metadata when removing integrations

### 2. Performance
- Use filtering parameters to reduce response sizes
- Access metadata only when needed via dedicated endpoints
- Use pagination for large result sets
- Cache story map structures when possible

## Error Handling

TBD

## File Upload Constraints

When creating story maps from file attachments:
- **Maximum file size**: TBD
- **Supported formats**: TBD
- **Content**: Requirements documents, user research, specifications

