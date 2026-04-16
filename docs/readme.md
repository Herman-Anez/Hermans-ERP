# ERP Core Documentation

Documentacion base del ERP Core, organizada para acompanar el diseno, la implementacion y el mantenimiento del sistema.

Este repositorio sigue un enfoque de **Domain-Driven Design (DDD)** y **Clean Architecture**, con enfasis en:

- Separacion clara entre dominio, aplicacion, infraestructura e integracion.
- Modulos desacoplados mediante bounded contexts.
- Trazabilidad de decisiones tecnicas y de negocio.
- Documentacion pensada para onboarding, evolucion y alineacion del equipo.

## Proposito de esta carpeta

La carpeta `docs/` concentra la documentacion funcional y tecnica necesaria para entender:

- Que problema resuelve el sistema.
- Como esta organizada su arquitectura.
- Que responsabilidades tiene cada modulo.
- Que decisiones de arquitectura ya fueron tomadas.
- Como mantener una documentacion consistente a medida que el proyecto crece.

## Navegacion rapida

- [Definicion del proyecto](./project-definition.md)
- [Requisitos globales](./requirements-index.md)
- [Arquitectura](./2-architecture/arquitectura-y-patrones.md)
- [Procesos globales](./3-global-processes/readme.md)
- [Eventos de integracion](./5-events/readme.md)

## Mapa de lectura recomendado

Si es tu primera vez en el proyecto, este es el mejor orden para recorrer la documentacion:

1. [Definicion del proyecto](./project-definition.md)
2. [Indice de requisitos globales](./requirements-index.md)
3. [Arquitectura y patrones](./2-architecture/arquitectura-y-patrones.md)
4. [Atributos de calidad](./2-architecture/quality-attributes.md)
5. [Infraestructura](./2-architecture/infrastructure.md)
6. [ADR Index](./2-architecture/adrs/index.md)
7. Modulos de negocio en `docs/4-modules/`

## Estructura de la documentacion

```text
docs/
|-- 1-standards/         # Estandares de trabajo y documentacion
|-- 2-architecture/      # Vision arquitectonica, patrones, infraestructura y ADRs
|-- 3-global-processes/  # Procesos transversales que involucran varios modulos
|-- 4-modules/           # Documentacion por bounded context / modulo
|-- 5-events/            # Eventos de integracion entre modulos
|-- readme.md            # Portal de entrada de la documentacion
`-- requirements-index.md
```

## Modulos actuales

### Modulos principales

| Modulo | Responsabilidad principal | Estado |
|---|---|---|
| [Identity & Access (IAM)](./4-modules/identity-and-access/readme.md) | Autenticacion, autorizacion, sesiones y gestion de identidades | En documentacion |
| [Human Resources (HR)](./4-modules/human-resources/readme.md) | Ciclo de vida del empleado, contratos y estructura organizacional | En documentacion |
| [Notifications](./4-modules/notifications/readme.md) | Envio confiable de comunicaciones salientes y procesamiento de eventos | En documentacion |

### Modulos de soporte considerados en la arquitectura

Estos componentes apoyan la orquestacion y la integracion del ecosistema, aunque no todos tienen aun documentacion detallada propia:

| Componente | Rol dentro de la solucion |
|---|---|
| API Gateway / BFF | Punto de entrada para clientes, composicion de respuestas y enforcement de politicas |
| Service Broker | Canaliza integraciones, eventos y comunicacion desacoplada entre modulos |

## Documentos clave

### Estandares

- [Guia de documentacion](./1-standards/documentation-guidelines.md)
- [Convenciones de codigo](./1-standards/coding-conventions.md)
- [Git workflow](./1-standards/git-workflow.md)

### Arquitectura

- [Definicion del proyecto](./project-definition.md)
- [Arquitectura y patrones](./2-architecture/arquitectura-y-patrones.md)
- [Atributos de calidad](./2-architecture/quality-attributes.md)
- [Infraestructura](./2-architecture/infrastructure.md)
- [C4 Model](./2-architecture/c4-model/Diagrama-de-Contenedores.md)

### Procesos y eventos

- [Procesos globales](./3-global-processes/readme.md)
- [Eventos de integracion](./5-events/readme.md)

### Requisitos por modulo

- [Identity & Access requirements](./4-modules/identity-and-access/requirements.md)
- [Human Resources requirements](./4-modules/human-resources/requirements.md)
- [Notifications requirements](./4-modules/notifications/requirements.md)

## Convenciones editoriales de esta documentacion

- Espanol para contexto funcional, decisiones y explicaciones de negocio.
- Ingles para nombres tecnicos, artefactos de codigo, patrones y terminos de implementacion.
- Un documento por tema.
- Enlaces relativos siempre que sea posible.
- Cada modulo debe tener un `readme.md` como punto de entrada.

## Estado actual

La base documental ya define la direccion del sistema, pero aun esta en crecimiento. Hay modulos con mejor nivel de detalle que otros y algunos componentes de soporte todavia necesitan documentacion dedicada.

La expectativa es que cada cambio relevante en codigo, arquitectura o integracion venga acompanado de su actualizacion documental correspondiente.

[back](../README.md)
