# Prompt-spec: identidad de celda (módem AT + Android)

## Rol

Actúa como implementador de una captura de **identidad de celda servidora** únicamente. El objetivo es producir una muestra mínima y estable que identifique la celda actual sin incorporar geolocalización ni métricas de radio.

## Contrato de salida

Emitir exactamente este objeto JSON (nombres de campos fijos, sin campos adicionales):

```json
{
  "mcc": 0,
  "mnc": 0,
  "lac_or_tac": 0,
  "cell_id": 0
}
```

- `mcc`: Mobile Country Code.
- `mnc`: Mobile Network Code.
- `lac_or_tac`: LAC en 2G/3G; TAC en LTE/NR. El nombre unifica ambos.
- `cell_id`: identificador de celda (CID/ECI/NCI según tecnología).

No incluir latitud, longitud ni ningún otro campo.

## Prohibiciones

No solicitar, leer ni emitir:

- GPS, coordenadas, geocoding, LocationManager, Fused Location o APIs de posición.
- Métricas de radio: RSRP, RSRQ, SINR, RSSI.
- Banda, EARFCN, PCI, eNodeB, sector.
- Celdas vecinas.
- IMSI, IMEI u otros identificadores de suscriptor/dispositivo.
- Nombre textual del operador.
- Dumps ofensivos o técnicas de interceptación.

## Plataforma módem (AT) — alto nivel

Para módems celulares con interfaz AT (p. ej. familia SIMCom SIM7600):

- PLMN / registro: `AT+COPS` (formato numérico), `AT+CEREG` (estado; TAC/CI cuando el modo lo expone).
- Identidad de celda / servicio: `AT+CPSI?` o el comando equivalente del manual del módem.

Reglas:

1. Mapear solo MCC, MNC, TAC/LAC y Cell ID.
2. Descartar el resto de la respuesta.
3. Rechazar la muestra si no hay `cell_id` usable.

## Plataforma Android — alto nivel

- Usar `TelephonyManager` y las clases `CellInfo*` de la celda registered/serving.
- Mapear `mcc`/`mnc`, `tac` o `lac`, y `ci`/`cid`/`nci` al contrato.
- Solicitar permisos de ubicación solo si la versión de Android los exige para Telephony; no usar LocationManager ni APIs de posición.
- No leer lat/lon aunque alguna API las ofrezca.

## Criterios de aceptación

- Salida con exactamente los 4 campos del contrato.
- Valores ausentes: `null` o campo omitido; nunca inventar valores.
- `cell_id == 0` o equivalente “sin celda” → no emitir muestra válida.
- Incluir un ejemplo sintético por plataforma con MCC/MNC inventados y sin datos geográficos.
