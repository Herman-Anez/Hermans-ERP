# [UC-IAM-06] Validar Token / Introspección

**Módulo:** Identity & Access  
**Actor Principal:** Sistema Externo o API Gateway (Resource Server)  
**Descripción:** Un módulo ajeno a IAM (por ejemplo, Human Resources o Notifications) necesita confirmar que el JWT Access Token propulsado por un cliente HTTP tiene los permisos adecuados, y que no fue invalidado globalmente de forma prematura.

---

## 1. Pre-condiciones

* Un módulo de la red interna o API Gateway posee un Token de Acceso provisto mediante un Header HTTP local.

## 2. Flujo Principal (Happy Path)

1. El módulo o Gateway ejecuta una llamada inter-red (idealmente HTTP/gRPC local sin latencia) a `POST /api/iam/auth/validate` exponiendo el Token.
2. El servicio IAM verifica la firma criptográfica usando su clave privada o compartida asimétrica.
3. El sistema IAM comprueba en Memcached/Redis de alta velocidad que el Token no esté suscrito a una lista de revocación global (debido a un Employee Offboarding o Bloqueo).
4. El servicio responde HTTP 200 OK informando `valid: true`, y escupiendo los claims puros (`userId`, `roles`, `permissions` granulares).
5. El Gateway o módulo receptor permite a su controlador de negocio proceder con la lógica funcional confiando en el sujeto reportado.

## 3. Flujos Alternativos / Excepciones

* **A1 - Token Inválido o Modificado (401 Unauthorized):** Si un atacante modificó los claims y la firma crasheó, o si el JWT no tiene el formato correcto, IAM rechaza sumariamente reportando `valid: false`.
* **A2 - Token en Central de Revocación Rápida (401 Unauthorized):** Si el JWT, a pesar de ser firmado y a pesar de no haber expirado por reloj, pertenece a un Usuario que fue borrado hace 5 segundos en un terminación (UC-HR-03), el sistema lo captura aquí. Garantiza consistencia inmediata y seguridad blindada.

## 4. Post-condiciones

* **Éxito:** Módulo obtiene luz verde para procesar una API en base a un rol certero proveído por la autoridad (IAM). Es una lectura sin estado (Stateless read).

## 5. Eventos de Dominio (Domain & Integration Events)

* Cero eventos emitidos. Esta es una operación pasiva de 1-2 ms de latencia llamada muchísimas veces por segundo, no debe generar overhead.

[back](./index.md)
