# [UC-NOT-01] Enviar Correo Transaccional

**Módulo:** Notifications  
**Actor Principal:** Sistema / Service Client (Inter-Módulo)  
**Descripción:** Otro módulo solicita asíncronamente (vía Broker) o mediante comando síncrono (REST/gRPC) el envío de un correo paramétrico.

---

## 1. Pre-condiciones

* El Payload de solicitud contiene el `TemplateId` que debe existir en la BD como Activo.
* El Payload contiene un `CorrelationId` único o una clave de idempotencia probada.
* `RecipientEmail` es una dirección sintácticamente válida.

## 2. Flujo Principal (Happy Path)

1. El sistema recibe la intención de envío (vía `POST /emails/send`, comando gRPC `SendEmail` o consumidor de RabbitMQ).
2. El sistema consulta la base de datos para verificar si ese UUID de comando ya fue procesado con éxito (Idempotencia).
3. El `Templating Engine` extrae el `Template` asociado en BD, fusionando sus variables (`{{var}}`).
4. Se instancia en memoria un Agregado `Notification` en estado `Pending`.
5. Se inserta en la BD el registro e inmediatamente se encola el intento en el `Delivery & Retry Worker`.
6. El *Adapter* de SMTP/SES responde exitosamente al Handshake de envío.
7. El Worker actualiza el estado del registro `Notification` a `Sent`.

## 3. Flujos Alternativos / Excepciones

* **A1 - SMTP Unavailable (Transient Error):** La API externa (ej. AWS SES) expulsa un TimeOut o *Rate Limit*. El Worker detecta el error transitorio y programa un Reintento Automático (con *Exponential Backoff*). El estado de `Notification` se etiqueta como `Retrying`.
* **A2 - Invalid Address (Permanent Error):** El proveedor externo detecta rebote instantáneo (Hard Bounce). El intento muere permanentemente y el estado se pasa a `Failed`.

## 4. Post-condiciones

* **Éxito:** Se materializa un registro inmutable en auditoría mostrando la entrega exacta y los metadatos (Message-ID externo).
* **Fallo:** Queda un registro analítico con la causa oficial de falla proveniente del servidor saliente.

## 5. Eventos de Dominio (Domain & Integration Events)

* **Domain Event:** `NotificationRetryExhausted` (solo consumo local para alertas a Operations).
* **Integration Event:** `EmailDeliveryFailedIntegrationEvent` (emitido si todos los reintentos fallan, para que otros módulos como HR o IAM asimilen la falta de entrega).

[back](./index.md)
