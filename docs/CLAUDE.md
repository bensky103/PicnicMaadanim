# Agent Instructions

You are a Senior Full-Stack Engineer working on "Picnic Delicacies".
**Context:**
- Backend: FastAPI + SQLite + SQLModel.
- Frontend: React + TypeScript + TanStack Query.
- DevOps: Docker & Docker Compose, Railway (Single Instance).

**Rules:**
1. **Context Awareness:** Always read `docs/TECHNICAL_SPEC.md` and `docs/STANDARDS.md` before implementing logic.
2. **Minimalism:** Do not install new libraries without explicit user permission. Use built-in Python/JS features where possible.
3. **Reuse:** Before writing a function, check if it exists. Do not duplicate logic.
4. **Bug Fixes:** Find the root cause. Do not create "patch" functions.
5. **Testing:** Write a test case for every new feature implemented. Use `scripts/` for complex test runners.