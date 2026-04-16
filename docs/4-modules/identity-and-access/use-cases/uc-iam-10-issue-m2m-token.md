# [UC-IAM-10] Emitir Token M2M (Client Credentials)

**Módulo:** Identity & Access (IAM)  
**Actor Principal:** Módulo de Sistema (Machine)  
**Descripción:** Emisión de un Token JWT (Access Token) transitorio mediante el flujo Oauth2 *Client Credentials*, con el propósito único de asegurar la comunicación segura *Zero Trust* entre módulos backend a través de la red local.

---

## 1. Pre-condiciones

* El módulo de consumo posee credenciales criptográficas seguras y parametrizadas (Client ID y Client Secret) provenientes de Secrets genéricos (ej. inyectadas por plataforma de contenedores).
* El componente o servicio de destino requiere token de autorización.

## 2. Flujo Principal (Happy Path)

1. Un módulo (ej. `Notifications`) requiere realizar una operación en otro sub-contexto (ej. recuperar el Token de Activación desde IAM, o interactuar consultando datos de `HR`).
2. El propio módulo consumidor realiza una solicitud HTTP `POST /api/iam/auth/m2m/token` enviando su identidad en payload seguro (`clientId` y `clientSecret`).
3. El `Authentication Service` del módulo IAM corrobora la autenticación estática registrada internamente en el sistema principal.
4. El IAM Service delega al dominio la firma de un JWT (`System Token`) anexando *Claims* de sistema (ej. `"sub": "service-notifications"`, `"roles": ["sys_notifications"]`).
5. El IAM retorna el `access_token` serializado de tipo `Bearer`.
6. El módulo consumidor almacena internamente el JWT (memoria o caché local) aprovechándolo hasta que indique la propiedad `exp` (vencimiento).

## 3. Flujos Alternativos / Excepciones

* **A1 - Client Secret Inválido o Corrupto (401 Unauthorized):** IAM frena dramáticamente la petición devolviendo error. Operaciones emite alerta de *Intrusión o Lateral Movement*.
* **A2 - Límite de Frecuencia (429 Too Many Requests):** Se requiere que el módulo consuma sabiamente el Token reciclándolo en caché antes de su terminación, previniendo saturación DDoS sobre la base de datos de Auth.

## 4. Post-condiciones

* Transacción técnica de lectura terminada en Token. No existe estado persistente humano alterado.

## 5. Eventos de Dominio (Domain & Integration Events)

* Cero eventos generados formalmente.

[back](./index.md)
