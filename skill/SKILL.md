---
name: latam-cell-identity-samples
description: >-
  Genera y valida muestras JSON de identidad de celda servidora (mcc, mnc,
  lac_or_tac, cell_id) para enumeración de telefonía en LATAM desde módem
  dedicado AT o Android. Usar cuando el usuario pida muestras de celda,
  enum de telefonía LATAM, captura sin GPS, o implementar agent/skill de
  identidad celular.
disable-model-invocation: true
---

# Skill: muestras de identidad celular (LATAM)

## Cuándo usar

- Pedidos de **muestras** o capturas mínimas de celda (módem dedicado o Android).
- Labs/talks de **enumeración de telefonía** en LATAM sin geolocalización.
- Validar que una salida cumple el contrato de 4 campos.

## Fuente de verdad

Leer y respetar siempre:

- [../prompts/obtener-identidad-celda.md](../prompts/obtener-identidad-celda.md)

Instrucciones de agente (flujos por plataforma):

- [../agent/AGENT.md](../agent/AGENT.md)

## Contrato (resumen)

Emitir solo:

```json
{
  "mcc": 0,
  "mnc": 0,
  "lac_or_tac": 0,
  "cell_id": 0
}
```

Sin lat/lon, sin métricas de radio, sin vecinos, sin IMSI/IMEI, sin nombre de operador.

## Procedimiento

1. Identificar plataforma: **módem dedicado (AT)** o **Android**.
2. Seguir el flujo correspondiente en [../agent/AGENT.md](../agent/AGENT.md).
3. Mapear únicamente MCC, MNC, LAC/TAC y Cell ID al JSON.
4. Si falta `cell_id` usable o es `0` → no emitir muestra válida; explicar el fallo en una línea.
5. Para demos LATAM: usar valores **sintéticos** en ejemplos (nunca coordenadas reales).

## Validación rápida

- [ ] Exactamente 4 claves del contrato (o `null`/omitido en ausentes)
- [ ] Ningún campo geográfico
- [ ] Ninguna métrica RF / banda / PCI / eNodeB
- [ ] `cell_id` usable si la muestra se marca válida

## Ejemplos sintéticos (LATAM demo)

Módem dedicado:

```json
{
  "mcc": 334,
  "mnc": 20,
  "lac_or_tac": 12345,
  "cell_id": 987654321
}
```

Android:

```json
{
  "mcc": 732,
  "mnc": 123,
  "lac_or_tac": 54321,
  "cell_id": 112233445
}
```

Los MCC anteriores son códigos de país de referencia para demos; los MNC/TAC/cell_id son inventados.
