# Infrastructure - Identity & Access

## Objetivo

Describir la infraestructura minima necesaria para operar el modulo `Identity & Access` en la primera etapa del ERP.

## Componentes esperados

- API REST para autenticacion y administracion de usuarios
- Persistencia para usuarios, roles, permisos y refresh tokens
- Worker o consumidor de eventos para aprovisionamiento desde HR
- Emision y validacion de tokens
- Publicacion de eventos de integracion hacia otros modulos

## Flujo tecnico general

1. El cliente accede a IAM a traves del API Gateway.
2. IAM valida credenciales, estado del usuario y permisos aplicables.
3. El modulo emite o renueva tokens segun corresponda.
4. Cuando HR publica un evento de alta, IAM lo consume y aprovisiona la identidad.
5. Si la cuenta necesita activacion, IAM publica un evento para `Notifications`.

## Consideraciones operativas

- Los refresh tokens deben almacenarse de forma revocable.
- La persistencia de IAM debe mantenerse aislada de HR.
- Los eventos de aprovisionamiento deben procesarse con idempotencia.
- Las acciones sensibles deben producir trazas auditables.

[back](../readme.md)
