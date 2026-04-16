# [UC-IAM-07] Procesar Activaciones Programadas

**Módulo:** Identity & Access (IAM)
**Actor Principal:** Sistema (Cron Job / Background Worker)
**Descripción:** Un proceso en segundo plano se ejecuta periódicamente para identificar usuarios en estado `Scheduled_For_Activation` cuya fecha de inicio laboral ha llegado, disparando su flujo de activación.

---

## 1. Pre-condiciones

* El `User` existe en la base de datos de IAM en estado `Scheduled_For_Activation`.
* La `EffectiveStartDate` (heredada de HR) es igual o anterior a la fecha actual del sistema.

## 2. Flujo Principal (Happy Path)

1. El sistema dispara el proceso de fondo (ej: Cron diario a las 00:01 UTC).
2. El proceso consulta en `iam_db` todos los usuarios con estado `Scheduled_For_Activation` y `EffectiveStartDate <= CurrentDate`.
3. Para cada usuario encontrado:
   - El sistema cambia el estado del usuario de `Scheduled_For_Activation` a `Pending_Activation`.
   - El sistema genera un `ActivationToken` seguro y lo vincula al usuario.
   - El sistema publica el evento de integración `UserActivationRequiredIntegrationEvent`.
4. El proceso finaliza el procesamiento por lotes (batch) e informa resultados al log de auditoría.

## 3. Flujos Alternativos / Excepciones

* **A1 - Fallo en Publicación de Evento:** Si el Message Broker no está disponible, el sistema debe reintentar la publicación (Outbox Pattern) para asegurar que el empleado reciba su notificación de bienvenida.
* **A2 - Usuario ya Cancelado:** Si el empleado fue dado de baja en HR antes de su fecha de inicio (GP-02), el usuario técnico ya debería haber sido marcado como `Inactive` o eliminado, por lo que este proceso lo ignora.

## 4. Post-condiciones

* **Éxito:** Las cuentas de los empleados que ingresan hoy han pasado a `Pending_Activation` y se ha disparado la notificación de bienvenida.
* **Fallo:** Las cuentas permanecen en `Scheduled_For_Activation`; el acceso al ERP no se habilita en la fecha prevista.

## 5. Eventos de Dominio (Domain & Integration Events)

* **Integration Event:** `UserActivationRequiredIntegrationEvent` (Consumido por el módulo de *Notifications*).

[back](./index.md)
