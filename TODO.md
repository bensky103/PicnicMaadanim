# Picnic Delicacies - Implementation Roadmap

## Phase 0: Project Setup & Infrastructure
Getting the project foundation ready - setting up folders, configuration files, and automatic testing pipelines.

- [ ] **Initialize Git repository** with `backend/` and `frontend/` folders
  - Creates the folder structure to organize backend code (server) and frontend code (user interface)

- [ ] **Create `config.yaml`** for application configuration
  - Configuration file that stores settings like when to reset orders (e.g., midnight)
  - Can be changed without touching code

- [ ] **Create `backend/.env.example`** with environment variable template
  - Template showing what secret credentials are needed: APP_PASSWORD, GOOGLE_TRANSLATE_API_KEY, DATABASE_URL
  - Actual `.env` file won't be committed to git for security

- [ ] **Create Docker Compose configuration** for Railway deployment
  - Docker Compose is a tool that makes it easy to run multiple services together
  - Backend service with SQLite database volume mount for data persistence
  - Frontend service for the user interface
  - One command to start the entire application

- [ ] **Create `scripts/setup_dev.py`** to automate environment setup
  - Python script that automatically installs all required dependencies
  - Sets up the local development environment in one command

- [ ] **Set up GitHub Actions CI/CD pipeline**
  - Automated testing on every code push
  - Backend: Runs pytest (tests) and ruff (code quality checker)
  - Frontend: Runs npm test and npm linter

---

## Phase 1: Backend Core - Database & Models
Building the foundation: database setup and defining what data we store.

- [ ] **Implement `backend/database.py`** with SQLite connection
  - Sets up connection to SQLite database (simple file-based database)
  - Manages database sessions for API requests
  - Tests: Verify database creates successfully and connections work

- [ ] **Create `backend/models.py`** with data models
  - **Station model**: Represents a cooking station/room (e.g., Grill Station, Pastry Station)
    - Fields: id, name, description, created_at
  - **User model**: Represents a cook working in a station
    - Fields: id, name, station_id (which station they work in), created_at
  - **Dish model**: Represents a menu item being prepared
    - Fields: id, name_he (Hebrew name), created_at
  - **Order model**: Represents a cooking order
    - Fields: id, dish_id (which dish), cook_id (assigned cook), station_id (which station), quantity_value, quantity_unit (kg/portions/pieces/other), notes, is_active, created_at, archived_at
  - **TranslationCache model**: Stores translated text to avoid re-translating
    - Fields: id, text_hash, text_he (original Hebrew), lang (language), translated_text, created_at
  - Tests: Verify all tables create, constraints work, relationships are correct

---

## Phase 2: Authentication System (Single Password)
Everyone uses one password - no role-based access control.

- [ ] **Implement `backend/auth.py`**
  - Function to verify password matches APP_PASSWORD from environment
  - Session management (stores whether user is logged in via cookie or localStorage flag)
  - Tests: Valid password → authenticated, Invalid password → rejected

- [ ] **Create authentication endpoint** `POST /auth/login`
  - User sends password, endpoint checks if it matches APP_PASSWORD
  - If correct: Returns success and creates session
  - If incorrect: Returns 401 (Unauthorized)
  - Tests: Correct password works, incorrect password fails

- [ ] **Add auth middleware** to all routes
  - Every API endpoint checks if user is authenticated first
  - If not authenticated: Returns 401 error
  - Tests: Unauthenticated requests are blocked

---

## Phase 3: Translation Service
Translating dish names and notes to Russian, Arabic, and Amharic for multilingual cooks.

- [ ] **Implement `backend/services/translation.py`**
  - Wraps Google Translate API with retry logic (tries 3 times if it fails)
  - Cache-first approach: Check database cache → If not found → Call Google API → Save to cache
  - Supports batch translation (translating multiple texts at once)
  - Target languages: Russian (ru), Arabic (ar), Amharic (am)
  - Error handling: If translation fails, returns Hebrew text
  - Tests: Cache hits, cache misses, API errors, all language combinations

- [ ] **Create translation endpoint** `POST /translate`
  - Input: List of Hebrew texts and target language
  - Output: List of translated texts
  - Tests: Russian/Arabic/Amharic translations work, cache prevents duplicate API calls

---

## Phase 4: Station Management
Creating the ability to add/edit/delete cooking stations.

