# TODO - Amendment System

Based on existing fis-amendments .NET application structure.

## High Priority - Backend Core

- [x] Create database models matching fis-amendments structure:
  - Amendment model with fields:
    - amendment_id (Primary Key, auto-increment)
    - amendment_reference (string, unique reference number)
    - amendment_type (enum/string: 'Bug', 'Enhancement', 'Feature', etc.)
    - description (text, required)
    - amendment_status (string: 'Open', 'In Progress', 'Testing', 'Completed', 'Deployed')
    - development_status (string: 'Not Started', 'In Development', 'Code Review', 'Ready for QA')
    - priority (enum: 'Low', 'Medium', 'High', 'Critical')
    - force (string, e.g., 'Army', 'Navy', 'RAF', 'All')
    - application (string, affected application name)
    - notes (text, optional)
    - reported_by (string)
    - assigned_to (string)
    - date_reported (datetime)
    - created_by, created_on (audit fields)
    - modified_by, modified_on (audit fields)
    - database_changes (boolean)
    - db_upgrade_changes (boolean)
    - release_notes (text)
    - QA fields:
      - qa_assigned_id (integer, nullable)
      - qa_assigned_date (datetime, nullable)
      - qa_test_plan_check (boolean)
      - qa_test_release_notes_check (boolean)
      - qa_completed (boolean)
      - qa_signature (string)
      - qa_completed_date (datetime, nullable)
      - qa_notes (text)
      - qa_test_plan_link (string, URL)

  - AmendmentProgress model (tracks progress updates):
    - amendment_progress_id (Primary Key)
    - amendment_id (Foreign Key)
    - start_date (datetime)
    - description (text)
    - notes (text)
    - created_by, created_on, modified_by, modified_on

  - AmendmentApplication model (many-to-many: amendments to applications):
    - amendment_id (Foreign Key)
    - application_name (string)
    - version (string)

  - AmendmentLink model (link related amendments):
    - amendment_link_id (Primary Key)
    - amendment_id (Foreign Key)
    - linked_amendment_id (integer)
    - link_type (string: 'Related', 'Duplicate', 'Blocks', 'Blocked By')

- [x] Set up SQLAlchemy database configuration and connection
  - Create database.py with SQLite connection
  - Create Base model class with common audit fields
  - Add database session management and dependencies

- [x] Create Pydantic schemas for request/response validation
  - AmendmentCreate schema (all required fields)
  - AmendmentUpdate schema (partial updates)
  - AmendmentResponse schema (full model with relationships)
  - AmendmentList/Summary schema (for list views)
  - AmendmentProgressCreate/Response schemas
  - AmendmentFilter schema (for search criteria)

- [x] Implement CRUD operations in crud.py
  - Create amendment with auto-generated reference number
  - Read amendment by ID or reference
  - List all amendments with filtering:
    - By status, development status
    - By priority, force, application
    - By date range (date_reported, created_on)
    - By assigned person, reported by
    - Search by description/notes
  - Update amendment (with audit tracking)
  - Delete/archive amendment
  - Add progress entry to amendment
  - Link related amendments

- [ ] Build FastAPI endpoints in main.py
  - POST /api/amendments - Create new amendment
  - GET /api/amendments - List all with advanced filtering/pagination
  - GET /api/amendments/{id} - Get specific amendment with full details
  - GET /api/amendments/reference/{ref} - Get by reference number
  - PUT /api/amendments/{id} - Update amendment
  - DELETE /api/amendments/{id} - Delete amendment
  - POST /api/amendments/{id}/progress - Add progress update
  - GET /api/amendments/{id}/progress - Get amendment progress history
  - POST /api/amendments/{id}/links - Link related amendments
  - GET /api/amendments/stats - Statistics (count by status, priority, etc.)
  - GET /api/amendments/reference/next - Get next available reference number

- [ ] Add lookup/reference data endpoints
  - GET /api/reference/statuses - List amendment statuses
  - GET /api/reference/dev-statuses - List development statuses
  - GET /api/reference/priorities - List priorities
  - GET /api/reference/forces - List forces
  - GET /api/reference/applications - List applications
  - GET /api/reference/types - List amendment types

- [ ] Add CORS middleware to allow frontend access

- [ ] Create database initialization/seeding script
  - Create all tables on startup
  - Seed reference data (statuses, priorities, forces, types, applications)

## High Priority - Frontend Core

