# Convenciones de Código y Estructura

**Versión:** 1.0.0

Este documento define las reglas de nomenclatura y estructura para mantener consistencia en todo el código base, alineándose con las prácticas de **Domain-Driven Design (DDD)** y **Clean Architecture**.

## Nomenclatura General

- **Directorios y archivos (excepto clases):** `kebab-case` (ej. `identity-and-access`, `user-created.event.ts`).
- **Clases e Interfaces:** `PascalCase` (ej. `UserRepository`, `CreateUserUseCase`).
- **Variables y Métodos:** `camelCase` (ej. `userId`, `activateAccount()`).
- **Constantes:** `UPPER_SNAKE_CASE` (ej. `MAX_RETRY_ATTEMPTS`).

## Domain-Driven Design (DDD) General

El código debe reflejar el **Lenguaje Ubicuo** definido en la documentación funcional.

### Entidades y Value Objects

- **Entities:** Nombres en singular, representando el concepto de negocio. No usar sufijos como `Entity` o `Model`.
  - ✅ `User` | ❌ `UserModel`
  - ✅ `Employee` | ❌ `EmployeeEntity`
- **Value Objects:** Suelen mantenerse inmutables. Nombres descriptivos sin sufijos.
  - ✅ `EmailAddress`
  - ✅ `Money`

### Interfaces y Contratos

- Evitar prefijar con `I` si no es estándar en el lenguaje de programación elegido, o utilizar la convención habitual del framework si es mandatorio. Preferir nombres basados en el rol que desempeñan.
- **Repositories:** Deben indicar la acción sobre el agregado.
  - ✅ `UserRepository` (interfaz), `PostgresUserRepository` (implementación).

## Capa de Aplicación (Use Cases)

- Los casos de uso manejan un solo flujo principal de la aplicación.
- Nomenclatura: `Verbo + Concepto + UseCase/CommandHandler`
  - ✅ `CreateUserUseCase` o `CreateUserCommandHandler`

## Capa de Infraestructura

- Encapsular librerías bajo interfaces propias si cambian la lógica de dominio.
- Ubicar ahí implementaciones de bases de datos, clientes de APIs de terceros (ej. envío de SMS).

## Eventos de Integración

- Nomenclatura: `[Sujeto][Verbo en Pasado]IntegrationEvent`
  - ✅ `UserCreatedIntegrationEvent`
  - ✅ `EmployeeHiredIntegrationEvent`

## Nomenclatura de APIs REST

Si el módulo expone endpoints, seguir convenciones HTTP estándar:

- **Rutas en plural:** `/users`, `/employees`
- **Acciones anidadas para sub-recursos (solo lectura/modificación simple):** `/users/{id}/roles`
- **Comandos explícitos de negocio (cuando CRUD no es suficiente):** `/users/{id}/activate` (Método POST preferible frente a un PATCH complejo).
