# Casos de Uso IAM - Sequence Diagram: Flujo de Autenticación

El siguiente diagrama detalla la dinámica operativa y de seguridad técnica dictada en la Arquitectura de Identidad.

## Diagrama: Login & Refresh Token Lifecycle

```plantuml
@startuml
autonumber
actor "Cliente / SPA" as spa
participant "API Gateway" as gw
participant "IAM : AuthController" as auth
participant "IAM : TokenService" as token
participant "IAM : UserRepo" as repo
database "IAM DB" as db

== Flujo Principal: Autenticación (Login) ==
spa -> gw: POST /api/iam/auth/login (email, pass)
gw -> auth: Reenvío con SSL Terminado
auth -> token: Validate Credentials
token -> repo: Buscar User por Email
repo -> db: SELECT FROM Users
db --> repo: Datos + Password Hash
repo --> token: Retorna Entidad + Hash
token -> token: Pasa verificación Argon2id
token -> token: Genera AccessToken (Exp 15m) y RefreshToken (Opaque/UUID, Exp 7d)
token -> repo: Persistir RefreshToken vinculado al Usuario
repo -> db: INSERT RefreshToken
auth --> gw: 200 OK (Tokens JSON o Cookies Seguras)
gw --> spa: Tokens almacenados en cliente

== Flujo Secundario: Uso y Expiración ==
spa -> gw: GET /api/hr/employees (Bearer ExpiredToken)
gw -> gw: Verifica Firma y Expiración
gw --> spa: 401 Unauthorized

== Flujo de Refresco (Silent Refresh) ==
spa -> gw: POST /api/iam/auth/refresh (RefreshToken)
gw -> auth: Payload Pass-through
auth -> token: Procesar rotación
token -> repo: Buscar RefreshToken
repo -> db: SELECT FROM RefreshTokens (Check Revoked/Expired)
db --> repo: Token Válido
token -> token: Genera NUEVO Access y NUEVO Refresh Token
token -> repo: Rotación (Invalida anterior, Guarda nuevo)
repo -> db: UPDATE (Revoke) & INSERT (Nuevo)
auth --> gw: 200 OK (Tokens Renovados)
gw --> spa: Continúa la sesión sin pedir clave al usuario
@enduml
```

## Notas Técnicas

1. **Token Opaque vs JWT:** El Access Token es JWT (para que la Gateway y otros servicios validen independientemente), pero el Refresh Token debe ser opaco (o almacenado bajo hash en la base de datos) para permitir revocación inmediata ante amenazas.
2. **Rotación:** Se debe aplicar rotación de *Refresh Token*. Si un token refrescado previamente intenta ser re-usado, el sistema debe asumir un ataque de robo e invalidar TODA la cadena de tokens del usuario (según recomendaciones de seguridad OAuth 2.1).

[back](index.md)
