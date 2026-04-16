# Requisitos: Identity & Access (IAM)

## Objetivo del modulo

Gestionar identidad tecnica, autenticacion, autorizacion y sesiones del ERP sin mezclar responsabilidades laborales propias de HR.

## Requisitos funcionales

- **RF-IAM-01:** Aprovisionar usuarios a partir de eventos laborales emitidos por HR. **[UC-IAM-01]**
- **RF-IAM-02:** Permitir activacion inicial de cuenta mediante token controlado. **[UC-IAM-02, UC-IAM-07]**
- **RF-IAM-03:** Autenticar usuarios con access token y refresh token. **[UC-IAM-03]**
- **RF-IAM-04:** Validar tokens y exponer claims relevantes para otros componentes del ecosistema. **[UC-IAM-06]**
- **RF-IAM-05:** Gestionar roles y permisos bajo un modelo RBAC. **[UC-IAM-08]**
- **RF-IAM-06:** Permitir cierre de sesion e invalidacion de refresh tokens. **[UC-IAM-05]**
- **RF-IAM-07:** Bloquear temporalmente cuentas ante intentos fallidos reiterados. **[UC-IAM-03]**
- **RF-IAM-08:** Rotación de tokens de sesión para seguridad activa. **[UC-IAM-04]**

## Reglas de negocio clave

- Un `User` no es equivalente a un `Employee`; solo mantiene referencia a su identidad laboral.
- Un usuario aprovisionado debe nacer en `Scheduled_For_Activation` o `Pending_Activation` segun la fecha efectiva de inicio.
- Un usuario no debe poder autenticarse antes de completar activacion.
- Todo refresh token debe ser rotado o invalidado segun politica de seguridad.
- Los permisos efectivos deben derivarse de roles y reglas controladas por IAM.

## Requisitos no funcionales

- **RNF-IAM-S1 (Seguridad):** Las contrasenas deben protegerse con hashing fuerte, como `Argon2id`.
- **RNF-IAM-S2 (Privacidad):** Los tokens no deben incluir PII innecesaria.
- **RNF-IAM-P1 (Rendimiento):** La validacion de token debe ser suficientemente rapida para no degradar el sistema.
- **RNF-IAM-A1 (Disponibilidad):** IAM debe priorizar alta disponibilidad por ser modulo critico del ERP.
- **RNF-IAM-A2 (Auditabilidad):** Login, bloqueo, activacion, refresh y revocacion deben quedar trazables.

## Integraciones esperadas

- **Consume:** `EmployeeHiredIntegrationEvent` y otros eventos laborales relevantes.
- **Publica:** `UserCreatedIntegrationEvent`, `UserActivationRequiredIntegrationEvent`, `UserAccountCreationFailedIntegrationEvent`, `UserAccountLockedIntegrationEvent`.

[back](./readme.md)
