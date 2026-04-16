# [UC-IAM-02] Activar Cuenta

**Módulo:** Identity & Access
**Actor Principal:** Nuevo Usuario / Empleado
**Descripción:** Un nuevo usuario (cuyo registro fue originado por HR) recibe un enlace de activación por correo electrónico que contiene un token JWT seguro. Utiliza este enlace para configurar su contraseña definitiva y habilitar su cuenta para ingresar al ERP.

---

## 1. Pre-condiciones

* El `User` existe en `iam_db` en estado `Pending_Activation`.
* El `ActivationToken` fue generado y enviado correctamente al usuario vía el módulo de Notificaciones.
* El enlace de activación no ha expirado (tiempo de vida configurado: ej. 48h).

## 2. Flujo Principal (Happy Path)

1. El usuario hace clic en el enlace recibido en su email y aterriza en el frontend (Página de Nueva Contraseña).
2. El usuario introduce una nueva contraseña (y su confirmación) y presiona "Activar Cuenta".
3. El frontend emite un `POST /api/iam/auth/activate` enviando el `ActivationToken` y el hash local/plano de la `NewPassword`.
4. El sistema (IAM) valida la firma del token JWT y extrae el `UserId`.
5. El sistema verifica que la cuenta sigue en `Pending_Activation` y que el token no ha sido usado antes.
6. El sistema valida si `NewPassword` cumple la política de seguridad (longitud, caracteres especiales, número, etc.).
7. El sistema ejecuta la función `Argon2id` para generar el `PasswordHash`.
8. El dominio cambia el estado del usuario de `Pending_Activation` a `Active`.
9. El repositorio de IAM actualiza la cuenta en base de datos de manera atómica.
10. Se devuelve un HTTP 200 OK informando al cliente de un éxito.

## 3. Flujos Alternativos / Excepciones

* **A1 - Token Expirado o Inválido (400 Bad Request):** Si la comprobación criptográfica del JWT falla, o si pasó el plazo legal, se rechaza y se le pide al usuario que solicite un reenvío.
* **A2 - Violación de Política de Contraseñas (400 Bad Request):** Si la contraseña no es lo suficientemente fuerte, se rechaza la operación antes de aplicar Hashes o cambios de BDD.
* **A3 - Usuario ya Activo (409 Conflict):** Si el token se extrae exitosamente, pero en la DB el usuario ya es `Active`, la solicitud se devuelve sin efectos (riesgo de reutilización de tokens evitado).

## 4. Post-condiciones

* **Éxito:** El usuario ya es `Active`, posee un salt+hash y a partir de ahora es capaz de emitir `Tokens de Acceso` a través del endpoint normal de Login.
* **Fallo:** La cuenta sigue estando desactivada y sin una contraseña que usar.

## 5. Eventos de Dominio (Domain & Integration Events)

* **Domain Event:** `UserActivatedDomainEvent` (puede usarse a nivel memoria local).
* **Integration Event:** Ninguno es estrictamente obligatorio aquí debido a que IAM tiene absoluta soberanía sobre este estado.

[back](./index.md)
