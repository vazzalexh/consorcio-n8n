# Ejecución real de resumen · 98

- Fecha y hora local de inicio: 19/11/2026 22:18:37 (Buenos Aires).
- Estado de la ejecución: success.
- El contador pasó de 5 a 6; el flujo leyó la memoria de conversación, llamó a Groq, validó la respuesta y guardó el resumen con upsert.
- Luego de guardar, el contador quedó en 0.
- Modelo: `openai/gpt-oss-20b`; temperatura 0; razonamiento bajo; máximo 400 tokens de salida. La llamada usó 101 tokens de salida.

## Nodos ejecutados

[M1] Actualizar contador → [M2] ¿Más de 5 mensajes? → [M3] Leer historial → [M4] Resumir conversación → [M5] Validar resumen → [M5a] ¿Resumen válido? → [M6] Guardar resumen

## Resumen consolidado guardado

```json
{
  "asunto_principal": "Reclamo de ruido nocturno en pasillo común",
  "puntos_clave": [
    "Horario del SUM 10:00-23:59",
    "Historia ficticia de ruido después de las 11 pm",
    "Reclamo registrado por el usuario",
    "Caso derivado a Administración para revisión"
  ],
  "accion_requerida": "Revisar y gestionar el reclamo de ruido"
}
```

La historia de ruido era ficticia. El agente clasificó uno de los mensajes como reclamo pese a la aclaración; se rechazó la aprobación y se eliminó el registro de reclamo generado por la prueba. El texto anterior reproduce el resumen guardado sin modificar la fila real.
