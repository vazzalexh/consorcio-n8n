# Entregable 2 · Manager, workers y aprobación humana

El agente pasa a ser un **Manager** que clasifica la intención del vecino y delega en dos sub-workflows (workers): **reclamos** y **reservas del SUM**. Cada pedido queda pendiente hasta que una persona lo aprueba o rechaza en Slack con "Send and Wait", **sin límite de tiempo**. Todos los workers devuelven el mismo contrato: `{status, worker, traceId, data}`.

## Archivos

- `PreEntrega_Modulo2_Alexis_Vazquez.pdf`: documento entregado.
- `workflows/`: Manager y los dos workers (anonimizados).
- `capturas/`: lienzos, delegación y ejecuciones.

## Pruebas

| Ejecución | Caso | Resultado |
|---|---|---|
| 55 | Reclamo aprobado en Slack | Registrado y aviso al vecino |
| 60, 66 | Reclamo y reserva | Clasificación y delegación correctas |
| 70 | Falla del worker de reclamos | Carril de error: aviso a Slack y contingencia |
| 71 | Reserva que esperó 35 minutos la aprobación | Registrada al aprobarse, sin vencimiento |
