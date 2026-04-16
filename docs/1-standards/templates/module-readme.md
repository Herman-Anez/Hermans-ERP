# [Nombre del Bounded Context]

**Bounded Context:** [Nombre Técnico]  
**Responsabilidad Principal:** [Una frase clara y concisa]  
**Versión:** 1.0.0

## 🎯 Objetivo

[Descripción de negocio en español - qué problema resuelve este contexto]

## 📋 Entidades Principales

- **`EntityName`** — Breve descripción y rol en el dominio.
- **`OtraEntidad`** — Breve descripción.

## 🔄 Responsabilidades Clave

- Responsabilidad 1
- Responsabilidad 2
- ...

## 🔗 Integración con Otros Contextos

- **Se suscribe a:** `EventoExterno1`, `EventoExterno2`
- **Publica:** `EventoPropio1`, `EventoPropio2`
- **Relación directa con:** IAM, HR, Notifications, etc.

## 🏗️ Arquitectura y Atributos

- **Integridad:** Invariantes de negocio protegidas en el núcleo del dominio (Hexagonal Architecture).
- **Auditabilidad:** Trazabilidad completa de movimientos contractuales y jerárquicos.
- **Resiliencia:** Uso de patrón Outbox para la publicación garantizada de eventos de Onboarding.
- **Seguridad:** Control estricto de acceso (RBAC) sobre datos salariales y PII.

## 📍 Enlaces Importantes

- [Domain Model](./domain-model.md)
- [Requirements](./requirements.md)
- [API Specification](./api-spec.md)
- [Use Cases](./use-cases/index.md)
- [C4 Component Diagram](./architecture/c4-component-diagram.md)
- [Integration Events](../../5-events/integration-events.md) (si aplica)
- [Architecture Details](./architecture/infrastructure.md)
- [Quality Attributes](./architecture/quality-attributes.md)
-

[back](../../readme.md)
