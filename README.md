# Talk: enumeración de identidad celular (LATAM)

Material reutilizable para talks y labs sobre **enumeración mínima de telefonía** en Latinoamérica: solo identidad de celda servidora (`mcc`, `mnc`, `lac_or_tac`, `cell_id`), sin geodatos ni métricas de radio.

## Objetivo

Producir **muestras** (JSON) desde:

1. **Módem dedicado** (placa/IoT con módulo AT, p. ej. familia SIM7600)
2. **Android** (`TelephonyManager` / `CellInfo*`)

El contrato de datos vive en la prompt-spec:

- [prompts/obtener-identidad-celda.md](prompts/obtener-identidad-celda.md)

## Estructura

| Ruta | Rol |
|------|-----|
| [prompts/](prompts/) | Spec/contrato (fuente de verdad del schema) |
| [skill/](skill/) | Skill Cursor: cuándo y cómo generar o validar muestras |
| [agent/](agent/) | Prompt de agente: flujos módem dedicado y Android para LATAM |

## Flujo sugerido en talk o lab

```text
1. Cargar skill (docs/talk/skill/SKILL.md)
2. Instruir al agente con docs/talk/agent/AGENT.md
3. Obtener muestra JSON según plataforma (módem AT o Android)
4. Validar contra el contrato de la prompt-spec
```

## Alcance LATAM

- El esquema es el mismo en cualquier región; en LATAM el `mcc` identifica el país (ejemplos de referencia, no exhaustivos): `334` MX, `732` CO, `722` AR, `724` BR, `730` CL, `716` PE, etc.
- Las muestras de ejemplo usan **valores sintéticos** (MCC/MNC inventados o de prueba). No incluir coordenadas ni ubicaciones reales.

## Fuera de alcance

- Wardriving georreferenciado, CSV con lat/lon, vecinos, IMSI/IMEI, interceptación.
- Detalles de un firmware concreto: este paquete es genérico para otros proyectos.
