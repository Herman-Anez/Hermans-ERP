# [UC-IAM-08] Gestionar Roles de Usuario

**Módulo:** Identity & Access  
**Actor Principal:** Administrador de Sistemas (IAM Admin)  
**Descripción:** Asigna, modifica o revoca uno o varios Roles lógicos dentro de un usuario específico, propiciándole o restringiéndole el acceso a partes del ERP.

---

## 1. Pre-condiciones

* El Actor Principal (Admin) posee el token activo con el permiso maestro `iam:roles:manage`.
* El usuario destino `User` existe en el sistema.

## 2. Flujo Principal (Happy Path)

1. El Administrador invoca `PUT /api/iam/users/{userId}/roles` mandando una matriz JSON formal (`["USER", "HR_MANAGER"]`).
2. IAM valida la existencia del Set de Roles provisto frente al catálogo del sistema interno.
3. IAM coteja que el Admin no incurra en escalada de privilegios ilegítimos (ej. un Sub-Admin intentando dar permisos GlobalAdmin a su propio amigo).
4. El sistema sobreescribe o realiza merge atómico de la nueva lista de roles vinculados al `User`.
5. Retorna 200 OK.

## 3. Flujos Alternativos / Excepciones

* **A1 - Self-Demotion (400 Bad Request):** El sistema puede abortar si el único Administrador Global intenta removerse el rol a sí mismo, dejando el sistema en estado "huérfano".
* **A2 - Revocación en Caliente:** Si se quitan permisos muy críticos (ej. destituir a un HR Manager), IAM de forma pasiva disparará `UserRolesChangedDomainEvent` que podría automáticamente invalidar los tokens actuales emitidos a ese usuario, para obligarle un re-login y aplicarle sus nuevos (menores) permisos inmediatamente y no 15 minutos después.

## 4. Post-condiciones

* **Éxito:** Roles de usuario actualizados, la base de datos se consolida.

## 5. Eventos de Dominio (Domain & Integration Events)

* **Domain Event:** `UserRolesChangedDomainEvent`. Consumo local para obligar el invalidado de su cache y tokens como medida de seguridad proactiva.
* **Integration Event:** Inexistente. El rol y los permisos son incumbencia única de IAM. Los módulos no les importa qué roles tiene, pues IAM se los inyecta en el Token cuando los valida en su nombre. Consistencia perfecta.

[back](./index.md)
