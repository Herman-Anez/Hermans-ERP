# Human Resources (HR)

**Bounded Context:** Gestión del Talento Humano  
**Responsabilidad Principal:** Administrar el ciclo de vida laboral completo del empleado y la estructura organizacional.  
**Versión:** 1.0.0

## 🎯 Objetivo

Gestionar la relación laboral desde la contratación hasta el cese, manteniendo la integridad de los datos contractuales y la jerarquía de la organización.

## 📋 Entidades Principales

- **`Employee`** — Agregado raíz que representa al trabajador.
- **`Contract`** — Entidad que define las condiciones legales y económicas.
- **`Department`** — Unidad organizativa que agrupa empleados.
- **`Position`** — Puesto de trabajo o cargo funcional.

## 🔄 Responsabilidades Clave

- Alta de empleados (Onboarding).
- Gestión de movimientos organizacionales (puestos/responsables).
- Seguimiento de historial de contratos.
- Procedimientos de baja laboral (Offboarding).
- Estructura organizacional y jerarquía.

## 🏗️ Arquitectura y Atributos

- **Integridad:** Invariantes de negocio protegidas en el núcleo del dominio (Hexagonal Architecture).
- **Auditabilidad:** Trazabilidad completa de movimientos contractuales y jerárquicos.
- **Resiliencia:** Uso de patrón Outbox para la publicación garantizada de eventos de Onboarding.
- **Seguridad:** Control estricto de acceso (RBAC) sobre datos salariales y PII.

## 🔗 Integración con Otros Contextos

- **Se suscribe a:** Ninguno (Contexto Maestro de datos laborales).
- **Publica:** `EmployeeHiredIntegrationEvent`, `EmployeeTerminatedIntegrationEvent`, `EmployeeOrganizationModifiedIntegrationEvent`.
- **Relación directa con:** IAM (para aprovisionamiento) y Notifications (para comunicaciones).

## 📍 Enlaces Importantes

- [Domain Model](./domain-model.md)
- [Requirements](./requirements.md)
- [API Specification](./api-spec.md)
- [Use Cases](./use-cases/index.md)
- [C4 Component Diagram](./architecture/c4-component-diagram.md)
- [Architecture Details](./architecture/infrastructure.md)
- [Quality Attributes](./architecture/quality-attributes.md)

[back](../../readme.md)