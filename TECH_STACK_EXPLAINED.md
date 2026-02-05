# Picnic Delicacies - Tech Stack Explained for Beginners

## What Are We Building?

Imagine a kitchen management system - think of it as a super-smart notebook that helps organize cooking tasks in your restaurant. Instead of writing orders on paper and trying to find who's responsible for what dish, this system automatically tracks everything digitally.

---

## The Big Picture: How Everything Works Together

**Simple analogy**:
- **Backend** = The brain of the operation (does calculations, stores information, makes decisions)
- **Frontend** = The face of the operation (what people see and interact with on screen)
- **Database** = The filing cabinet (stores all the information)

When someone uses the app:
1. They type something on their screen (Frontend)
2. That request travels to the Backend (like sending a message)
3. Backend thinks about it and talks to the Database to store/retrieve info
4. Backend sends back a response
5. Frontend shows the result on screen

---

## Part 1: The Backend (The Brain)

### What is Python?
Python is a programming language that's easy to read and write. It's like writing instructions in almost plain English. For example:
```python
if cook_has_orders:
    show_orders_on_screen()
else:
    show_message("No orders yet")
```

### What is FastAPI?
FastAPI is a Python framework that makes it easy to create APIs (ways for the frontend to talk to the backend). Think of it as a mail sorting system:
- Frontend sends a request (like a letter)
- FastAPI routes it to the right handler (like a postal worker)
- Handler processes it and sends back a response

**Example**: When a chef creates an order:
```
Frontend: "Hey backend, create an order for Dish #5, give it to Cook #2"
Backend: "Got it! I'll save that to the database and send you confirmation"
```

### What is SQLAlchemy/SQLModel?
These tools help Python talk to databases using plain Python code instead of complicated database language. Think of it as a translator between Python and the database.

```python
# This Python code automatically creates a table in the database
class Order:
    dish_id: int
    cook_id: int
    station_id: int
    quantity: str
    is_active: bool
```

### What is SQLite?
SQLite is a simple database that stores data in a single file (like `database.db`). It's perfect for smaller projects that don't need a huge database server. The data lives in a file on the server, and it's automatically saved there.

### What is APScheduler?
APScheduler is like a robot alarm clock for your code. You tell it "Every day at midnight, do this job" and it automatically runs that job without anyone having to press a button.

```python
# Pseudo-code
schedule_job("Every day at 00:00", reset_all_orders)
```

### What is Google Translate API?
This is a service from Google that translates text from one language to another. Your backend sends Hebrew text to Google and gets back Russian/Arabic/Amharic text.

**How it works in your app**:
- Cook sees a dish name in Hebrew: "פנקייק"
- Backend calls Google Translate: "Translate to Russian"
- Google responds: "Блины"
- Cook sees "Блины" on their screen

**Optimization**: We cache results (save them) so we don't ask Google to translate the same word twice.

---

## Part 2: The Frontend (The Face)

### What is React?
React is a JavaScript library for building interactive user interfaces. Think of React as a smart LEGO system - you build small reusable blocks (components) and combine them to make bigger screens.

**Example**:
- Button component = A reusable button you can use anywhere
- OrderCard component = A reusable card that displays one order
- CookPage = A full page made from many smaller components

When data changes (like a new order arriving), React automatically updates only the parts that need updating without refreshing the whole page.

### What is TypeScript?
TypeScript is JavaScript with type safety. Instead of:
```javascript
// Regular JavaScript - might cause errors
function addOrder(order) {
    // What properties should 'order' have?
    return order.dish_id + order.cook_id
}
```

With TypeScript:
```typescript
// TypeScript - much safer
interface Order {
    dish_id: number
    cook_id: number
    station_id: number
}

function addOrder(order: Order) {
    // Now we know exactly what properties order has
    return order.dish_id + order.cook_id
}
```

TypeScript catches errors BEFORE you run the code, which saves time and prevents bugs.

### What is Vite?
Vite is a build tool - it takes your React code and optimizes it for the web browser. It also provides a development server so you can see changes instantly as you type.

Think of it like:
- **Development**: You write code → Vite updates instantly in your browser
- **Production**: Vite packages everything into optimized files for speed

### What is TanStack Query?
TanStack Query (formerly React Query) makes fetching data from the backend MUCH easier and automatic.

**Without it**: You manually write code to fetch data, handle loading states, handle errors, cache data, etc.

**With TanStack Query**:
```typescript
// Automatically handles fetching, loading, error, caching
const { data: orders, isLoading, error } = useQuery({
    queryKey: ['orders'],
    queryFn: () => fetch('/api/orders')
})
```

### What is React Router?
React Router lets you navigate between different pages in your app without having to reload the page.

