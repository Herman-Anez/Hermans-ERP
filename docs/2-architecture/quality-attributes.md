# Atributos de Calidad

Este documento define las características arquitectónicas (requisitos no funcionales / atributos de calidad) que deben ser consideradas y protegidas en la evolución del ERP Core.

## Mantenibilidad e Independencia

- **Bajo Acoplamiento (Loose Coupling):** Los *Bounded Contexts* no comparten bases de datos ni invocan directamente el estado interno de otro módulo.
- **Evolucionabilidad:** Cada módulo debe poder ser mantenido, refactorizado o reescrito (con justificación) de manera aislada, sin afectar todo el ecosistema.
- **Trazabilidad:** Cualquier invocación asíncrona entre módulos, así como cambios críticos en los agregados de dominio, deben incluir `correlationId` para un seguimiento completo.

## Seguridad e Identidad

- **Autorización Centralizada, Verificación Local:** Todo acceso al sistema es intermediado y autenticado por IAM. Los demás módulos asumen la identidad y los claims (roles/permisos) contenidos en los tokens JWT sin necesidad de interrogar a la base de datos central de usuarios en cada invocación.
- **Auditoría:** Procesos de alta criticidad (como cambios en los contratos de HR o bloqueo de cuentas en IAM) deben guardar registro.
- **Zero Trust & Comunicación M2M:** Se rechaza la confianza implícita por red (VPC/LAN local). Cualquier comunicación síncrona o *backchannel* HTTP entre módulos requiere mandatoriamente la presentación de un token JWT efímero (Machine-to-Machine) con los permisos de servicio específicos, obtenido mediante un flujo *Client Credentials*. Se prohíbe el uso de Security Keys estáticas.

## Escalabilidad y Resiliencia

- **Tolerancia a Fallos Transitorios:** Las integraciones asíncronas deben incluir políticas de *Retry* con *Exponential Backoff*. Por ejemplo, el módulo `Notifications` reintenta los envíos si el proveedor (SMTP/API HTTP) está caído.
- **Idempotencia:** Al utilizar Eventos de Integración, los consumidores deben gestionar la idempotencia. Si un módulo recibe `UserCreatedIntegrationEvent` dos veces, la segunda invocación es silenciada (sin efectos colaterales).

## Rendimiento y Operación

- **Eventual Consistency Aceptable:** El diseño acepta que los datos entre módulos pueden retrasarse de forma segura. Si HR da de alta a un empleado, IAM tardará unas fracciones de segundo (o más, en caso de caídas de red) en aprovisionarlo.
- **CQRS Ligero (Command Query Responsibility Segregation):** Las consultas complejas o las vistas agregadas deben eludir el modelo de escritura (Aggregate Roots) para realizar *queries* ultra rápidas directamente a la persistencia mediante DTOs u otras tecnologías de lectura veloz.
