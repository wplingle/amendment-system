# Shared Task Notes - Amendment System

## Current Status

**Backend API: ✅ COMPLETE**
- All CRUD operations implemented and tested (148 tests passing)
- All API endpoints implemented and working:
  - Amendment CRUD: create, read, update, delete
  - Progress tracking: add/get progress updates
  - Amendment linking: link/unlink related amendments
  - Statistics: dashboard stats endpoint
  - Reference data: statuses, priorities, types, forces, etc.
- Server running successfully on port 8000

## What's Working

1. **Backend (FastAPI + SQLite)**
   - Database models for amendments, progress, applications, links
   - Comprehensive CRUD operations in `backend/app/crud.py`
   - All API endpoints in `backend/app/main.py`
   - Pydantic schemas for validation
   - 148 passing tests

2. **API Endpoints Available**
   ```
   POST   /api/amendments                    - Create amendment
   GET    /api/amendments                    - List with filtering/pagination
   GET    /api/amendments/stats              - Get statistics
   GET    /api/amendments/{id}               - Get by ID
   GET    /api/amendments/reference/{ref}    - Get by reference
   PUT    /api/amendments/{id}               - Update
   DELETE /api/amendments/{id}               - Delete
   POST   /api/amendments/{id}/progress      - Add progress
   GET    /api/amendments/{id}/progress      - Get progress history
   POST   /api/amendments/{id}/links         - Link amendments
   GET    /api/reference/*                   - Reference data
   ```

3. **Tested & Working**
   - Created test amendment via API
   - Added progress update
   - Updated amendment status
   - Retrieved statistics
   - All reference data endpoints working

## Next Steps (Priority Order)

### 1. Frontend Implementation (CRITICAL - NOTHING EXISTS)
**Status**: The `frontend/` directory exists but is completely empty (only .gitkeep files)

This is the **LARGEST REMAINING WORK ITEM** for a working system:
- Initialize React app (if needed) or use existing structure
- Install dependencies: axios, react-router-dom, UI library (Material-UI/Tailwind)
- Create directory structure: components/, pages/, services/, utils/, hooks/
- Build API service layer (src/services/api.js) to call backend
- Create Amendment List/Enquiry page with filtering
- Create Amendment Detail/Update page
- Create Amendment Create form
- Add progress update modal
- Implement routing

**Estimated effort**: 20-30 hours

### 2. API Integration Tests
Create `tests/test_api.py` to test all endpoints end-to-end:
- Test full CRUD workflows via HTTP
- Test filtering, pagination, sorting
- Test error responses (404, 400, 422)
- Test progress and linking workflows

**Estimated effort**: 4-5 hours

### 3. Database Seeding Script (RECOMMENDED NEXT)
Create `scripts/seed_db.py` to populate test data:
- Generate 20-50 sample amendments
- Various statuses, priorities, types
- Sample progress updates and links
- Makes frontend development much easier

**Estimated effort**: 2-3 hours

### 4. Authentication (SECURITY GAP)
Currently no auth - API is completely open:
- Implement JWT authentication
- User model with roles
- Protected endpoints
- Login/logout

**Estimated effort**: 8-10 hours

## Technical Notes

- **Server**: Run with `cd backend && source venv/bin/activate && uvicorn app.main:app --reload`
- **Tests**: Run with `source backend/venv/bin/activate && pytest`
- **Database**: SQLite file at `amendment_system.db` (auto-created)
- **Route Order**: Stats endpoint MUST be before `{amendment_id}` to avoid collision

## Known Issues / Technical Debt

From TODO.md:
- SQLAlchemy deprecation warning (use DeclarativeBase instead of declarative_base)
- Missing foreign key constraint on AmendmentLink.linked_amendment_id
- CORS origins hardcoded (should use env vars)
- No rate limiting or security headers
- No database migrations (Alembic not setup)

## Goal

Get this system working so it can replace fis-amendments, then migrate data over.

**NEXT DEVELOPER**: Start with the database seeding script (makes testing easier), then tackle the frontend implementation. The backend is solid and ready to use.
