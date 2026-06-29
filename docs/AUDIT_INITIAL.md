# Auditoria inicial - TECHNOSAT DEV V4 / Traccar 6.14

Data: 2026-06-20
Branch de trabalho: `fix/traccar-camera-plate-live-image`

## Resumo do projeto

Frontend React/Vite com mapa Leaflet, tela de frota, comandos, relatórios, atributos, perfil e configurações. O backend do próprio projeto é um BFF Express que autentica no Traccar usando sessão/cookie remoto e expõe endpoints seguros para o navegador.

## Stack detectada

- React 19, Vite 7, Leaflet/react-leaflet e lucide-react.
- Node/Express com helmet, compression, morgan e express-rate-limit.
- Railway via `railway.toml`, build `npm ci && npm run build`, start `npm start`.
- Dockerfile multi-stage com Node Alpine.

## Estrutura atual

- `src/main.jsx`: aplicação frontend principal em arquivo único.
- `src/styles.css`: estilos globais do painel.
- `server.js`: BFF/proxy para Traccar, autenticação, snapshot, comandos e notificações.
- `data/config.local.example.json`: configuração local de URL Traccar e opções públicas.
- `docs/`: documentação operacional existente.
- `public/`: assets, ícones e PWA.

## Como roda localmente

Scripts disponíveis em `package.json`:

- `npm run dev`
- `npm run build`
- `npm run preview`
- `npm start`
- `npm run check:server`
- `npm run lint`
- `npm run audit:prod`

Neste ambiente local de Codex não há `node`/`npm` no PATH, então a validação final dependerá do Railway/GitHub ou de ambiente com Node 20+.

## Deploy Railway

`railway.toml` usa Nixpacks com:

- Build command: `npm ci && npm run build`
- Start command: `npm start`
- Healthcheck: `/api/health`

O guia atual em `docs/RAILWAY_DEPLOY.md` ainda cita `TECHNOSAT-DEV2`; precisa ser atualizado para `TECHNOSAT-DEV-V4`.

## Integração Traccar atual

- Login: `/api/auth/login` autentica no `/api/session` do Traccar.
- Sessão: cookie local HttpOnly `rafacar_sid` guarda referência à sessão remota.
- Snapshot: `/api/bootstrap` e `/api/snapshot` carregam `/api/server`, `/api/devices`, `/api/positions` e `/api/reports/events`.
- Comandos: `/api/command-types` consulta `/api/commands/types`; `/api/send-command` envia para `/api/commands/send`.
- Proxy genérico: `/api/traccar/*` usa allowlist de endpoints Traccar.

## Segurança atual

- Credenciais não ficam no browser.
- Proxy não aceita URL arbitrária.
- Rate limit em `/api`.
- Helmet/CSP ativo.
- Risco: CSP ainda não inclui fontes de mídia HLS/stream.
- Risco: `/api/send-command` aceita qualquer tipo suportado pelo Traccar sem camada específica para comandos perigosos além da UI de bloqueio.

## WebSocket/live data

Não há uso de `/api/socket` no frontend atual. O painel usa polling por `/api/snapshot`.

## Motoristas

O frontend não carrega `/api/drivers` no snapshot atual. A coluna `Dispositivos > Motorista` usa `collectTelemetryGroups()` e o grupo genérico inclui `driverName`, `driver`, `driverUniqueId`, `driverId`, `rfid`, `ibutton` e também `phone`. Isso pode exibir telefone como motorista, contrariando a regra de referência.

Correção necessária:

- Normalizar `position.attributes.driverUniqueId`.
- Não usar `Device.uniqueId`, `Device.phone` ou `Device.contact` como motorista.
- Exibir `driverUniqueId` bruto quando existir e não houver cadastro de `Driver`.
- Quando não houver `driverUniqueId`, exibir `Não informado`.

## Live video/câmera

Não há módulo de live video/câmera no frontend atual. A placa aparece como texto no popup do mapa e na tabela de veículos, sem ação. O BFF não possui endpoint específico para abrir stream ou solicitar imagem.

Referências lidas:

- OpenAPI oficial Traccar 6.14.5: comandos `/commands/types`, `/commands/send`, posições, dispositivos e API WebSocket `/api/socket`.
- Traccar API: WebSocket oficial em `/api/socket` com autenticação por cookie.
- PDF JT1078: vídeo ao vivo usa sinalização e stream separados; 0x9101 inicia transmissão, 0x9102 controla/fecha canal, canais lógicos 1-32 são vídeo.
- PDF JT808: imagem ao vivo/captura imediata usa 0x8801; `save flag = 0` indica upload em tempo real; 0x0805 responde ao comando; 0x0801 envia multimídia.
- PDF de segurança proativa JT808: anexos de alarme podem ser imagem, áudio, vídeo e texto, enviados ao servidor de anexos.

## Riscos críticos

- Traccar pode não expor HLS para todos os equipamentos, protocolos ou canais.
- O frontend não deve enviar binário JT808/JT1078 bruto.
- Sem suporte do dispositivo/comando, a UI deve exibir estado `unsupported` ou `waiting_stream`.
- Captura de imagem ao vivo depende do comando Traccar/saved command disponível para o dispositivo.

## Plano de implementação

1. Copiar/listar PDFs em `docs/references`.
2. Corrigir exibição de motorista para não usar telefone/IMEI.
3. Criar endpoint BFF para sessão de câmera que valida `deviceId`, `channel`, comandos suportados e gera URLs HLS permitidas.
4. Criar painel/modal de câmera aberto ao clicar na placa, com pedido imediato de imagem/stream.
5. Atualizar CSP para permitir mídia do próprio app.
6. Atualizar docs Railway, driver e auditoria final.
7. Testar sintaxe/build quando o ambiente permitir.
