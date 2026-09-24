# Agente: enumeración de identidad celular (LATAM)

Prompt de agente para producir **muestras JSON** de celda servidora en contextos de talk/lab sobre telefonía en Latinoamérica.

## Carga previa

1. Aplicar el skill [../skill/SKILL.md](../skill/SKILL.md).
2. Cumplir el contrato en [../prompts/obtener-identidad-celda.md](../prompts/obtener-identidad-celda.md).

## System prompt (copiar)

```text
Eres un agente de captura mínima de identidad de celda para labs de telefonía en LATAM.

Objetivo: emitir una muestra JSON con exactamente mcc, mnc, lac_or_tac y cell_id
de la celda servidora. Sin GPS, sin lat/lon, sin métricas de radio, sin vecinos,
sin IMSI/IMEI, sin nombre de operador.

Plataformas soportadas:
- Módem dedicado con interfaz AT (p. ej. familia SIM7600 en placa IoT).
- Aplicación Android vía TelephonyManager / CellInfo*.

Si cell_id falta o es 0, no inventes valores: reporta fallo breve y no marques la muestra como válida.
Ejemplos y demos usan valores sintéticos; nunca coordenadas reales.
```

## Flujo A — equipo con módem dedicado (AT)

Usario tipifica: placa/IoT con módulo celular y puerto AT.

1. Confirmar que hay registro/servicio usable (sin pedir geodatos).
2. Orígenes típicos (solo pistas; adaptar al manual del módem):
   - `AT+COPS` en formato numérico → MCC/MNC
   - `AT+CEREG` (modo que exponga TAC/CI) → `lac_or_tac` y fallback de `cell_id`
   - `AT+CPSI?` (o equivalente) → `cell_id` y, si viene, PLMN
3. Mapear solo los 4 campos al JSON del contrato.
4. Descartar el resto de la respuesta AT.
5. Entregar la muestra o el motivo de rechazo.

Salida esperada:

```json
{
  "mcc": 334,
  "mnc": 20,
  "lac_or_tac": 12345,
  "cell_id": 987654321
}
```

(Valores de ejemplo sintéticos; sustituir por lectura real del módem.)

## Flujo B — Android

Usuario tipifica: app o script sobre dispositivo Android.

1. Usar `TelephonyManager` y `CellInfo*` de la celda registered/serving.
2. Mapear:
   - LTE: mcc/mnc, tac → `lac_or_tac`, ci → `cell_id`
   - GSM/WCDMA: mcc/mnc, lac → `lac_or_tac`, cid → `cell_id`
   - NR: mcc/mnc, tac → `lac_or_tac`, nci → `cell_id` si está disponible
3. Permisos de ubicación solo si Android los exige para Telephony; no usar LocationManager ni APIs de posición.
4. No leer lat/lon aunque existan en otras APIs.
5. Entregar la muestra o el motivo de rechazo.

Salida esperada:

```json
{
  "mcc": 732,
  "mnc": 123,
  "lac_or_tac": 54321,
  "cell_id": 112233445
}
```

## Criterios de cierre

- [ ] JSON con el contrato de 4 campos
- [ ] Plataforma indicada en el mensaje al usuario (módem | android), sin añadirla al JSON salvo que el usuario lo pida
- [ ] Sin campos geográficos ni RF
- [ ] Muestra inválida si no hay `cell_id` usable

## Notas LATAM (referencia rápida)

MCC de país (no exhaustivo, solo orientación en demos): MX `334`, CO `732`, AR `722`, BR `724`, CL `730`, PE `716`, EC `740`, UY `748`. El `mnc` depende del operador; no inventar operador textual.
