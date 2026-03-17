# Security

## Risk Tiers

- **Tier 1 — Must Fix**: Blocks all workflow progression. Resolve with concrete mitigation before proceeding.
- **Tier 2 — Should Address**: Must appear in impl plan with mitigation or formal risk acceptance. Human disposition required.
- **Tier 3 — Noted**: Log to impl-log; no immediate action.

## Non-Negotiables

<!-- Define the security invariants that apply to every story and task in this project -->
- No credentials in code; config via environment variables only
- Parameterized queries everywhere — prevent injection attacks
- Validate and sanitize all input at system boundaries
- Run secret scanning (e.g., gitleaks) before commits
- [Add project-specific non-negotiables below]
- [e.g., "All API endpoints require authentication unless explicitly designated public"]
- [e.g., "Sensitive PII must be encrypted at rest"]

## Auth Model

<!-- Describe your authentication and authorization approach -->
- **Auth provider**: [e.g., "Auth0", "AWS Cognito", "Adobe IMS", "Custom JWT"]
- **Session strategy**: [e.g., "Short-lived JWT + refresh tokens", "Server sessions"]
- **Authorization**: [e.g., "Role-based (RBAC)", "Attribute-based (ABAC)", "Scopes"]
