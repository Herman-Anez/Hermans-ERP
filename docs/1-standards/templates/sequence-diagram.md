# Plantilla: Diagrama de Secuencia

## Flujo: [Nombre del Proceso]

```plantuml
@startuml
autonumber
actor "Actor" as A
participant "Modulo A" as MA
queue "Mensajería" as bus
participant "Modulo B" as MB

A -> MA: Acción inicial (POST /resource)
MA -> MA: Validar lógica interna
MA -> bus: Publicar Evento de Integración
MA --> A: 202 Accepted

bus -> MB: Consumir Evento
MB -> MB: Ejecutar tarea secundaria
MB -> bus: Responder con Éxito/Fallo (Opcional)
@enduml
```

## Descripción del Escenario

Explicar brevemente qué sucede en este flujo y por qué es importante para el sistema.

[back](../diagram-conventions.md)
