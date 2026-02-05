# Development Standards

## 1. Core Principles
- **Reuse Over Reinvent:** Always check for existing components or utility functions before creating new ones.
- **Root Cause Fixes:** When fixing bugs, identify and fix the core logic error. Do NOT create wrapper functions or new files to bypass the bug.
- **Zero-Bloat:** Strictly forbid creation of unnecessary files or boilerplate code.
- **Scripting:** Do not write complex shell commands in documentation or terminals. Create Python scripts in the `scripts/` folder for operational tasks (e.g., seeding DB, running specific test suites).

## 2. Code Style
- **Python:** PEP 8. Use `black` for formatting.
- **React:** Functional Components only. Use Hooks.
- **Naming:**
    - Variables: `snake_case` (Python), `camelCase` (JS).
    - Components: `PascalCase`.
    - Files: `snake_case.py`, `kebab-case.tsx`.

## 3. Best Practices
- **Type Safety:**
    - Python: strict `Pydantic` models for all API I/O.
    - TypeScript: No `any`. Define Interfaces for all Props and API responses.
- **State Management:** Use `TanStack Query` for all API fetching. Do NOT use `useEffect` for data fetching.
- **CSS:** Use Tailwind CSS (or standard Modules) for rapid, consistent styling.

## 4. Directory Structure
```text
/
  /scripts         # Python scripts for maintenance/testing
  /src
    /backend
      /app
        /models    # SQLModel classes
        /routers   # API endpoints
        /services  # Business logic
        main.py
    /frontend
      /src
        /components
        /pages
        /hooks
  docker-compose.yml

## 5. Testing Protocols
Backend: pytest. Every endpoint must have a success test and a validation failure test.

Frontend: vitest. Test critical flows (Order submission, Swimlane rendering).

## 6. Progress Tracking (PROGRESS.md)

AI agents must update `docs/PROGRESS.md` after completing each major implementation phase (from TODO.md).

**Format:**
```markdown
# Project Progress

## Phase X: [Phase Name] - Completed [Date]

**What was implemented:**
- Brief list of completed features/endpoints/components
- Key files created or modified

**Tests added:**
- Test coverage summary

**Deviations from plan (if any):**
- Any changes made during implementation
- Rationale for changes

**Next phase:**
- Phase Y: [Next Phase Name]
```

**Guidelines:**
- Update after completing each TODO.md phase (not after individual tasks)
- Keep entries concise - 5-10 bullet points per phase
- Focus on what was built, not how it was built
- Document deviations to maintain alignment with original plan
- This creates an audit trail for the project
