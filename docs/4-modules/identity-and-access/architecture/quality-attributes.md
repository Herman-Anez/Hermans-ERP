# Quality Attributes - Identity & Access

## Objetivo

Documentar los atributos de calidad mas relevantes para el diseno del modulo `Identity & Access`.

## Atributos principales

### Seguridad

- Las credenciales deben protegerse mediante hashing fuerte y politicas de password.
- Los tokens deben emitirse con expiracion, rotacion y capacidad de revocacion.
- El modulo debe soportar bloqueo de cuentas y controles contra abuso.

### Disponibilidad

- Si IAM no esta disponible, gran parte del ERP queda inutilizable.
- Las capacidades de login, refresh y validacion deben priorizar alta disponibilidad.

### Rendimiento

- La validacion de identidad y permisos debe ser rapida para no penalizar la experiencia global.
- La emision y renovacion de tokens no debe introducir latencia innecesaria.

### Auditabilidad

- Deben quedar registradas acciones sensibles como login, bloqueo, revocacion y aprovisionamiento.
- Los eventos de seguridad deben poder correlacionarse con procesos y usuarios.

### Mantenibilidad

- La logica de autenticacion, autorizacion y aprovisionamiento debe permanecer separada.
- La politica RBAC no debe quedar dispersa entre controladores, middleware y adaptadores.

[back](../readme.md)
