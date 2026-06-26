# Identificação de motoristas

## Regra aplicada

Motorista atual deve vir da última posição:

```text
position.attributes.driverUniqueId
```

Quando `/api/drivers` está disponível, o frontend cruza:

```text
Driver.uniqueId === position.attributes.driverUniqueId
```

## Fallbacks

- Com match em `Driver.uniqueId`: exibe `Driver.name (driverUniqueId)`.
- Sem match, mas com `driverUniqueId`: exibe o `driverUniqueId` bruto.
- Sem `driverUniqueId`: exibe `Não informado`.

## O que foi bloqueado

O frontend não usa mais `Device.phone`, `Device.contact` ou `Device.uniqueId` como motorista. Esses campos podem identificar dispositivo ou contato, mas não comprovam motorista atual.

## Locais atualizados

- Popup do mapa.
- Tabela `Veículos > Motorista`.
- Enriquecimento central de frota em `enrichDevices()`.
- Grupo de telemetria `Motorista`, removendo `phone` como fallback.
