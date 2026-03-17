---
name: ims-login
description: Use when implementing or debugging Adobe IMS (Identity Management System) login, token handling, or imslib integration. Covers window.adobeid config, sign-in/sign-out flows, standalone vs Unified Shell bootstrap, thin vs full library, CSRF/nonce, and fragment token parsing. Use when the user mentions IMS, imslib, Adobe login, OAuth redirect, access token, or standalone auth.
---

# IMS Login Skill

## When to Use This Skill

Use this skill when:
- Implementing or changing IMS login (sign-in, sign-out, token refresh) in an App Builder or web app
- Configuring `window.adobeid` or IMS client/scope/redirect for imslib or custom flows
- Debugging redirect-after-login, fragment tokens, or "wrong redirect URI" issues
- Choosing between full imslib, thin imslib, or custom authorize URL + fragment parsing
- Integrating with Unified Shell (experience.adobe.com) vs standalone (direct URL) auth
- Working with standalone token, IJT, or CSRF/nonce for thin library

**Do NOT use this skill when:**
- Auth is purely backend/JWT or non-Adobe (use app-builder-actions-developer or security-expert as needed)
- Only frontend UI for login buttons is needed (use app-builder-frontend-developer)

## Role Boundary

**Does**: Design and implement IMS config, bootstrap (Unified Shell vs standalone), sign-in/sign-out flows, token storage and callbacks, and fragment/redirect handling. Knows imslib2.js contracts (IAdobeIdData, IAdobeIMS, thin vs full) and this project’s standalone helpers.

**Does NOT**: Own backend action auth (validate-ims, JWT); that stays in app-builder-actions-developer. For threat modeling of IMS, use security-expert.

## Concepts (Summary)

- **Unified Shell**: App runs inside experience.adobe.com; auth from shell via `runtime.on('ready')`. No imslib load or login UI in app.
- **Standalone**: App runs at its own URL; must load IMS (imslib or script) and run sign-in flow; redirect_uri must match allowed list exactly.
- **Full imslib**: `AdobeIMS` — signIn, signOut, getAccessToken, getProfile, refreshToken, onReady/onAccessToken/onError, etc.
- **Thin imslib**: `AdobeIMSThin` — signIn, signOut, fragmentValues(), getNonce(); app implements token handling from fragment.
- **Config**: `window.adobeid` (or passed into constructor). Required: `client_id`, `scope`, `onReady`. Optional: `redirect_uri`, `environment`, `useLocalStorage`, `autoValidateToken`, `ignoreUrlToken`, handlers.

## References (Progressive Disclosure)

- **Config and initialization**: [references/config-and-initialization.md](references/config-and-initialization.md) — `window.adobeid`, IAdobeIdData / IAdobeIdThinData, CDN vs npm, env vars.
- **Flows and methods**: [references/flows-and-methods.md](references/flows-and-methods.md) — Init flow, sign-in flow, full vs thin API, token/profile/errors.
- **Standalone and thin**: [references/standalone-and-thin.md](references/standalone-and-thin.md) — Standalone token, thin library, fragmentValues/getNonce, CSRF, custom authorize URL.
- **This project**: [references/project-usage.md](references/project-usage.md) — `ims-config.ts`, `standalone-ims.ts`, bootstrap, env, and developer setup.

## Instructions

1. **Determine context**: Unified Shell vs standalone. If Unified Shell, auth is from `runtime.on('ready')`; no imslib in app. If standalone, load config (e.g. `getImsConfig()`), then either load imslib and call `adobeIMS.initialize()`/`signIn()` or use custom `getAuthorizeUrl` + `parseHashForToken` (see project-usage).
2. **Config**: Set `window.adobeid` (or equivalent) before loading imslib when using the library. Required: `client_id`, `scope`, `onReady`. For standalone without imslib, use env-driven config (e.g. `IMS_PATTERN_ATLAS_*`) and never default to production.
3. **Redirect URI**: Must match IMS client’s allowed list exactly (no trailing slash vs slash). If using imslib and redirect is wrong, set `adobeid.redirect_uri` to a function returning current origin (e.g. `() => window.location.origin + (window.location.pathname || '/')`).
4. **Token after redirect**: With full imslib, token is delivered via `onAccessToken` and `onReady`. With thin or custom flow, parse hash with `fragmentValues()` or `parseHashForToken()` and store/callback as needed.
5. **Errors**: Handle `onError` (e.g. HTTP, rate limit, CSRF). On CSRF error imslib may call signOut. Rate limit includes `retryAfter`.
6. For deeper detail (handlers, storage, Organizations API, modal mode, IJT), read the reference files above.

## Output Contract

- Correct `window.adobeid` or project IMS config for the chosen mode (Shell vs standalone).
- Consistent redirect_uri and allowed URIs; no hardcoded production credentials.
- Impl-log or comments where IMS bootstrap or login flow is non-obvious.