- [ ] Set up React project structure
  - Create src/components directory (reusable UI components)
  - Create src/pages directory (main pages)
  - Create src/services directory (API service layer)
  - Create src/utils directory (helpers, formatters)
  - Create src/hooks directory (custom React hooks)
  - Create src/context directory (global state if needed)

- [ ] Create API service layer (src/services/api.js)
  - Axios configuration with base URL and interceptors
  - Amendment API functions:
    - createAmendment(data)
    - getAmendments(filters, pagination)
    - getAmendmentById(id)
    - getAmendmentByReference(ref)
    - updateAmendment(id, data)
    - deleteAmendment(id)
    - getNextReference()
  - Progress API functions:
    - addProgress(amendmentId, data)
    - getProgressHistory(amendmentId)
  - Reference data API functions:
    - getStatuses(), getPriorities(), getForces(), etc.

- [ ] Build Amendment Enquiry/List page (matching fis-amendments AmendmentEnquiry.cs)
  - Advanced search/filter panel:
    - Filter by reference number
    - Filter by status, development status
    - Filter by priority, force, application
    - Filter by date range
    - Filter by assigned to, reported by
    - Text search in description/notes
  - Results grid/table with columns:
    - Reference, Type, Description (truncated)
    - Status, Development Status, Priority
    - Application, Force
    - Reported By, Assigned To
    - Date Reported, Created/Modified dates
  - Sortable columns
  - Pagination
  - Click row to view details
  - Export to CSV/Excel functionality

- [ ] Build Amendment Detail/Update page (matching fis-amendments AmendmentUpdate.cs)
  - View/Edit mode toggle
  - Tab/section layout:
    - General Information tab:
      - Reference (read-only)
      - Type, Priority, Force dropdowns
      - Status, Development Status dropdowns
      - Application selector
      - Reported By, Assigned To
      - Date Reported picker
      - Description (textarea)
      - Notes (textarea)
    - Development tab:
      - Database Changes checkbox
      - DB Upgrade Changes checkbox
      - Release Notes (textarea)
    - QA tab:
      - QA Assigned dropdown
      - QA Assigned Date picker
      - QA Test Plan Check checkbox
      - QA Test Release Notes Check checkbox
      - QA Completed checkbox
      - QA Completed Date picker
      - QA Signature
      - QA Notes (textarea)
      - QA Test Plan Link
    - Progress History tab:
      - List all progress updates
      - Add new progress entry form
    - Related Amendments tab:
      - Link to related amendments
      - View linked amendments
  - Save/Cancel buttons
  - Delete button (with confirmation)
  - Audit info display (Created By/On, Modified By/On)

- [ ] Build Amendment Create form (new amendment)
  - Auto-fetch next available reference number
  - Required field validation (Type, Priority, Description, Application)
  - All fields from Update page but in create mode
  - Save button creates amendment and navigates to detail

- [ ] Build Progress Update modal/dialog
  - Start Date picker
  - Description (textarea, required)
  - Notes (textarea)
  - Add to amendment

- [ ] Create main App.js with routing
  - / - Amendment List/Enquiry page
  - /amendments/new - Create new amendment
  - /amendments/:id - View/Edit amendment detail
  - /amendments/reference/:ref - View amendment by reference

- [ ] Build reusable UI components
  - DataGrid/Table component with sorting, filtering
  - FormField components (Input, TextArea, Select, DatePicker, Checkbox)
  - Modal/Dialog component
  - SearchPanel component
  - StatusBadge component (color-coded by status)
  - PriorityIndicator component

- [ ] Add professional styling
  - Use Material-UI or Tailwind CSS for consistent look
  - Responsive design for tablet/desktop
  - Color-coded status badges
  - Professional business application theme

## Medium Priority - Features

- [ ] Add advanced filtering and saved searches
  - Save frequently used search criteria
  - Quick filters for common queries

- [ ] Implement dashboard/home page
  - Statistics cards (total amendments, by status, by priority)
  - Recent amendments list
  - My assigned amendments
  - Amendments requiring QA
  - Upcoming amendments calendar

- [ ] Add bulk operations
  - Bulk status updates
  - Bulk assignment
  - Bulk export to Excel/CSV

- [ ] Create amendment reporting features
  - Amendments by application report
  - Amendments by force report
  - Development status report
  - QA status report
  - Custom date range reports

