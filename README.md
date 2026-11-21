# Consorcio en n8n

Chatbot de administración de un consorcio construido en n8n, proyecto integrador del curso **Automation Avanzado** de Coderhouse. Los vecinos escriben o mandan notas de voz por Telegram; el asistente responde con el reglamento del edificio, registra reclamos y reservas del SUM con aprobación humana en Slack y supervisa cada respuesta con un segundo modelo.

## Entregables

| # | Tema | Carpeta |
|---|---|---|
| 1 | Agente base | [entregable_01](entregable_01/) |
| 2 | Manager, workers y aprobación humana | [entregable_02](entregable_02/) |
| 3 | Memoria en Airtable y resumen | [entregable_03](entregable_03/) |
| 4 | Casilla de Administración (Gmail) | [entregable_04](entregable_04/) |
| 5 | RAG con LlamaCloud | [entregable_05](entregable_05/) |
| 6 | Voz: Whisper + ElevenLabs | [entregable_06](entregable_06/) |
| 7 | Línea de base y KPIs | [entregable_07](entregable_07/) |
| 8 | Supervisor AI-as-a-Judge y prueba A/B | [entregable_08](entregable_08/) |
| 9 | Trazabilidad y log estructurado | [entregable_09](entregable_09/) |
| 10 | Brochure | [entregable_10](entregable_10/) |
| Final | Proyecto integrador | [final](final/) |

## Estructura de cada carpeta

- `README.md`: qué pide el entregable, qué se construyó y con qué ejecuciones se probó.
- El documento entregado (PDF), cuando corresponde.
- `workflows/`: los workflows de n8n exportados en JSON.
- `capturas/`: las imágenes que usa el README.

## Stack

n8n 2.32.6 self-hosted · Groq (`gpt-oss-20b` agente, `gpt-oss-120b` juez, Whisper) · Airtable (CRM y base de datos) · Telegram (canal del vecino) · Slack (aprobaciones y errores) · LlamaCloud (RAG) · ElevenLabs (voz) · Gmail (casilla de Administración).

## Cómo importar un workflow

Los JSON están **anonimizados**: IDs de Airtable, canales de Slack, dominios y emails reemplazados por marcadores (`appXXXXXXXXXXXXXX`, `tblXXXXXXXXXXXXXX`, `CXXXXXXXXXX`, `https://TU-DOMINIO/`). Para usarlos:

1. En n8n, **Import from File** con el JSON.
2. Crear las credenciales con los mismos nombres que figuran en cada nodo.
3. Reemplazar los marcadores por los valores propios.
4. Importar primero los sub-workflows y después el Manager, que los llama por ID.
