# Railway Deploy Guide - TECHNOSAT DEV V4

This project runs on Railway as one Node/Express service that serves the built React frontend and proxies API calls to the Traccar server.

## Architecture

```text
Browser
  -> TECHNOSAT DEV V4 on Railway
  -> Express BFF in server.js
  -> Traccar Server 6.14
```

## Railway Project Setup

1. Create or open the Railway project.
2. Deploy from GitHub repo `raphaeltoledo91/TECHNOSAT-DEV-V4`.
3. Use branch `main` after the fix branch is merged.
4. Railway reads `railway.toml` automatically.

## Build and Runtime

```text
Build command: npm ci && npm run build
Start command: npm start
Healthcheck: /api/health
```

## Required Variables

```env
TRACCAR_URL=https://gps2.rafacarrastreadores.com.br
POLLING_MS=30000
ALLOW_UNSAFE_GOOGLE_TILES=true
SESSION_TTL_MS=28800000
NODE_ENV=production
```

Do not commit Traccar passwords, admin passwords, API tokens, or Railway tokens to GitHub.

## First Test

Open:

```text
https://YOUR-RAILWAY-DOMAIN/api/health
```

Then sign in with a valid Traccar user and click a vehicle plate to open the camera webview.

## Camera Notes

Live camera depends on Traccar returning compatible command types or saved commands for the device. If the device does not expose a command, the UI opens the HLS webview and reports that image capture was not confirmed.