- [ ] Add email/notification system
  - Email when assigned an amendment
  - Email when amendment status changes
  - Email when QA is required
  - Configurable notification preferences

- [ ] Implement user/employee management
  - User profiles
  - Role-based permissions (Developer, QA, Manager, Admin)
  - Assignment to users rather than strings

## High Priority - Code Quality & Technical Debt

- [ ] **CRITICAL: Fix SQLAlchemy deprecation warning**
  - Impact: HIGH | Effort: LOW | Priority: CRITICAL
  - Replace deprecated `declarative_base()` with SQLAlchemy 2.0 pattern
  - File: `backend/app/database.py:2,16`
  - Change from `sqlalchemy.ext.declarative.declarative_base()` to `sqlalchemy.orm.DeclarativeBase`
  - Prevents breaking changes in future SQLAlchemy versions
  - Quick win: ~5 minute fix, eliminates deprecation warnings

- [ ] **CRITICAL: Add database connection error handling**
  - Impact: HIGH | Effort: MEDIUM | Priority: CRITICAL
  - Add try-catch for database connection failures in `database.py`
  - Validate DATABASE_URL format before connecting
  - Make `check_same_thread` SQLite-specific (breaks PostgreSQL/MySQL)
  - Add connection retry logic and meaningful error messages
  - Prevents silent failures and improves reliability

- [ ] **HIGH: Fix missing foreign key constraint on AmendmentLink**
  - Impact: HIGH | Effort: LOW | Priority: HIGH
  - File: `backend/app/models.py:178`
  - Add `ForeignKey("amendments.amendment_id")` to `linked_amendment_id`
  - Add `ondelete="CASCADE"` for proper cleanup
  - Prevents orphaned amendment links and data integrity issues
  - Write test to verify constraint works

- [ ] **HIGH: Move CORS configuration to environment variables**
  - Impact: MEDIUM | Effort: LOW | Priority: HIGH
  - File: `backend/app/main.py:21`
  - Replace hardcoded `["http://localhost:3000"]` with env var `CORS_ORIGINS`
  - Add security headers (X-Content-Type-Options, X-Frame-Options, X-XSS-Protection)
  - Add TrustedHostMiddleware
  - Restrict allow_methods and allow_headers to specific values
  - Update `.env.example` with CORS_ORIGINS and ALLOWED_HOSTS

- [ ] **HIGH: Add type hints throughout codebase**
  - Impact: MEDIUM | Effort: MEDIUM | Priority: HIGH
  - Add return type hint to `get_db()` function: `Generator[Session, None, None]`
  - Add type hints to all CRUD functions (when created)
  - Add `__repr__` methods to all models for better debugging
  - Run mypy for type checking (add to requirements.txt)
  - Improves IDE support and catches type-related bugs early

## High Priority - Developer Experience & Tooling

- [ ] **Setup pre-commit hooks for code quality**
  - Impact: VERY HIGH | Effort: MEDIUM | Priority: CRITICAL
  - Create `.pre-commit-config.yaml` with hooks for:
    - black (Python formatting)
    - flake8 (Python linting)
    - isort (import sorting)
    - prettier (JS/React formatting)
    - eslint (JS/React linting)
    - trailing whitespace removal
  - Add `isort` and `pre-commit` to requirements.txt
  - Add `prettier` and `eslint` to frontend package.json
  - Create `pyproject.toml` for black/isort configuration
  - Prevents bad code from being committed, enforces consistency

- [ ] **Create Makefile for development workflow**
  - Impact: VERY HIGH | Effort: MEDIUM | Priority: HIGH
  - Create comprehensive Makefile with targets:
    - `make setup` - Initial project setup (venv, npm install, db init)
    - `make dev` - Run backend + frontend concurrently
    - `make test` - Run all tests (pytest + jest)
    - `make lint` - Run all linters
    - `make format` - Auto-format all code
    - `make clean` - Clean build artifacts and databases
    - `make db-reset` - Reset and seed database
    - `make help` - Show all available commands
  - Reduces onboarding time from hours to minutes

- [ ] **Add Docker Compose for local development**
  - Impact: VERY HIGH | Effort: MEDIUM | Priority: HIGH
  - Create `docker-compose.yml` with services:
    - Backend (Python 3.9 + FastAPI with hot reload)
    - Frontend (Node.js + React with hot reload)
    - Volume for SQLite database persistence
  - Create `backend/Dockerfile` and `frontend/Dockerfile`
  - Create `.dockerignore` files
  - Update `.gitignore` to exclude `*.db` files
  - Eliminates "works on my machine" problems

