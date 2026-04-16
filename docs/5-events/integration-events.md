# Catalogo de Eventos de Integracion

**Version:** 1.1.0  
**Estado:** En evolucion  
**Objetivo:** Definir los contratos asincronos intercambiados entre bounded contexts del ERP.

## Reglas globales

Todo evento de integracion debe incluir:

- `eventId`: identificador unico del mensaje para soportar idempotencia.
- `eventType`: nombre estable del evento.
- `occurredOn`: timestamp en formato ISO 8601 UTC.
- `correlationId`: identificador de seguimiento del proceso distribuido cuando aplique.
- `data`: payload minimo necesario para que el consumidor actue.

Ademas:

- Los eventos no deben exponer estructuras internas de persistencia.
- El payload debe ser estable y versionable.
- Los consumidores deben tolerar reintentos y mensajes duplicados.

## Eventos del modulo Human Resources

### `EmployeeHiredIntegrationEvent`

Se emite cuando HR consolida el alta laboral y contrato inicial de un nuevo empleado.

- **Productor:** `human-resources`
- **Routing Key:** `hr.employee.hired`
- **Consumidores conocidos:** `identity-and-access`

**Payload de ejemplo**

```json
{
  "eventId": "123e4567-e89b-12d3-a456-426614174000",
  "eventType": "EmployeeHiredIntegrationEvent",
  "occurredOn": "2026-04-13T12:00:00Z",
  "correlationId": "corr-onb-550e8400",
  "data": {
    "employeeId": "emp_550e8400",
    "firstName": "Juan",
    "lastName": "Perez",
    "personalEmail": "juan.perez@email.com",
    "departmentId": "dept_sales",
    "startDate": "2026-05-01T00:00:00Z"
  }
}
```

### `EmployeeOrganizationModifiedIntegrationEvent`

Se emite cuando ocurren movimientos departamentales o cambios de jefatura sin rescindir el empleado.

- **Productor:** `human-resources`
- **Routing Key:** `hr.employee.organization-modified`
- **Consumidores conocidos:** `identity-and-access` (vía ACL)

**Payload de ejemplo**

```json
{
  "eventId": "98bc2132-124b-3d44-c782-998877661122",
  "eventType": "EmployeeOrganizationModifiedIntegrationEvent",
  "occurredOn": "2026-04-14T09:30:00Z",
  "data": {
    "employeeId": "emp_550e8400",
    "newDepartmentId": "dept_marketing",
    "newManagerId": "emp_999a123b"
  }
}
```

### `EmployeeTerminatedIntegrationEvent`

Se emite en el momento exacto en que cesa la relación laboral activa. Crítico para revocar accesos.

- **Productor:** `human-resources`
- **Routing Key:** `hr.employee.terminated`
- **Consumidores conocidos:** `identity-and-access`, `notifications` (para alertas de IT/Ops)

**Payload de ejemplo**

```json
{
  "eventId": "55bb1234-a1b2-c3d4-e5f6-112233445566",
  "eventType": "EmployeeTerminatedIntegrationEvent",
  "occurredOn": "2026-12-31T23:59:59Z",
  "data": {
    "employeeId": "emp_550e8400",
    "terminationReason": "RESIGNATION"
  }
}
```

> [!NOTE]
> Se ha identificado la necesidad de un evento especializado `EmployeeOffboardingAlertRequired` para transportar detalles de recuperación de activos físicos. Por ahora, el módulo `notifications` consume este evento base para disparar alertas genéricas a soporte técnico.

### `EmployeeContractRenewedIntegrationEvent`

Lanzado tras asentar una renegociación, anexo o nueva temporalidad del contrato original.

- **Productor:** `human-resources`
- **Routing Key:** `hr.employee.contract-renewed`
- **Consumidores conocidos:** Opcional (sistemas Core Bancarios o Nóminas Externas)

**Payload de ejemplo**

```json
{
  "eventId": "aaabbb45-1234-4bc1-aa99-111122223333",
  "eventType": "EmployeeContractRenewedIntegrationEvent",
  "occurredOn": "2027-01-01T10:00:00Z",
  "data": {
    "employeeId": "emp_550e8400",
    "contractType": "FULL_TIME",
    "salary": 50000,
    "currency": "USD"
  }
}
```

### `EmployeePromotedIntegrationEvent`

Se emite cuando un empleado cambia de puesto o categoría profesional, impactando potencialmente en sus permisos de acceso.

- **Productor:** `human-resources`
- **Routing Key:** `hr.employee.promoted`
- **Consumidores conocidos:** `identity-and-access` (vía ACL)


**Payload de ejemplo**

```json
{
  "eventId": "eeffaa12-3456-4789-abcd-1234567890ab",
  "eventType": "EmployeePromotedIntegrationEvent",
  "occurredOn": "2026-06-01T09:00:00Z",
  "data": {
    "employeeId": "emp_550e8400",
    "newPositionId": "pos_senior_manager",
    "oldPositionId": "pos_junior_analyst",
    "newGrade": "G12"
  }
}
```

## Eventos del modulo Identity & Access

### `UserCreatedIntegrationEvent`

Se emite cuando IAM crea exitosamente una nueva identidad digital.

- **Productor:** `identity-and-access`
- **Routing Key:** `iam.user.created`
- **Consumidores conocidos:** `notifications`

**Payload de ejemplo**

