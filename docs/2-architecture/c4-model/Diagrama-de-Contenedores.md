# Infraestructura - C4 Model (Containers)

Para orquestar la interaccion entre clientes, modulos y componentes de integracion, la solucion adopta una vista por contenedores basada en **API Gateway**, bounded contexts desacoplados y mensajeria asincrona.

## Diagrama de Contenedores

```plantuml
@startuml C4_Elements
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Container.puml

Person(employee, "Empleado", "Usuario autenticado del ERP")
Person(hr_manager, "Gestor HR", "Administra empleados y contratos")
Person(sys_admin, "Administrador", "Gestiona roles, permisos y operacion")

Container(spa, "Web Application", "React / TypeScript", "Interfaz principal del ERP Core")
Container(api_gateway, "API Gateway / BFF", "Node.js / Nginx", "Punto de entrada, autenticacion delegada y enrutamiento")

System_Boundary(erp, "ERP Core") {
    Container(iam, "Identity & Access", "Service", "Autenticacion, autorizacion y sesiones")
    Container(hr, "Human Resources", "Service", "Gestion del ciclo de vida laboral")
    Container(notifications, "Notifications", "Service / Worker", "Comunicaciones salientes y trazabilidad")
    ContainerDb(iam_db, "IAM Database", "PostgreSQL", "Usuarios, roles, refresh tokens")
    ContainerDb(hr_db, "HR Database", "PostgreSQL", "Empleados, contratos, estructura")
    ContainerDb(notifications_db, "Notifications Database", "PostgreSQL", "Notificaciones, plantillas, intentos")
    ContainerDb(msg_bus, "Message Broker", "RabbitMQ / Kafka", "Eventos de integracion y procesos asincronos")
}

System_Ext(email_provider, "Proveedor Email", "Servicio externo para entrega de correos")

Rel(employee, spa, "Usa portal ERP", "HTTPS")
Rel(hr_manager, spa, "Administra HR", "HTTPS")
Rel(sys_admin, spa, "Administra IAM y operacion", "HTTPS")
Rel(spa, api_gateway, "Invoca APIs", "HTTPS")

Rel(api_gateway, iam, "Login, refresh, validacion de token", "HTTP")
Rel(api_gateway, hr, "Operaciones de empleados y contratos", "HTTP")
Rel(api_gateway, notifications, "Consultas operativas y reprocesos", "HTTP")

Rel(iam, iam_db, "Lee y escribe", "SQL/TCP")
Rel(hr, hr_db, "Lee y escribe", "SQL/TCP")
Rel(notifications, notifications_db, "Lee y escribe", "SQL/TCP")

Rel(hr, msg_bus, "Publica EmployeeHiredIntegrationEvent", "AMQP")
Rel(msg_bus, iam, "Entrega eventos de HR", "AMQP")
Rel(iam, msg_bus, "Publica UserActivationRequiredIntegrationEvent", "AMQP")
Rel(msg_bus, notifications, "Entrega eventos de IAM y otros modulos", "AMQP")
Rel(notifications, email_provider, "Entrega correos transaccionales", "HTTPS")
@enduml
```

## Notas de la vista

- Cada bounded context conserva su propia persistencia.
- `Notifications` actua como servicio y worker segun el tipo de flujo.
- El broker desacopla procesos globales como onboarding y fallos de entrega.

## Documentos relacionados

- [Diagrama de Contexto](./Diagrama-de-Contexto.md)
- [Infrastructure](../infrastructure.md)
- [Eventos de integracion](../../5-events/integration-events.md)

[back](../arquitectura-y-patrones.md)
