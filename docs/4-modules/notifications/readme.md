# Notifications

**Bounded Context:** Gestión de Comunicaciones Salientes  
**Responsabilidad Principal:** Enviar y rastrear notificaciones de forma confiable, desacoplada y reusable.  
**Versión:** 1.0.0

## 🎯 Objetivo

Proporcionar un servicio centralizado de comunicaciones salientes para que otros modulos del ERP puedan solicitar entregas sin acoplarse a proveedores, plantillas o politicas de reintento.

## 📋 Entidades Principales

- **`Notification`** — Registro de cada intento de envío.
- **`Template`** — Plantilla reusable con variables.
- **`Channel`** — Canal de entrega (EMAIL, SMS, PUSH).
- **`Provider`** — Servicio externo (SendGrid, AWS SES, etc.).
- **`DeliveryAttempt`** — Intento individual de entrega con resultado trazable.

## 🔄 Responsabilidades Clave

- Procesar Integration Events y solicitudes internas de envio
- Resolver plantillas y variables de contenido
- Aplicar politicas de retry, resiliencia y control de duplicados
- Registrar trazabilidad de entrega por notificacion e intento
- Aislar al resto del sistema de proveedores externos

## 🔗 Integración con Otros Contextos

- **Se suscribe a:** `UserActivationRequiredIntegrationEvent`, `UserAccountLockedIntegrationEvent`, `EmployeeTerminatedIntegrationEvent`.
- **Publica:** `EmailDeliveryFailedIntegrationEvent` (cuando una entrega crítica falla definitivamente).
- **Relación directa con:** **Identity & Access** (vía API interna para resolver secretos de activación antes del envío).

## 📍 Enlaces Importantes

- [Domain Model](./domain-model.md)
- [Requirements](./requirements.md)
- [API Specification](./api-spec.md)
- [Use Cases](./use-cases/index.md)
- [Quality Attributes](./architecture/quality-attributes.md)
- [Infrastructure](./architecture/infrastructure.md)

[back](../../readme.md)
