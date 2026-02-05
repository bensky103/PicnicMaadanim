# Project State & Status

## Project Overview
**System:** Picnic Delicacies - Kitchen Production Management
**Stack:** React (Vite) | Python (FastAPI) | SQLite (Railway Volume) | Docker & Docker Compose
**Current Phase:** Initialization

## Master Feature Checklist

### Phase 0: Infrastructure & CI/CD
- [ ] **Docker Setup:** Create `Dockerfile` and `docker-compose.yml` for local dev (Backend + Frontend + Nginx proxy if needed).
- [ ] **Testing Pipeline:** Setup GitHub Actions for `pytest` and `npm test`.

### Phase 1: Foundation (Backend)
- [ ] **Setup FastAPI Project:** Initialize structure with Poetry/Pipenv.
- [ ] **Database Setup:** Configure SQLModel with SQLite.
- [ ] **Railway Configuration:** Create `railway.json` and Volume mount setup.
- [ ] **Data Models:** Define `User`, `Dish`, `Order`, `TranslationCache`.

### Phase 2: The Chef's Domain (/order)
- [ ] **API - Create Order:** Endpoint to accept dish, amount, and cook assignment.
- [ ] **API - "Yesterday's Logic":** Query to fetch the last cook assigned to a dish ID.
- [ ] **UI - Dish Search:** Component to search/filter dishes.
- [ ] **UI - Order Form:** Input for amount and dynamic Cook selection.
- [ ] **Logic - Translation:** Integration with Google Translate API (with Caching).

### Phase 3: The Cook's Domain (/watch)
- [ ] **API - Fetch Orders:** Endpoint to get active orders (filterable by Station/Cook).
- [ ] **UI - Swimlane Layout:** Dynamic columns based on active Cooks in the department.
- [ ] **UI - Local "Done" State:** Click handler to gray out item and persist ID in `localStorage`.
- [ ] **UI - Auto Refresh:** Polling mechanism (SWR/React Query) to fetch new orders every X seconds.

### Phase 4: Admin & Settings (/admin)
- [ ] **CRUD Operations:** Manage Dishes, Users (Cooks), and Departments.
- [ ] **Reset Logic:** Scheduler to clear/archive orders at specific time.

## Change Log
- **2026-02-05:** Architecture defined: FastAPI, SQLite, React. "Cook Status" defined as Client-Side only.