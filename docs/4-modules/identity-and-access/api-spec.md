# API Specification - Identity & Access (IAM)

**Bounded Context:** Identity & Access  
**Versión:** 1.0.0  
**Formato:** OpenAPI 3.1.0  
**Base URL:** `/api/iam`

## Especificación OpenAPI

El siguiente bloque documenta todos los endpoints expuestos para autenticación y gestión de acceso:

```yaml
openapi: 3.1.0
info:
  title: Identity & Access API
  version: 1.0.0
  description: |
    API para la gestión de acceso, generación y rotación de tokens (Login, Refresh, Logout) 
    y activación de cuentas de usuario técnico.
servers:
  - url: /api/iam

security:
  - bearerAuth: []

paths:
  /auth/login:
    post:
      summary: Login con Credenciales
      security: []
      description: Genera un Access Token y Refresh Token validando las credenciales proporcionadas.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - email
                - password
              properties:
                email:
                  type: string
                  format: email
                password:
                  type: string
                  format: password
      responses:
        '200':
          description: Login exitoso. Retorna JWT access token y refresh token.
        '401':
          description: Credenciales inválidas.
        '423':
          description: Cuenta bloqueada (por intentos fallidos).

  /auth/refresh:
    post:
      summary: Refrescar Token
      security: []
      description: Permite obtener un nuevo Access Token enviando un Refresh Token válido.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - refreshToken
              properties:
                refreshToken:
                  type: string
      responses:
        '200':
          description: Token refrescado exitosamente. Retorna un nuevo conjunto de tokens.
        '401':
          description: Refresh token expirado o inválido.

  /auth/validate:
    post:
      summary: Validar Token
      description: Valida si un Access Token dado sigue siendo válido y retorna sus claims asociados.
      responses:
        '200':
          description: Token es válido.
        '401':
          description: Token no es válido o está expirado.

  /auth/logout:
    post:
      summary: Cerrar Sesión
      description: Invalida el refresh token y finaliza la sesión activa.
      responses:
        '204':
          description: Logout exitoso.

  /auth/m2m/token:
    post:
      summary: Emitir Token Machine-to-Machine
      security: []
      description: |
        Endpoint exclusivo para módulos internos (Client Credentials). Emite un 
        Access Token destinado al uso entre servicios (Zero Trust).
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - clientId
                - clientSecret
              properties:
                clientId:
                  type: string
                clientSecret:
                  type: string
                  format: password
      responses:
        '200':
          description: Token M2M exitoso.
        '401':
          description: Credenciales inválidas.

  /auth/activate:
    post:
      summary: Activación de Cuenta
      security: []
      description: Permite a un usuario establecer su contraseña inicial usando el token de activación.
      requestBody:
        required: true
        content:
          application/json:
            schema:
              type: object
              required:
                - activationToken
                - newPassword
              properties:
                activationToken:
                  type: string
                newPassword:
                  type: string
                  format: password
      responses:
        '200':
          description: Cuenta activada con éxito.
        '400':
          description: Token inválido, expirado o política de contraseñas no cumplida.
          
      responses:
        '200':
          description: Roles actualizados correctamente.
        '403':
          description: Permisos insuficientes o intento de escalada.

  /internal/users/{userId}/activation-token:
    get:
      summary: Obtener Token de Activación (Interno)
      description: |
        Endpoint privado para que el módulo de Notifications recupere el secreto de activación 
        tras recibir un evento de integración. Requiere token M2M válido.
      security:
        - bearerAuth: []
      parameters:
        - name: userId
          in: path
          required: true
          schema:
            type: string
      responses:
        '200':
          description: Token recuperado exitosamente.
          content:
            application/json:
              schema:
                type: object
                properties:
                  activationToken:
                    type: string
        '404':
          description: Usuario o token no encontrado.
        '401':
          description: No autorizado.

components:
  securitySchemes:
    bearerAuth:
      type: http
      scheme: bearer
      bearerFormat: JWT
```

## Detalles y Documentación Anexa

Dado que ciertos endpoints tienen reglas de negocio complejas, se mantiene documentación anexada con detalles funcionales en los casos de uso correspondientes:

- **[Crear Usuario (Invitación)](./use-cases/uc-iam-01-provision-user.md)**: Gestionado de manera interna asíncrona pero documentable.
- **[Activación de Cuenta](./use-cases/uc-iam-02-activate-account.md)**
- **[Login con Credenciales](./use-cases/uc-iam-03-authenticate.md)**
- **[Refresh Token](./use-cases/uc-iam-04-refresh-token.md)**
- **[Logout](./use-cases/uc-iam-05-logout.md)**
- **[Validar Token](./use-cases/uc-iam-06-validate-token.md)**
- **[Procesamiento de Activaciones Programadas](./use-cases/uc-iam-07-process-scheduled-activations.md)**
- **[Gestión de Roles](./use-cases/uc-iam-08-manage-roles.md)**
- **[Emitir Token M2M](./use-cases/uc-iam-10-issue-m2m-token.md)**

## Seguridad y Reglas Técnicas

- Contraseñas almacenadas con Argon2id.
- Bloqueo automático tras 5 intentos fallidos (emite `UserAccountLockedIntegrationEvent`).
- Refresh Token rotación y persistencia (para mitigación de robos de sesión).
- Rate limiting en login (5/minuto por IP).
- Todos los endpoints bajo HTTPS obligatoriamente (Capa Transporte).

[back](./readme.md)
