# [UC-HR-04] Registrar Nuevo Contrato

**Módulo:** Human Resources  
**Actor Principal:** Gestor de Recursos Humanos / Especialista en Compensaciones  
**Descripción:** Renovar o adjuntar un nuevo contrato formal a la historia de vida laboral de un empleado existente (ej. promociones que requieren nuevo contrato legal, paso de Contratista a Empleado Tiempo Completo).

---

## 1. Pre-condiciones

- El empleado existe.
- El empleado no posee un contrato activo de naturaleza incompatible operando simultáneamente.

## 2. Flujo Principal (Happy Path)

1. El Gestor especifica la nueva modalidad contractual (Salario, Tipo de Moneda, Rango de fechas, Posición asociada).
2. El sistema verifica que las fechas del nuevo contrato no traslapen inválidamente con un contrato en rigor (si la ley o el dominio dicta exclusividad).
3. El sistema da de alta el nuevo contrato, sumándolo al historial del Agregado `Employee`.
4. El contrato vigente anterior (si lo hay) es marcado como finalizado por renovación, o la vigencia arranca como programada.
5. Se lanza `EmployeeContractRenewedIntegrationEvent` o `EmployeePromotedIntegrationEvent` dependiendo del delta detectado.
6. Se devuelve a interfaz la verificación de éxito.

## 3. Flujos Alternativos / Excepciones

* **A1 - Violación de exclusividad contractual:** Si el negocio dicta que sólo puede existir 1 contrato activo y se intenta solapar forzosamente 2 contratos sin marcar una renovación/término para uno de ellos, el sistema rechaza la petición.

## 4. Post-condiciones

* **Éxito:** El contrato queda consolidado y persistido dentro de la entidad organizativa del empleado. Trazabilidad y visibilidad temporal sobre cambios pasados (ej. historial de salarios o contratos previos).
* **Fallo:** No se crea el nuevo contrato ni se modifica el historial.

## 5. Eventos de Dominio (Domain & Integration Events)

* **Integration Event:** `EmployeeContractRenewedIntegrationEvent` o `EmployeePromotedIntegrationEvent`.

[back](./index.md)
