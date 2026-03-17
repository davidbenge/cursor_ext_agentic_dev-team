> **FRAMEWORK EXAMPLE**: This file documents Adobe IMS (Identity Management System) standalone authentication setup. It is domain-specific and paired with the `ims-login` example skill. Replace with your auth provider's equivalent or delete if not applicable.

# IMS standalone setup ({{PROJECT_NAME}})

When the app runs **outside** the Adobe Unified Shell (e.g. direct URL), it uses IMS (OAuth 2.0 implicit) for login. Config is read from environment variables at build/runtime. **Production values must never be hardcoded**; they are set via CI or local `.env`.

## Environment variables

Set these in `.env` (or your build environment). See `.env.example` for placeholders. **Do not commit secrets** (e.g. client secret); the standalone flow uses **implicit** grant only — no client secret in the app.

| Variable | Purpose |
|----------|---------|
| `IMS_PATTERN_ATLAS_CLIENT_ID` | IMS client ID (public). Use stage value for local/stage; prod only via env. |
| `IMS_PATTERN_ATLAS_ENVIRONMENT` | `stg1` (default) or `prod`. Drives which IMS library and authorize endpoint are used: **stg1** → `https://auth-stg1.services.adobe.com/imslib2.js` and `ims-na1-stg1.adobelogin.com`; **prod** → `https://auth.services.adobe.com/imslib/imslib.min.js` and `ims-na1.adobelogin.com`. |
| `IMS_PATTERN_ATLAS_SCOPES` | Space-separated OAuth scopes (e.g. `openid`, `AdobeID`, `org.read`, `additional_info.*`). |
| `IMS_PATTERN_ATLAS_ORG_ID` | Adobe org context (e.g. `...@AdobeOrg`). |

## Defaults when unset

If these env vars are **not** set (e.g. local dev without `.env`), the app uses **stage** defaults so you can run locally without prod credentials. **Prod client ID and prod URLs are never used as defaults** — they must be supplied via env for prod builds.

## Dev-only JWT (local / Cursor)

When `AIO_ENVIRONMENT=development` and `PATTERN_ATLAS_DEV_JWT_SECRET` is set in the action runtime, protected actions accept a Bearer token that is a JWT signed with that secret and `iss: 'pattern-atlas-dev'`. IMS is not called for that token; the backend maps the JWT payload to user/org and continues.

**How to get a token:** Run `npm run mint-dev-jwt` (with `PATTERN_ATLAS_DEV_JWT_SECRET` in `.env` or exported). The script prints one line: the JWT. Use it in `Authorization: Bearer <token>` for API calls from Cursor, Postman, or scripts. Optional env or CLI: `--sub`, `--org_id`, `--email`, `--name`, `--expiry` (e.g. `24h`).

**Security:** Dev JWT path runs only when both `AIO_ENVIRONMENT=development` and the secret are set. Never set these in production.

## Where config is used

- **Unified Shell**: IMS config is **not** used; the shell provides auth via `runtime.on('ready')`.
- **Standalone**: The bootstrap path (e.g. `bootstrapRaw()` in `index.tsx`) reads IMS config from `getImsConfig()` in `web-src/src/ims-config.ts` and uses it to load the IMS library and initiate login.

## Build / deploy

Ensure `IMS_PATTERN_ATLAS_*` are available when the **frontend** is built (e.g. `aio app build` or your CI). The App Builder build may need to expose these env vars to the web bundle; if only `AIO_*` is inlined by default, add `IMS_PATTERN_ATLAS_*` to the build’s env injection (e.g. DefinePlugin or equivalent) so `process.env.IMS_PATTERN_ATLAS_CLIENT_ID` is defined at build time.

### Config at deploy time ({{JIRA_PREFIX}}-74)

IMS config reaches the client via **build-time injection**: the web bundle is built with `process.env.IMS_PATTERN_ATLAS_*` inlined by the build tool (e.g. webpack DefinePlugin or Parcel env replacement). Stage and production therefore use **separate builds** (different env at build time). There is no runtime config endpoint; the same built bundle does not switch between stage and prod. For CI/deploy: set the appropriate `IMS_PATTERN_ATLAS_*` in the build environment for each target.

## How to run and test both modes

