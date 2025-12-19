# Pydantic Schemas Documentation

This document describes the Pydantic schemas used for API request/response validation in the Amendment System.

## Overview

The schemas are defined in `backend/app/schemas.py` and provide:
- Type-safe API validation
- Request/response serialization
- Automatic API documentation
- Data transformation between API and database layers

## Core Schemas

### Amendment Schemas

#### `AmendmentCreate`
Used for creating new amendments.

**Required fields:**
- `amendment_type`: AmendmentType enum (Bug, Enhancement, Feature, etc.)
- `description`: Text description of the amendment

**Optional fields with defaults:**
- `amendment_status`: Defaults to "Open"
- `development_status`: Defaults to "Not Started"
- `priority`: Defaults to "Medium"
- `force`, `application`, `notes`, `reported_by`, `assigned_to`, `date_reported`
- `database_changes`, `db_upgrade_changes`, `release_notes`
- `created_by`: For audit tracking

**Example:**
```json
{
  "amendment_type": "Bug",
  "description": "Login page not loading correctly",
  "priority": "High",
  "application": "Web Portal",
  "reported_by": "John Doe",
  "assigned_to": "Jane Smith"
}
```

#### `AmendmentUpdate`
Used for partial updates to existing amendments. All fields are optional.

**Example:**
```json
{
  "amendment_status": "In Progress",
  "assigned_to": "New Developer",
  "notes": "Started working on this issue",
  "modified_by": "admin"
}
```

#### `AmendmentQAUpdate`
Specialized schema for updating QA-specific fields only.

**Fields:**
- `qa_assigned_id`, `qa_assigned_date`
- `qa_test_plan_check`, `qa_test_release_notes_check`
- `qa_completed`, `qa_signature`, `qa_completed_date`
- `qa_notes`, `qa_test_plan_link`

#### `AmendmentSummary`
Lightweight schema for list views, excludes relationships and QA fields.

**Use case:** Amendment list/grid displays where full details aren't needed.

#### `AmendmentResponse`
Complete amendment data including all fields and relationships.

**Includes:**
- All amendment fields
- `progress_entries`: List of progress updates
- `applications`: List of linked applications
- `links`: List of related amendments
- Audit fields (created_by, created_on, modified_by, modified_on)

### Progress Schemas

#### `AmendmentProgressCreate`
Create a new progress entry for an amendment.

**Required:**
- `description`: What progress was made

**Optional:**
- `start_date`: When work started
- `notes`: Additional details
- `created_by`: Who created this entry

#### `AmendmentProgressResponse`
Full progress entry with audit information.

### Application Schemas

#### `AmendmentApplicationCreate`
Link an amendment to an application.

**Fields:**
- `application_name`: Name of the application (required)
- `version`: Version number (optional)

#### `AmendmentApplicationResponse`
Application link with IDs.

### Link Schemas

#### `AmendmentLinkCreate`
Link two amendments together.

**Fields:**
- `linked_amendment_id`: ID of the amendment to link to (required)
- `link_type`: Type of relationship - "Related", "Duplicate", "Blocks", "Blocked By" (defaults to "Related")

#### `AmendmentLinkResponse`
Link with full details.

## Filter and Search Schemas

### `AmendmentFilter`
Comprehensive filtering for amendment queries.

**Filter options:**
- By reference: `amendment_reference`
- By IDs: `amendment_ids` (list)
- By status: `amendment_status`, `development_status` (lists)
- By priority: `priority` (list)
- By type/force/application: Lists of values
- By people: `assigned_to`, `reported_by` (lists)
- By date ranges: `date_reported_from/to`, `created_on_from/to`, `modified_on_from/to`
- Text search: `search_text` (searches description, notes, release_notes)
- QA filters: `qa_completed`, `qa_assigned` (boolean)
- Database changes: `database_changes`, `db_upgrade_changes` (boolean)

**Pagination:**
- `skip`: Offset (default: 0)
- `limit`: Max results (default: 100, max: 1000)

**Sorting:**
- `sort_by`: Field name (default: "amendment_id")
- `sort_order`: "asc" or "desc" (default: "desc")

**Example:**
```json
{
  "amendment_status": ["Open", "In Progress"],
  "priority": ["High", "Critical"],
  "assigned_to": ["John Doe"],
  "date_reported_from": "2024-01-01T00:00:00",
  "date_reported_to": "2024-12-31T23:59:59",
  "search_text": "login bug",
  "skip": 0,
  "limit": 50,
  "sort_by": "priority",
  "sort_order": "desc"
}
```

### `AmendmentListResponse`
Paginated list response.

**Fields:**
- `items`: List of AmendmentSummary
- `total`: Total count matching filters
- `skip`: Current offset
- `limit`: Current page size

## Statistics and Reference Data

### `AmendmentStats`
System statistics.

**Fields:**
- `total_amendments`: Total count
- `by_status`: Count per status
- `by_priority`: Count per priority
- `by_type`: Count per type
- `by_development_status`: Count per dev status
- `qa_pending`: Count needing QA
- `database_changes_count`: Count with DB changes

### `ReferenceData`
Available enum values for dropdowns.

**Fields:**
- `amendment_types`: List of types
- `amendment_statuses`: List of statuses
- `development_statuses`: List of dev statuses
- `priorities`: List of priorities
- `forces`: List of forces
- `link_types`: List of link types

### `NextReferenceResponse`
Next available amendment reference number.

**Fields:**
- `next_reference`: The generated reference (e.g., "AMD-20241219-001")
- `pattern`: Description of the pattern

## Bulk Operations

### `BulkUpdateRequest`
Update multiple amendments at once.

**Fields:**
- `amendment_ids`: List of IDs to update (min 1 required)
- `updates`: AmendmentUpdate schema with changes to apply

### `BulkUpdateResponse`
Result of bulk update operation.

**Fields:**
- `updated_count`: Number successfully updated
- `failed_ids`: List of IDs that failed
- `errors`: Map of ID to error message

## Validation Rules

### Common Validations
- String lengths are enforced (e.g., reference: 50 chars, application: 100 chars)
- Required fields must be provided
- Enums must use valid values
- Dates must be valid datetime objects
- Min/max constraints on numeric fields

### Field Constraints
- `description`: Min length 1 character
- `limit`: Min 1, max 1000
- `skip`: Min 0
- `sort_order`: Must be "asc" or "desc"
- Email/URL fields validated when present

## Usage in FastAPI

Schemas integrate seamlessly with FastAPI:

```python
from fastapi import APIRouter
from app.schemas import AmendmentCreate, AmendmentResponse

router = APIRouter()

@router.post("/amendments", response_model=AmendmentResponse)
def create_amendment(amendment: AmendmentCreate):
    # amendment is automatically validated
    # Convert to database model and save
    pass
```

FastAPI will:
- Validate incoming JSON against the schema
- Return 422 for validation errors
- Auto-generate OpenAPI/Swagger documentation
- Convert database models to response schemas

## Testing

All schemas have comprehensive test coverage in `tests/test_schemas.py`:
- Creation with required/optional fields
- Default value handling
- Partial updates
- Complex filtering
- Validation error handling
- Enum validation

Run tests with:
```bash
pytest tests/test_schemas.py -v
```

## Next Steps

With schemas in place, you can now:
1. Implement CRUD operations (`backend/app/crud.py`)
2. Create FastAPI endpoints (`backend/app/main.py`)
3. Build the frontend with TypeScript types generated from these schemas
