# Referências técnicas

Arquivos anexados nesta pasta em 2026-06-20:

- `AGENTS_TASK_RAFACAR_TRACCAR_614.pdf`
- `JT1078 Data Sending Protocols(1).pdf`
- `JT808 Data Sending Protocols.pdf`
- `JT808 Additional Information Item Protocol(1).pdf`
- `JT808 Proactive Safety Additional Information Protocol.pdf`

Uso nesta implementação:

- JT1078: fluxo de vídeo ao vivo por canal lógico, início 0x9101, controle/parada 0x9102 e URLs de stream.
- JT808: captura imediata de imagem 0x8801, resposta 0x0805 e upload multimídia 0x0801.
- Segurança proativa JT808: eventos ADAS/DSM e anexos de alarme com imagem, áudio, vídeo e texto.

O frontend não envia binário JT808/JT1078 bruto. Ele solicita ao BFF que use somente comandos retornados pelo Traccar para o dispositivo ou comandos salvos configurados no Traccar.
