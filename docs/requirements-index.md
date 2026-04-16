# Requisitos del Sistema ERP Core

Este documento resume los requisitos globales del sistema y sirve como punto de partida para los requisitos especificos de cada modulo.

## Documentos relacionados

- [Definicion del proyecto](./project-definition.md)
- [Arquitectura y patrones](./2-architecture/arquitectura-y-patrones.md)
- [Procesos globales](./3-global-processes/readme.md)
- [Eventos de integracion](./5-events/readme.md)

## Objetivos de negocio

- Centralizar la gestion de identidad para evitar silos de datos y accesos inconsistentes.
- Automatizar el ciclo de vida del empleado desde su onboarding hasta su salida de la organizacion.
- Mantener trazabilidad, seguridad y evolucion controlada del ecosistema ERP.

## Alcance funcional global

### Identity & Access

- Autenticacion de usuarios
- Autorizacion basada en roles y permisos
- Gestion de sesiones y refresh tokens
- Gobierno de identidad tecnica

### Human Resources

- Alta, actualizacion y baja de empleados
- Gestion contractual y expediente laboral
- Estructura organizacional
- Procesos de onboarding y offboarding

### Integracion y soporte

- Publicacion y consumo de eventos entre modulos
- Notificaciones salientes derivadas de procesos de negocio
- Coordinacion de procesos transversales

## Requisitos no funcionales globales

### Seguridad

- **RNF-S1:** Toda comunicacion externa debe utilizar HTTPS.
- **RNF-S2:** Los refresh tokens deben persistirse con rotacion y controles de revocacion.
- **RNF-S3:** Las acciones sensibles deben ser auditables.

### Mantenibilidad

- **RNF-M1:** El dominio no debe depender de frameworks externos.
- **RNF-M2:** La estructura del sistema debe favorecer bounded contexts explicitamente desacoplados.
- **RNF-M3:** La documentacion tecnica y funcional debe mantenerse alineada con el codigo.

### Rendimiento

- **RNF-R1:** Las validaciones de autenticacion y autorizacion no deben introducir latencia perceptible para el usuario final.
- **RNF-R2:** Las consultas de lectura intensiva deben poder optimizarse sin comprometer las invariantes del dominio.

### Escalabilidad

- **RNF-E1:** Los modulos deben poder evolucionar de manera independiente.
- **RNF-E2:** Las integraciones asincronas deben tolerar reintentos y fallos transitorios.

## Trazabilidad hacia modulos

Los requisitos detallados deben vivir en los documentos propios de cada bounded context:

- [Identity & Access requirements](./4-modules/identity-and-access/requirements.md)
- [Human Resources requirements](./4-modules/human-resources/requirements.md)
- [Notifications requirements](./4-modules/notifications/requirements.md)

## Nota sobre plantillas

Si necesitas crear requisitos para un nuevo modulo, usa este archivo como referencia de alcance global y crea el detalle en `docs/4-modules/[modulo]/requirements.md`.

Evita incluir placeholders o plantillas incompletas en documentos indice, ya que reducen claridad y dificultan la trazabilidad.

[back](./readme.md)
