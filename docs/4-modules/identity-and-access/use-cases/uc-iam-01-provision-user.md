# [UC-IAM-01] Alta de Nuevo Usuario (Invitación)

**Módulo:** Identity & Access (IAM)
**Actor Principal:** Sistema (Vía Event Listener)
**Descripción:** El sistema aprovisiona de manera automatizada y asíncrona una nueva cuenta de acceso tras reaccionar a un evento de consolidación laboral, dejándola preparada para su activación.

---

## 1. Pre-condiciones

* El actor principal (Admin) debe estar autenticado.
* El token del Admin debe contener el permiso o *claim* explícito `users:create`.

## 2. Flujo Principal (Reacción a Evento)

1. El *Message Broker* entrega el evento `EmployeeHiredIntegrationEvent` al módulo IAM (incluye `EmployeeId` y `EffectiveStartDate`).
2. El consumidor verifica la **Idempotencia**.
3. El sistema invoca al **`OrganizationalMapper` (ACL)** para traducir el `departmentId` recibido en el evento a un conjunto inicial de **Roles Técnicos** o **Grupos**.
4. El sistema evalúa la fecha de inicio (`EffectiveStartDate`):
   * **Si la fecha es Hoy o Pasada:** Crea la entidad `User` con sus roles iniciales en estado `Pending_Activation`, genera el `ActivationToken` y publica el evento `UserActivationRequiredIntegrationEvent`.
   * **Si la fecha es Futura:** Crea la entidad `User` con sus roles iniciales en estado `Scheduled_For_Activation`.
5. Se hace *commit* y se confirma el mensaje (ACK).

## 3. Flujos Alternativos / Excepciones

* **A1 - Fecha Futura (Proceso Programado):** Un proceso en segundo plano (ej. Cron Job diario) revisa los usuarios en estado `Scheduled_For_Activation`. Si la fecha actual coincide con `EffectiveStartDate`:
  1. Cambia estado a `Pending_Activation`.
  2. Genera `ActivationToken`.
  3. Publica `UserActivationRequiredIntegrationEvent`.
* **A2 - Correo Duplicado (Fallo de Invariante):** Si el email ya está registrado en IAM, el sistema:
  1. Cancela la transacción de creación.
  2. **Emite Evento de Fallo:** Publica `UserAccountCreationFailedIntegrationEvent` con `reasonCode: DUPLICATE_EMAIL`.
  3. Alerta a soporte vía módulo de Notifications.

## 4. Post-condiciones

* **Éxito:** Se ha reservado el correo en el sistema y existe un identificador único (`User_ID`) listo para ser enlazado con el módulo de Recursos Humanos.
* **Fallo:** La base de datos de IAM no sufre ninguna modificación. Se dispara alerta a soporte vía Notifications.

## 5. Eventos de Dominio (Domain & Integration Events)

* **Domain Event:** `UserProvisionedDomainEvent` (si aplica internamente).
* **Integration Event:** 
  * `UserCreatedIntegrationEvent`: Notifica creación exitosa.
  * `UserActivationRequiredIntegrationEvent`: Notifica cambio a estado pendiente de activación.
  * `UserAccountCreationFailedIntegrationEvent`: Notifica fallo técnico en la creación para alertas de soporte.

[back](./index.md)
