# Guia de Documentacion - ERP Core

**Version:** 1.1.0  
**Estado:** Listo para uso interno  
**Fecha:** 2026-04-12

## Objetivo

Definir un estandar practico para que la documentacion del proyecto sea:

- Consistente
- Facil de navegar
- Util durante el onboarding
- Alineada con el codigo y la arquitectura real
- Sostenible a medida que el sistema crece

## Principios generales

- **Idioma**:
  - **Inglés** para términos técnicos, nombres de clases, patrones y código.
  - **Español** para descripciones de negocio, requisitos y explicaciones orientadas al usuario final.
- **La documentacion es parte del producto**: no es un entregable secundario.
- **Primero el por que y el que**: despues el como.
- **Un documento, una responsabilidad**: evitar mezclar requisitos, decisiones y detalles de implementacion en el mismo archivo.
- **La fuente de verdad debe ser unica**: si un concepto vive en un documento, otros deben enlazarlo en lugar de duplicarlo.
- **La documentacion debe evolucionar junto al codigo**: un cambio relevante sin actualizacion documental se considera incompleto.

## Regla de idioma

- Usar **espanol** para:
  - contexto de negocio
  - objetivos funcionales
  - procesos operativos
  - explicaciones orientadas al equipo o stakeholders
- Usar **ingles** para:
  - nombres de clases, paquetes, endpoints y artefactos tecnicos
  - patrones arquitectonicos
  - conceptos de infraestructura
  - nombres de eventos, comandos, aggregates, entities y value objects

Evita alternar innecesariamente entre ambos idiomas dentro de la misma frase o del mismo titulo.

## Estructura recomendada del repositorio documental

```text
docs/
|-- 1-standards/
|-- 2-architecture/
|-- 3-global-processes/
|-- 4-modules/
|-- 5-events/
|-- readme.md
`-- requirements-index.md
```

## Estructura recomendada por modulo

Cada bounded context debe intentar seguir esta estructura:

```text
docs/4-modules/[bounded-context]/
|-- readme.md
|-- requirements.md
|-- domain-model.md
|-- api-spec.md
|-- use-cases/
|   `-- index.md
|-- endpoints/                # solo si el modulo expone endpoints relevantes
|   `-- [endpoint-name].md
|-- architecture/             # solo si el modulo necesita detalle adicional
|   |-- quality-attributes.md
|   |-- infrastructure.md
|   `-- c4-model/
`-- integration-events.md     # si el modulo publica o consume eventos
```

## Que debe responder cada documento

### `readme.md`

Debe responder rapidamente:

- Que responsabilidad tiene el modulo
- Que entidades principales existen
- Que eventos publica o consume
- Que documentos complementarios deben leerse despues

### `requirements.md`

Debe capturar:

- alcance funcional del modulo
- reglas de negocio principales
- restricciones relevantes
- criterios de aceptacion o escenarios clave cuando aplique

### `domain-model.md`

Debe explicar:

- aggregates
- entities
- value objects
- invariantes del dominio
- lenguaje ubicuo del contexto

### `api-spec.md`

Debe describir:

- contratos expuestos
- comandos o queries principales
- errores esperados
- consideraciones de seguridad y versionado si aplica

### `use-cases/`

Cada caso de uso debe dejar claro:

- actor o disparador
- precondiciones
- flujo principal
- flujos alternos
- postcondiciones
- eventos o integraciones disparadas

### `architecture/`

Debe reservarse para decisiones tecnicas del modulo, por ejemplo:

- topologia interna
- dependencias externas
- atributos de calidad especificos
- diagramas C4 del contexto siguiendo las [Convenciones de Diagramas](./diagram-conventions.md).

## Reglas de calidad editorial

- Usar titulos descriptivos y estables.
- Empezar cada documento con contexto breve, no con detalles menores.
- Preferir tablas solo cuando realmente mejoran comparacion o trazabilidad.
- Usar listas para responsabilidades, reglas o pasos; evitar bloques extensos sin estructura.
- Mantener enlaces relativos y verificar que apunten a archivos reales.
- Evitar placeholders como "Nombre", "Descripcion", "TODO" en documentos considerados vigentes.
- Si un documento es una plantilla, debe indicarlo explicitamente en el titulo o en la primera seccion.

## Convenciones de nombres

- Archivos en `kebab-case`.
- Casos de uso con prefijo identificable:
  - `uc-iam-00-provision-user.md`
- ADRs con fecha y descripcion corta:
  - `20260405-delegacion-del-envio-de-correos-a-un-modulo-dedicado-notifications.md`
- Un `index.md` cuando una carpeta necesite punto de entrada.

## Criterios para considerar un documento "completo"

Un documento esta en buen estado cuando:

- explica el objetivo del tema
- define claramente alcance y limites
- enlaza a documentos relacionados
- no contradice otros artefactos del repositorio
- evita placeholders y ambiguedades innecesarias
- permite a otra persona continuar el trabajo sin depender del autor original

## Mantenimiento

Actualizar documentacion cuando cambie cualquiera de estos puntos:

- reglas de negocio
- contratos API
- eventos publicados o consumidos
- decisiones de arquitectura
- estructura de modulos
- dependencias externas relevantes

## Anti-patrones a evitar

- Duplicar el mismo concepto en varios archivos.
- Usar el `readme.md` del modulo como deposito de todo.
- Documentar solo implementacion y omitir el motivo de negocio.
- Mantener secciones vacias "por si luego se usan".
- Escribir documentacion que no coincide con el estado real del sistema.

## Plantillas disponibles

- [Module README template](./templates/module-readme.md)
- [Domain model template](./templates/domain-model.md)
- [C4 Component Diagram template](./templates/c4-component-diagram.md)
- [Sequence Diagram template](./templates/sequence-diagram.md)
- [Domain Model Diagram template](./templates/domain-model-diagram.md)

## Navegacion relacionada

- [Portal de documentacion](../readme.md)
- [Definicion del proyecto](../project-definition.md)
- [Requisitos globales](../requirements-index.md)

[back](../readme.md)
