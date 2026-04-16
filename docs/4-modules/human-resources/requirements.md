# Requisitos: Human Resources (HR)

## Objetivo del modulo

Gestionar el ciclo de vida laboral del empleado, sus datos principales, su contrato y su ubicacion dentro de la estructura organizacional.

## Requisitos funcionales

- **RF-HR-01:** Registrar un nuevo empleado con datos personales y laborales minimos. **[UC-HR-01]**
- **RF-HR-02:** Crear y mantener el contrato inicial del empleado. **[UC-HR-01, UC-HR-04]**
- **RF-HR-03:** Gestionar cambios de puesto, departamento o responsable. **[UC-HR-02]**
- **RF-HR-04:** Mantener historial contractual y trazabilidad de cambios relevantes. **[UC-HR-04, UC-HR-06]**
- **RF-HR-05:** Permitir baja o terminacion laboral sin perder historial. **[UC-HR-03]**
- **RF-HR-06:** Publicar eventos de integracion cuando el alta o cambio laboral afecte a otros modulos. **[UC-HR-01, UC-HR-02, UC-HR-03]**
- **RF-HR-07:** Consultar empleados por identificadores de negocio, estado laboral y estructura organizacional. **[UC-HR-06]**

## Reglas de negocio clave

- Un empleado se identifica por un `EmployeeId` interno y un identificador legal validado.
- No pueden existir dos empleados con el mismo `TaxId`.
- Un empleado no puede tener contratos activos incompatibles al mismo tiempo.
- Un empleado no puede ser su propio responsable jerarquico.
- Todo alta laboral valida debe ser trazable y potencialmente integrable con IAM.

## Requisitos no funcionales

- **RNF-HR-S1 (Seguridad):** Los datos personales y contractuales deben tener controles de acceso estrictos.
- **RNF-HR-S2 (Privacidad):** La informacion sensible debe minimizarse en eventos de integracion.
- **RNF-HR-M1 (Mantenibilidad):** El modelo de dominio debe proteger invariantes laborales dentro del agregado.
- **RNF-HR-P1 (Rendimiento):** Las consultas operativas de empleados deben responder en tiempos adecuados para uso administrativo normal.
- **RNF-HR-A1 (Auditabilidad):** Toda alta, cambio contractual y baja debe quedar registrada.

## Integraciones esperadas

- **Publica:** `EmployeeHiredIntegrationEvent`, `EmployeeTerminatedIntegrationEvent`, `EmployeeOrganizationModifiedIntegrationEvent`, `EmployeeContractRenewedIntegrationEvent`.
- **Consume:** Ninguno (Contexto Maestro).

[back](./readme.md)
