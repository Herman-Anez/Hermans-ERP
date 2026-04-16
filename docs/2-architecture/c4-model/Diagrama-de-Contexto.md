# Arquitectura - C4 Model (Context)

## Diagrama de Contexto (Nivel 1)

Este diagrama muestra la vista mas alta del sistema: quienes interactuan con `ERP Core` y con que sistemas externos se integra en la primera etapa.

```plantuml
@startuml C4_Elements
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Context.puml

title Diagrama de Contexto - ERP Core

Person(employee, "Empleado", "Activa su cuenta y utiliza el ERP segun sus permisos")
Person(hr_manager, "Gestor HR", "Administra empleados, contratos y estructura organizacional")
Person(sys_admin, "Administrador del Sistema", "Gestiona usuarios, roles, permisos y politicas")

System(erp_core, "ERP Core", "Plataforma modular que centraliza HR, Identity & Access y Notifications")

System_Ext(email_provider, "Proveedor Email", "Servicio externo de envio de correo transaccional")
System_Ext(payroll_system, "Sistema de Nomina (Futuro)", "Integracion futura para procesos de liquidacion")

Rel(employee, erp_core, "Inicia sesion, activa cuenta y consume funcionalidades", "HTTPS")
Rel(hr_manager, erp_core, "Gestiona ciclo de vida laboral", "HTTPS")
Rel(sys_admin, erp_core, "Administra accesos y configuracion", "HTTPS")

Rel(erp_core, email_provider, "Delegacion de entrega de emails", "HTTPS")
Rel(erp_core, payroll_system, "Publicacion futura de datos laborales consolidados", "Async API / Events")

@enduml
```

## Resumen de relaciones

- `ERP Core` es el punto central de interaccion para actores internos.
- Los usuarios no interactuan directamente con proveedores de mensajeria ni con otros sistemas externos.
- El proveedor de email es una dependencia de infraestructura aislada tras el modulo `Notifications`.
- Se ha previsto una vía de integración asíncrona hacia futuros sistemas como *Nómina* que simplemente escucharán desde afuera la verdad consolidada en HR/ERP Core.
- La integracion con nomina existe como direccion futura, no como alcance de la primera etapa.

## Documentos relacionados

- [Diagrama de Contenedores](./Diagrama-de-Contenedores.md)
- [Arquitectura y patrones](../arquitectura-y-patrones.md)
- [Definicion del proyecto](../../project-definition.md)

[back](../arquitectura-y-patrones.md)
