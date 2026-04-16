# Plantilla: Diagrama de Modelo de Dominio

## Modelo Táctico

```plantuml
@startuml
' Configuración de estilo
skinparam class {
    BackgroundColor White
    ArrowColor Black
    BorderColor Black
}

title Modelo de Dominio - Nombre del Módulo

package "Aggregates" {
    class AggregateName << (A,#FF7700) Aggregate Root >> {
        - Id
        - Status
        + BusinessMethod()
    }
}

package "Entities" {
    class EntityName {
        - Id
        - Attributes
    }
}

package "Value Objects" {
    class ValueObjectName << (V,#AAAAAA) Value Object >> {
        - Attribute
    }
}

' Relaciones
AggregateName "1" *-- "n" EntityName : contains
AggregateName "1" *-- "1" ValueObjectName : has
@enduml
```

## Explicación del Modelo

Detallar las invariantes principales y las relaciones clave entre los objetos del dominio.

[back](../diagram-conventions.md)