- **Unified Shell:** Run the app from the Adobe Experience Cloud Shell (exc-runtime present). The app uses `bootstrapInExcShell()`; no IMS library is loaded and no login UI is shown. Auth comes from `runtime.on('ready', { imsOrg, imsToken, imsProfile })`.
- **Standalone:** Run the app by opening its URL directly (no Shell). The app uses `bootstrapRaw()`: it loads the IMS script (URL derived from `IMS_PATTERN_ATLAS_ENVIRONMENT`), shows the login gate, and on “Sign in with Adobe” redirects to IMS. After login, IMS redirects back with the token in the URL hash; the app parses it and renders the main UI with `ims` set. Set `IMS_PATTERN_ATLAS_*` in `.env` (or build env) so the standalone path has client ID, scopes, and redirect URI. The redirect URI must match the app origin and **must be registered in the IMS client’s allowed redirect URIs** (Adobe Developer Console or **IMSS** for Adobe internal tooling). The app sends the current origin for the root path **without a trailing slash** (e.g. `https://localhost:9080` when you open `https://localhost:9080/`). **IMS does exact string matching; if the URI is not in the allow list, IMS redirects after login to another allowed URI (e.g. your stage URL).**

- **Local dev:** In IMSS or Developer Console, add `https://localhost:9080` and, if needed, `http://localhost:9080` (no trailing slash for root). The app sends root as origin only so it matches these entries.
- **Stage/prod:** Add the exact origin used (e.g. `https://27200-25adobepatternatlas-stage.adobeio-static.net` or with trailing slash if your config uses it).
- For 127.0.0.1 include the port if you use it (e.g. `http://127.0.0.1:9080`, `https://127.0.0.1:9080`).
- **Unit tests:** `npm test` runs config tests (`getImsConfig` shape and no-prod default) and standalone helpers (`getAuthorizeUrl`, `profileFromToken`, `parseHashForToken`). Shell path is not exercised by these tests; it is unchanged (no imslib load or login UI in `bootstrapInExcShell()`).

## Troubleshooting

**After “Sign in with Adobe” I’m sent to the stage URL instead of localhost.**  
IMS matches `redirect_uri` **exactly**. The app sends the current origin for the root path **without a trailing slash** (e.g. `https://localhost:9080` when you’re on `https://localhost:9080/`). If that exact string is not in your allowed redirect URIs (IMSS or [Adobe Developer Console](https://developer.adobe.com/console) → project → OAuth client → Redirect URIs), IMS will redirect to another allowed URI (often stage). Add `https://localhost:9080` (no trailing slash) to the allow list. Check DevTools → Console before clicking “Sign in”; the app logs the redirect URI it’s sending so you can confirm it matches your IMSS/Console entry.

**SUSI opens with `redirect_uri` set to the stage URL in the address bar.**  
When you click “Sign in with Adobe”, the app sends `redirect_uri=https://localhost:9080` to `ims-na1-stg1.adobelogin.com/ims/authorize/v3`. The IMS server then redirects to SUSI at `auth-stg1.services.adobe.com`. If the SUSI URL you see already contains `redirect_uri=https%3A%2F%2F...-stage.adobeio-static.net%2F` (the stage URL), the **replacement is done by the IMS server** when it builds the redirect to the auth UI—the app is sending the correct value (confirm in the console log). In **IMSS**, the client may have a “default” or “primary” redirect URI that is used when redirecting to SUSI; that value can override the one from the request. For local dev, set `https://localhost:9080` as the primary/default redirect URI for the `pattern-atlas` client in IMSS, or contact the IMS/identity team to have the server preserve the `redirect_uri` from the authorize request when building the SUSI redirect.

## Reference: imslib2.js demo and library (imslib2.js-master/demo-apps/react)

The reference copy under `imslib2.js-master/` documents how the official IMS library handles `redirect_uri`:

- **Demo flow:** The React demo sets `window.adobeid` to config (client_id, scope, locale, environment) **before** injecting the library script. It does **not** set `redirect_uri` on adobeid. Login is triggered via `this.imsLib.signIn()` (see `ims-actions.js`).
- **Library priority** (`RedirectHelper.getInitialRedirectUri`): the value used for redirect is, in order: (1) `externalParameters["redirect_uri"]` (params passed to signIn()), (2) `adobeId.redirect_uri`, (3) **`window.location.href`**. So with no `redirect_uri` in adobeid, the library falls back to the current page URL at **sign-in time**.
- **`redirect_uri` can be a function** (`IAdobeIdThinData`: `redirect_uri?: string | ( () => string ) | undefined`). If you use the library for login and need the redirect to always match the current origin, set `adobeid.redirect_uri` to a function, e.g. `() => window.location.origin + (window.location.pathname || '/')`, so it is evaluated when signIn() runs.
- **{{PROJECT_NAME}} choice:** We do **not** load the library before login and instead build the authorize URL ourselves (`getAuthorizeUrl` in `standalone-ims.ts`) and set `window.location.href`. That avoids the library substituting a configured (e.g. stage) redirect. If we ever switch to using the library for the redirect, set `redirect_uri` on adobeid to a function that returns the current origin (no trailing slash for root) so the library sends the same value we use today.
