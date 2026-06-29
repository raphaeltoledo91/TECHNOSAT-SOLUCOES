# Auditoria final - correções TECHNOSAT DEV V4

Data: 2026-06-21
Branch: `fix/traccar-camera-plate-live-image`

## Implementado

- Placa do veículo agora é ação de câmera no popup do mapa, lista lateral e tabela `Veículos`.
- Ao clicar na placa, abre o webview de câmera e chama `POST /api/live-camera/session`.
- O BFF solicita imagem ao vivo quando há comando compatível retornado pelo Traccar ou comando salvo compatível.
- O BFF tenta iniciar vídeo ao vivo por comando compatível e expõe HLS via `/api/stream/{deviceId}/{channel}/live.m3u8`.
- Adicionado fallback `/api/stream/{deviceId}/live.m3u8`.
- Ao fechar o webview, o frontend chama `/api/live-camera/stop` em best effort.
- Snapshot agora traz `/api/drivers` quando permitido.
- Motorista atual passa a usar `position.attributes.driverUniqueId` e match opcional em `Driver.uniqueId`.
- Telefone/IMEI não são mais usados como motorista.
- PDFs de referência foram copiados para `docs/references/`.
- Configuração padrão Traccar atualizada para `http://54.83.243.63`.

## Classificação

- Login, devices, positions, drivers, commands: `NATIVO_TRACCAR`.
- HLS `/api/stream/*`: `NATIVO_TRACCAR` quando suportado pelo servidor/equipamento; `PARCIAL_TRACCAR` na UI por depender do dispositivo/canal.
- Webview de câmera e UX de placa clicável: `CUSTOMIZADO_PRODUTO`.
- Solicitação de imagem ao vivo via comando detectado/salvo: `PARCIAL_TRACCAR`.
- Anexos ADAS/DSM com mídia histórica: documentado, mas não prometido como automático; `NAO_SUPORTADO` sem backend/anexo configurado.

## Referências usadas

- Traccar OpenAPI/API oficial: `/api/commands/types`, `/api/commands/send`, `/api/devices`, `/api/positions`, `/api/drivers`, `/api/socket`, stream.
- JT1078: 0x9101 para transmissão áudio/vídeo em tempo real, 0x9102 para controle/parada, canais lógicos 1-32.
- JT808: 0x8801 para captura imediata, `save flag = 0` para upload em tempo real, 0x0805 resposta, 0x0801 upload multimídia.
- JT808 segurança proativa: anexos de alarme com imagem, áudio, vídeo e texto.

## Validação executada

- `git diff --check`: passou; apenas avisos de CRLF esperados no Windows.
- Revisão estática dos trechos alterados em `server.js`, `src/main.jsx` e `src/styles.css`.

## Limitação de teste local

Este ambiente não possui `node`, `npm`, `npx`, `gh` nem `railway` no PATH. Portanto, `npm run build`, `npm run lint`, `npm run check:server` e deploy via CLI Railway não puderam ser executados localmente.

## Pendências operacionais

- Confirmar no Traccar os nomes reais dos comandos de foto/vídeo por modelo WY02/H20P ou cadastrar comandos salvos contendo 0x8801/0x9101/0x9102.
- Verificar no Railway o build Nixpacks após push.
- Validar com veículo online e câmera habilitada se o HLS é entregue pelo Traccar 6.14.
