# Arquitectura HR - C4 Componentes

## Diagrama de Componentes (Nivel 3)

Esta vista detalla los componentes internos del modulo `Human Resources` para la gestion de empleados, contratos y estructura organizacional.

```plantuml
@startuml C4_Elements
!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml

title Componentes - Human Resources

Container_Boundary(hr, "Human Resources") {
    Component(employee_api, "Employee API", "REST", "Expone el directorio y gestion de perfiles")
    Component(contract_api, "Contract API", "REST", "Gestion de contratos y compensaciones")
    Component(org_api, "Organization API", "REST", "Gestion de organigrama y departamentos")
    
    Component(hiring_service, "Hiring Service", "Application Service", "Coordina el onboarding de nuevos empleados")
    Component(termination_service, "Termination Service", "Application Service", "Gestiona el proceso de offboarding")
    Component(org_management_service, "Org Management Service", "Application Service", "Gestiona cambios en la estructura")
    
    Component(employee_repository, "Employee Repository", "Repository", "Persistencia de empleados y su historia")
    Component(contract_repository, "Contract Repository", "Repository", "Persistencia de contratos y anexos")
    Component(org_repository, "Organization Repository", "Repository", "Persistencia de departamentos y jerarquias")
    
    Component(event_publisher, "Integration Event Publisher", "Infrastructure", "Publica eventos de HR al broker")
}

ContainerDb(hr_db, "HR Database", "PostgreSQL", "Empleados, contratos, organigrama, historial")
ContainerDb(msg_bus, "Message Broker", "RabbitMQ", "Bus de integracion")

Rel(employee_api, hiring_service, "Inicia contratacion")
Rel(employee_api, employee_repository, "Consulta perfiles")
Rel(contract_api, contract_repository, "Administra contratos")
Rel(org_api, org_repository, "Administra estructura")

Rel(hiring_service, employee_repository, "Crea empleado")
Rel(hiring_service, event_publisher, "Notifica EmployeeHired")

Rel(termination_service, employee_repository, "Marca terminacion")
Rel(termination_service, event_publisher, "Notifica EmployeeTerminated")

Rel(org_management_service, org_repository, "Actualiza jerarquias")
Rel(org_management_service, event_publisher, "Notifica OrganizationModified")

Rel(employee_repository, hr_db, "Lee y escribe", "SQL/TCP")
Rel(contract_repository, hr_db, "Lee y escribe", "SQL/TCP")
Rel(org_repository, hr_db, "Lee y escribe", "SQL/TCP")

Rel(event_publisher, msg_bus, "Publica eventos laborales", "AMQP")

@enduml
```

## Componentes destacados

- **Employee API:** Punto de entrada para la consulta y navegacion del talento humano.
- **Hiring Service:** Encapsula la coreografia de alta, asegurando que el contrato y el empleado nazcan sincronizados.
- **Organization Repository:** Gestiona la complejidad de jerarquias y pertenencias departamentales.

[back](../../../1-standards/diagram-conventions.md)
