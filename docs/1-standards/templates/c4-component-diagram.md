# Plantilla: Diagrama de Componentes C4 (Nivel 3)

## Diagrama de Componentes

```plantuml
@startuml C4_Elements
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title Componentes - [Nombre del Módulo]

Container_Boundary(mod, "[Nombre del Bounded Context]") {
    Component(api, "API Layer", "REST/GraphQL", "Expone funcionalidades al exterior")
    Component(app, "Application Services", "Service", "Coordina casos de uso")
    Component(domain, "Domain Model", "Logic", "Invariantes y reglas de negocio")
    Component(infra, "Infrastructure", "Adapter", "Implementaciones técnicas (DB, Broker, etc)")
}

ContainerDb(db, "Database", "PostgreSQL", "Almacenamiento persistente")
ContainerDb(msg_bus, "Message Broker", "RabbitMQ", "Bus de eventos")

Rel(api, app, "Invoca")
Rel(app, domain, "Usa")
Rel(app, infra, "Implementa via interfaces")
Rel(infra, db, "Escribe/Lee", "SQL")
Rel(infra, msg_bus, "Publica/Consume", "AMQP")
@enduml
```

## Componentes destacados

- **[Componente 1]:** Breve descripción.
- **[Componente 2]:** Breve descripción.

[back](../diagram-conventions.md)
