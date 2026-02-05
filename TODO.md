# TODO - Implementation Roadmap

## 0. Setup & CI/CD
- [ ] Initialize Git repo with `backend/` and `frontend/` folders.
- [ ] Create `docker-compose.yml` for full stack development.
- [ ] Create `scripts/setup_dev.py` to automate environment setup.
- [ ] **Testing Pipeline:**
    - [ ] Configure GitHub Action to run `pytest` on push.
    - [ ] Configure GitHub Action to run `npm test` on push.

## 1. Backend Core
- [ ] Implement `database.py` with SQLite connection string.
- [ ] Create `models.py` with User, Dish, Order schemas.
- [ ] Write tests for DB creation and connectivity.

## 2. Feature: Chef Ordering (TDD)
- [ ] **Test:** Write `test_create_order` (expect 200 OK).
- [ ] **Impl:** Create POST endpoint.
- [ ] **Test:** Write `test_auto_assign_logic` (verify it finds yesterday's cook).
- [ ] **Impl:** Create prediction logic.

## 3. Feature: Cook Display
- [ ] **Test:** Write `test_fetch_orders_by_dept`.
- [ ] **Impl:** Create GET endpoint.
- [ ] **Frontend:** Build `Swimlane` component.
- [ ] **Frontend:** Implement `useLocalStorage` hook for "Done" status.

## 4. Resilience & Polish
- [ ] Add Retry logic to Google Translate wrapper.
- [ ] Verify Dockerfile setup for Railway Volume mounting.