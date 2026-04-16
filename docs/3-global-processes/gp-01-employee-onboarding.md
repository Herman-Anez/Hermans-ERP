# Proceso Global 01: Employee Onboarding

## Objetivo

Describir el flujo end-to-end desde la contratacion administrativa de un empleado hasta la activacion efectiva de su acceso al ERP.

## Modulos participantes

- **Human Resources (HR):** fuente de verdad del alta laboral.
- **Identity & Access (IAM):** gestiona identidad tecnica, credenciales y activacion.
- **Notifications:** envia comunicaciones necesarias para completar el proceso.

## Flujo principal

### 1. Alta administrativa

- **Actor:** gestor de Recursos Humanos
- **Modulo:** `human-resources`
- **Caso de uso relacionado:** `UC-HR-01: Contratar Empleado`

HR registra los datos personales, contractuales y organizacionales del empleado. Una vez confirmada el alta, publica el evento `EmployeeHiredIntegrationEvent`.

### 2. Aprovisionamiento de identidad

- **Actor:** sistema
- **Modulo:** `identity-and-access`
- **Casos de uso relacionados:**
  - `UC-IAM-01: Provision User`
  - `UC-IAM-07: Process Scheduled Activations`

IAM consume el evento de HR y crea la identidad del usuario. Si la fecha de inicio del contrato es futura, la cuenta nace en estado `Scheduled_For_Activation`. Llegada la fecha efectiva, el proceso de fondo `UC-IAM-07` la transiciona a `Pending_Activation` y dispara la notificación.

### 3. Notificación al empleado

- **Actor:** sistema
- **Modulo:** `notifications`
- **Caso de uso relacionado:** `UC-NOT-01: Enviar Correo Transaccional`

Notifications consume el evento `UserActivationRequiredIntegrationEvent` (sin secretos). Tras recibirlo, Notifications solicita el token de activación a `IAM` vía API interna segura para poder incluirlo en el correo de bienvenida.

### 4. Activacion de cuenta

- **Actor:** nuevo empleado
- **Modulo:** `identity-and-access`
- **Caso de uso relacionado:** `UC-IAM-02: Activate Account`

El empleado completa la activacion mediante el flujo definido por IAM. Si la validacion del token y la politica de password son correctas, la cuenta pasa a estado `Active`.

## Diagrama de Secuencia (Onboarding Asíncrono)

```plantuml
@startuml
autonumber
actor "HR Manager" as hr
participant "HR Module" as hr_mod
queue "Mensajería (RabbitMQ)" as bus
participant "IAM Module" as iam_mod
participant "IAM Worker" as iam_cron
participant "Notifications Module" as notif_mod
actor "Nuevo Empleado" as emp

hr -> hr_mod: POST /employees (alta)
hr_mod -> hr_mod: Persiste Employee
hr_mod -> bus: Publica EmployeeHiredIntegrationEvent
hr_mod --> hr: 201 Created

bus -> iam_mod: Consume Evento Hired
alt Caso de Éxito
    alt Ingreso HOY o PASADO
        iam_mod -> iam_mod: Crea cuenta (Pending_Activation)
        iam_mod -> bus: Publica UserActivationRequiredIntegrationEvent
    else Ingreso FUTURO
        iam_mod -> iam_mod: Crea cuenta (Scheduled_For_Activation)
        ... Tiempo después (Fecha Inicio) ...
        iam_cron -> iam_cron: UC-IAM-07: Trigger Diario
        iam_cron -> iam_mod: Cambia a Pending_Activation
        iam_cron -> bus: Publica UserActivationRequiredIntegrationEvent
    end
else Fallo de IAM (Ej: Email Duplicado)
    iam_mod -> iam_mod: Falla la creación
    iam_mod -> bus: Publica UserAccountCreationFailedIntegrationEvent
    bus -> notif_mod: Consume Evento de Fallo
    notif_mod -> notif_mod: UC-NOT-03: Alerta IT Admin (Email/Slack)
end

bus -> notif_mod: Consume Evento ActivationRequired
notif_mod -> iam_mod: GET /internal/users/{userId}/activation-token
iam_mod --> notif_mod: Retorna ActivationToken
notif_mod -> emp: Envía Correo con Token

emp -> iam_mod: POST /auth/activate (Token + Password)
iam_mod -> iam_mod: Cambia cuenta a Active
iam_mod --> emp: 200 OK
@enduml
```

## Resultado esperado

Al finalizar el proceso:

- el empleado existe legalmente en HR
- la identidad tecnica existe en IAM
- el usuario recibio la notificacion necesaria
- la cuenta queda activa y lista para operar

## Riesgos y consideraciones

- Si IAM falla al crear la cuenta, debe emitir un evento de fallo para alertar a Soporte Técnico (IT), sin revertir el alta en HR.
- Si Notifications no logra entregar el correo, el sistema debe permitir reintentos o escalamiento operativo.
- Todo el flujo debe ser auditable mediante `correlationId`.

> [!NOTE]
> **Nota de Implementación Técnica:** El paso de "Tiempo después" basado en la Fecha de Inicio es gestionado por un componente `Scheduler` (UC-IAM-07) que escanea diariamente las cuentas en estado `Scheduled_For_Activation` cuya fecha objetivo sea `<= hoy`, transicionándolas y publicando el evento de activación manual.
