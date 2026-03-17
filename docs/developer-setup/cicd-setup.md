# CI/CD Setup — {{PROJECT_NAME}} Publishing

CI/CD runs on **CircleCI**. The pipeline uses **base64-encoded `.aio` and `.env`** for App Builder auth and build; these files are **never committed** to the repo.

## Overview

| Workflow       | Trigger              | Jobs                                      |
|----------------|----------------------|-------------------------------------------|
| **pr_checks**  | Pull requests (any branch except `stage`) | `quality` (lint, type-check, test) |
| **stage_deploy** | Push to `stage`   | `deploy-stage` → `ingest-neo4j`           |

- **Quality gate:** Lint, type-check, and unit tests run on every PR. No deploy on PRs.
- **Stage deploy:** Merging to `stage` runs App Builder deploy then full Neo4j ingest. Quality is enforced at PR merge.

## Base64-encoded `.aio` and `.env` (build credentials)

App Builder deploy needs Adobe I/O CLI credentials. The repo does **not** contain `.aio` or `.env` (both are in `.gitignore`). CI restores them from **base64-encoded strings** stored in CircleCI.

### Why base64?

- `.aio` and `.env` are multi-line and may contain special characters; base64 avoids quoting and newline issues when storing in CI env vars.
- Files stay out of the repo; only the encoded strings live in CircleCI (preferably in a **Context** for access control).

### One-time setup: generating the encoded values

1. **Create credentials locally** (per [Adobe custom CI/CD](https://developer.adobe.com/app-builder/docs/guides/app_builder_guides/deployment/cicd-custom)):
   - Download `workspace.json` from Adobe Developer Console (Stage workspace).
   - Run: `aio app use workspace.json` to generate `.aio` and `.env` in the project root.

2. **Base64-encode and copy** (no newlines in the encoded string; `-w 0` on Linux, or equivalent on macOS):
   ```bash
   cat .aio | base64 -w 0 | pbcopy   # → paste into AIO_FILE_B64
   cat .env | base64 -w 0 | pbcopy   # → paste into ENV_FILE_B64
   ```
   On macOS without `-w 0`: `base64 -i .aio | tr -d '\n' | pbcopy`

3. **Store in CircleCI:**
   - **Recommended:** Create a CircleCI **Context** (e.g. `pattern-atlas-stage`) and add env vars:
     - `AIO_FILE_B64` = pasted base64 string for `.aio`
     - `ENV_FILE_B64` = pasted base64 string for `.env`
   - Also add to that context (or project env): `AIO_IMS_CONTEXT_NAME`, `AIO_RUNTIME_NAMESPACE_STAGE`, `AIO_RUNTIME_AUTH_STAGE`, and other `AIO_*` / `AIO_PROJECT_*` vars required by `aio app deploy`.

### How the pipeline uses them

The **deploy-stage** job:

1. Restores the two files by decoding the env vars (and stripping whitespace so wrapped/pasted values work):
   ```bash
   echo "$AIO_FILE_B64" | tr -d ' \n\r\t' | base64 --decode > .aio
   echo "$ENV_FILE_B64" | tr -d ' \n\r\t' | base64 --decode > .env
   ```
2. Sets the IMS context: `aio context -s $AIO_IMS_CONTEXT_NAME`
3. Builds: `aio app build` (with `AIO_RUNTIME_NAMESPACE` set)
4. Deploys: `aio app deploy --no-publish` (with required `AIO_*` env vars)

So the **build** (and deploy) step uses the decoded `.aio` and `.env` only inside the runner; they are never committed or logged.

## Jobs

### quality

- **Image:** `cimg/node:22.14`
- **Steps:** checkout → `npm install` → lint → type-check → test
- No secrets; no `.aio`/`.env`.

### deploy-stage

- **Image:** `cimg/node:22.14`
- **Steps:** checkout → install deps → install `@adobe/aio-cli` globally → restore `.aio` and `.env` from base64 → set IMS context → `aio app build` → `aio app deploy --no-publish`
- **Secrets:** Uses CircleCI env vars (or Context) for `AIO_FILE_B64`, `ENV_FILE_B64`, and all `AIO_*` / `AIO_PROJECT_*` values.

### ingest-neo4j

- **Image:** `cimg/node:22.14`, `resource_class: large`
- **Steps:** checkout → install deps → optional “show public IP” (for Neo4j allowlist) → `npm run ingest:neo4j:full` (timeout 30m)
- **Secrets:** Neo4j and Azure OpenAI vars (`NEO4J_URI`, `NEO4J_USERNAME`, `NEO4J_PASSWORD`, `AZURE_OPENAI_*`) from CircleCI env/Context. Does **not** use a `.env` file in the job; env vars are set directly in the job.

## Security notes

- **Never commit `.aio` or `.env`.** They are in `.gitignore`; only base64-encoded copies live in CircleCI.
- Prefer **CircleCI Contexts** (org-level) for `AIO_FILE_B64`, `ENV_FILE_B64`, and other deploy secrets so access is controlled in one place.
- Credentials are decoded only in the deploy job and exist only for the duration of that job.

## Config location

Pipeline definition: [.circleci/config.yml](../../.circleci/config.yml).
