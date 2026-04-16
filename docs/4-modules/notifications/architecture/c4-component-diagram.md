# Arquitectura Notifications - C4 Componentes

## Diagrama de Componentes (Nivel 3)

Esta vista muestra como se organiza internamente el modulo `Notifications` para recibir solicitudes, renderizar contenido, entregar mensajes y registrar trazabilidad operativa.

```plantuml
@startuml C4_Elements
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title Componentes - Notifications

Container_Boundary(notifications, "Notifications") {
    Component(notification_api, "Notification API", "REST", "Consultas operativas, envios manuales y reprocesos")
    Component(event_consumer, "Integration Event Consumer", "Worker", "Consume eventos que requieren comunicacion")
    Component(notification_service, "Notification Application Service", "Application Service", "Coordina creacion, cola e idempotencia")
    Component(template_service, "Template Service", "Application Service", "Gestiona plantillas y versiones activas")
    Component(rendering_engine, "Rendering Engine", "Domain / Service", "Resuelve placeholders y contenido final")
    Component(delivery_worker, "Delivery Worker", "Background Worker", "Ejecuta entrega y reintentos")
    Component(provider_adapter, "Email Provider Adapter", "Infrastructure", "Encapsula proveedor externo de correo")
    Component(notification_repository, "Notification Repository", "Repository", "Persistencia de notificaciones e intentos")
    Component(template_repository, "Template Repository", "Repository", "Persistencia de plantillas")
    Component(event_publisher, "Integration Event Publisher", "Infrastructure", "Publica fallos definitivos y alertas")
}

ContainerDb(notifications_db, "Notifications Database", "PostgreSQL", "Notificaciones, plantillas, delivery attempts")
ContainerDb(msg_bus, "Message Broker", "RabbitMQ", "Bus de integracion")
System_Ext(email_provider, "Proveedor Email", "AWS SES / SendGrid")

Rel(notification_api, notification_service, "Invoca operaciones")
Rel(event_consumer, notification_service, "Solicita procesamiento de eventos")
Rel(notification_service, template_service, "Resuelve plantilla requerida")
Rel(notification_service, rendering_engine, "Renderiza contenido")
Rel(notification_service, notification_repository, "Crea y actualiza notificaciones")
Rel(template_service, template_repository, "Lee y escribe plantillas")
Rel(delivery_worker, provider_adapter, "Entrega mensajes")
Rel(delivery_worker, notification_repository, "Actualiza estado e intentos")
Rel(provider_adapter, email_provider, "Llama API externa", "HTTPS")
Rel(msg_bus, event_consumer, "Entrega UserActivationRequiredIntegrationEvent", "AMQP")
Rel(notification_service, delivery_worker, "Encola entrega")
Rel(delivery_worker, event_publisher, "Notifica fallo definitivo")
Rel(event_publisher, msg_bus, "Publica EmailDeliveryFailedIntegrationEvent", "AMQP")
Rel(notification_repository, notifications_db, "Lee y escribe", "SQL/TCP")
Rel(template_repository, notifications_db, "Lee y escribe", "SQL/TCP")
@enduml
```

## Componentes destacados

- **Notification Application Service:** coordina el flujo funcional sin mezclar detalles de proveedor.
- **Rendering Engine:** mantiene separada la composicion del contenido respecto al envio.
- **Delivery Worker:** concentra resiliencia, reintentos y actualizacion de estados.
- **Email Provider Adapter:** permite cambiar proveedor sin afectar a modulos emisores.

[back](../../../1-standards/diagram-conventions.md)