- [ ] **Setup CI/CD pipeline with GitHub Actions**
  - Impact: HIGH | Effort: MEDIUM | Priority: HIGH
  - Create `.github/workflows/ci.yml` for automated testing:
    - Run pytest with coverage reporting
    - Run linters (black, flake8, isort)
    - Run frontend tests (when implemented)
    - Validate frontend builds successfully
  - Create `.github/workflows/pr-validation.yml` for PR checks
  - Add `pytest-cov` to requirements.txt for coverage reports
  - Enforce minimum 80% test coverage
  - Catches bugs before they reach main branch

- [ ] **Add database seeding script**
  - Impact: HIGH | Effort: LOW | Priority: HIGH
  - Create `scripts/seed_db.py` to populate reference data:
    - Sample amendments with various statuses
    - Sample progress updates
    - Sample linked amendments
    - All enum values (statuses, priorities, forces, types)
  - Add CLI argument for number of sample records
  - Makes local development and testing much easier

## Medium Priority - Testing & Quality

- [ ] Write comprehensive backend API tests (pytest)
  - Test all CRUD endpoints for amendments
  - Test progress tracking endpoints
  - Test reference data endpoints
  - Test filtering and search functionality
  - Test validation (required fields, data types)
  - Test error handling and edge cases
  - Test database constraints (unique references, foreign keys)

- [ ] Write frontend component tests (Jest/React Testing Library)
  - Test Amendment list rendering and filtering
  - Test Create/Update form validation and submission
  - Test Progress update functionality
  - Test search and filter controls
  - Test navigation and routing

- [ ] Add API documentation with FastAPI auto-docs
  - Ensure /docs endpoint works and is comprehensive
  - Add detailed descriptions to all endpoints
  - Document request/response schemas
  - Add example requests and responses
  - Document error codes and messages

- [ ] Add comprehensive input validation and error handling
  - Backend validation for all required fields
  - Database constraint validation
  - Business rule validation (e.g., QA complete requires certain fields)
  - Proper HTTP status codes (400, 404, 422, 500)
  - User-friendly validation error messages
  - Frontend validation with helpful hints
  - Error display with actionable messages

## Low Priority - Enhancements

- [ ] Add file attachment support
  - Upload test plans, documents, screenshots
  - Store files in filesystem or cloud storage
  - Link files to amendments
  - Download/preview attachments

- [ ] Create advanced analytics and charts
  - Amendment trends over time (line charts)
  - Status distribution (pie charts)
  - Priority distribution by application
  - Average time to completion by type
  - Developer/QA productivity metrics

- [ ] Add amendment templates
  - Template for common amendment types
  - Pre-fill common fields
  - Template management

- [ ] Implement full-text search
  - Search across description, notes, release notes
  - Highlight search terms in results
  - Advanced search operators

- [ ] Add amendment workflow automation
  - Auto-assign based on application or type
  - Auto-progress status when QA completes
  - Auto-send notifications based on rules

- [ ] Create mobile-responsive design
  - Mobile view for amendment list
  - Mobile-friendly forms
  - Touch-optimized UI

- [ ] Add dark mode theme toggle

- [ ] Implement real-time updates
  - WebSocket connection for live updates
  - Show when other users are viewing/editing
  - Auto-refresh when changes occur

## Medium Priority - Documentation

- [ ] **Create comprehensive developer documentation**
  - Impact: HIGH | Effort: MEDIUM | Priority: MEDIUM
  - Create `CONTRIBUTING.md` with:
    - Code style guidelines
    - PR submission process
    - Commit message conventions
    - How to add new features
  - Create `docs/DEVELOPMENT.md` with:
    - Project architecture overview
    - Local development workflow
    - How to run tests
    - Debugging tips
  - Create `docs/API.md` with endpoint usage examples
  - Create `docs/DATABASE.md` with schema documentation
  - Reduces onboarding friction for new developers

- [ ] **Add pytest coverage reporting and enforcement**
  - Impact: MEDIUM | Effort: LOW | Priority: MEDIUM
  - Update `pytest.ini` with coverage configuration:
    - `--cov=backend/app` for coverage tracking
    - `--cov-report=html` for detailed HTML reports
    - `--cov-report=term-missing` for terminal output
    - `--cov-fail-under=80` to enforce minimum coverage
  - Add `pytest-cov` to requirements.txt
  - Add coverage reports to `.gitignore`
  - Ensures test quality and catches untested code

