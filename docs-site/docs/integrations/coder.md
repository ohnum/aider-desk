---
title: "Coder (Devcontainer)"
sidebar_label: "Coder"
---

# Run AiderDesk in Coder (Kubernetes Devcontainer)

This repository includes a Coder template under `coder/` that can expose AiderDesk in a browser by running AiderDesk in **headless server mode** inside the workspace pod and serving the UI as a web app.

## Architecture

- **AiderDesk API (backend)**: runs inside the workspace pod on `http://localhost:24337`
  - REST API: `http://localhost:24337/api`
  - Socket.IO: same origin
- **AiderDesk UI (frontend)**: served inside the workspace pod on `http://localhost:5173`
  - Uses `BrowserApi` to connect to the backend.

The Coder template exposes both as `coder_app` subdomain apps:

- `aiderdesk-api` → `http://localhost:24337`
- `aiderdesk` → `http://localhost:5173`

## Required UI configuration

When running behind Coder subdomain apps, the UI and API will be on different hostnames.

The AiderDesk UI uses `BrowserApi`, which can read a runtime-provided API base URL from:

- `window.__AIDERDESK_API_BASE_URL__`

The provided Coder template writes a `config.js` file into the static UI directory and injects it into `index.html` so the UI connects to the correct `aiderdesk-api` subdomain.

Example value:

```
https://aiderdesk-api--<workspace>--<user>.<coder-domain>
```

## Notes

- The Coder template starts AiderDesk by cloning `https://github.com/hotovo/aider-desk` into `/workspaces/.aiderdesk/aider-desk` and building at workspace startup.
- Node.js is installed via `nvm` in the workspace startup script (Node 22).
- The AiderDesk backend supports Basic Auth via `AIDER_DESK_USERNAME` / `AIDER_DESK_PASSWORD` (recommended if your Coder deployment allows sharing apps).
- WebSockets must be supported by the Coder app proxy for streaming responses and terminal events.
