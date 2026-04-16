# [UC-IAM-04] Refrescar Token

**Módulo:** Identity & Access  
**Actor Principal:** Aplicación Cliente (SPA / Mobile)  
**Descripción:** El cliente HTTP canjea un Refresh Token válido y no expirado para obtener un nuevo Access Token JWT de corta duración, manteniendo la sesión viva sin pedir nuevamente las credenciales al usuario.

---

## 1. Pre-condiciones

* El `Refresh Token` debe haber sido emitido previamente por un Login exitoso y estar almacenado de forma segura en el cliente (ej. HttpOnly cookie o Secure Storage).
* El usuario asociado al token debe hallarse en estado `Active` en la base de datos (no suspendido ni bloqueado).

## 2. Flujo Principal (Happy Path)

1. El cliente intercepta un `401 Unauthorized` de una API o verifica de antemano que su JWT expiró.
2. El cliente emite `POST /api/iam/auth/refresh` con el Payload del Refresh Token.
3. El sistema busca el Refresh Token en su almacén seguro o valida criptográficamente su firma (si es opaco o self-contained).
4. El sistema verifica en BD que no pertenezca a la "Blacklist" de sesiones revocadas.
5. El sistema carga la entidad `User` actual y verifica que su cuenta sigue siendo `Active` y mantiene roles vigentes.
6. El sistema genera un nuevo conjunto de **JWT Access Token** y **Refresh Token** (Rotación de Refresh Tokens).
7. Se invalida el Refresh Token anterior para prevenir ataques de "Replay".
8. El cliente recibe HTTP 200 OK con el Payload de Tokens nuevos.

## 3. Flujos Alternativos / Excepciones

* **A1 - Token Expirado o Inválido (401 Unauthorized):** Si el Refresh Token ya pasó su Time-to-Live (ej. 7 días) o su firma es inválida, se rechaza. El cliente deberá mandar al usuario a la pantalla de Login obligatoriamente.
* **A2 - Token en Blacklist (Family Compromised) (401 Unauthorized):** Si se detecta un intento de canje de un token previamente revocado (o en la versión anterior en caso de rotación), se asume un compromiso de la familia de tokens. Todo el árbol de sesiones asociado se invalida inmediatamente.
* **A3 - Usuario Inhabilitado (403 Forbidden):** Si durante la validación IAM detecta que el usuario fue dado de baja (Terminado u Offboarded) durante la vigencia del Refresh Token, el sistema lo invalida y responde 403, cortando el acceso de forma defintiva.

## 4. Post-condiciones

* **Éxito:** La sesión se ha extendido exitosamente. El usuario ni se entera y continúa navegando.
* **Fallo:** La sesión térmica finaliza, el usuario es expulsado del sistema al perder tokens válidos.

## 5. Eventos de Dominio (Domain & Integration Events)

* **Domain Event:** `SessionRefreshedDomainEvent` (sólo para analítica o prevención de fraudes locales).

[back](./index.md)
