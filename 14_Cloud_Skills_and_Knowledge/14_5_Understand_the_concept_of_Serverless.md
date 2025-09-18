| **Inicio**         | **atrás 4**                                                      | **Siguiente 6**                                                                  |
| ------------------ | ---------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| [🏠](../README.md) | [⏪](./14_4_Understand_the_concept_of_Infrastructure_as_Code.md) | [⏩](./14_6_Understand_the_basics_and_general_flow_of_deploying_in_the_cloud.md) |

---

## **Índice**

| Temario                                                                              |
| ------------------------------------------------------------------------------------ |
| [522. ¿Qué es la computación sin servidor?](#522-qué-es-la-computación-sin-servidor) |
| [523. Que es serverless?](#523-que-es-serverless)                                    |

# **Comprender el concepto de Serverless**

## **522. ¿Qué es la computación sin servidor?**

![Comprender el concepto de Serverless](/img/14_Cloud_Skills_and_Knowledge/serverless.jpeg "Comprender el concepto de Serverless")

**Definición breve:** la informática _sin servidor_ es un modelo de computación en la nube donde el proveedor gestiona el aprovisionamiento, la escalabilidad y la operación de la infraestructura; tú despliegas _funciones_ o _servicios_ y sólo pagas por el tiempo/recursos utilizados. El desarrollador se concentra en código y lógica, no en servidores, OS o clusters.

**Idea clave:** _no significa “sin servidores”_ (siempre hay servidores en el provider), sino que **el proveedor abstrae** esa capa operativa.

**Ejemplo conceptual:** en vez de aprovisionar una VM para procesar imágenes, subes una función que se dispara cuando llega un archivo a un bucket; el proveedor ejecuta esa función, escala según demanda y te factura por la ejecución.

### Informática sin servidor **vs.** contenedores — comparación práctica

| Aspecto                |                                          Serverless (FaaS) | Contenedores (Docker + K8s)                                      |
| ---------------------- | ---------------------------------------------------------: | ---------------------------------------------------------------- |
| Abstracción operativa  |                         Muy alta — no gestionas servidores | Media-baja — gestionas contenedores y/o orquestador              |
| Tiempo de vida         |                          Ejecuciones cortas y event-driven | Persistente (pods/servicios)                                     |
| Escalado               |                                  Automático por invocación | Automático (Horizontal Pod Autoscaler) pero configuras límites   |
| Latencia (cold starts) |     Puede haber _cold starts_ en invocaciones infrecuentes | Menor si pods están siempre calientes                            |
| Modelo de facturación  |                    Pago por ejecución / memoria / duración | Pago por instancias/CPU/memoria en uso continuo                  |
| Casos ideales          | Event-driven, tareas cortas, APIs simples, ETL por eventos | Apps con estado parcial, microservicios complejos, cargas largas |
| Control                |                          Menos control fino sobre ambiente | Control total del runtime y networking                           |
| Portabilidad           |                     Menos portable (vendor FaaS specifics) | Alta portabilidad (imágenes Docker)                              |

**Cuándo elegir qué:**

- **Serverless** → APIs de baja/média complejidad, procesado por evento, picos impredecibles, prototipos rápidos.
- **Contenedores** → aplicaciones de larga duración, dependencias complejas, necesidad de control de red, portabilidad entre clouds.

### Informática sin servidor y JavaScript (Node.js)

**Por qué JavaScript/Node.js encaja bien:** event loop no bloqueante, arranque rápido y amplia comunidad de librerías para I/O, ideal para funciones pequeñas y APIs.

**Ejemplo (AWS Lambda, Node.js simple):**

```javascript
// handler.js
exports.handler = async (event) => {
  // event: puede ser S3, API Gateway, SNS, etc.
  const name = event.queryStringParameters?.name || "mundo";
  return {
    statusCode: 200,
    body: JSON.stringify({ message: `Hola ${name}` }),
  };
};
```

- Lo despliegas como función, lo enlazas a **API Gateway** (para HTTP) o a un evento S3 (cuando se sube un archivo).
- Ventajas: despliegue rápido, uso de npm, escala automática.
- Riesgos: _cold starts_ en paquetes grandes, limitaciones de ejecución (duración, memoria), vendor lock-in si usas librerías específicas del proveedor.

**Buenas prácticas con Node.js en serverless:**

- Mantén paquete pequeño (tree-shaking, evitar dependencias pesadas).
- Usa _layers_ o artefactos compartidos para librerías comunes.
- Maneja correctamente timeouts y retries.
- Usa observabilidad (tracing, logs estructurados).

### Función como servicio (FaaS) — la esencia del serverless

**Qué es:** FaaS permite subir funciones (pequeños fragmentos de código) que se ejecutan en respuesta a eventos (HTTP, colas, cambios en storage, cron). Ejemplos de FaaS: AWS Lambda, Azure Functions, Google Cloud Functions.

**Características típicas:**

- Invocación por evento.
- Autoescalado por concurrency/invocaciones.
- Facturación por tiempo de ejecución y memoria asignada.
- Límites por ejecución (p. ej. 15 min, 512MB–10GB según provider).

**Patrones comunes con FaaS:**

- **API backend**: función por endpoint (micro-endpoints).
- **ETL por eventos**: transformar datos cuando llegan a storage.
- **Procesamiento asíncrono**: consumidor de colas para trabajo en background.
- **Cron jobs**: tareas periódicas (scheduled functions).

**Ejemplo de flujo FaaS en una app:**

1. Usuario sube imagen a bucket S3.
2. Evento S3 dispara Lambda que crea thumbnail y guarda metadatos en BD.
3. Lambda notifica a un servicio via SNS.

### Edge computing y serverless en el borde (Edge + FaaS)

**Qué es Edge computing:** ejecución de código lo más cerca posible del usuario o del origen de datos para reducir latencia y mover procesamiento hacia la periferia (CDN edge nodes, gateways IoT, PoPs).

**Edge + serverless:** muchas plataformas ofrecen _serverless at the edge_: funciones ligeras que corren en nodos CDN (Cloudflare Workers, AWS Lambda\@Edge, Fastly Compute\@Edge).

**Beneficios del Edge serverless:**

- Latencia muy baja para personalización en el borde (ej. A/B tests, autenticación, reescrituras de contenido).
- Reducción de tráfico al origen (cache dynamic content at edge).
- Procesamiento pre-analítico en IoT (filtrado, agregación).

**Limitaciones:**

- Entornos runtime más restrictivos (tamaño, API disponibles).
- Estado limitado (stateless o usar KV stores distribuidos).

**Ejemplo:** personalizar HTML en el edge según geolocalización del request antes de devolverlo al usuario.

### Plataforma como servicio (PaaS) — relación con serverless

**Qué es PaaS:** capa intermedia donde el proveedor ofrece plataforma completa (runtime, frameworks, base de datos gestionada) y tú despliegas aplicaciones, sin gestionar VMs. Ejemplos: Heroku, Google App Engine, AWS Elastic Beanstalk.

**Diferencias clave con serverless:**

- **PaaS**: instancia de app “siempre encendida” (o con autoscaling); más control del proceso y entorno; facturación por instancia/uso.
- **Serverless (FaaS)**: funciones event-driven, facturación por ejecución, mayor abstracción operativa.

**Cuándo usar PaaS vs FaaS:**

- **PaaS** → apps web tradicionales con estado parcial, runtimes largos, control de proceso y logs persistentes.
- **FaaS** → workloads event-driven, microtareas, APIs con cold-start aceptable o mitigado.

**Ejemplo combinado:** usar PaaS (Heroku) para app principal y FaaS para tareas asíncronas pesadas (procesado de vídeo).

### Ventajas y limitaciones prácticas del serverless (resumen)

**Ventajas**

- Menor overhead operativo (no gestionas infra).
- Escala automática y granular.
- Pago por uso (económico para cargas intermitentes).
- Time-to-market rápido.

**Limitaciones**

- _Cold starts_ (latencia al arrancar función inactiva).
- Límites de ejecución (tiempo/memoria).
- Depuración y testing locales pueden ser más difíciles.
- Observabilidad/monitorización y tracing requieren herramientas.
- Riesgo de vendor lock-in (APIs específicas del proveedor).

### Patrones de arquitectura serverless (listas prácticas)

- **Backend for Frontend (BFF):** API Gateway → funciones por endpoint → DB managed.
- **Event-driven microservices:** productor → cola/event bus (SNS/EventBridge) → funciones consumidoras.
- **Stream processing:** eventos Kinesis/PubSub → funciones que procesan por lotes.
- **Orquestación larga duración:** state machines / step functions (coordinate multiple functions with retries/compensation).

### Mini-ejemplos y snippets (rápidos)

**HTTP function (Google Cloud Functions, Node.js):**

```javascript
exports.helloHttp = (req, res) => {
  const name = req.query.name || "mundo";
  res.send(`Hola ${name}`);
};
```

**Python AWS Lambda (S3 trigger):**

```python
import json
import boto3

def lambda_handler(event, context):
    for record in event['Records']:
        bucket = record['s3']['bucket']['name']
        key = record['s3']['object']['key']
        # procesar archivo...
    return {'statusCode': 200, 'body': json.dumps('Procesado')}
```

### Mejores prácticas para diseñar apps serverless

1. **Mantén funciones pequeñas y enfocadas** (single responsibility).
2. **Evita dependencias pesadas** en cold paths; usa layers donde sea apropiado.
3. **Diseño idempotente**: funciones pueden ejecutarse varias veces.
4. **Timeouts y retries**: define políticas claras y backoffs exponenciales.
5. **Observabilidad**: logs estructurados, tracing distribuido (X-Ray, OpenTelemetry).
6. **Gestión de secretos**: Secret Manager / KMS, no variables env en repos.
7. **Pruebas locales y CI**: integrar pruebas unitarias y de integración con emuladores si existen.
8. **Políticas de seguridad**: least-privilege IAM roles por función.
9. **Estrategia de cold start**: warmers (no recomendado a gran escala), runtimes más rápidos (Go/Node), planificar SLAs.

### Mini-proyecto sugerido (práctico, 1–2 horas)

**Objetivo:** construir una API serverless que procese formularios y almacene datos.

- **Stack sugerido:** AWS (API Gateway + Lambda Node.js + DynamoDB) o GCP (Cloud Functions + Firestore).
- **Features:** endpoint POST `/submit` que valida payload, lo guarda en DB y publica un mensaje a SNS/Topic para procesamiento asíncrono.
- **Qué aprendes:** desplegar FaaS, configurar triggers, manejo de IAM roles mínimos y pruebas con herramientas como Postman.

### Conclusión rápida

- **Serverless** = mayor abstracción operativa + facturación por uso + excelente para workloads event-driven.
- **Contenedores** = más control, mejor para aplicaciones de larga duración y portabilidad.
- **JavaScript/Node.js** es una opción natural para serverless, pero no la única.
- **FaaS, Edge y PaaS** son capas relacionadas que permiten elegir trade-offs entre control, latencia y simplicidad.

---

[🔼](#índice)

---

## **523. Que es serverless?**

**Respuesta corta:** _Serverless_ (informática sin servidor) es un modelo de computación en la nube donde **el proveedor gestiona la infraestructura** (servidores, escalado, parches) y tú entregas **código o servicios** que se ejecutan bajo demanda. Pagas por la ejecución efectiva (tiempo/recursos usados) en lugar de por servidores siempre activos.
No significa “no hay servidores”; significa _“no te preocupes por ellos”_.

### 1. Componentes clave del modelo serverless

- **FaaS (Function as a Service):** ejecutas funciones cortas desencadenadas por eventos (AWS Lambda, Azure Functions, Google Cloud Functions).
- **BaaS (Backend as a Service):** servicios gestionados (bases de datos serverless, autenticación, colas, almacenamiento) que sustituyen código backend. Ej.: DynamoDB, Firebase Auth, S3.
- **Edge / Functions at the edge:** funciones ligeras desplegadas en CDN/PoPs para latencia ultra-baja (Cloudflare Workers, Lambda\@Edge).
- **Orquestación serverless:** coordinadores para flujos largos (AWS Step Functions, Durable Functions).

### 2. ¿Cómo funciona (flujo típico)?

1. **Definición:** escribes una función pequeña (handler) y la despliegas.
2. **Trigger/evento:** la función se invoca por un evento: petición HTTP, subida a storage, mensaje en cola, cron, streaming.
3. **Ejecución aislada:** el proveedor arranca el runtime, ejecuta la función y cobra por la duración y memoria.
4. **Escalado automático:** si llegan 1000 eventos simultáneos, el proveedor escala a 1000 instancias (según límites).
5. **Estado:** las funciones suelen ser _stateless_; el estado se guarda en bases de datos o caches serverless.

### 3. Ejemplo práctico (muy común): procesamiento de imágenes

Flujo: Usuario sube imagen → Evento S3 → Lambda procesa thumbnail → Guarda en S3 y registra metadatos en DynamoDB.

**Snippet Node.js (AWS Lambda) – API simple**

```javascript
// handler.js
exports.handler = async (event) => {
  const name = event.queryStringParameters?.name || "mundo";
  return { statusCode: 200, body: JSON.stringify({ message: `Hola ${name}` }) };
};
```

Este `handler` lo puedes conectar a API Gateway para exponer un endpoint HTTP sin gestionar servidores.

**Snippet Python (trigger S3)**

```python
import boto3
def lambda_handler(event, context):
    for r in event['Records']:
        bucket = r['s3']['bucket']['name']
        key = r['s3']['object']['key']
        # Procesar archivo...
    return {'statusCode':200}
```

### 4. Serverless vs Contenedores vs PaaS (comparación rápida)

- **Serverless (FaaS):** máxima abstracción, facturación por ejecución, ideal para tareas event-driven y picos impredecibles. Limitaciones: tiempos máximos, cold starts, menos control del runtime.
- **Contenedores (Docker + K8s):** control y portabilidad, mejor para procesos de larga duración y dependencias complejas. Necesitas administrar orquestador (o usar servicios gestionados).
- **PaaS (Heroku, App Engine):** equilibrio: menos gestión que un contenedor puro, más control que FaaS; instancias más persistentes.

Elige según: duración de la tarea, latencia requerida, control/portabilidad y modelo de costes.

### 5. Casos de uso típicos y patrones arquitectónicos

- **API Backends (HTTP + microendpoints)** via API Gateway → funciones.
- **ETL / Procesamiento por lotes**: triggers desde storage/colas.
- **Trabajos programados (cron)**: funciones programadas para tareas periódicas.
- **Procesamiento de streams**: funciones que consumen Kinesis/PubSub.
- **Orquestación de flujos**: Step Functions coordina múltiples funciones con branching/retry.
- **Edge personalization / caching**: manipular respuestas en el CDN antes de llegar al origen.

### 6. Beneficios principales

- **T0 / time-to-market**: despliegues muy rápidos.
- **Costes eficientes para cargas intermitentes** (pagas por ejecución).
- **Escalado automático** sin configuración compleja.
- **Menos complejidad operativa** (no patching, no servers).
- **Alta fiabilidad y disponibilidad** por la plataforma del proveedor.

### 7. Limitaciones y riesgos importantes

- **Cold starts**: latencia inicial en funciones inactivas.
- **Límites de ejecución**: max time, memoria y concurrencia por función.
- **Observabilidad**: tracing y debugging distribuidos requieren herramientas (X-Ray, OpenTelemetry).
- **Vendor lock-in**: usar APIs/servicios propios del proveedor puede dificultar la migración.
- **Costos inesperados**: muy barato en picos bajos, pero mal diseñado puede generar facturas altas (ej.: bucles o ejecuciones masivas).
- **Testing local**: emular el entorno cloud puede ser más complejo que pruebas en contenedores.

### 8. Buenas prácticas (resumen accionable)

- **Funciones pequeñas y con una sola responsabilidad.**
- **Idempotencia**: las funciones deben tolerar reintentos sin efectos duplicados.
- **Least privilege**: roles/Políticas IAM mínimos.
- **Externalizar estado**: usar DBs serverless (DynamoDB, Firestore, Aurora Serverless) o caches (ElastiCache).
- **Observabilidad completa**: logs estructurados, métricas y tracing distribuido.
- **Gestión de secretos**: Secret Manager / KMS, no variables en código.
- **Pruebas y CI/CD**: integrar tests unitarios y de integración; usar frameworks/CLI (SAM, Serverless Framework, Functions Core Tools).
- **Controlar costes**: alerts/budgets, throttling, limitar concurrencia si procede.

### 9. Herramientas, proveedores y servicios relacionados

- **AWS:** Lambda, API Gateway, Step Functions, DynamoDB, S3, Lambda\@Edge.
- **Azure:** Functions, Durable Functions, Event Grid, Cosmos DB.
- **GCP:** Cloud Functions, Cloud Run (serverless containers), Pub/Sub, Firestore.
- **Edge:** Cloudflare Workers, Fastly Compute\@Edge.
- **Frameworks y tooling:** Serverless Framework, AWS SAM, Terraform, Pulumi, OpenFaaS, Knative (serverless sobre k8s).

### 10. Migración práctica a serverless (ejemplo de estrategia)

1. **Identifica candidatos**: tareas event-driven, cron jobs, endpoints con baja latencia crítica.
2. **Refactor ligero**: extrae la lógica principal a funciones pequeñas desacopladas.
3. **Sustituye servicios gestionados**: usar DB serverless y colas en vez de máquinas largas.
4. **Automatiza despliegues**: IaC + pipelines (Terraform/SAM + GitHub Actions).
5. **Prueba a escala**: load testing y validación de costes.
6. **Monitorea y optimiza**: observe cold starts, tiempos de ejecución, coste por invocación.

### 11. Ejemplo de arquitectura simple (thumbnail processor)

1. Cliente sube imagen → **S3**
2. Evento S3 → **Lambda** (procesa thumbnail)
3. Lambda guarda thumbnail → **S3** y metadatos → **DynamoDB**
4. Notifica al usuario vía **SNS** o **WebSocket** si procede

Beneficios: escalado automático al subir miles de imágenes; sólo se paga mientras se procesan.

### 12. Recomendación final y próximos pasos

Serverless es ideal cuando quieres **velocidad, escalado automático y menos operaciones**. No obstante, si tu workload exige control fino, tiempos de ejecución largos o portabilidad total, contempla contenedores o híbridos (Cloud Run, K8s + Knative).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 4**                                                      | **Siguiente 6**                                                                  |
| ------------------ | ---------------------------------------------------------------- | -------------------------------------------------------------------------------- |
| [🏠](../README.md) | [⏪](./14_4_Understand_the_concept_of_Infrastructure_as_Code.md) | [⏩](./14_6_Understand_the_basics_and_general_flow_of_deploying_in_the_cloud.md) |
