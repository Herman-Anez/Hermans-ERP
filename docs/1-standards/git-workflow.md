# Flujo de Trabajo en Git (Git Workflow)

**Versión:** 1.0.0

Este repositorio utiliza un flujo de trabajo basado en ramas de características (*Feature Branch Workflow* con influencias de *Trunk-Based Development*) para integraciones rápidas y controladas.

## Ramas Principales

- `main`: Es la rama principal. Representa el estado actual e instalable del software en entorno productivo o de *staging*. **No se permiten *commits* directos**; toda integración sucede a través de *Pull Requests* (PRs).

## Ramas Temporales

Se crean a partir de `main` y, una vez aprobadas y *mergeadas*, se eliminan.

### Convención de Nombres de Ramas

`<tipo>/[ID-TICKET]-<descripcion-corta>`

Tipos permitidos:

- `feature/`: nuevas funcionalidades.
- `fix/`: corrección de bugs documentados.
- `chore/`: mantenimiento del proyecto (dependencias, refactors estructurales, documentación no funcional).
- `docs/`: actualizaciones de documentación.

*Ejemplo:* `feature/HR-101-employee-onboarding`

## Convenciones de Commits (Conventional Commits)

Es altamente recomendado el uso de la convención de [Conventional Commits](https://www.conventionalcommits.org/en/v1.0.0/).

Estructura: `<tipo>(<alcance opcional>): <descripción>`

Tipos comunes:

- `feat:` Una nueva característica
- `fix:` Corrección de un error
- `docs:` Cambios exclusivos en la documentación
- `refactor:` Un cambio en el código que ni corrige un error ni añade funcionalidad
- `test:` Añadiendo o corrigiendo pruebas

*Ejemplo:* `feat(iam): agregar token de activación para nuevos empleados`

## Proceso de Pull Requests

1. **Crear PR hacia `main`:** El título debe ser descriptivo (y seguir formato de commit si hay Squash Merge).
2. **Revisión Continua:** Siempre que sea posible, el código no debería vivir fuera de `main` por más de un par de días para evitar conflictos grandes.
3. **Checklist básico para Merge:**
   - [ ] Las pruebas unitarias/integración pasan.
   - [ ] La documentación en la carpeta `docs/` se ha actualizado si cambió el dominio o la arquitectura.
   - [ ] Aprobación de al menos un revisor técnico.

## Manejo de Conflictos y Actualización

- Los desarrolladores deben hacer *Rebase* desde `main` hacia sus ramas de funcionalidad en lugar de *Merge* para mantener un historial lineal y limpio, u optar por el *Squash and Merge* al finalizar el PR.
