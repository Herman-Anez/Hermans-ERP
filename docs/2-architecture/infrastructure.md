# Infraestructura Arquitectónica

Este documento expone los componentes lógicos de infraestructura necesarios para soportar el diseño modular e impulsado por eventos del ERP Core.

## Componentes Fundamentales

### 1. API Gateway / Punto de Entrada

Todos los clientes interactúan con un *API Gateway* o *BFF (Backend For Frontend)* en lugar de conectarse directamente a cada módulo lógico.

- **Responsabilidades:** Enrutamiento HTTP, terminación SSL, validación inicial del token de acceso y protección contra tráfico malicioso (*Rate Limiting*).

### 2. Persistencia Aislada (Data Sovereignty)

Para garantizar la independencia estipulada en los estándares, los *Bounded Contexts* (HR, IAM, Notifications) no comparten esquemas SQL o colecciones NoSQL en tiempo de ejecución.

- **Ventajas:** Cada módulo elige el motor de bases de datos que mejor le convenga.
- **Referencia ADR:** [20260404-data-sovereignty-database-per-bounded-context](./adrs/20260404-data-sovereignty-database-per-bounded-context.md)

### 3. Bus de Mensajes o Event Broker

Un canal asíncrono robusto (ej. RabbitMQ, Apache Kafka, AWS SNS/SQS o Azure Service Bus) permite la coreografía de eventos.

- **Estructura lógica recomendada:** Implementación de intercambios tipo *Topic* o de *Pub/Sub*, permitiendo que el emisor carezca de conocimiento explícito sobre todos sus consumidores.
- **Durabilidad:** Los mensajes deben encolarse de forma durable (*Durable Queues*/DLQ) para mitigar temporales indisponibilidades del consumidor.

### 4. Proveedores Externos Encapsulados (Adapter Pattern)

- **Ej. Envío de Mails:** Soluciones como SendGrid o AWS SES son empleadas exclusivamente dentro de `Notifications` a través de interfaces adaptadoras. Los demás módulos están ajenos a estas tecnologías externas.

## Visión General a Futuro

Según el *C4 Model* o el diagrama de contenedores proyectado, el despliegue de estos elementos podría derivar en microservicios independientes o un monolito modular (*Modular Monolith*), donde, aunque desplieguen juntos, la arquitectura de código imponga fronteras infranqueables de proceso/memoria. La decisión a corto plazo se detallará en posteriores iteraciones de Arquitectura o ADRs.
