# Requisitos: Notifications

## Objetivo del modulo

Centralizar el envio de comunicaciones salientes y mantener trazabilidad completa del resultado de cada entrega.

## Requisitos funcionales

- **RF-NOT-01:** Enviar correos transaccionales a partir de eventos o comandos internos.
- **RF-NOT-02:** Gestionar plantillas reutilizables por tipo de comunicacion.
- **RF-NOT-03:** Registrar cada intento de envio con su estado final.
- **RF-NOT-04:** Reintentar fallos transitorios segun politica configurada.
- **RF-NOT-05:** Publicar eventos de fallo definitivo cuando una entrega critica no pueda completarse.
- **RF-NOT-06:** Garantizar idempotencia ante reprocesamiento del mismo mensaje de origen.
- **RF-NOT-07:** Soportar multiples canales de entrega aunque la primera etapa se concentre en email.
- **RF-NOT-08:** Permitir consultar el estado de una notificacion por su identificador tecnico.
- **RF-NOT-09:** Permitir administrar versiones activas de plantillas sin perder trazabilidad historica.

## Reglas de negocio clave

- Toda notificacion debe estar asociada a un canal y a una plantilla valida.
- Un mismo evento de origen no debe provocar envios duplicados.
- La falla de un proveedor no debe romper el flujo de negocio del modulo emisor.
- El historial de entrega debe ser auditable y consultable.
- Una notificacion aceptada para procesamiento no implica entrega exitosa inmediata.
- Las plantillas deben poder evolucionar sin alterar el contenido historico ya emitido.

## Requisitos no funcionales

- **RNF-NOT-R1 (Resiliencia):** El modulo debe soportar reintentos y fallos transitorios de proveedores externos.
- **RNF-NOT-A1 (Auditabilidad):** Debe conservarse el estado de cada envio y sus errores asociados.
- **RNF-NOT-S1 (Seguridad):** Las plantillas y payloads no deben exponer secretos ni datos innecesarios.
- **RNF-NOT-E1 (Escalabilidad):** El procesamiento debe desacoplarse del flujo principal de negocio.
- **RNF-NOT-M1 (Mantenibilidad):** Los proveedores deben abstraerse mediante adaptadores intercambiables.
- **RNF-NOT-P1 (Rendimiento):** La aceptacion de solicitudes internas no debe depender del tiempo de respuesta del proveedor externo.
- **RNF-NOT-O1 (Operabilidad):** Deben existir datos suficientes para soporte, reprocesamiento y analisis de fallos.

## Integraciones esperadas

- **Consume:** `UserActivationRequiredIntegrationEvent`, eventos de HR, IAM u otros modulos que requieran comunicacion saliente.
- **Publica:** `EmailDeliveryFailedIntegrationEvent` y eventos equivalentes segun canal.

## Escenarios principales

- Envio de correo de activacion de cuenta
- Envio de notificaciones derivadas de onboarding
- Reintento controlado ante timeout o error transitorio del proveedor
- Registro y escalamiento cuando una entrega critica falla definitivamente

[back](./readme.md)