```json
{
  "eventId": "f47ac10b-58cc-4372-a567-0e02b2c3d479",
  "eventType": "UserCreatedIntegrationEvent",
  "occurredOn": "2026-04-05T16:15:18Z",
  "correlationId": "corr-123-456-789",
  "data": {
    "userId": "usr_987654321",
    "email": "nuevo.empleado@tu-erp.com",
    "assignedRoles": ["USER"]
  }
}
```

Nota: el evento no debe transportar secretos ni tokens de activacion en claro.

### `UserActivationRequiredIntegrationEvent`

Se emite cuando una cuenta ya puede iniciar su proceso de activacion y el sistema necesita disparar la comunicacion correspondiente.

- **Productor:** `identity-and-access`
- **Routing Key:** `iam.user.activation-required`
- **Consumidores conocidos:** `notifications`

**Payload de ejemplo**

```json
{
  "eventId": "d16cc7b8-f7c5-4bf1-b1a1-52842c8db001",
  "eventType": "UserActivationRequiredIntegrationEvent",
  "occurredOn": "2026-04-05T16:20:00Z",
  "correlationId": "corr-123-456-789",
  "data": {
    "userId": "usr_987654321",
    "employeeId": "emp_550e8400",
    "email": "nuevo.empleado@tu-erp.com",
    "expiresAt": "2026-04-06T16:20:00Z"
  }
}
```

### `UserAccountCreationFailedIntegrationEvent`

Se emite cuando IAM no puede crear la identidad digital requerida para un empleado.

- **Productor:** `identity-and-access`
- **Routing Key:** `iam.user.account-creation-failed`
- **Consumidores conocidos:** `notifications` (para alertas a soporte IT)

**Payload de ejemplo**

```json
{
  "eventId": "e8d9c1b2-3344-4372-a567-0e02b2c3d479",
  "eventType": "UserAccountCreationFailedIntegrationEvent",
  "occurredOn": "2026-04-05T16:16:00Z",
  "correlationId": "corr-999-888-777",
  "data": {
    "employeeId": "emp_550e8400",
    "email": "juan.perez@empresa.com",
    "reasonCode": "DUPLICATE_EMAIL",
    "reasonMessage": "El correo electronico ya esta registrado en IAM.",
    "isRecoverable": false
  }
}
```

### `UserAccountLockedIntegrationEvent`

Se emite cuando una cuenta es bloqueada temporalmente por superar el limite de intentos fallidos.

- **Productor:** `identity-and-access`
- **Routing Key:** `iam.user.locked`
- **Consumidores conocidos:** `notifications`

**Payload de ejemplo**

```json
{
  "eventId": "a1b2c3d4-e5f6-7890-1234-56789abcdef0",
  "eventType": "UserAccountLockedIntegrationEvent",
  "occurredOn": "2026-04-05T18:20:00Z",
  "correlationId": "corr-321-654-987",
  "data": {
    "userId": "usr_123456789",
    "email": "admin@tu-erp.com",
    "lockoutDurationMinutes": 15,
    "ipAddress": "192.168.1.50"
  }
}
```

## Eventos del modulo Notifications

### `EmailDeliveryFailedIntegrationEvent`

Se emite cuando el modulo Notifications agota sus reintentos sin lograr una entrega exitosa de correo.

- **Productor:** `notifications`
- **Routing Key:** `notifications.email.delivery-failed`
- **Consumidores conocidos:** `identity-and-access`


**Payload de ejemplo**

```json
{
  "eventId": "b2c3d4e5-f6a7-4b8c-9d0e-1f2a3b4c5d6e",
  "eventType": "EmailDeliveryFailedIntegrationEvent",
  "occurredOn": "2026-04-05T16:45:00Z",
  "correlationId": "corr-999-888-777",
  "data": {
    "recipientEmail": "juan.perez@empresa.com",
    "templateId": "welcome-email",
    "providerError": "SMTP_TIMEOUT",
    "attemptsMade": 3
  }
}
```

## Consideraciones de diseno

- `correlationId` es clave para trazabilidad entre procesos distribuidos.
- `reasonCode` permite automatizar respuestas, traducciones y acciones correctivas.
- Los nombres de evento deben permanecer estables aunque cambie la implementacion interna.
- La semantica del evento debe ser de negocio, no de tecnologia interna.
- **Acoplamiento Cohesivo:** Los consumidores de eventos que contengan lógica de negocio (como cambios organizacionales) deben emplear capas de adaptación (ACL) para mapear dichos conceptos a su propio lenguaje técnico, evitando la fuga de dominio.

## Seguridad y Manejo de Secretos

Para garantizar la integridad del sistema y cumplir con las normativas de protección de datos:

1. **Prohibición de Secretos en el Bus**: Bajo ninguna circunstancia se deben enviar tokens de activación, contraseñas temporales o secretos criptográficos a través del Message Broker (RabbitMQ).
2. **Resolición de Secretos (Fetch-on-Demand)**: En flujos que requieran el envío de secretos (ej: activación de cuenta), el consumidor debe recibir un evento de estado genérico y posteriormente "jalar" (pull) el secreto desde el módulo emisor a través de una API interna privada y segura.
3. **Eventos de Estado**: Los eventos de integración deben limitarse a notificar cambios de estado (`UserActivationRequired`, `UserCreated`) para permitir que la orquestación continúe sin comprometer datos sensibles.

[back](../readme.md)
