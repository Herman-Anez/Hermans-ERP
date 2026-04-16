# API Specification - Human Resources

**Bounded Context:** Human Resources
**Version:** 1.0.0
**Formato:** OpenAPI 3.1.0
**Base URL:** `/api/hr`

## Objetivo de la API

Exponer operaciones administrativas para gestionar empleados, contratos y estructura organizacional sin mezclar responsabilidades de autenticacion o autorizacion tecnica.

## Especificación OpenAPI

A continuación se detalla el contrato formal en formato OpenAPI 3.1.0:

```yaml
openapi: 3.1.0
info:
  title: Human Resources API
  version: 1.0.0
  description: |
    API para la gestión del ciclo de vida del empleado, incluyendo contrataciones, 
    movimientos organizacionales y terminaciones de relación laboral.
servers:
  - url: /api/hr
    description: Internal ERP Base URL

security:
  - bearerAuth: []

paths:
  /employees:
    get:
      summary: Lista empleados
      description: Permite listar empleados por filtros como estado, departamento o identificador legal.
      parameters:
        - name: status
          in: query
          schema:
            type: string
        - name: departmentId
          in: query
          schema:
            type: string
      responses:
        '200':
          description: Listado de empleados.
    post:
      summary: Registra un nuevo empleado
      description: Registra un nuevo empleado con su información inicial.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateEmployeeRequest'
      responses:
        '201':
          description: Empleado registrado exitosamente.

  /employees/{employeeId}:
    parameters:
      - name: employeeId
        in: path
        required: true
        schema:
          type: string
          format: uuid
    get:
      summary: Consulta detalle de empleado
      description: Consulta el detalle completo de un empleado.
      responses:
        '200':
          description: Detalle del empleado.
        '404':
          description: Empleado no encontrado.

  /employees/{employeeId}/contracts:
    post:
      summary: Registra un nuevo contrato
      description: Registra un nuevo contrato formal para el empleado.
      parameters:
        - name: employeeId
          in: path
          required: true
          schema:
            type: string
            format: uuid
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/CreateContractRequest'
      responses:
        '201':
          description: Contrato registrado.

  /employees/{employeeId}/organization:
    patch:
      summary: Actualiza asignación organizacional
      description: Actualiza asignaciones organizativas como departamento, puesto o manager.
      parameters:
        - name: employeeId
          in: path
          required: true
          schema:
            type: string
            format: uuid
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/UpdateOrganizationRequest'
      responses:
        '200':
          description: Asignación actualizada correctamente.

  /employees/{employeeId}/termination:
    post:
      summary: Finaliza la relación laboral
      description: Marca la finalización de la relación laboral según reglas del negocio.
      parameters:
        - name: employeeId
          in: path
          required: true
          schema:
            type: string
            format: uuid
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TerminateEmployeeRequest'
      responses:
        '200':
          description: Relación laboral terminada exitosamente.

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:
    CreateEmployeeRequest:
      type: object
      required:
        - firstName
        - lastName
        - legalId
      properties:
        firstName:
          type: string
        lastName:
          type: string
        legalId:
          type: string
        dob:
          type: string
          format: date

    CreateContractRequest:
      type: object
      properties:
        type:
          type: string
        startDate:
          type: string
          format: date
        endDate:
          type: string
          format: date
        salary:
          type: number
        currency:
          type: string

    UpdateOrganizationRequest:
      type: object
      properties:
        departmentId:
          type: string
          format: uuid
        positionId:
          type: string
          format: uuid
        managerId:
          type: string
          format: uuid

    TerminateEmployeeRequest:
      type: object
      required:
        - terminationDate
        - reason
      properties:
        terminationDate:
          type: string
          format: date
        reason:
          type: string
```

## Reglas de contrato

- **Zero Trust M2M:** Toda comunicación síncrona originada desde otros módulos hacia esta API exige terminantemente autenticación estructurada con un Token JWT efímero (M2M) válido emitido por IAM, sin importar si existe confianza de red subyacente.
- La API no debe exponer reglas internas del dominio como simples campos editables sin validacion.
- Las operaciones que afecten onboarding u offboarding deben disparar integraciones cuando corresponda.
- Los errores de validacion deben ser claros y distinguibles de errores tecnicos.

## Detalles y Documentación Anexa

Para comprender las reglas de negocio detalladas tras estos endpoints, consulte los casos de uso:

- **[Contratación de Empleado (Onboarding)](./use-cases/uc-hr-01-hire-employee.md)**: Ref: `POST /employees`.
- **[Modificación Organizacional](./use-cases/uc-hr-02-modify-organization.md)**: Ref: `PATCH /employees/{id}/organization`.
- **[Terminación de Relación Laboral](./use-cases/uc-hr-03-terminate-employment.md)**: Ref: `POST /employees/{id}/termination`.
- **[Gestión de Contratos](./use-cases/uc-hr-04-add-contract.md)**: Ref: `POST /employees/{id}/contracts`.
- **[Búsqueda y Consulta de Perfiles](./use-cases/uc-hr-05-search-employee.md)**: Ref: `GET /employees`.

[back](./readme.md)
