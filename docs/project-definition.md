# Definicion del Proyecto ERP Core

Este documento consolida la definicion funcional y arquitectonica del proyecto antes de iniciar el desarrollo de codigo productivo.

## Objetivo del proyecto

Construir un ERP modular que permita gestionar de forma segura la identidad digital de los usuarios y el ciclo de vida laboral de los empleados, con integraciones desacopladas y trazables.

## Documentos relacionados

- [Portal de documentacion](./readme.md)
- [Requisitos globales](./requirements-index.md)
- [Arquitectura y patrones](./2-architecture/arquitectura-y-patrones.md)
- [Procesos globales](./3-global-processes/readme.md)
- [Eventos de integracion](./5-events/readme.md)

## Problema que resuelve

Muchas organizaciones terminan con informacion dispersa entre sistemas de RRHH, autenticacion y comunicaciones. Eso genera:

- duplicidad de datos
- errores de aprovisionamiento
- baja trazabilidad
- procesos manuales de onboarding y offboarding
- debilidad en controles de acceso

ERP Core busca resolver ese problema con una base coherente, extensible y orientada al dominio.

## Vision de producto

El sistema debe permitir que:

- HR administre la relacion laboral de cada empleado como fuente de verdad del negocio.
- IAM administre la identidad tecnica y el acceso al ERP sin mezclarlo con datos laborales.
- Notifications gestione comunicaciones salientes sin acoplarse a la logica interna de otros modulos.
- los procesos transversales se resuelvan mediante eventos y contratos claros.

## Actores principales

- **Gestor de Recursos Humanos:** registra, actualiza y finaliza la relacion laboral del empleado.
- **Administrador del sistema:** gestiona usuarios, roles, permisos y politicas de acceso.
- **Empleado:** activa su cuenta y utiliza el ERP segun sus permisos.
- **Procesos automaticos del sistema:** reaccionan a eventos, ejecutan reintentos y disparan integraciones.

## Alcance de la primera etapa

### Modulos incluidos

- **Human Resources**
- **Identity & Access**
- **Notifications**

### Capacidades incluidas

- alta de empleados
- vinculacion entre empleado e identidad digital
- aprovisionamiento de usuario por evento
- activacion de cuenta
- autenticacion con access token y refresh token
- autorizacion por roles y permisos
- envio de correos transaccionales
- trazabilidad por eventos de integracion

### Fuera de alcance por ahora

- nomina
- evaluacion de desempeno
- vacaciones complejas y calendario laboral avanzado
- firma electronica de documentos
- portal del empleado de autoservicio
- workflow engine generalista

## Bounded contexts definidos

### Human Resources

Es la fuente de verdad para:

- empleado
- contrato
- estructura organizacional
- alta y baja laboral

No debe encargarse de credenciales ni de permisos tecnicos.

### Identity & Access

Es la fuente de verdad para:

- usuario tecnico
- autenticacion
- autorizacion
- sesiones
- roles y permisos

No debe encargarse de datos laborales detallados.

### Notifications

Es la fuente de verdad para:

- plantillas de notificacion
- historial de envios
- estado de entrega
- reintentos y fallos de comunicacion

No debe tomar decisiones de negocio ajenas al acto de comunicar.

## Procesos transversales ya identificados

- Employee onboarding
- User provisioning desde HR hacia IAM
- Account activation via Notifications
- Error handling y compensacion ante fallos de aprovisionamiento o entrega

## Reglas globales del sistema

- Un empleado y un usuario no son el mismo concepto.
- Todo acceso al ERP debe pasar por IAM.
- Toda integracion entre modulos debe tener contratos explicitos.
- Los eventos deben ser idempotentes y trazables.
- Los datos sensibles no deben viajar en eventos salvo estricta necesidad.
- La documentacion debe preceder o acompanar cada cambio de arquitectura o comportamiento.

## Garantías de Calidad Documental (Rigid Base)

Para garantizar la integridad del sistema antes de la codificación, cada módulo cumple estrictamente con el estándar:

- **Estructura Standard:** `readme.md`, `requirements.md`, `domain-model.md`, `api-spec.md`, `use-cases/index.md`.
- **Contratos Claros:** Eventos publicados y consumidos identificados y catalogados.
- **Trazabilidad Total:** Decisiones arquitectónicas registradas como ADR y procesos transversales mapeados.

## Estado de Definición Final

**Estado:** Digital Base Hardened & Ready (100% Definido).

Con la finalización de los procesos de estandarización, endurecimiento y consolidación de casos de uso transversales, el **ERP Core** ha alcanzado el nivel de madurez necesario para iniciar la fase de codificación productiva. Toda la estructura de módulos (`IAM`, `HR`, `Notifications`), el catálogo de eventos de integración y la arquitectura de componentes están alineados y verificados.

## Fase de Implementación

El proyecto se considera listo para el primer *Sprint* de desarrollo, priorizando:

1. **Fundamentos de Infraestructura:** Implementación del Event Bus y bases de datos por contexto.
2. **Módulo IAM:** Núcleo de seguridad y autenticación.
3. **Módulo HR:** Flujos de contratación base.
4. **Integraciones:** Orquestación de Onboarding mediante eventos.

[back](./readme.md)
