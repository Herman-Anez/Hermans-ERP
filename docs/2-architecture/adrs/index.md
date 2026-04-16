# Architecture Decision Records

Registro de decisiones arquitectónicas del proyecto ERP Core. Cada ADR documenta una decisión significativa, su contexto, alternativas consideradas y consecuencias.

## ADRs vigentes

| Fecha | Decisión | Estado |
|---|---|---|
| 2026-04-03 | [Use Markdown Architectural Decision Records](./20260404-use-markdown-architectural-decision-records.md) | Accepted |
| 2026-04-03 | [Use Log4brains to manage the ADRs](./20260404-use-log4brains-to-manage-the-adrs.md) | Accepted |
| 2026-04-03 | [Uso de PlantUML para Diagramas de Arquitectura](./20260404-uso-de-platuml-para-graficos.md) | Accepted |
| 2026-04-04 | [Data Sovereignty: Database per Bounded Context](./20260404-data-sovereignty-database-per-bounded-context.md) | Accepted |
| 2026-04-09 | [Separación de Identidad (User) y Dominio Laboral (Employee)](./20260404-separacion-de-identidad-y-empleado.md) | Accepted |
| 2026-04-05 | [Delegación del envío de correos a Notifications](./20260405-delegacion-del-envio-de-correos-a-un-modulo-dedicado-notifications.md) | Accepted |

## Convenciones

- Los ADRs siguen el formato [MADR 2.1.2](https://adr.github.io/madr/) con el parche de Log4brains.
- Nomenclatura de archivo: `YYYYMMDD-descripcion-corta.md`
- Un ADR es inmutable: solo su estado puede cambiar (deprecado o superado).
- La plantilla para nuevos ADRs está en [template.md](./template.md).

## Más información

- [Log4brains documentation](https://github.com/thomvaill/log4brains/tree/develop#readme)
- [What is an ADR and why should you use them](https://github.com/thomvaill/log4brains/tree/develop#-what-is-an-adr-and-why-should-you-use-them)
- [ADR GitHub organization](https://adr.github.io/)

[back](../arquitectura-y-patrones.md)
