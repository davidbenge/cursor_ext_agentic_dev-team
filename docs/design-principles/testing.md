# Testing

## Philosophy

- Define success before starting
- Verify before claiming completion
- Given/When/Then from acceptance criteria → automated tests

## Expectations

<!-- Define the test coverage standards for this project -->
- Unit tests for [core business logic, utility functions, etc.]
- Integration tests for [database, external APIs, etc.]
- E2E for critical user flows
- Run tests before marking any task complete

## Test Stack

<!-- Define approved testing tools -->
- **Unit**: [e.g., "Jest", "Vitest", "pytest"]
- **Integration**: [e.g., "Supertest", "Testcontainers", "pytest-integration"]
- **E2E**: [e.g., "Playwright", "Cypress"]

## Release Gate

<!-- Define what must pass before a release is approved -->
- On every release, run through the application E2E using [your E2E tool].
- Cover all routes and flows that do **not** require auth; ensure each loads and behaves correctly.
- [Add project-specific release gate criteria]

## Auth-Protected Route Testing

<!-- Define how authenticated routes are tested -->
- [Describe your strategy — e.g., "Use a dev token minted from a test identity"]
- [e.g., "Assert 401 when no token is present; assert 2xx with valid token"]
