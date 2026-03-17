> **FRAMEWORK EXAMPLE**: This file documents a Frame.io upload integration for test artifact delivery. It is Adobe/Frame.io-specific and retained as a reference example. Delete or replace with your artifact delivery process.

# Frame.io artifact upload

Upload screenshots and videos from front-end testing to Frame.io for the sprint folder. The upload script uses an Adobe IMS Server-to-Server (S2S) token obtained by a reusable helper script.

## Prerequisites

- **IMS S2S credentials** for a workspace (e.g. Stage) in `.env`:
  - `SERVICE_API_KEY` (client id)
  - `AIO_ims_contexts_Credential__in__adobe__pattern__atlas__-__{Workspace}_client__secrets` (JSON array; first element is client secret)
  - `AIO_ims_contexts_Credential__in__adobe__pattern__atlas__-__{Workspace}_scopes` (JSON array; must include `frame.s2s.all`)
- **Frame.io target**: set `FRAME_IO_ACCOUNT_ID` and `FRAME_IO_FOLDER_ID` in `.env`, or use `FRAME_SPRINT_FOLDER` with a URL like `https://next.frame.io/project/{projectId}/{folderId}` (the script parses the two UUIDs as account and folder).

See [Server-to-Server authentication](https://developer.adobe.com/developer-console/docs/guides/authentication/ServerToServerAuthentication/) for how S2S works.

## Reusable: get service token

Any automation that needs an IMS Bearer token can use:

**CLI** (prints only the token):

```bash
node scripts/dev/get-service-token.js
# or with workspace / env path
node scripts/dev/get-service-token.js --workspace=Stage --env=.env
token=$(node scripts/dev/get-service-token.js)
```

**Programmatic**:

```js
const { getServiceToken } = require('./scripts/dev/get-service-token.js');
const token = await getServiceToken({ workspace: 'Stage' });
```

## Upload script

**Usage:**

```bash
# Upload specific files (names on Frame.io = original basename)
node scripts/dev/upload-frameio-artifacts.js screenshot.png recording.webm

# With story prefix (recommended for test artifacts)
node scripts/dev/upload-frameio-artifacts.js --story-id={{JIRA_PREFIX}}-123 --story-name="Feature complete" screenshot.png

# From a directory, optional glob
node scripts/dev/upload-frameio-artifacts.js --story-id={{JIRA_PREFIX}}-123 --story-name="Feature complete" --dir=./playwright-results --glob="*.png"
```

**Options:**

- `--workspace=Stage` – IMS workspace (default: `Stage` or `FRAME_IO_WORKSPACE`)
- `--story-id={{JIRA_PREFIX}}-123` – story key; uploaded file names start with `{storyId}-{slug}_...`
- `--story-name="Feature complete"` – slug derived from this (e.g. `feature-complete`)
- `--dir=<path>` – directory to scan for files
- `--glob=<pattern>` – simple glob for `--dir` (e.g. `*.png`)

**Env fallback:** `JIRA_ISSUE_KEY` and `FRAME_IO_STORY_NAME` can replace `--story-id` and `--story-name`.

**Allowed extensions:** `.png`, `.jpg`, `.jpeg`, `.gif`, `.webp`, `.webm`, `.mp4`, `.mov`, `.avi`.

## Getting account and folder IDs

- From the Frame.io web URL: e.g. `https://next.frame.io/project/{projectId}/{folderId}` — the script can parse these two UUIDs if you set `FRAME_SPRINT_FOLDER` and leave `FRAME_IO_ACCOUNT_ID` / `FRAME_IO_FOLDER_ID` unset (script uses first UUID as account, second as folder). If you get **403 Forbidden**, the URL may use project id rather than account id; set the IDs explicitly.
- From the API: `GET /v4/accounts`, then `GET /v4/accounts/{id}/folders/...` to find the folder id. Set `FRAME_IO_ACCOUNT_ID` and `FRAME_IO_FOLDER_ID` explicitly in `.env`.

## Test-engineer responsibility

For stories with UI changes, the test-engineer (or process they define) should run this upload for screenshots/videos from front-end testing and reference the artifacts in the sprint folder (e.g. in impl-log/test or task notes). See `.cursor/skills/test-engineer/SKILL.md`.
