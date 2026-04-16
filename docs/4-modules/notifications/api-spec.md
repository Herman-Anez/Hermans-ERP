# API Specification - Notifications

**Bounded Context:** Notifications  
**Version:** 1.0.0  
**Formato:** OpenAPI 3.1.0  
**Base URL:** `/api/notifications`

## Objetivo de la API

Permitir la solicitud controlada de notificaciones salientes y la consulta del estado de entrega cuando sea necesario.

## Estilo de interacción

El modulo esta orientado principalmente a procesamiento asincrono por eventos, pero expone los siguientes endpoints internos para interacciones sincrónicas, administracion y soporte técnico.

## Especificación OpenAPI

```yaml
openapi: 3.1.0
info:
  title: Notifications internal API
  version: 1.0.0
  description: |
    API para la delegación de envíos de correo transaccional, administración de 
    plantillas, reintentos y consulta de trazabilidad de entrega.
servers:
  - url: /api/notifications

security:
  - bearerAuth: []

paths:
  /emails/send:
    post:
      summary: Solicitud de Correo Transaccional
      description: Solicita el envío asíncrono de un correo de forma controlada.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - correlationId
                - templateId
                - recipient
                - channel
              properties:
                correlationId:
                  type: string
                templateId:
                  type: string
                recipient:
                  type: string
                  format: email
                channel:
                  type: string
                  enum: [EMAIL, SMS, PUSH]
                data:
                  type: object
                  additionalProperties: true
                source:
                  type: string
      responses:
        '202':
          description: Aceptado para procesamiento asíncrono.
        '400':
          description: Faltan datos obligatorios del payload.
        '409':
          description: Conflicto - Duplicidad incompatible de correlationId.

  /notifications/{notificationId}:
    parameters:
      - name: notificationId
        in: path
        required: true
        schema:
          type: string
          format: uuid
    get:
      summary: Trazabilidad de una notificación
      description: Consulta el estado histórico y la trazabilidad de una notificación procesada.
      responses:
        '200':
          description: Detalle de la notificación y sus intentos.
          content:
            application/json:
              schema:
                $ref: '#/components/schemas/NotificationStatusResponse'
        '404':
          description: Notificación no hallada.

  /notifications/{notificationId}/retry:
    post:
      summary: Reprocesamiento de notificación
      description: Solicita un reprocesamiento manual y controlado de una notificacion fallida.
      parameters:
        - name: notificationId
          in: path
          required: true
          schema:
            type: string
            format: uuid
      responses:
        '202':
          description: Reintento encolado.
        '400':
          description: La notificación no aplica a reintentos (ej. ya exitosa).

  /templates:
    get:
      summary: Listar Plantillas
      description: Lista plantillas disponibles estructuradas para notificación.
      responses:
        '200':
          description: Colección de identificadores de plantilla.
    post:
      summary: Crear Plantilla
      description: Crea una nueva plantilla reutilizable.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TemplatePayload'
      responses:
        '201':
          description: La plantilla fue creada exitosamente. Devuelve el ID generado.

  /templates/{templateId}:
    patch:
      summary: Actualizar plantilla
      description: Actualiza una plantilla existente creando una nueva revisión, garantizando que el historial de notificaciones no rompa su trazabilidad visual.
      parameters:
        - name: templateId
          in: path
          required: true
          schema:
            type: string
      requestBody:
        required: true
        content:
          application/json:
            schema:
              $ref: '#/components/schemas/TemplatePayload'
      responses:
        '200':
          description: Plantilla modificada.

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT

  schemas:
    NotificationStatusResponse:
      type: object
      properties:
        notificationId:
          type: string
        status:
          type: string
          enum: [PENDING, DELIVERED, FAILED, RETRYING]
        channel:
          type: string
        recipient:
          type: string
        attempts:
          type: integer
        lastError:
          type: string
        correlationId:
          type: string

    TemplatePayload:
      type: object
      required:
        - templateId
        - name
        - channel
        - subject
        - body
      properties:
        templateId:
          type: string
        name:
          type: string
        channel:
          type: string
        subject:
          type: string
        body:
          type: string
        isActive:
          type: boolean
          default: true
```

## Reglas de contrato

- Toda solicitud de mensajería debe incluir un `correlationId` para trazar el flujo distribuido.
- El modulo debe rechazar de un principio las peticiones malformadas con HTTP 400.
- `202 Accepted` significa encolado persistente; no garantiza la llegada sincrónica al destinatario, la cual puede estar expuesta a retries.
- Los cambios de plantilla (vía PATCH) no pueden sobrescribir mutando los registros pasados.

[back](./readme.md)
