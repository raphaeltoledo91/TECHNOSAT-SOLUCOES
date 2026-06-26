# Segurança

## Sessão

- Credenciais Traccar são enviadas ao BFF em `/api/auth/login`.
- O BFF autentica em `/api/session` do Traccar e guarda a sessão remota em memória.
- O browser recebe apenas cookie local HttpOnly `rafacar_sid`.

## Proxy Traccar

- O proxy genérico `/api/traccar/*` usa allowlist de endpoints.
- Não há proxy aberto para URL arbitrária.
- Streams HLS usam rota dedicada `/api/stream/*`, restrita a caminhos internos de `/api/stream`.
- `helmet` e CSP foram ajustados com `media-src 'self' blob:` para vídeo.

## Comandos de câmera

- O frontend não envia binário JT808/JT1078.
- O BFF consulta comandos do Traccar com `/api/commands/types?deviceId=` e comandos salvos com `/api/commands/send?deviceId=`.
- A imagem ao vivo só é solicitada quando há comando compatível retornado pelo Traccar ou comando salvo compatível.
- Se o comando não existir, a UI informa a limitação e tenta apenas abrir o HLS caso já exista stream.

## Pendências

- Sessões em memória devem migrar para Redis antes de escala horizontal.
- Confirmar nomes reais de comandos de foto/vídeo configurados no Traccar para cada modelo WY02/H20P.
- Validar domínio final no CSP quando o app sair do domínio Railway temporário.
