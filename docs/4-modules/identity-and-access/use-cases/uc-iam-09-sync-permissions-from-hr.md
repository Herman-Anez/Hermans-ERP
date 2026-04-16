# [UC-IAM-09] Sincronizar Permisos desde HR (ACL)

**Módulo:** Identity & Access (IAM)  
**Actor Principal:** Proceso Automático (Consumer)  
**Descripción:** Reaccionar a cambios en la estructura organizacional de un empleado en HR para actualizar sus roles y permisos en IAM de forma desacoplada.

---

## 1. Pre-condiciones

- Empleado posee una identidad digital activa en IAM.
- Se ha recibido un evento de integración organizacional (`EmployeeOrganizationModifiedIntegrationEvent` o `EmployeePromotedIntegrationEvent`).
- El componente `OrganizationalMapper` tiene cargadas las reglas de mapeo vigentes.

## 2. Flujo Principal (Happy Path)

1. El **Adaptador de Integración** de IAM recibe el evento desde el Message Broker.
2. El Adaptador extrae los identificadores de negocio (ej: `newDepartmentId`, `newGrade`).
3. El Adaptador invoca al **`OrganizationalMapper`** (ACL) para traducir estos IDs a un conjunto de **Roles Técnicos** o **Grupos**.
   - *Ejemplo:* `dept_marketing` -> `GROUP_MARKETING_BASIC`, `ROLE_DOC_VIEWER`.
4. El Adaptador orquesta la ejecución de comandos técnicos en el Dominio de IAM:
   - Identifica el `User` asociado al `employeeId`.
   - Solicita la revocación de roles/membresías antiguas si el mapeo así lo indica.
   - Solicita la asignación de nuevos roles/membresías.
5. El sistema persiste los cambios en el agregado `User` y registra la auditoría de seguridad.
6. La identidad digital del usuario queda actualizada para su próxima sesión o validación de token.

## 3. Flujos Alternativos / Excepciones

- **A1 - Sin mapeo definido:** Si el `OrganizationalMapper` no encuentra una regla para el departamento o puesto recibido, el sistema registra una advertencia en los logs y mantiene los permisos actuales para intervención técnica manual.
- **A2 - Usuario no existe en IAM:** Si el evento refiere a un `employeeId` que no tiene una identidad técnica creada, el proceso ignora el evento (puesto que no hay nada que actualizar).

## 4. Post-condiciones

- **Éxito:** Los roles y permisos técnicos del usuario en IAM reflejan su posición organizacional actual según las reglas del ACL.
- **Fallo:** No se modifican los permisos; se genera una alerta técnica si el fallo fue por inconsistencia de datos.

## 5. Diseño del Mapeo (ACL)

### Componente OrganizationalMapper

Este componente reside en la capa de **Infraestructura/Aplicación** de IAM y es el único punto de contacto con el lenguaje de RRHH.

> [!NOTE]
> **Implementación Fase 1:** Basada en archivos de configuración (`mappings.json`).
> **Implementación Futura:** Se moverá a una tabla de base de datos (`iam_organizational_mappings`) gestionable por el administrador de sistemas para permitir cambios dinámicos sin despliegue.

---

[back](./index.md)
