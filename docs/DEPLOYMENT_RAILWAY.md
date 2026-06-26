# Deploy Railway - RAFACAR DEV V4

## Projeto

Repositório: `raphaeltoledo91/RAFACAR-DEV-V4`

Branch recomendada para produção após validação: `main`

## Build

Railway lê `railway.toml`:

```text
Build command: npm ci && npm run build
Start command: npm start
Healthcheck: /api/health
```

## Variáveis mínimas

```env
TRACCAR_URL=https://gps2.rafacarrastreadores.com.br
POLLING_MS=30000
SESSION_TTL_MS=28800000
ALLOW_UNSAFE_GOOGLE_TILES=true
NODE_ENV=production
```

## Teste pós-deploy

1. Abrir `/api/health`.
2. Entrar com usuário Traccar válido.
3. No Dashboard, clicar na placa de um veículo.
4. Confirmar abertura do webview de câmera.
5. Confirmar que o painel informa se a imagem ao vivo foi solicitada ou se falta comando compatível no Traccar.

## Observação

Este projeto não substitui o Traccar. Ele é um frontend/BFF que usa a API do Traccar 6.14 e preserva credenciais fora do navegador.
