# [UC-IAM-05] Cerrar Sesión (Invalidar Sesión)

**Módulo:** Identity & Access  
**Actor Principal:** Usuario (Activo)  
**Descripción:** El usuario decide finalizar proactivamente su sesión en el ERP. El sistema debe asegurar que los tokens actuales no puedan reutilizarse jamás una vez efectuada la acción.

---

## 1. Pre-condiciones

* El cliente posee un Access Token o Refresh Token válido en su navegador.

## 2. Flujo Principal (Happy Path)

1. El cliente envía `POST /api/iam/auth/logout` adjuntando obligatoriamente el Refresh Token válido.
2. El sistema verifica la existencia criptográfica de los datos.
3. El sistema añade el identificador único del Refresh Token (y la familia subyacente si aplica rotación) a la tabla/caché de **Blacklist** (lista de revocación).
4. El caso de uso finaliza devolviendo HTTP 204 No Content.
5. El cliente, al recibir el 204, borra localmente sus cookies y estado de Redux/Vuex de la sesión, enviando al usuario al login.

## 3. Flujos Alternativos / Excepciones

* **A1 - Token Inexistente o ya Revocado (204 No Content):** Si alguien llama repetidamente al logout con un token que ya está cancelado, el sistema responde igual con éxito para hacer la operación idempotente sin arrojar errores confusos. No se ejecuta nada a nivel BD.

## 4. Post-condiciones

* **Éxito:** Se cancela el acceso al ERP. Si alguien interceptó el Refresh Token hace un minuto y trata de canjearlo (UC-IAM-04), rebotará en la Blacklist. (El JWT de vida ultracorta muere naturalmente poco después).

## 5. Eventos de Dominio (Domain & Integration Events)

* **Domain Event:** `UserLoggedOutDomainEvent` (estadística técnica).
* Ningún evento externo, ya que los módulos delegadamente consumen la validación en tiempo real.

[back](./index.md)
