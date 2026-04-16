# Uso de PlantUML para Diagramas de Arquitectura (C4 Model)

- Status: accepted
- Date: 2026-04-03
- Tags: dev-tools, doc, architecture

## Context and Problem Statement

Necesitamos una herramienta estándar para documentar la arquitectura de nuestro ERP (C4 Model, diagramas de secuencia, etc.) de manera colaborativa. ¿Qué herramienta de modelado debemos utilizar para asegurar mantenibilidad y versionado?

## Decision Drivers

- **Trazabilidad:** Los diagramas deben poder versionarse junto con el código fuente en Git (Diagrams as Code).
- **Mantenibilidad:** Evitar herramientas gráficas (Drag & Drop) donde un cambio menor desajusta todo el lienzo visual.
- **Automatización:** Debe integrarse fácilmente en pipelines de CI/CD y en herramientas de documentación estática (como Log4brains).

## Considered Options

- [PlantUML](https://plantuml.com/): Herramienta basada en texto plano para generar diagramas UML y C4.
- Draw.io / Lucidchart: Herramientas de diseño visual tradicionales.
- Structurizr: Especializada en C4 Model, pero requiere infraestructura adicional.

## Decision Outcome

Chosen option: "PlantUML".

Justificación técnica:

1. Cumple al 100% con la filosofía de **Diagramas como Código**, lo que facilita las revisiones en las Pull Requests.
2. Tiene soporte nativo para la librería estándar de C4 (`C4-PlantUML`).
3. Puede usarse en línea o hostearse el servidor de renderizado internamente.
4. Se integra perfectamente con nuestro flujo actual de Markdown y permite su renderizado en nuestra base de conocimiento.
