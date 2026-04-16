# [UC-NOT-03] Alertar a Soporte IT (Fallo Crítico)

**Módulo:** Notifications  
**Actor Principal:** Sistema (Vía Event Listener)  
**Descripción:** El módulo reacciona a fallos críticos de infraestructura en otros contextos (principalmente IAM) y notifica de manera inmediata al Administrador del Sistema (IT Admin) para intervención manual.

---

## 1. Pre-condiciones

* El Message Broker entrega el evento `UserAccountCreationFailedIntegrationEvent`.
* El evento contiene el `EmployeeId`, el `Email` en conflicto y el `ReasonCode` (ej. `DUPLICATE_EMAIL`).

## 2. Flujo Principal (Happy Path)

1. El `Event Listener` de Notifications intercepta el fallo de creación de cuenta proveniente de IAM.
2. El sistema identifica el canal de alerta configurado para "Soporte IT" (ej. Lista de distribución de correo o Integración con Slack/Teams).
3. El sistema selecciona la plantilla de alerta `it-support-critical-alert`.
4. El motor de plantillas fusiona los datos del error: "No se pudo crear la cuenta para [Email]. Motivo: [Reason]".
5. El sistema envía la notificación urgente.
6. Se registra el incidente en la tabla de auditoría de notificaciones para seguimiento operativo.

## 3. Flujos Alternativos / Excepciones

* **A1 - Canal de Soporte No Configurado:** Si no hay un destino de alerta definido, se genera un log de error de nivel `CRITICAL` en el sistema de monitoreo general para evitar el silencio operativo.

## 4. Post-condiciones

* **Éxito:** El equipo de soporte técnico recibe la alerta con los metadatos necesarios para resolver el conflicto sin que el proceso de contratación legal en HR se vea afectado.
* **Fallo:** La alerta no se envía, pero queda constancia en los logs de error del módulo.

## 5. Eventos de Dominio (Domain & Integration Events)

* **Domain Event:** `ITSupportAlertSentDomainEvent`.
* **Integration Event:** Ninguno necesario (es un sumidero de error para acción humana).

[back](./index.md)
