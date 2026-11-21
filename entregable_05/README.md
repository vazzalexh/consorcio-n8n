# Entregable 5 · RAG con LlamaCloud

El agente responde las consultas de convivencia solo con el reglamento interno y la normativa de consorcios de CABA, indexados en LlamaCloud. Usa una herramienta (`RAG_reglamento`) conectada por la conexión lateral del agente, cita el documento y el artículo, y responde "No sé." cuando el dato no está.

- Búsqueda híbrida (70 % vectorial, 30 % texto), **Top-K 4**, **Min Score 0.005** (los puntajes híbridos reales van de 0,011 a 0,016).
- Un nodo de control verifica el "No sé." literal y que la fuente citada corresponda al fragmento usado.

## Archivos

- `PreEntrega_Modulo5_Alexis_Vazquez.pdf`: documento entregado (incluye la tabla de 5 preguntas ciegas y el plan de gobernanza).
- `workflows/`: "Consorcio · Tool · RAG" y el Manager (anonimizados).
- `capturas/`: índice de LlamaCloud, herramienta y respuestas con fuente.

## Pruebas (5 preguntas ciegas)

| Ejecución | Pregunta | Veredicto |
|---|---|---|
| 149 | ¿Puedo tener un perro en el depto? | Correcta (Art. 11) |
| 139 | ¿Hasta qué hora se puede hacer ruido el sábado? | Correcta (Art. 3) |
| 141 | ¿Puedo dejar la bici en el palier? | Correcta (Art. 8) |
| 145 | ¿Qué pasa si el administrador no rinde cuentas? | Parcial; corregida en la 154 |
| 147 | ¿Cuánto sale la expensa de diciembre? (sin cobertura) | Correcta: "No sé." |
