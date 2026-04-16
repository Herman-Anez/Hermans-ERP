# [UC-HR-03] Finalizar Relación Laboral (Baja)

**Módulo:** Human Resources  
**Actor Principal:** Gestor de Recursos Humanos  
**Descripción:** Registrar legalmente el fin de la relación laboral de un empleado y garantizar que la desvinculación se refleje en todo el ecosistema ERP.

---

## 1. Pre-condiciones

- El empleado existe y se encuentra en estado Activo, Suspendido o Programado.
- El actor posee autorización de nivel de gestión de personal.

## 2. Flujo Principal (Happy Path)

1. El Gestor introduce la fecha efectiva de terminación y el motivo de cese (renuncia, despido, mutuo acuerdo, etc.).
2. El sistema valida que la fecha de terminación no entre en conflicto lógico irrecuperable con fechas de inicio del contrato.
3. El Agregado `Employee` cierra el contrato activo y muta su estado general a `Terminated` (o programa la transición si la fecha es a futuro).
4. El sistema guarda registro de auditoría sobre el cese.
5. El sistema emite inmediatamente el evento `EmployeeTerminatedIntegrationEvent`.
6. El sistema confirma la operación de cese al Gestor.

## 3. Flujos Alternativos / Excepciones

- **A1 - Fecha futura (Programada):** Si la fecha de desvinculación es mayor al día actual, el empleado conserva su estado activo, pero el sistema agendará el bloqueo o emitirá el evento llegado el momento para rescindir el acceso en la fecha solicitada a medianoche.
- **A2 - Terminación por Error Humano:** (A documentar en siguiente fase): Reglas excepcionales para "Deshacer Terminación" que emitiría una compensación de reactivación.

## 4. Post-condiciones

- **Éxito:** El contrato del empleado finaliza formálmente. El empleado no puede recibir nuevos beneficios organizativos.
- **Fallo:** El estado del contrato se mantiene.

## 5. Eventos de Dominio (Domain & Integration Events)

- **Integration Event:** `EmployeeTerminatedIntegrationEvent`
  - **Impacto Externo Crítico (IAM):** El módulo IAM debe suscribirse para revocar credenciales, expirar sesiones y cambiar estado del Usuario técnico a Bloqueado/Inactivo.
  - **Impacto Externo (Notifications):** El departamento de TI/Operaciones puede recibir una notificación automatizada de alerta.

[back](./index.md)
