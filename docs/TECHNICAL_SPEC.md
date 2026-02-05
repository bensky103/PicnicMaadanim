# Technical Specification

## 1. Architecture Overview
- **Pattern:** Monolithic Backend (FastAPI) + SPA Frontend (React).
- **Containerization:** Docker & Docker Compose.
- **Hosting:** Railway (Dockerized).
- **Persistence:** SQLite mounted on `/app/data` volume.
- **Offline Strategy:** "Optimistic Read-Only". Cooks receive updates when online. Local interactions ("Done" status) are stored in Browser LocalStorage and do NOT sync back to server.

## 2. Database Schema (SQLModel)

### Table: User (Cooks/Chefs)
- `id`: Int (PK)
- `name`: String
- `role`: Enum (Chef, Cook, Admin)
- `language`: String (e.g., 'he', 'ru', 'ar') - used for translation target.
- `department_id`: Int (FK)

### Table: Dish
- `id`: Int (PK)
- `name_he`: String (Original Hebrew Name)
- `category`: String

### Table: TranslationCache
- `original_text`: String (PK) - e.g., "Schnitzel"
- `target_lang`: String (PK) - e.g., "ru"
- `translated_text`: String
- *Logic:* Composite PK. If `name_he` changes in Dish, matching rows here must be deleted (Cache Invalidation).

### Table: Order
- `id`: Int (PK)
- `dish_id`: Int (FK)
- `assigned_cook_id`: Int (FK)
- `quantity`: String (e.g., "5 kg")
- `created_at`: DateTime
- `is_active`: Boolean (Default True. Set to False by nightly reset).

## 3. API Interfaces

### Chef Endpoints
- `POST /orders`: Create new order.
    - *Input:* `{ dish_id, quantity, cook_id }`
    - *Side Effect:* If this is the first time this dish is ordered today, logic does nothing special. History lookup happens on Frontend or separate "Suggestion" endpoint.
- `GET /dishes/suggestion/{dish_id}`: Returns the `cook_id` who last prepared this dish.

### Cook Endpoints
- `GET /orders/stream?department={id}`:
    - Returns JSON of all `is_active=True` orders for that department.
    - Frontend filters this list into Swimlanes based on `assigned_cook_id`.

## 4. Specific Logic

### The "Yesterday's Cook" Algorithm
1.  **Trigger:** Chef selects a Dish in the UI.
2.  **Action:** UI calls `GET /suggestion`.
3.  **Query:** `SELECT assigned_cook_id FROM orders WHERE dish_id = ? ORDER BY created_at DESC LIMIT 1`.
4.  **Result:** UI pre-fills the Cook dropdown.

### Offline "Done" State
1.  **User Action:** Cook taps Order #123.
2.  **Client Logic:**
    - Apply CSS class `.completed` (Gray out, move to bottom).
    - Save `{"completed_orders": [123, ...]}` to `localStorage`.
3.  **Refresh Logic:** On page reload, fetch orders from API, then apply `.completed` class to any ID found in `localStorage`.