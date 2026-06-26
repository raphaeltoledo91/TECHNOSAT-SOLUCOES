# RAFACAR DEV V4

Frontend RAFACAR para Traccar 6.14 com BFF Express, mapa em tempo real, comandos seguros, identificação de motoristas e webview de câmera ao clicar na placa.

## Principais recursos

- Login por credenciais Traccar com cookie local HttpOnly.
- Proxy seguro no `server.js`; credenciais não ficam no React/browser.
- Rate limit, Helmet, CSP, timeout, cache control e allowlist de endpoints.
- Dashboard com mapa, camadas, frota, alertas, comandos, atributos e relatórios.
- Placa clicável abre câmera em webview e solicita imagem ao vivo quando o Traccar retorna comando compatível.
- Motorista atual resolvido por `position.attributes.driverUniqueId`, com match opcional em `Driver.uniqueId`.

## Instalação local

```bash
npm ci
npm run build
npm start
```

Durante desenvolvimento:

```bash
npm run dev
```

## Configuração segura

Exemplo de `data/config.local.json`:

```json
{
  "traccarUrl": "https://gps2.rafacarrastreadores.com.br",
  "port": 3000,
  "pollingMs": 30000,
  "authMode": "traccar-user-session",
  "featureLiveVideo": true,
  "featureDriverIdentification": true,
  "allowUnsafeGoogleTiles": true
}
```

Também pode usar variáveis Railway:

```env
TRACCAR_URL=https://gps2.rafacarrastreadores.com.br
POLLING_MS=30000
SESSION_TTL_MS=28800000
NODE_ENV=production
```

## Câmera ao vivo

Fluxo implementado:

- Clicar na placa do veículo no mapa, lista ou tabela.
- Abrir webview de câmera.
- Chamar `POST /api/live-camera/session`.
- Solicitar imagem ao vivo se houver comando compatível ou comando salvo no Traccar.
- Abrir HLS via `/api/stream/{deviceId}/{channel}/live.m3u8`.

Vídeo e imagem dependem de suporte real do equipamento, canal, comandos do Traccar e servidor de stream.

## Railway

Use Railway como Web Service Node/Express.

- Guia operacional: [`docs/RAILWAY_DEPLOY.md`](docs/RAILWAY_DEPLOY.md)
- Deploy detalhado: [`docs/DEPLOYMENT_RAILWAY.md`](docs/DEPLOYMENT_RAILWAY.md)
- App mobile e push: [`docs/MOBILE_APP_PUSH.md`](docs/MOBILE_APP_PUSH.md)
- Wrapper Android/WebView: [`mobile/capacitor`](mobile/capacitor)
- Plano de evolução: [`docs/LONG_TERM_PLAN.md`](docs/LONG_TERM_PLAN.md)
- Configuração versionada: [`railway.toml`](railway.toml)

O Traccar continua rodando separado. A Railway hospeda apenas o frontend RAFACAR e o proxy Express que consome a API do Traccar.
