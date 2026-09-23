# Ecosistema de Automatización IA — Atención al Cliente con Memoria

**Entrega Final — Curso IA Automation (Arquitecto de Flujos IA)**
Estudio Contable · Stefano Ciravegna

## El proyecto

El sistema recibe consultas de clientes por Gmail, busca al cliente y su
historial en Airtable (memoria), redacta una respuesta con Gemini API, y
**espera aprobación humana en Slack antes de enviar nada** — ningún mensaje
sale a un cliente real sin que alguien del estudio lo apruebe primero.
Validado con 8+ ejecuciones reales de punta a punta (ver `capturas/`).

| | |
|---|---|
| Orquestador | n8n |
| Base de datos / memoria | Airtable |
| Procesamiento IA | Google Gemini API (Gemini 3.6 Flash) |
| Canal de salida | Gmail (cliente, vía reply) + Slack (aprobación interna) |
| Punto HITL | Slack "Send and Wait" — aprobación antes del envío |

## Archivos de este repositorio

| Archivo | Qué es | Criterio de rúbrica |
|---|---|---|
| `01_Diagrama_Arquitectura_Ciravegna.pdf` | Diagrama completo del flujo: triggers, routers, APIs, nodo de IA y destino de los datos | Mapa de Arquitectura (20%) |
| `02_Manual_Operativo_Datos_Ciravegna.pdf` | Esquema real de las tablas de Airtable (Clientes/Consultas/Errores) + los 7 esquemas JSON de cada integración | Estructuras de Datos (20%) |
| `03_Matriz_Costos_Ciravegna.pdf` | Qué modelo de IA se usa por tarea y por qué, con el cálculo real de ahorro (Batch API + selección de tier) | Optimización de Costos (20%) |
| `04_Seguridad_Resiliencia_Ciravegna.pdf` | Minimización de datos, manejo de errores, y el punto HITL explicado | Seguridad y Resiliencia (20%) |
| `Estudio_Contable_Atencion_Cliente_FINAL.json` | Blueprint técnico del flujo, validado con ejecuciones reales | Lógica del Flujo (archivo técnico) |
| `capturas/` | 13 screenshots: Slack (Aprobar_rechazar_1-6) + Gmail (Respuesta_1-6) de 6 consultas distintas, más el panel de ejecuciones de n8n (Ejecuciones_Exitosas) | Evidencia requerida por el formato de entrega |
| `05_Instrucciones_Claude_Chrome.md` | Instrucciones para importar el flujo por primera vez en n8n | Uso interno / reproducibilidad |
| `06_Instrucciones_Fix_Claude_Chrome.md` / `07_Instrucciones_Fix_v2_Claude_Chrome.md` | Historial de las dos rondas de debugging | Uso interno / reproducibilidad — **ver nota abajo** |

> **Nota sobre 06 y 07:** documentan el proceso real de debugging, pero la
> solución final que terminó funcionando (`_FINAL.json`) tomó un camino más
> simple que el que esos documentos proponían para el punto HITL: en vez de
> un Webhook separado + Slack Block Kit armado a mano, se usó la operación
> nativa **"Send and Wait"** del nodo de Slack de n8n (pausa la misma
> ejecución, sin necesitar un endpoint aparte), y en vez de reconstruir el
> destinatario del email se usó **Gmail → Reply** sobre el mensaje original.
> El archivo técnico válido es `Estudio_Contable_Atencion_Cliente_FINAL.json`.

## Enlaces obligatorios

- **Base de Airtable (modo lectura):** https://airtable.com/invite/l?inviteId=invOZIHLZnLdtNCMx&inviteToken=368432e2fd03c0841117d85453da447178c64d73d94682dbcb6eeaf649a4ac2e
- **Dashboard de Control (Shared View pública):** https://airtable.com/appgfBlFJe7xulFaQ/pagciHGA9NLUk4BlY

> **Nota sobre el link de la base:** es un link de invitación a colaborador
> (requiere crear cuenta de Airtable y aceptar la invitación), no un link
> público de solo lectura sin login. Se dejó así a propósito — decisión
> tomada conscientemente, no un descuido.

## Estado actual

- [x] Los 4 documentos de la rúbrica (arquitectura, datos, costos, seguridad)
- [x] Flujo validado de punta a punta — 8+ ejecuciones exitosas reales,
      incluyendo aprobación y rechazo en Slack, y envío de respuesta por Gmail
- [x] Capturas de evidencia (Slack + Gmail de 6 consultas + panel de
      ejecuciones de n8n)
- [x] Dashboard de Control publicado (Airtable Interface)
- [x] Enlace a la base de Airtable (como invitación a colaborador, ver nota)
- [x] Subida final a GitHub

## Nota sobre el proveedor de IA

El nodo de procesamiento usa **Gemini API**, no OpenAI ni Anthropic. La
consigna de la entrega final lista explícitamente "OpenAI (GPT) o Anthropic
(Claude)" como las dos opciones habilitadas para esta categoría — Gemini no
es una de ellas. Es una decisión tomada a conciencia, no un descuido; queda
documentada acá por transparencia con el corrector.

Además, el modelo pasó de `gemini-3.8-flash` a `gemini-3.6-flash` durante el
desarrollo: el primero devolvía error al invocarlo desde la API real pese a
estar anunciado, y el segundo respondió sin problemas. Misma tarifa
($1.50/$7.50 por millón de tokens), así que no afecta la Matriz de Costos.

## Cómo reproducir el flujo

1. Importar `Estudio_Contable_Atencion_Cliente_FINAL.json` en una instancia
   de n8n.
2. Configurar las 4 credenciales (Gmail OAuth2, Airtable Personal Access
   Token, Slack Bot Token, Google Gemini API key).
3. Crear en Airtable las 3 tablas descriptas en el Manual Operativo de Datos
   (`02_Manual_Operativo_Datos_Ciravegna.pdf`): Clientes, Consultas, Errores.
4. Activar el flujo y probar con un email real a la casilla configurada en
   el trigger de Gmail.
