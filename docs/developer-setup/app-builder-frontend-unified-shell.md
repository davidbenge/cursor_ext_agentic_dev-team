> **FRAMEWORK EXAMPLE**: This file is Adobe App Builder / Unified Shell-specific. It demonstrates how to author a platform integration reference doc for a frontend specialist skill. Replace with the equivalent for your platform or delete if not applicable.

# App Builder Frontend — Unified Shell (Experience Shell)

> **Purpose**: Reference for frontends that extend the unified shell (experience.adobe.com). Loaded by App Builder Frontend Developer skill. For directory signatures (e.g. `{{APP_EXTENSION_POINT}}`), see [app-builder-application-points.md](app-builder-application-points.md).

---

## Stack

- **React** with **Adobe React Spectrum** only — no other UI libraries.
- **Jamstack** frontend served via App Builder (static assets on CDN).
- **Dual TypeScript**: Frontend uses `tsconfig.web.json` (Browser, ESM, JSX); actions use `tsconfig.actions.json`. No DOM/Node crossover — see design-principles or archive `typescript-dual-runtime-patterns.md`.

---

## Structure (Unified/Experience Shell)

- **Source**: `src/{{APP_EXTENSION_POINT}}/web-src/` (React app).
- **Static assets**: `src/{{APP_EXTENSION_POINT}}/public/`.
- **Generator**: `@adobe/generator-app-excshell` scaffolds experience-shell SPAs.

---

## Preview & Deployment

- **Local**: `aio app run` — preview at `http://localhost:9080/`.
- **Experience Cloud**: `https://experience.adobe.com/?devMode=true#/apps/?localDevUrl=https://localhost:9080` (with local server running).
- **Deployed**: `https://<namespace>.adobeio-static.net/<appname>-<appversion>/index.html`.

---

## Rules

- Wrap app in `<Provider>` with theme; use Spectrum components only.
- Consume backend via HTTP (actions); no Node/`process` in web-src.
- No DOM APIs in actions; no Node-only modules in web-src (dual-runtime boundary).

---

## References

- [Front-end applications for App Builder](https://experienceleague.adobe.com/en/docs/experience-manager-cloud-service/content/implementing/configuring-and-extending/app-builder/front-end-applications)
- [App Builder Architecture Overview](https://developer.adobe.com/app-builder/docs/guides/app_builder_guides/architecture_overview/architecture_overview/)
- [design-principles/frontend.md](../design-principles/frontend.md)
