# [UC-NOT-02] Gestionar Plantilla de Notificación

**Módulo:** Notifications  
**Actor Principal:** Administrador de Sistemas / Operador  
**Descripción:** Crea, modifica y versiona el formato semántico (HTML/Text) de un correo parametrizado utilizado en interacciones del ERP.

---

## 1. Pre-condiciones

* El Administrador debe estar autenticado con nivel jerárquico global o roles `templates:write`.
* En un escenario de actualización, el registro en curso no debe haber sido borrado lógicamente.
* La sintaxis del cuerpo HTML está bien formada.

## 2. Flujo Principal (Happy Path)

1. El Admin envía los datos de la plantilla (vía `POST /api/templates`, comando gRPC `UpdateTemplate` o Interfaz Administrativa).
2. El sistema parsea el Body buscando etiquetas de interpolación (ej. `{{firstName}}`).
3. El sistema instancia la Entidad `Template` en su Versión 1 (o Versión X+1) y la asigna como `Active`.
4. El Repositorio guarda atómicamente la configuración.
5. El sistema confirma la operación (enviando `201 Created`  / `200 OK` o respuesta gRPC exitosa).

## 3. Flujos Alternativos / Excepciones

* **A1 - Placeholders Corruptos:** Si se detectan tags abiertos permanentemente o mal formados, el sistema aborta y retorna un error de validación (ej. `400 Bad Request`).
* **A2 - Historial inmutable (Versionamiento Automático):** Si se modifica una plantilla existente, el dominio sube la revisión a `Versión X+1`. La versión anterior queda `Archived`.

## 4. Post-condiciones

* **Éxito:** La Base de Datos recibe un nuevo registro de Template Inmutable. El sistema de caché puede invalidarse para reflejar el cambio.
* **Fallo:** La configuración de plantillas permanece inalterada.

## 5. Eventos de Dominio (Domain & Integration Events)

* **Domain Event:** `NotificationTemplatePublishedDomainEvent`.
* **Integration Event:** Ninguno (las definiciones de plantillas están encapsuladas dentro del módulo de Notifications).

[back](./index.md)