- [ ] **Implement `backend/routers/stations.py`**
  - `GET /stations` - List all stations
  - `POST /stations` - Create new station (name, description)
  - `PUT /stations/{station_id}` - Update station details
  - `DELETE /stations/{station_id}` - Delete a station
  - Tests: All CRUD operations work correctly

---

## Phase 5: Dish Management
Creating the menu - ability to add/edit/delete dishes.

- [ ] **Implement `backend/routers/dishes.py`**
  - `GET /dishes` - List all dishes (supports ?lang=ru for Russian translation)
  - `POST /dishes` - Create new dish (Hebrew name)
  - `PUT /dishes/{dish_id}` - Update dish name
  - `DELETE /dishes/{dish_id}` - Delete a dish
  - Tests: CRUD works, translations work for all languages

- [ ] **Implement cook suggestion logic** `backend/services/assignment.py`
  - Function: Look at yesterday's orders for a dish → Find which cook made it
  - Returns that cook as the suggestion for today
  - If no history exists, returns no suggestion
  - Tests: With/without historical data, station filtering

- [ ] **Create suggestion endpoint** `GET /dishes/{dish_id}/suggestion?station_id={station_id}`
  - Frontend calls this to get suggested cook for a dish
  - Returns cook_id and cook name, or null if no history

---

## Phase 6: Order Management
Creating orders - the core feature where chefs assign dishes to cooks.

- [ ] **Implement `backend/routers/orders.py`**
  - `POST /orders` - Create new order
    - Input: dish_id, cook_id (optional), station_id, quantity_value, quantity_unit, notes
    - If no cook_id provided: Call suggestion endpoint
    - Set is_active=True
    - Tests: With/without auto-assign, validation errors
  - `PUT /orders/{order_id}` - Update order (reassign cook, change quantity, update notes)
    - Tests: Updates work, validation
  - `DELETE /orders/{order_id}` - Soft delete (set is_active=False, mark archived_at)
    - Tests: Deletion works
  - `GET /orders` - Get active orders (is_active=True), filterable by station_id and/or cook_id
    - Supports ?lang=ru for translations
    - Tests: Only active orders returned, filtering works, translation works

---

## Phase 7: User Management (Cooks)
Creating/managing cooks and assigning them to stations.

- [ ] **Implement `backend/routers/users.py`**
  - `GET /users` - List all users, filterable by ?station_id=X
  - `POST /users` - Create new cook (name, station_id)
  - `PUT /users/{user_id}` - Update cook (name, reassign to different station)
  - `DELETE /users/{user_id}` - Delete a cook
  - Tests: All operations work, station filtering works

---

## Phase 8: Scheduler - Configurable Reset
Orders reset automatically at configured time each day.

- [ ] **Create `config.yaml`** configuration file
  - Example: `reset_time: "00:00"` (midnight)
  - Can be changed to any time

- [ ] **Implement `backend/scheduler.py`** using APScheduler
  - Reads reset_time from config.yaml
  - Every day at that time, runs reset job
  - Reset job: All is_active=True orders → set is_active=False, add archived_at timestamp
  - Tests: Scheduler triggers at right time, orders archive correctly

- [ ] **Document config.yaml** in DEPLOYMENT.md

---

## Phase 9: Frontend - Project Setup
Setting up the React frontend project structure.

- [ ] **Initialize Vite + React + TypeScript** in `frontend/` folder
  - Vite: Fast build tool for web development
  - React: JavaScript library for user interfaces
  - TypeScript: Adds type checking to JavaScript

- [ ] **Install dependencies**
  - TanStack Query: Makes API calls and caching automatic
  - React Router: Navigation between pages
  - Axios: HTTP client for API calls
  - date-fns: Date/time utilities

- [ ] **Create folder structure**
  - `frontend/src/components/` - Reusable UI components (buttons, forms, etc.)
  - `frontend/src/pages/` - Full pages (login, orders, swimlane, etc.)
  - `frontend/src/hooks/` - Custom React hooks (reusable logic)
  - `frontend/src/services/` - API client code
  - `frontend/src/types/` - TypeScript type definitions

- [ ] **Set up linting and formatting**
  - ESLint: Checks code for errors and best practices
  - Prettier: Automatically formats code consistently

---

## Phase 10: Frontend - API Client & Authentication
Connecting frontend to backend and handling login.

- [ ] **Create `frontend/src/services/api.ts`**
  - Axios instance configured with backend URL
  - Request interceptor: Adds session cookie/flag to every API call
  - Response interceptor: If 401 (unauthorized), redirect to login page
  - Tests: Mock API calls work, redirects on 401

