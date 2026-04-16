# [UC-HR-01] Contratar Empleado (Onboarding)

**Módulo:** Human Resources  
**Actor Principal:** Gestor de Recursos Humanos  
**Descripción:** Un gestor autenticado registra un nuevo empleado (Onboarding) definiendo sus variables identitarias y de contrato inicial, dejando preparado el modelo para la creación de usuario.

---

## 1. Pre-condiciones

* El Gestor debe estar autenticado vía JWT (IAM) con claims para escribir en HR (`hr:employee:create`).
* El `TaxId` o Identificador legal no debe constar ya en la base de datos de HR.
* Las entidades relacionadas por ID (`DepartmentId`, `PositionId`) deben ser vigentes y válidas.

## 2. Flujo Principal (Happy Path)

1. El Gestor envía los datos de registro (vía `POST /api/hr/employees`, comando gRPC `HireEmployee` o Interfaz Administrativa).
2. El sistema valida las reglas de nulidad, sintaxis de correos y obligatoriedad de campos.
3. El sistema busca internamente el `TaxId` descartando duplicidad.
4. El sistema crea el Agregado Raíz `Employee` en la memoria (Estado: Vigente o Programado acorde a la fecha base).
5. El sistema asienta el sub-registro del contrato inicial anexado al `Employee`.
6. El repositorio persiste de manera atómica todos los datos del empleado en `HR Database`.
7. El sistema confirma la operación (vía `201 Created` / `200 OK` o respuesta gRPC exitosa).

## 3. Flujos Alternativos / Excepciones

* **A1 - `TaxId` Duplicado (409 Conflict):** Si el empleado ya consta laboralmente, se cancela la transacción. No se inserta nada.
* **A2 - Violación Invariantes (400 Bad Request):** Si se detecta un salario menor al mínimo legal estructurado o se introducen fechas de nacimiento irracionales.

## 4. Post-condiciones

* **Éxito:** La capa de persistencia consolida de manera unitaria el inicio de la relación laboral en una transacción.
* **Fallo:** Mutabilidad nula (Rollback absoluto en base a atomicidad de persistencia).

## 5. Eventos de Dominio (Domain & Integration Events)

* **Domain Event:** `EmployeeInstantiatedDomainEvent` (sólo interceptado por handlers dentro de HR, ej: inicializar carpeta de expedientes).
* **Integration Event:** `EmployeeHiredIntegrationEvent`. Este evento se publica en el Broker para que el módulo `Identity & Access` reaccione y realice el aprovisionamiento de cuenta (Ver [UC-IAM-01 Provision User](../../identity-and-access/use-cases/uc-iam-01-provision-user.md)).

[back](./index.md)
