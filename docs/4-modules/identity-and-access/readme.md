# Identity & Access (IAM)

**Bounded Context:** Gestión de Identidad y Acceso  
**Responsabilidad Principal:** Proteger el ecosistema del ERP garantizando autenticación y autorización segura.  
**Versión:** 1.0.0

## 🎯 Objetivo

Controlar quién puede acceder al sistema y qué acciones puede realizar, manteniendo alta seguridad y trazabilidad de las identidades técnicas.

## 📋 Entidades Principales

- **`User`** — Identidad técnica del usuario (independiente del empleado).
- **`Role`** — Agrupación lógica de permisos para RBAC.
- **`Permission`** — Claims específicos para recursos protegidos.

## 🔄 Responsabilidades Clave

- Autenticación segura (Login/Logout/Refresh).
- Aprovisionamiento de identidades desde eventos de HR.
- Gestión granular de roles y permisos.
- Auditoría de seguridad y bloqueo de cuentas.

## 🏗️ Arquitectura y Atributos

- **Seguridad:** Uso de hashing Argon2id y rotación estricta de Refresh Tokens.
- **Disponibilidad:** Alta disponibilidad para servicios de validación de tokens.
- **Auditabilidad:** Registro inmutable de eventos de acceso y cambios de permisos.
- **Rendimiento:** Validación de identidad descentralizada mediante JWT.

## 🔗 Integración con Otros Contextos

- **Se suscribe a:** `EmployeeHiredIntegrationEvent`, `EmployeeTerminatedIntegrationEvent` (para revocación de acceso) y eventos organizacionales como `EmployeeOrganizationModifiedIntegrationEvent`.
- **Decoupling Strategy:** Los eventos de HR se procesan mediante un **Integration Adapter (ACL)** que mapea atributos de negocio (departamentos, puestos) a roles técnicos de IAM, evitando la fuga de lógica organizacional hacia el núcleo de seguridad.
- **Publica:** `UserCreatedIntegrationEvent`, `UserActivationRequiredIntegrationEvent`, `UserAccountLockedIntegrationEvent`.

- **Relación directa con:** API Gateway, todos los servicios protegidos y **Notifications** (vía API interna para envío de secretos).

## 📍 Enlaces Importantes

- [Domain Model](./domain-model.md)
- [Requirements](./requirements.md)
- [API Specification](./api-spec.md)
- [Use Cases](./use-cases/index.md)
- [Architecture Details](./architecture/infrastructure.md)

[back](../../readme.md)