- [ ] **Create `frontend/src/hooks/useAuth.ts`** custom hook
  - Functions: login(password), logout(), isAuthenticated()
  - Stores authenticated flag in localStorage
  - Tests: Login stores flag, logout clears it

- [ ] **Create `frontend/src/pages/LoginPage.tsx`**
  - Simple form with password input
  - On submit: Calls POST /auth/login with password
  - If success: Stores auth flag and redirects to main page
  - If error: Shows error message
  - Tests: Successful login redirects, failed login shows error

---

## Phase 11: Frontend - Management Pages
Interface for managing stations, dishes, and cooks.

- [ ] **Create `frontend/src/pages/ManagementPage.tsx`**
  - Three tabs:
    - **Stations**: List stations, add new, edit, delete
    - **Dishes**: List dishes, add new, edit, delete
    - **Users**: List cooks, add new with station assignment, edit, delete
  - Tests: All CRUD operations work

---

## Phase 12: Frontend - Order Creation Page
Interface for creating and managing orders.

- [ ] **Create `frontend/src/pages/OrderCreationPage.tsx`**
  - **Station selector** dropdown - user picks which station to work with
  - **Order creation form** with:
    - Dish dropdown (populated from GET /dishes)
    - Cook dropdown (populated from GET /users?station_id={selected})
    - "Auto-assign" option using suggestion endpoint
    - Quantity: Number input + unit dropdown (kg, portions, pieces, other)
    - Notes: Optional textarea
    - Submit button
  - **Active orders list** for selected station with edit/delete buttons
  - Tests: Station selection filters cooks, form creates orders, auto-assign works

- [ ] **Create `frontend/src/hooks/useOrders.ts`**
  - Uses TanStack Query for fetching orders
  - useMutation for creating/updating/deleting orders
  - Automatic cache invalidation after mutations
  - Tests: Mutations trigger refetch, caching works

---

## Phase 13: Frontend - Cook Swimlane View
The main view where cooks see their assigned orders.

- [ ] **Create `frontend/src/pages/CookPage.tsx`**
  - **Station selector** dropdown - stores choice in localStorage
  - **Language selector** dropdown (Hebrew, Russian, Arabic, Amharic) - stores in localStorage
  - Fetches orders: `GET /orders?station_id={selected}&lang={selected}`
  - Fetches cooks: `GET /users?station_id={selected}`
  - **Swimlane layout**: One column per cook in selected station
  - Each order card shows: Dish name (translated), quantity, notes, "Done" checkbox
  - Orders are filtered out when marked done (stored in localStorage)
  - Tests: Station/language filters work, done status persists, columns render correctly

- [ ] **Create `frontend/src/components/OrderCard.tsx`**
  - Reusable card component showing one order
  - Displays: Translated dish name, quantity with unit, notes, done checkbox
  - Tests: Renders data correctly, checkbox toggles done

- [ ] **Create `frontend/src/hooks/useLocalStorage.ts`**
  - Custom hook to store/retrieve done order IDs from browser storage
  - Allows marking orders as done without syncing to server

---

## Phase 14: Polish & Deployment
Final testing and deploying to Railway.

- [ ] **Add UI polish**
  - Loading states on buttons/forms
  - Error messages and toast notifications
  - Form validation with helpful error text

- [ ] **Run end-to-end tests**
  - [ ] Create station → appears in dropdowns
  - [ ] Create order → cook sees it in swimlane
  - [ ] Mark order done → persists in localStorage
  - [ ] Auto-assign works based on yesterday's orders
  - [ ] Create dish → appears in order form
  - [ ] Translations work in all languages
  - [ ] Reset happens at configured time
  - [ ] Station filtering works correctly

- [ ] **Create Docker setup**
  - `backend/Dockerfile` - Container for backend
  - `frontend/Dockerfile` - Container for frontend
  - `docker-compose.yml` - Orchestrates both services

- [ ] **Deploy to Railway**
  - Push code to GitHub
  - Connect Railway to GitHub repo with docker-compose
  - Set environment variables: APP_PASSWORD, GOOGLE_TRANSLATE_API_KEY, DATABASE_URL
  - Configure SQLite volume mount at /app/data
  - Test health check endpoint
  - Verify production works end-to-end

- [ ] **Update deployment documentation** in DEPLOYMENT.md

---

## Summary
This roadmap follows a natural development flow - build backend APIs first, then frontend UI. Testing is integrated into each feature, not done separately. Each phase builds on the previous one.
