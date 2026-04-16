# Arquitectura y Patrones

Este proyecto adopta una arquitectura modular guiada por **Domain-Driven Design (DDD)** y **Clean Architecture** para separar con claridad las reglas de negocio de las preocupaciones tecnicas.

## Principios arquitectonicos

- **Bounded Contexts**: cada modulo define su propio lenguaje ubicuo, sus reglas y sus fronteras.
- **Separacion de capas**: dominio, aplicacion, infraestructura e interfaces deben tener responsabilidades claras.
- **Bajo acoplamiento**: los modulos colaboran a traves de contratos y eventos, no por dependencias internas.
- **Evolucion controlada**: las decisiones relevantes deben registrarse mediante ADRs.
- **Enfoque en dominio**: las reglas de negocio importantes no deben quedar escondidas en controladores, ORMs o detalles de framework.

## Patrones principales

### Domain-Driven Design

Se modela el negocio mediante:

- Entities
- Value Objects
- Aggregates
- Repositories
- Domain Services
- Domain Events

Cada bounded context debe proteger sus invariantes y evitar que otros modulos manipulen directamente su modelo interno.

### Clean Architecture

La estructura favorece dependencias hacia adentro:

- El dominio no conoce frameworks.
- La aplicacion orquesta casos de uso.
- La infraestructura implementa adaptadores y acceso a servicios externos.
- Las interfaces exponen APIs, mensajes o puntos de entrada.

## Integracion entre modulos

La colaboracion entre modulos se apoya principalmente en **eventos de integracion** y procesos desacoplados.

### Zero Trust y Backchannels
Aunque la arquitectura prioriza la asincronía, existen flujos donde un módulo técnico debe invocar por vía HTTP sincrónica a otro subsistema (Backchannels). Bajo ninguna circunstancia estas peticiones pueden considerarse seguras "solo por estar en la subred interna". Las conexiones Inter-Módulo exigen firmas de Token M2M generados dinámicamente por IAM (Oauth2 Client Credentials Flow), anulando así la técnica de Static API Keys.

### Choreographed Sagas

Todas las transacciones distribuidas o flujos de procesos transversales (como el Onboarding de un empleado) se modelan bajo el patrón **Saga (Coreografiada)**.

- **No existe un "Cerebro" Central:** En lugar de un Service Orchestrator, cada Bounded Context reacciona a los eventos publicados en el broker.
- **Compensaciones (Rollbacks) de Negocio:** Como regla general, si un módulo falla al procesar una regla de negocio (ej. falta de crédito), emite un evento de compensación que los módulos previos escuchan pasivamente para ejecutar un *Rollback* local, devolviendo sus agregados a un estado consistente o neutral.
- **Excepción para Fallos de Infraestructura (Caso Onboarding):** Para fallos estrictamente técnicos o de infraestructura (ej. el correo de IAM ya existe), se evita el rollback de negocio en HR para no desacoplar la realidad legal de la técnica. En su lugar, el módulo afectado emite un evento de fallo (ej. `UserAccountCreationFailedIntegrationEvent`) que el módulo de `Notifications` consume para alertar a Soporte Técnico (IT Admin). Esta excepción garantiza que un problema de TI no anule una contratación ya consolidada en el mundo real.

Esto permite:

- reducir dependencias directas
- aislar fallos
- escalar capacidades de forma independiente
- mantener autonomia por bounded context

Cuando una operacion atraviese varios modulos, debe modelarse explicitamente como proceso global, orquestacion o flujo basado en eventos, segun su naturaleza.

## Persistencia y autonomia

Como direccion arquitectonica general, cada bounded context debe evitar compartir su modelo persistente con otros contextos. La autonomia de datos reduce acoplamiento y hace mas clara la evolucion de cada modulo.

## Documentos relacionados

- [Quality attributes](./quality-attributes.md)
- [Infrastructure](./infrastructure.md)
- [ADR Index](./adrs/index.md)
- [C4 Model](./c4-model/Diagrama-de-Contenedores.md)