**Pages in our app**:
- Login page (password entry)
- Management page (manage stations, dishes, cooks)
- Order creation page (create orders)
- Cook swimlane page (view assigned orders)

React Router automatically shows the right page based on the URL.

### What is Axios?
Axios is a tool for making HTTP requests (talking to the backend). It's simpler than the built-in fetch API:

```typescript
// Make a request to the backend
const response = await axios.post('/api/orders', {
    dish_id: 5,
    cook_id: 2,
    station_id: 1
})
```

### What is localStorage?
localStorage is a place in the browser where you can save small pieces of data that persist even after closing the browser.

**Our use case**: When a cook marks an order as "Done", we save that in localStorage so it stays marked as done even if they close and reopen the browser. The server doesn't need to know about it.

### What is Tailwind CSS?
Tailwind CSS is a utility-first CSS framework. Instead of writing complex CSS files, you add classes directly to HTML:

```typescript
// Without Tailwind - complex CSS file
<button style={buttonStyle}> // need separate CSS file

// With Tailwind - all styling in one place
<button className="bg-blue-500 hover:bg-blue-700 text-white px-4 py-2 rounded">
```

---

## Part 3: The Infrastructure

### What is Docker?
Docker is a containerization tool. Imagine putting your entire application (code, dependencies, everything) into a shipping container. Then that container works the same way anywhere:
- Local computer ✓
- Friend's computer ✓
- Production server ✓
- Railway ✓

**Files**:
- `Dockerfile` = Instructions for building a container
- `docker-compose.yml` = Instructions for running multiple containers together

### What is Docker Compose?
Docker Compose runs multiple containers at once. Your app has:
1. Backend container (Python FastAPI)
2. Frontend container (React)
3. Database (SQLite file inside backend container)

With `docker-compose up`, all three start together and can talk to each other.

### What is Railway?
Railway is a hosting platform (like Heroku, AWS, or DigitalOcean). It's where your app lives on the internet so people can access it from anywhere.

**Process**:
1. Push code to GitHub
2. Connect Railway to your GitHub repo
3. Railway automatically builds Docker containers from your code
4. Railway runs those containers on its servers
5. Your app is live on the internet!

### What is GitHub?
GitHub is a version control system - it tracks every change you make to your code. Think of it like:
- Undo button for your entire codebase
- Multiple people can work on the same project
- History of who changed what and when

---

## Part 4: Testing (Ensuring Nothing Breaks)

### What is pytest?
pytest is a testing framework for Python. It lets you write tests that verify your backend code works correctly:

```python
def test_create_order():
    # Create a test order
    order = create_order(dish_id=5, cook_id=2)
    # Verify it was created with the right data
    assert order.dish_id == 5
    assert order.cook_id == 2
```

### What is vitest?
vitest is a testing framework for JavaScript/React. Similar to pytest but for frontend code:

```typescript
test('Order card displays dish name', () => {
    render(<OrderCard order={mockOrder} />)
    expect(screen.getByText('Pasta')).toBeInTheDocument()
})
```

### What is CI/CD?
CI/CD stands for Continuous Integration / Continuous Deployment:

**Continuous Integration**: Every time you push code to GitHub, automated tests run. If tests fail, GitHub notifies you before code is merged.

**Continuous Deployment**: If tests pass, code is automatically deployed to production.

