# Ambiente e variáveis

## Traccar

Valor padrão aplicado conforme referência da tarefa:

```env
TRACCAR_URL=https://gps2.rafacarrastreadores.com.br
POLLING_MS=30000
SESSION_TTL_MS=28800000
ALLOW_UNSAFE_GOOGLE_TILES=true
NODE_ENV=production
```

O frontend não usa IP do Traccar diretamente. O navegador fala com o BFF Express e o BFF encaminha para o Traccar usando a sessão HttpOnly.

## Recursos

```env
FEATURE_LIVE_VIDEO=true
FEATURE_DRIVER_IDENTIFICATION=true
PUBLIC_APP_NAME=TECHNOSAT
PUBLIC_APP_URL=https://rafacar-dev-v4-production.up.railway.app
```

## Câmera ao vivo

O painel usa:

- `POST /api/live-camera/session`
- `POST /api/live-camera/stop`
- `GET /api/stream/{deviceId}/{channel}/live.m3u8`
- fallback `GET /api/stream/{deviceId}/live.m3u8`

O stream é sempre proxied pelo BFF para preservar cookies e evitar expor credenciais no browser.
