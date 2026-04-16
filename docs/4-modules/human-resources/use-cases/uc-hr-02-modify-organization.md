# [UC-HR-02] Modificar Asignación Organizacional

**Módulo:** Human Resources  
**Actor Principal:** Gestor de Recursos Humanos  
**Descripción:** Modificar el rol, puesto, departamento o supervisor directo de un empleado activo dentro de la estructura organizacional.

---

## 1. Pre-condiciones

* El empleado existe y su estado es Activo (o programado para estarlo).
* El departamento destino existe.
* El rol/puesto destino existe.
* El actor tiene permisos administrativos sobre el área a modificar.

## 2. Flujo Principal (Happy Path)

1. El Gestor solicita actualizar los datos organizacionales del empleado.
2. El sistema recibe la petición y verifica la vigencia del contrato actual.
3. El sistema evalúa validaciones de negocio (por ejemplo, evitar jerarquías cíclicas o asignaciones a departamentos inactivos).
4. El empleado (Agregado `Employee`) muta su estado incorporando la nueva estructura organizacional.
5. El sistema registra internamente la variación para histórico temporal (Auditoría/Trazabilidad).
6. El sistema publica `EmployeeOrganizationModifiedIntegrationEvent` si corresponde (para que reportes, IAM o sistemas de BI refresquen organigramas).
7. La operación finaliza y se notifica el éxito.

## 3. Flujos Alternativos / Excepciones

* **A1 - Validación de Manager Inexistente/Cíclica:** Si el Gestor asigna al Empleado como supervisor de la misma persona que será su nuevo jefe, el sistema cancela la operación y advierte de la circularidad.
* **A2 - Empleado inactivo:** Si la relación laboral del empleado ya fue terminada o suspendida irrevocablemente, no se le pueden asignar puestos. La operación falla informando que la entidad no es elegible.

## 4. Post-condiciones

* **Éxito:** El empleado posee nueva asignación de manager, rol o departamento. La organización actualiza implícitamente su organigrama. Cualquier actualización asíncrona a modelos de lectura reacciona al evento publicado.
* **Fallo:** La estructura organizacional no cambia.

## 5. Eventos de Dominio (Domain & Integration Events)

* **Domain Event:** `EmployeeOrganizationModifiedDomainEvent`.
* **Integration Event:** `EmployeeOrganizationModifiedIntegrationEvent` (Relevancia: Sistemas como **IAM** reaccionan a este evento a través de un **ACL** para sincronizar accesos técnicos de forma desacoplada).

[back](./index.md)
