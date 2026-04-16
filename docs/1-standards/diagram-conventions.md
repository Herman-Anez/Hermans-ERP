# Convenciones de Diagramas - ERP Core

**Version:** 1.0.0  
**Estado:** Vigente  
**Herramienta Estándar:** PlantUML

## Objetivo

Garantizar que todos los diagramas técnicos del proyecto tengan una estética unificada, sean fáciles de mantener (diagrama como código) y utilicen la simbología correcta para su propósito.

## Estándares por Tipo de Diagrama

### 1. Modelo C4 - Diagrama de Componentes (Nivel 3)

* **Propósito:** Mostrar la estructura interna de un Bounded Context (API, Aplicación, Dominio, Infraestructura).
* **Librería:** `C4-PlantUML`.
* **Include Obligatorio:** `!include https://raw.githubusercontent.com/plantuml-stdlib/C4-PlantUML/master/C4_Component.puml`.
* **Ubicación:** `docs/4-modules/[nombre]/architecture/c4-component-diagram.md`.

### 2. Diagramas de Secuencia

* **Propósito:** Detallar la interacción entre componentes o procesos complejos (Sagas).
* **Sintaxis:** Standard PlantUML Sequence.
* **Ubicación:** Dentro de la carpeta `use-cases/` (si es específico) o `3-global-processes/` (si es transversal).

### 3. Modelo de Dominio (Diagrama de Clases)

* **Propósito:** Visualizar relaciones entre Aggregates, Entities y Value Objects.
* **Ubicación:** Dentro de `docs/4-modules/[nombre]/domain-model.md`.

## Reglas de Estilo

1. **Nombres:** Usar **PascalCase** para nombres de componentes y entidades.
2. **Relaciones:** Todas las flechas de interacción deben tener una etiqueta descriptiva (ej: "Persiste datos", "Emite evento").
3. **Encabezados:** Cada diagrama debe incluir un título descriptivo (`title`).
4. **Uso de Colores:** Seguir la paleta por defecto de C4-PlantUML para evitar distracciones visuales.

## Mantenimiento

* Los diagramas deben vivir **dentro de bloques de código Markdown** con el identificador `plantuml`.
* No subir imágenes estáticas (PNG/SVG) al repositorio; el sistema debe renderizarlas a partir del código.

## Plantillas Relacionadas

* [Template: C4 Component](./templates/c4-component-diagram.md)
* [Template: Sequence Diagram](./templates/sequence-diagram.md)
* [Template: Domain Model Diagram](./templates/domain-model-diagram.md)

[back](./documentation-guidelines.md)
