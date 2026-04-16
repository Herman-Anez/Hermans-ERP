# Quality Attributes - Human Resources

## Objetivo

Documentar los atributos de calidad fundamentales para el diseno y operacion del modulo `Human Resources`.

## Atributos principales

### Integridad de Datos

- La historia laboral y contractual debe ser inmutable y protegida contra manipulaciones accidentales.
- Las invariantes de negocio (ej. no solapamiento de contratos) deben validarse de forma estricta en el nucleo del dominio.

### Privacidad y Confidencialidad

- Acceso restringido a datos sensibles (Salarios, TaxId) segun el rol del usuario (RBAC).
- Cumplimiento con regulaciones locales de proteccion de datos (GDPR u equivalentes locales).

### Consistencia

- HR actua como el motor de identidad laboral; sus eventos deben ser entregados de forma confiable a IAM y otros modulos.
- El uso del patron Outbox asegura que la persistencia y la publicacion de eventos ocurran como una unidad atomica.

### Disponibilidad

- Las operaciones de lectura (directorio de empleados) deben ser altamente disponibles.
- Las operaciones de escritura, aunque menos frecuentes, son criticas para procesos de negocio como el alta de personal.

### Escalabilidad

- La consulta de perfiles y el organigrama debe estar optimizada mediante cache o modelos de lectura (CQRS) para soportar el crecimiento de la organizacion.

[back](../readme.md)
