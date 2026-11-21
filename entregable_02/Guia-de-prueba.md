> **Documento histórico:** esta guía corresponde a una implementación anterior con PostgreSQL y OpenAI. Su instrucción de vencimiento de la aprobación ya no aplica; el proyecto actual usa Airtable, Groq y aprobación sin límite de espera. No la uses para configurar ni probar los workflows actuales.

# Prueba local del asistente de consorcios
Los cuatro flujos se pueden inspeccionar desactivados. Usá un bot de Telegram de pruebas, un canal privado y una base de pruebas. Para probar desde localhost podés simular el ingreso con PowerShell. El canal de Slack y el método sendMessage de Telegram sí efectúan envíos cuando ejecutás esos nodos.

## Preparar conexiones
1. En PostgreSQL ejecutá preparar-demo.sql. Usá una base dedicada y asociá la credencial PostgreSQL a los nodos de cupo, normas y registro. Agregá tu usuario de prueba con una consulta como la siguiente, reemplazando el identificador por el de tu cuenta de Telegram. Primero iniciá el chat con el bot.
```sql
INSERT INTO consorcio_demo.vecinos(persona, unidad)
VALUES ('000000000', 'Unidad prueba') ON CONFLICT DO NOTHING;
```
2. En cada flujo principal, asigná una credencial Header Auth al webhook con el nombre de encabezado X-Telegram-Bot-Api-Secret-Token y un secreto privado. En el nodo de espera del Manager, asigná otra credencial Header Auth con el encabezado X-Aprobador y un secreto distinto que solo tenga Administración.
3. Asigná una credencial OpenAI al modelo, una de Telegram al nodo de respuesta y una de Slack OAuth2 a todos los nodos Slack. Seleccioná en cada uno el canal privado de administración. Verificá que el bot de Slack pertenezca a ese canal. En el Manager comprobá que los nodos Delegar reclamos y Delegar reservas apuntan a sus respectivos workers. Guardá los cambios.
4. Para la demo local, abrí el flujo, presioná Execute workflow y copiá la Test URL del webhook. En PowerShell podés enviar un ejemplo con este bloque. Repetí la escucha manual para cada caso y cambiá message_id para cada pedido nuevo.
```powershell
$url = Read-Host 'Test URL del webhook'
$secret = Read-Host 'Secreto del webhook' -AsSecureString
$headers = @{'X-Telegram-Bot-Api-Secret-Token' = [Net.NetworkCredential]::new('', $secret).Password}
$persona = [long](Read-Host 'Tu ID de Telegram de prueba')
$body = @{message=@{message_id=1;from=@{id=$persona};chat=@{type='private';id=$persona};text='¿Qué requisito tiene la reserva del SUM?'}} | ConvertTo-Json -Depth 5
Invoke-RestMethod -Method Post -Uri $url -Headers $headers -ContentType 'application/json; charset=utf-8' -Body ([Text.Encoding]::UTF8.GetBytes($body))
```
El acuse inmediato del webhook solo confirma recepción. Verificá el desenlace en Executions. Para recibir actualizaciones directamente desde Telegram hace falta configurar el webhook HTTPS accesible al bot y el mismo secret_token. Para la prueba local alcanza el ejemplo de arriba.

## Casos para registrar en la entrega
- M1. Preguntá por la regla del SUM. Revisá la herramienta de normas, la respuesta y el registro en Slack con ejecución, clasificación y cantidad de llamadas. Una solicitud fuera del alcance debe quedar como derivar.
- M2 reclamos. Enviá «Quiero registrar un reclamo por la luz rota del pasillo». El Manager debe enviar solo evento y detalle, esperar al worker y devolver su resultado.
- M2 reservas. Pedí una fecha futura explícita con hora de inicio y fin para el mismo día. Verificá que el Manager quede en espera y la tabla de reservas siga sin esa fila. Revisá el mensaje original y el horario solicitado antes de aprobar. Con el identificador y la URL recibidos por Administración, enviá la decisión desde PowerShell:
```powershell
$resume = Read-Host 'URL de reanudación de esa ejecución'
$evento = Read-Host 'Evento que figura en la solicitud'
$clave = Read-Host 'Secreto de Administración' -AsSecureString
$cabecera = @{'X-Aprobador' = [Net.NetworkCredential]::new('', $clave).Password}
$decision = @{decision='aprobar';evento=$evento} | ConvertTo-Json
Invoke-RestMethod -Method Post -Uri $resume -Headers $cabecera -ContentType 'application/json' -Body $decision
```
- Repetí con otro evento y decisión rechazar. Probá también dejar pasar 15 minutos. La ejecución debe finalizar sin reserva en ambos casos. Un intento con clave incorrecta debe mantener la espera.
- Falla de worker. Solicitá el mismo horario con otro evento y aprobalo. La restricción de solapamiento de la base debe impedir la segunda reserva y devolver status error al Manager. Si falla el envío de la respuesta después de una escritura, revisá la ejecución y la tabla antes de informar el resultado.
- Cupos. Con un usuario de prueba intentá seis pedidos distintos en menos de un minuto. El sexto debe terminar en el control de cupo, antes de consumir tokens o enviar mensajes. Repetir un evento admitido tampoco debe consumir otro turno. El cupo es compartido entre M1 y M2. Los topes son 40 por vecino y 100 para todo el consorcio por 24 horas móviles. Cada entrada admite hasta 1.200 caracteres, cada evento permite dos lecturas exitosas de normas y el modelo tiene cinco iteraciones y hasta 500 tokens de salida por llamada. Esto acota el uso; el costo también depende de los tokens de entrada y de las tarifas del proveedor.

## Evidencia para la certificación
M1 se entrega como checkpoint1_nombre_apellido.json, reemplazando nombre y apellido y exportando después de configurar y probar. M2 requiere preentrega_modulo2_apellido_nombre.pdf con capturas del Manager, ambos workers, la configuración del contrato y la espera del hijo, un recorrido exitoso y la ruta de error. Incluí también una captura de la espera humana antes de aprobar. El contrato de reservas contiene evento, inicio y fin; el de reclamos contiene evento y detalle. Ambos devuelven status, worker y data.mensaje. La decisión de reservar corresponde a Administración y el registro definitivo lo realiza el worker cuando la base acepta el turno.
