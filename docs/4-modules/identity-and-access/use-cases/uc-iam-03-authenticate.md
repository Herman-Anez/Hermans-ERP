# [UC-IAM-03] Autenticar Usuario (Login)

**Módulo:** Identity & Access
**Actor Principal:** Usuario (Activo)
**Descripción:** El sistema autentica las credenciales (Email y Password) de un usuario para proporcionarle los tokens de sesión firmados, permitiéndole operar en todo el proyecto como una identidad autorizada.

---

## 1. Pre-condiciones

* El `User` existe en la base de datos de IAM y se encuentra en estado `Active`.
* En la tabla de seguridad no tiene excedido el límite de la política dinámica de *Intrusion Prevention* (Demasiados intentos fallidos consecutivos).

## 2. Flujo Principal (Happy Path)

1. El actor emite una solicitud `POST /api/iam/auth/login` que contiene Payload JSON (`email`, `password`).
2. El sistema localiza un `User` en la BD filtrando por el correo electrónico normalizado.
3. El sistema extrae el `PasswordHash` previamente calculado y los datos (Salt).
4. El motor criptográfico (Argon2id) vuelve a codificar la contraseña entrante utilizando los parámetros guardados y efectúa una comparación asincrónica anti-timming attacks.
5. Los passwords convergen al 100%.
6. El sistema crea la pareja de tokens técnicos: un **JWT Access Token** (de vida corta) con los Claims de identidad y Roles y un **Refresh Token** opaco (persistido o cifrado asimétricamente, con largo tiempo de expiración).
7. Se actualiza silenciosamente en la BD el "LastLoginDate", e IAM reinicia el contador de *Failed Login Attempts* a Cero.
8. El cliente recibe HTTP 200 OK con el Payload de Tokens.

## 3. Flujos Alternativos / Excepciones

* **A1 - Correo no existe o contraseña incorrecta (401 Unauthorized):** Por razones de enumeración de cuentas, ambas advertencias comparten el mismo texto estático: "Las credenciales provistas son inválidas." Se incrementa en la Base de Datos el contador `FailedLoginAttempts`.
* **A2 - Bloqueo de Fuerza Bruta (403 Forbidden):** Si la validación del intento actual eleva el `FailedLoginAttempts` a 5 (o el límite estipulado), la contraseña no importa si fue la correcta. El sistema mutará el estado de `User` a `Locked`. (Envía `UserAccountLockedIntegrationEvent`).
* **A3 - Usuario Pendiente o Suspendido (401 Unauthorized):** Si las credenciales validan, pero el estado lógico del `User` dictamina que ha sido dado de baja o todavía no completó Onboarding, se interrumpe y se informa la anomalía.

## 4. Post-condiciones

* **Éxito:** Sistema ha extendido la confianza vía Tokens al cliente HTTP, quien ahora pasará dichos Tokens como Bearer Authentication Header hacia cualquier otro Módulo (`HR`, `Notifications`).
* **Fallo:** La sesión no se emite, el request fue neutro de cara a JWTs emitidos.

## 5. Eventos de Dominio (Domain & Integration Events)

* **Domain Event:** `UserLoginFailedDomainEvent` / `UserLoggedInDomainEvent`
* **Integration Event (Excepcional):** Como dicta en el flujo alternativo, si cruza el umbral de errores, obligatoriamente se debe gritar al aire mediante el bus de eventos: `UserAccountLockedIntegrationEvent` (para que e.g. Notifications envíe un Safety Alert Email).

[back](./index.md)