**Our setup**: GitHub Actions (GitHub's built-in CI/CD tool) runs pytest and vitest automatically on every push.

---

## Part 5: The Data Flow (Step by Step Example)

Let's trace what happens when a chef creates an order:

### Step 1: User Types in Frontend
```
Chef clicks "Create Order" button
Frontend sees: dish_id=5, cook_id=2, station_id=1, quantity="5 kg"
```

### Step 2: Frontend Sends Request
```
Frontend (React): "POST /api/orders" with data
Uses Axios to send request to backend
```

### Step 3: Backend Receives Request
```
FastAPI receives POST request on /api/orders
Validates the data (is it valid? all required fields present?)
```

### Step 4: Backend Processes
```
Backend checks: Does this cook exist in station 1?
Backend checks: Does this dish exist?
Backend saves to database: INSERT new order into database
```

### Step 5: Backend Responds
```
Backend: "Success! Order created with ID #123"
Sends back: { order_id: 123, status: "success" }
```

### Step 6: Frontend Updates
```
React receives response
TanStack Query automatically refetches orders
Cook screen updates to show new order in swimlane
```

### Step 7: Cook Sees It
```
Cook picks station from dropdown
Swimlane view shows: Cook "Ahmed" has a new order
Order card shows: "5 kg Pasta"
```

---

## Part 6: The Translation Flow

When a Russian cook opens the app:

### Step 1: Frontend Loads
```
CookPage loads, language selector shows "Russian"
Stores in localStorage: { language: 'ru' }
```

### Step 2: Fetch Orders with Translation
```
Frontend requests: GET /api/orders?station_id=1&lang=ru
```

### Step 3: Backend Handles Translation
```
Backend gets dishes from database (all in Hebrew)
For each dish name:
  - Check TranslationCache: Do we have Russian translation?
  - If YES: Use cached translation
  - If NO: Call Google Translate API
        → Save result to cache
        → Return translated text
```

### Step 4: Frontend Displays
```
Frontend receives:
  {
    orders: [
      { dish_name: "Блины", quantity: "5 kg" },
      { dish_name: "Борщ", quantity: "3 portions" }
    ]
  }
Frontend displays translated names on screen
```

---

## Part 7: Key Concepts

### API (Application Programming Interface)
An API is a set of rules for how different parts communicate. Our backend exposes an API:

```
POST   /api/orders              - Create order
GET    /api/orders              - Get orders
PUT    /api/orders/{id}         - Update order
DELETE /api/orders/{id}         - Delete order
GET    /api/stations            - Get stations
POST   /api/stations            - Create station
...
```

Frontend calls these endpoints using Axios.

### RESTful API
REST is a style of API design using HTTP methods:
- **GET** = Retrieve data
- **POST** = Create new data
- **PUT** = Update existing data
- **DELETE** = Remove data

### Authentication
Our app uses simple password authentication:
1. User enters password on login page
2. Frontend sends: `POST /api/auth/login` with password
3. Backend checks: Does password match APP_PASSWORD?
4. If yes: Returns success, session created
5. Frontend stores session flag in localStorage
6. Every future request includes session flag
7. Backend verifies session is valid before responding

### Caching
Saving data so you don't have to recalculate or refetch it:
- **Translation cache**: Save translations to database so we don't ask Google twice
- **Browser cache**: TanStack Query caches orders so we don't refetch after every click
- **localStorage cache**: "Done" status stays even if you close browser

---

## Summary: How It All Works Together

```
Kitchen → Cooks & Chefs
   ↓
Phone/Browser (Frontend - React + TypeScript)
   ↓
Internet Connection
   ↓
Railway Servers (Hosting)
   ↓
Backend (Python + FastAPI)
   ↓
SQLite Database (Stores everything)
   ↓
Google Translate (For translations)
```

**The flow**:
1. **User** uses Frontend (React app in browser)
2. **Frontend** sends requests to **Backend** (Python/FastAPI)
3. **Backend** processes requests, talks to **Database** (SQLite)
4. **Backend** may call **Google Translate API** for translations
5. **Backend** sends response back to **Frontend**
6. **Frontend** updates screen so user sees results

Each part has a specific job, and together they create a smooth user experience for managing a busy kitchen.

---

## Getting Started for Developers

### Prerequisites
- Python 3.9+
- Node.js 18+
- Docker (optional, for containerization)
- Git (for version control)

### Local Development Setup
1. Clone repo: `git clone <repo-url>`
2. Backend: `cd backend && pip install -r requirements.txt`
3. Frontend: `cd frontend && npm install`
4. Backend: `python -m uvicorn main:app --reload`
5. Frontend: `npm run dev`
6. Both run simultaneously on different ports

### Adding a New Feature
1. Write backend test
2. Write backend API endpoint
3. Write frontend test
4. Write frontend component
5. Test integration
6. Commit and push
7. GitHub Actions tests run automatically

---

## Questions Your Friend Might Ask

**Q: Why do we need both frontend and backend?**
A: Frontend (what users see) and backend (brain of the app) do different jobs. Frontend is fast but limited, backend is powerful. Together they're perfect.

**Q: Why SQLite instead of a "real" database?**
A: SQLite is perfect for this size of app. It's simple, requires no server, data is just one file. Later if we need millions of users, we can upgrade to PostgreSQL.

**Q: Why use React instead of just writing HTML?**
A: React makes interactive UIs much easier. Without it, updating one value would require reloading the entire page. React only updates what changed.

**Q: Why do we cache translations?**
A: Google Translate API costs money and is slower. If 100 cooks see "Pasta", we don't want to translate it 100 times. Cache it once, use it forever.

**Q: What happens if the internet goes down?**
A: Frontend can still work offline for some things (marking orders done is saved in localStorage). Backend needs internet to function.

---

## This Explains Everything!
Now your friend understands:
- What each technology does
- Why we chose it
- How pieces fit together
- Data flows through the system
- How to think about new features

The architecture is solid, scalable, and built for success! 🚀
