# Arquitectura IAM - C4 Componentes

## Diagrama de Componentes (Nivel 3)

Esta vista detalla los componentes internos del modulo `Identity & Access` para autenticacion, autorizacion, sesiones y aprovisionamiento de identidades.

```plantuml
@startuml C4_Elements
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title Componentes - Identity & Access

Container_Boundary(iam, "Identity & Access") {
    Component(auth_api, "Auth API", "REST", "Expone login, refresh, logout y validacion")
    Component(user_admin_api, "User Admin API", "REST", "Gestion administrativa de usuarios, roles y permisos")
    Component(auth_service, "Authentication Service", "Application Service", "Valida credenciales y coordina sesiones")
    Component(token_service, "Token Service", "Domain / Security", "Genera y valida access token y refresh token")
    Component(rbac_engine, "RBAC Engine", "Domain Service", "Resuelve permisos efectivos del usuario")
    Component(provisioning_consumer, "Provisioning Consumer", "Worker", "Consume eventos de HR para crear usuarios")
    Component(user_repository, "User Repository", "Repository", "Persistencia de usuarios y relaciones")
    Component(refresh_token_repository, "RefreshToken Repository", "Repository", "Persistencia y rotacion de sesiones")
    Component(event_publisher, "Integration Event Publisher", "Infrastructure", "Publica eventos de IAM al broker")
}

ContainerDb(iam_db, "IAM Database", "PostgreSQL", "Usuarios, roles, permisos, refresh tokens")
ContainerDb(msg_bus, "Message Broker", "RabbitMQ", "Bus de integracion")

Rel(auth_api, auth_service, "Invoca casos de uso de autenticacion")
Rel(user_admin_api, user_repository, "Consulta y administra usuarios")
Rel(auth_service, token_service, "Solicita generacion y validacion de tokens")
Rel(auth_service, user_repository, "Consulta credenciales y estado del usuario")
Rel(auth_service, refresh_token_repository, "Gestiona refresh tokens")
Rel(token_service, rbac_engine, "Obtiene claims y permisos")
Rel(user_repository, iam_db, "Lee y escribe", "SQL/TCP")
Rel(refresh_token_repository, iam_db, "Lee y escribe", "SQL/TCP")

Rel(msg_bus, provisioning_consumer, "Entrega EmployeeHiredIntegrationEvent", "AMQP")
Rel(provisioning_consumer, user_repository, "Crea o actualiza usuarios aprovisionados")
Rel(provisioning_consumer, event_publisher, "Publica eventos de activacion")
Rel(event_publisher, msg_bus, "Publica UserActivationRequiredIntegrationEvent", "AMQP")
@enduml
```

## Componentes destacados

- **Auth API:** punto de entrada para autenticacion y sesion.
- **Authentication Service:** coordina login, refresh, bloqueo y revocacion.
- **RBAC Engine:** encapsula la logica de permisos y evita dispersarla.
- **Provisioning Consumer:** integra IAM con HR sin acoplamiento directo.

[back](../../../1-standards/diagram-conventions.md)
