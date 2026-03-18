# App Builder Application Points — Directory Signatures

> **Purpose**: Single source of truth for which directory paths belong to which App Builder extension type. Loaded by both App Builder Actions Developer and App Builder Frontend Developer skills. Paths are **universal** to all App Builder apps of that type.

---

## Overview

An App Builder app can extend **many application points**. Each application point has a **different directory signature** under `src/`. The directory name defines the project type (e.g. unified shell vs other extension points).

---

## Mapping: Application Point → Directory Signature

- **Actions extension pattern** live under `src/**/actions/`.
- **Frontend extension pattern** live under `src/**/web-src/`.
- **Standalone app action pattern** `src\actions`.
- **Standalone app web pattern** `src\web-src`.
- **app builder app manifests** `app.config.yaml`,
- **app builder app extension manifest** `src/**/ext.config.yaml`.

The directory name under `src/` is derived from the extension point (`xp`) path by replacing every `/` with `-`. For example `aem/cf-console-admin/1` → `src/aem-cf-console-admin-1/`.

| Application point | XP path | Directory signature | Has Actions | Has Frontend | Notes |
| --- | --- | --- | --- | --- | --- |
| **Unified Shell / Firefly Experience Cloud Shell** | `dx/excshell/1` | `src/dx-excshell-1/` | ✓ | ✓ | [unified-shell.md](unified-shell.md) |
| **Asset Compute Worker** | `dx/asset-compute/worker/1` | `src/dx-asset-compute-worker-1/` | ✓ | — | Worker only, no UI |
| **AEM Content Fragment Console Extension** | `aem/cf-console-admin/1` | `src/aem-cf-console-admin-1/` | ✓ | ✓ | |
| **Adobe Commerce UI extensions (admin panel)** | `commerce/backend-ui/1` | `src/commerce-backend-ui-1/` | ✓ | ✓ | |
| **AEM Content Fragment Editor Extension** | `aem/cf-editor/1` | `src/aem-cf-editor-1/` | ✓ | ✓ | |
| **Universal Editor Extension** | `universal-editor/ui/1` | `src/universal-editor-ui-1/` | ✓ | ✓ | |
| **Workfront Document Details** | `workfront/doc-details/1` | `src/workfront-doc-details-1/` | ✓ | ✓ | |
| **Workfront UI** | `workfront/ui/1` | `src/workfront-ui-1/` | ✓ | ✓ | See [Workfront Extension Hooks](#workfront-extension-hooks) below |
| **AEM Experience Success Studio** | `aem/experience-success-studio/1` | `src/aem-experience-success-studio-1/` | ✓ | ✓ | |
| **AEM Assets Details View Extension** | `aem/assets/details/1` | `src/aem-assets-details-1/` | ✓ | ✓ | |
| **AEM Assets Browse Extension** | `aem/assets/browse/1` | `src/aem-assets-browse-1/` | ✓ | ✓ | |
| **AEM Assets Collections Extension** | `aem/assets/collections/1` | `src/aem-assets-collections-1/` | ✓ | ✓ | |
| **GenStudio for Performance Marketing** | `dx_genstudio/genstudiopem/1` | `src/dx_genstudio-genstudiopem-1/` | ✓ | ✓ | |
| **GenStudio Translation** | `dx_genstudio/translation/1` | `src/dx_genstudio-translation-1/` | ✓ | ✓ | |
| **AEM Content Hub Asset Details** | `aem/contenthub/assets/details/1` | `src/aem-contenthub-assets-details-1/` | ✓ | ✓ | |
| **AEM Launchpad Extension** | `aem/launchpad/1` | `src/aem-launchpad-1/` | ✓ | ✓ | |
| **AEM Content Fragment Model Editor** | `aem/cf-model-editor/1` | `src/aem-cf-model-editor-1/` | ✓ | ✓ | |
| **Adobe Commerce configuration (admin panel)** | `commerce/configuration/1` | `src/commerce-configuration-1/` | ✓ | ✓ | |
| **Adobe Commerce extensibility (app management)** | `commerce/extensibility/1` | `src/commerce-extensibility-1/` | ✓ | ✓ | |


---

When adding or documenting other extension points (e.g. other App Builder templates), add a row to the table above with that application point's directory signature.

---

## Workfront Extension Hooks

The `workfront/ui/1` extension point (`src/workfront-ui-1/`) exposes three distinct UI hooks, all registered in `src/workfront-ui-1/web-src/src/components/ExtensionRegistration.js` inside the `methods` object of `register()`.

### 1. Main Menu (`mainMenu`)

Adds navigation items to the Workfront top-level Main Menu bar. A Workfront administrator adds these to layout templates; users see the app embedded within Workfront rather than opening it separately.

```js
mainMenu: {
  getItems() {
    return [
      {
        id: 'my-item',           // unique ID
        url: '/index.html#/my-item',
        label: 'My Label',
        icon: icon1,             // imported SVG/Spectrum icon
      },
    ];
  },
},
```

**Constitution note**: record which Main Menu items this project adds and their IDs.

---

### 2. Secondary Navigation / Left Panel (`secondaryNav`)

Adds items to the left-panel navigation for specific Workfront object types. Must be registered separately for each object type. Supported object types: `PROJECT`, `TASK`, `ISSUE`, `PORTFOLIO`, `PROGRAM`.

```js
secondaryNav: {
  TASK: {
    getItems() {
      return [
        {
          id: 'TASK',
          label: 'My Task Panel',
          icon: metricsIcon,
          url: '/myTask',
        },
      ];
    },
  },
  PROJECT: {
    getItems() { return [{ id: 'PROJECT', label: 'My Project Panel', icon: projectIcon, url: '/myProject' }]; },
  },
  // also: ISSUE, PORTFOLIO, PROGRAM
},
```

**Constitution note**: list which object types this project adds left-panel items for.

---

### 3. Forms Widget (`widgets`)

Embeds custom UI components inside Workfront custom form fields. Widgets appear as a "UI Extensions" field type in the custom form builder. Active only when the app is deployed or when `extensionOverride=TRUE` is set in Local Storage for local testing.

```js
widgets: {
  getItems() {
    return [
      {
        id: 'my-widget',                    // unique ID, required
        url: '/index.html#/my-widget',      // route to widget component, required
        label: 'My Widget',                 // display name in form builder, required
        dimensions: {                        // optional sizing constraints
          height: 450,
          width: 300,
          maxHeight: 600,
          maxWidth: 400,
        },
      },
    ];
  },
},
```

**Dimension rules**: all `dimensions` properties are optional. Omitting `dimensions` uses default sizing. Widgets receive shared context: `auth`, `objCode`, `objID`, `hostname`, `user`, `isLoginAs`, `isInBulkEditing`.

**Constitution note**: list which widgets this project exposes, their IDs, and target form objects.

---

### Shared Context Available to All Workfront Hooks

```js
const user    = conn?.sharedContext?.get('user');    // { ID, email }
const context = conn?.sharedContext;                 // objCode, objID, hostname, protocol, user, isLoginAs
```

---

### Local Testing for Workfront Extensions

1. Run `aio app run` to get `https://localhost:9080` (or `aio app deploy` for a static domain).
2. Open Workfront in Chrome → DevTools → Application → Local Storage.
3. Add key `extensionOverride`, value = the App Builder URL.
4. Reload the layout template page — the extension buttons appear.

> **Chrome 142+ note**: Local Network Access Restrictions may block `localhost`. Disable the flag at `chrome://flags/#local-network-access-check`.

---

### Template

Use the `@adobe/workfront-ui-ext-tpl` template when initialising via `aio app init`:

```bash
aio app init my-app
# Select: @adobe/workfront-ui-ext-tpl
```

Reference: [Workfront App Builder documentation](https://experienceleague.adobe.com/en/docs/workfront/using/app-builder/app-builder)

## For This Repo (Pattern Atlas Publishing)

Pattern Atlas extends the **unified/experience shell**. Therefore:

- **Actions** live under `src/dx-excshell-1/actions/`.
- **Frontend** lives under `src/dx-excshell-1/web-src/` and `src/dx-excshell-1/public/`.

---

## Key Commands

- aio app dev = Local development
- aio app deploy = Deploy to production
- aio app logs = View runtime logs

when launching an application which has more than one extension point defined in the project you must issues the run and dev commands with the -e flag and specify the extension point. IG `aio app run -e dx-excshell-1`
`aio app deploy` remains the same when using multipule extension points

**Project Structure:**

```
my-ai-app/
├── src/
│   ├── dx-excshell-1/         # Unified Shell (if being extended)
│   │   ├── actions/           # Backend actions
│   │   └── web-src/           # Frontend UI
|   |   └── ext.config.yaml    # Extension configuration
│   ├── workfront-ui-1/        # Workfront (if being extended)
│   └── ...                    # Other extension points
├── app.config.yaml            # App configuration
├── package.json
└── .env                       # Local secrets
```

## References

- [Adobe App Builder Architecture](https://developer.adobe.com/app-builder/docs/guides/app_builder_guides/architecture_overview/architecture_overview/)
- docs/design-principles/architecture.md — module boundaries for this project