- [ ] **Add database indexing for performance**
  - Impact: MEDIUM | Effort: LOW | Priority: MEDIUM
  - Add indexes to frequently queried fields in models.py:
    - `amendment_status` (for status filtering)
    - `development_status` (for development filtering)
    - `assigned_to` (for user assignment queries)
    - `priority` (for priority filtering)
    - `created_on` and `modified_on` (for date range queries)
    - `application` (for application filtering)
  - Improves query performance as dataset grows

## DevOps & Deployment

- [ ] **Add database migration support (Alembic)**
  - Impact: CRITICAL | Effort: MEDIUM | Priority: HIGH
  - Install Alembic and create initial migration
  - Create `alembic.ini` configuration
  - Create migration scripts in `backend/alembic/versions/`
  - Document migration workflow in `docs/DATABASE.md`
  - Critical for production deployments and schema changes
  - Prevents data loss during schema updates

- [ ] Add environment configuration (.env file handling)
  - Database URL
  - API URL for frontend
  - CORS origins

- [ ] Write deployment documentation
  - How to deploy to production
  - Environment setup instructions
  - Security hardening checklist

## High Priority - New Strategic Improvements (Added by PLANNER)

- [x] **CRITICAL: Implement CRUD operations module (backend/app/crud.py)** ✅ COMPLETED
  - Impact: CRITICAL | Effort: HIGH | Priority: CRITICAL
  - **BLOCKER**: No CRUD module exists - API endpoints cannot function without it
  - The backend currently has models, schemas, and main.py but NO crud.py
  - API endpoints in TODO depend on CRUD functions that don't exist yet
  - Create comprehensive CRUD operations:
    - `create_amendment()` - with auto-generated reference number ✅
    - `get_amendment()`, `get_amendment_by_reference()` ✅
    - `get_amendments()` - with advanced filtering (status, priority, dates, text search) ✅
    - `update_amendment()` - with audit field tracking ✅
    - `delete_amendment()` ✅
    - `add_amendment_progress()`, `get_amendment_progress()` ✅
    - `link_amendments()`, `get_linked_amendments()` ✅
    - `get_amendment_stats()` - for dashboard statistics ✅
  - Add proper error handling for not found, duplicate references ✅
  - Add transaction management and rollback on errors ✅
  - **BLOCKS**: All API endpoint implementation work
  - **ESTIMATED EFFORT**: 4-6 hours of focused development
  - File: `backend/app/crud.py` ✅ CREATED

