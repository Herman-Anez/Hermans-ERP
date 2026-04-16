# Infrastructure - Human Resources

## Objetivo

Describir la infraestructura minima necesaria para operar el modulo `Human Resources` y soportar su rol como emisor de eventos clave para el ERP.

## Componentes esperados

- API REST para gestion de empleados, contratos y estructura organizacional.
- Persistencia para informacion laboral sensible e historia contractual.
- Bus de eventos para publicar altas, bajas y modificaciones.
- Integracion con servicios de Almacenamiento (opcional, para documentos de contrato).

## Flujo tecnico general

1. El Gestor de HR interactua con el portal administrativo.
2. Las solicitudes de contratacion o cambio organizacional se procesan de forma atomica en el Agregado `Employee`.
3. Tras cada mutacion exitosa, se publica un evento de integracion (Outbox Pattern recomendado).
4. El sistema mantiene proyecciones actualizadas del organigrama para consultas rapidas.


## Consideraciones operativas

- **Seguridad de Datos:** La informacion de salarios y TaxId es altamente sensible y debe estar cifrada en reposo. Acorde al paradigma Zero Trust, el Load Balancer interno de HR debe descartar indiscriminadamente cualquier *request* falto de firma criptográfica válida M2M, suprimiendo confianzas originadas por la capa de red o el firewall.
- **Soberania de Datos:** La base de datos de HR es el origen de la verdad para la nomina y el organigrama.
- **Trazabilidad:** Cualquier cambio en contratos o salarios debe quedar registrado en tablas de auditoria inmutables.
- **Consistencia Eventual:** La sincronizacion con IAM y otros modulos se garantiza mediante el Message Broker.

[back](../readme.md)
