# [UC-HR-05] Buscar y Ver Perfil de Empleado

**Módulo:** Human Resources  
**Actor Principal:** Cualquier Empleado / Manager / Gestor HR  
**Descripción:** Operación de lectura (CQRS) transversal para navegar el directorio de talento o consultar la ficha completa de un empleado en la organización. El resultado está condicionado a los permisos del espectador.

---

## 1. Pre-condiciones

* El usuario tiene acceso legítimo al ERP.
* Estricta delegación de Data Masking (Enmascaramiento).

## 2. Flujo Principal (Happy Path)

1. El Actor emite un `GET /api/hr/employees/{id}` o una búsqueda paginada en el directorio general.
2. El sistema identifica el `UserId` subyacente en el JWT.
3. El módulo HR recupera el modelo de lectura (Read Model / Proyección) del Employee.
4. **Filtro de Datos por RBAC:** 
   * Si el que mira es un "Compañero", solo renderiza: Nombre, Puesto, Email de Trabajo, y Departamento.
   * Si el que mira es el "Manager Directo", añade: Fecha de Alta, Calendario de Vacaciones.
   * Si el que mira es "Gestor HR", expone el modelo irrestricto: Salario histórico, TaxId, Contratos, etc.
5. El sistema devuelve un HTTP 200 OK con el DTO (Data Transfer Object) filtrado a la medida de la seguridad.

## 3. Flujos Alternativos / Excepciones

* **A1 - Offboarded Employee (404 Not Found / 403 Forbidden):** Si alguien busca el perfil de alguien que ya fue despedido, el sistema lo suprime de las búsquedas al público, y sólo permite visualizarlo para fines de auditoría a gestores autorizados.
* **A2 - Exceso de Carga Masiva (429 Too Many Requests):** Al ser una operación pública usada como directorio (autocompletados en todo el ERP), aplica de estrictos controles de cache (Redis) y rate-limiters.

## 4. Post-condiciones

* Transacción pasiva. Lectura en memoria/proyecciones, 0 mutaciones en base de datos.
* Respeto absoluto dictaminado a la Ley de Privacidad de Datos.

## 5. Eventos de Dominio (Domain & Integration Events)

* Cero eventos publicados. Lectura de estado.

[back](./index.md)