- [ ] **HIGH: Implement core API endpoints (backend/app/main.py)**
  - Impact: CRITICAL | Effort: HIGH | Priority: HIGH
  - **DEPENDS ON**: CRUD operations module must be completed first
  - Currently main.py only has root and health check endpoints
  - Implement 15+ essential API endpoints for amendments:
    - POST /api/amendments - Create amendment
    - GET /api/amendments - List with filtering/pagination
    - GET /api/amendments/{id} - Get by ID
    - GET /api/amendments/reference/{ref} - Get by reference
    - PUT /api/amendments/{id} - Update amendment
    - DELETE /api/amendments/{id} - Delete amendment
    - PATCH /api/amendments/{id}/qa - Update QA fields
    - POST /api/amendments/{id}/progress - Add progress
    - GET /api/amendments/{id}/progress - Get progress history
    - POST /api/amendments/{id}/links - Link amendments
    - GET /api/amendments/stats - Dashboard statistics
    - GET /api/reference/* - Reference data endpoints
  - Add proper request validation, error handling, status codes
  - Add comprehensive API documentation strings
  - Add request/response examples in docstrings
  - **ESTIMATED EFFORT**: 6-8 hours
  - File: `backend/app/main.py:80+` (after existing code)

- [ ] **HIGH: Create comprehensive API integration tests**
  - Impact: HIGH | Effort: MEDIUM | Priority: HIGH
  - Currently only have basic model and schema tests
  - NO API endpoint tests exist yet
  - Create `tests/test_api.py` with comprehensive endpoint testing:
    - Test all CRUD operations via API
    - Test filtering, pagination, sorting
    - Test validation and error responses (400, 404, 422)
    - Test progress tracking workflow
    - Test amendment linking
    - Test statistics endpoint
    - Test reference data endpoints
  - Add test fixtures for sample data
  - Test concurrent access scenarios
  - Aim for 80%+ API coverage
  - **ESTIMATED EFFORT**: 4-5 hours
  - File: `tests/test_api.py` (to be created)

- [ ] **HIGH: Implement frontend React application**
  - Impact: CRITICAL | Effort: VERY HIGH | Priority: HIGH
  - **CRITICAL GAP**: Frontend directory exists but is completely empty (only .gitkeep files)
  - No React components, no pages, no API client, no routing - nothing implemented
  - This is the largest remaining work item for MVP
  - Required components (from TODO but not started):
    - Amendment List/Enquiry page with advanced filtering
    - Amendment Detail/Update page with tabbed layout
    - Amendment Create form
    - Progress update modal
    - Reusable UI components (DataGrid, FormFields, StatusBadge)
    - API service layer (src/services/api.js)
    - Routing (React Router)
  - Setup required:
    - Initialize React app structure (if not done)
    - Install dependencies (axios, react-router-dom, UI library)
    - Create directory structure (components, pages, services, utils)
  - **ESTIMATED EFFORT**: 20-30 hours (this is a major undertaking)
  - **RECOMMENDATION**: Break this into smaller sub-tasks
  - Directory: `frontend/src/` (currently empty)

- [ ] **MEDIUM: Add authentication and authorization system**
  - Impact: HIGH | Effort: HIGH | Priority: MEDIUM
  - **SECURITY GAP**: No authentication exists - API is completely open
  - Current state: No auth, anyone can access/modify all data
  - Implement JWT-based authentication:
    - User login/logout endpoints
    - JWT token generation and validation
    - Password hashing (bcrypt)
    - User model with roles (Developer, QA, Manager, Admin)
    - Protect endpoints with auth middleware
    - Role-based permissions (e.g., only QA can mark QA complete)
  - Add OAuth2 support for enterprise SSO
  - Update frontend to handle auth tokens
  - Add login page and protected routes
  - **SECURITY IMPACT**: Currently a critical vulnerability
  - **ESTIMATED EFFORT**: 8-10 hours
  - Files: `backend/app/auth.py`, `backend/app/users.py` (to be created)

- [ ] **MEDIUM: Add request rate limiting and API security**
  - Impact: MEDIUM | Effort: LOW | Priority: MEDIUM
  - **SECURITY GAP**: No rate limiting, vulnerable to abuse
  - Add SlowAPI or similar for rate limiting:
    - Limit requests per IP/user (e.g., 100 req/min)
    - Different limits for read vs write operations
    - 429 Too Many Requests responses
  - Add security headers middleware:
    - X-Content-Type-Options: nosniff
    - X-Frame-Options: DENY
    - X-XSS-Protection: 1; mode=block
    - Strict-Transport-Security (HTTPS only)
  - Add request/response size limits
  - Add TrustedHostMiddleware
  - Add request ID tracking for debugging
  - **ESTIMATED EFFORT**: 2-3 hours
  - File: `backend/app/main.py` (middleware section)

- [ ] **MEDIUM: Create database seeding and sample data script**
  - Impact: MEDIUM | Effort: LOW | Priority: MEDIUM
  - **DX GAP**: No way to populate database with test data
  - Makes local development and demos difficult
  - Create `scripts/seed_db.py`:
    - CLI arguments for number of records
    - Generate realistic sample amendments (20-50)
    - Various statuses, priorities, types
    - Sample progress updates
    - Sample linked amendments
    - Sample QA data
  - Add `--reset` flag to clear and reseed
  - Document usage in README
  - **DEVELOPER EXPERIENCE IMPACT**: Saves hours of manual data entry
  - **ESTIMATED EFFORT**: 2-3 hours
  - File: `scripts/seed_db.py` (to be created)

- [ ] **MEDIUM: Add comprehensive error handling and logging**
  - Impact: MEDIUM | Effort: MEDIUM | Priority: MEDIUM
  - **OPERATIONAL GAP**: Limited error handling and logging
  - Add structured logging:
    - Use structlog or python-json-logger
    - Log all API requests (endpoint, method, status, duration)
    - Log all database operations
    - Log authentication attempts
    - Different log levels (DEBUG, INFO, WARNING, ERROR)
  - Add custom exception handlers:
    - DatabaseError -> 500 Internal Server Error
    - ValidationError -> 422 Unprocessable Entity
    - NotFoundError -> 404 Not Found
    - AuthenticationError -> 401 Unauthorized
  - Return consistent error response format
  - Add correlation IDs for request tracking
  - **ESTIMATED EFFORT**: 3-4 hours
  - Files: `backend/app/logging.py`, `backend/app/exceptions.py` (to be created)

## Medium Priority - Testing & Quality Enhancements (Added by PLANNER)

- [ ] **MEDIUM: Implement integration tests for database operations**
  - Impact: MEDIUM | Effort: MEDIUM | Priority: MEDIUM
  - Current tests are unit tests only (models, schemas, database connection)
  - Add integration tests that verify end-to-end workflows:
    - Create amendment -> Add progress -> Update status -> Complete QA
    - Test cascade deletes (amendment -> progress entries)
    - Test foreign key constraints
    - Test concurrent updates (optimistic locking)
    - Test transaction rollback scenarios
  - Create `tests/test_integration.py`
  - **ESTIMATED EFFORT**: 3-4 hours
  - File: `tests/test_integration.py` (to be created)

- [ ] **MEDIUM: Add API performance tests and benchmarks**
  - Impact: MEDIUM | Effort: MEDIUM | Priority: MEDIUM
  - No performance testing exists
  - As dataset grows, need to ensure queries remain fast
  - Create `tests/test_performance.py`:
    - Test list endpoint with 1000+ amendments
    - Test filtering performance
    - Test text search performance
    - Benchmark response times (<200ms target)
    - Test database query counts (N+1 query detection)
  - Use pytest-benchmark for measurements
  - **ESTIMATED EFFORT**: 2-3 hours
  - File: `tests/test_performance.py` (to be created)

- [ ] **LOW: Add frontend unit tests (Jest/React Testing Library)**
  - Impact: MEDIUM | Effort: MEDIUM | Priority: LOW
  - **DEPENDS ON**: Frontend implementation must exist first
  - No frontend tests exist (frontend not implemented yet)
  - Once frontend is built, add:
    - Component unit tests
    - Integration tests for forms
    - API service mocking
    - Router navigation tests
  - Aim for 70%+ frontend coverage
  - **ESTIMATED EFFORT**: 6-8 hours (after frontend exists)
  - Directory: `frontend/src/__tests__/` (to be created)

## Low Priority - Developer Experience (Added by PLANNER)

- [ ] **MEDIUM: Add API response caching**
  - Impact: MEDIUM | Effort: MEDIUM | Priority: LOW
  - Improve performance for frequently accessed data
  - Add Redis-based caching:
    - Cache reference data (statuses, priorities, etc.) - rarely changes
    - Cache amendment stats (5-minute TTL)
    - Cache individual amendments (1-minute TTL)
    - Cache-Control headers for frontend
  - Add cache invalidation on updates
  - Make Redis optional (fallback to in-memory cache)
  - **ESTIMATED EFFORT**: 4-5 hours
  - File: `backend/app/cache.py` (to be created)

- [ ] **LOW: Add database query optimization and indexing strategy**
  - Impact: LOW | Effort: LOW | Priority: LOW
  - Current models have basic indexes, but could be optimized
  - Add composite indexes for common queries:
    - (amendment_status, priority) - status+priority filtering
    - (assigned_to, amendment_status) - user's assignments
    - (created_on DESC) - recent amendments
    - (application, amendment_status) - app-specific queries
  - Add SQL query profiling in development
  - Document indexing strategy
  - **ESTIMATED EFFORT**: 2-3 hours
  - File: `backend/app/models.py` (index additions)

- [ ] **LOW: Create API usage documentation with examples**
  - Impact: LOW | Effort: MEDIUM | Priority: LOW
  - FastAPI auto-docs exist but lack examples
  - Create `docs/API_EXAMPLES.md`:
    - Complete curl examples for each endpoint
    - Example request/response payloads
    - Common filtering patterns
    - Error handling examples
    - Pagination examples
  - Add Postman collection export
  - **ESTIMATED EFFORT**: 3-4 hours
  - File: `docs/API_EXAMPLES.md` (to be created)

## Completed

- [x] Initialize git repository
- [x] Create project structure (backend/frontend/tests/docs)
- [x] Create README.md
- [x] Create requirements.txt
- [x] Create package.json
