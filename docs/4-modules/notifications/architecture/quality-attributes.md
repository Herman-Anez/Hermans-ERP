# Quality Attributes - Notifications

## Objetivo

Documentar los atributos de calidad que mas impactan el diseno del modulo Notifications.

## Atributos principales

### Resiliencia

- El modulo debe tolerar fallos transitorios de proveedores externos.
- La aceptacion de una solicitud no debe depender de la entrega inmediata.
- Deben existir reintentos controlados y deteccion de fallos definitivos.

### Auditabilidad

- Cada notificacion debe conservar trazabilidad de origen, intentos y resultado.
- Debe poder identificarse que proceso origino una comunicacion mediante `correlationId`.

### Escalabilidad

- El procesamiento debe poder ejecutarse de forma asincrona y desacoplada.
- La carga de envio no debe bloquear al modulo emisor.

### Mantenibilidad

- Los proveedores deben integrarse mediante adaptadores intercambiables.
- Las plantillas y reglas operativas no deben quedar acopladas a un proveedor concreto.

### Seguridad

- El payload procesado debe minimizar datos sensibles.
- No deben persistirse secretos del proveedor dentro del dominio del modulo.

[back](../readme.md)
