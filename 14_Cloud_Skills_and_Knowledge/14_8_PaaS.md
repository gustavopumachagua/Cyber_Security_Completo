| **Inicio**         | **atrás 7**          | **Siguiente 9**      |
| ------------------ | -------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./14_7_SaaS.md) | [⏩](./14_9_IaaS.md) |

---

## **Índice**

| Temario                                    |
| ------------------------------------------ |
| [528. ¿Qué es PaaS?](#528-qué-es-paas)     |
| [529. PaaS explicado](#529-paas-explicado) |

# **PaaS**

## **528. ¿Qué es PaaS?**

![PaaS](/img/14_Cloud_Skills_and_Knowledge/PaaS.webp "PaaS")

PaaS (Platform as a Service) es un modelo de computación en la nube que entrega **una plataforma completa** —runtime, middleware, sistemas de despliegue, base de datos gestionada y herramientas de desarrollo— para que equipos construyan, desplieguen y administren aplicaciones sin ocuparse del sistema operativo, servidores o infraestructura subyacente. Permite centrarte en el **código y la lógica de negocio**, no en la operación del stack.

### 1. Definición (formal)

PaaS es un servicio gestionado por un proveedor de nube que ofrece:

- Entorno de ejecución (runtimes para Java, Node.js, Python, .NET, etc.).
- Servicios de datos (bases de datos, caches, colas) con gestión automática.
- Herramientas de CI/CD, escalado automático y monitoreo integrado.
- Interfaces web/CLI para desplegar y gestionar aplicaciones.

El proveedor administra la infraestructura, parches, alta disponibilidad y gran parte de la seguridad operativa.

### 2. ¿Cómo funciona PaaS? (flujo y componentes)

#### Flujo típico

1. **Desarrollas** la aplicación (por ejemplo, una API en Node.js).
2. **Empaquetas** (o no: algunos PaaS toman el repo directamente).
3. **Despliegas** con un comando, push al Git remoto del proveedor o desde CI/CD.
4. El **proveedor** aprovisiona contenedores/instancias, configura el runtime, conecta servicios gestionados (DB, cache), aplica certificados TLS y comienza a servir el tráfico.
5. **Escala** automáticamente según políticas y te entrega métricas/alertas.

#### Componentes habituales que ofrece un PaaS

- Runtime gestionado (Node, Python, Java, Ruby, .NET).
- Buildpacks / builders (construcción automática de artefactos).
- Administración de dependencias y variables de entorno.
- Balanceo de carga y endpoints HTTPS con certificación automática (ACM/Let’s Encrypt).
- Servicios “adjuntos”: bases de datos (managed SQL/NoSQL), Redis, colas, almacenamiento.
- Consola/CLI para logs, métricas y escalado manual.
- Integraciones de CI/CD o despliegue desde Git.

### 3. Ventajas de PaaS (con ejemplos prácticos)

#### Velocidad y simplicidad

- **Ejemplo:** con Heroku, `git push heroku main` construye y despliega tu app; no configuras servidores.
- Montas un MVP en horas/días.

#### Menos operación

- No gestionas OS, parches ni runtime. El proveedor hace upgrades y parches de seguridad.

#### Escalado automático

- **Ejemplo:** Azure App Service escala instancias según CPU/requests sin que tú aprietes botones.

#### Servicios integrados

- Bases de datos y caches configurables desde la misma consola (ej.: add-on de PostgreSQL en Heroku).

#### Buen para equipos pequeños y prototipos

- Reduce la necesidad de expertos en infra.

### 4. Inconvenientes / limitaciones (a considerar)

- **Menor control**: no puedes customizar kernel ni instalar software arbitrario de bajo nivel.
- **Costo**: para cargas muy estables y altas, IaaS o Kubernetes optimizado puede salir más barato.
- **Lock-in potencial**: uso intensivo de add-ons/proveedores específicos puede dificultar migración.
- **Limitaciones de runtime**: límites de tiempo, tamaño de paquetes o procesos en background según proveedor.
- **Complejidad para arquitecturas muy específicas**: micro-control de red, hardware aceleradores, control de latencia extremo.

### 5. PaaS vs. IaaS vs. SaaS — comparación rápida

- **IaaS (Infra as a Service):** tú gestionas OS, middleware y apps (p. ej. EC2, VMs). Más control, más operación.
- **PaaS:** gestionan runtime y plataforma; tú solo subes código y config. (p. ej. Heroku, Azure App Service, Google App Engine).
- **SaaS:** aplicación lista para usar; ni siquiera desarrollas (p. ej. Salesforce).

### 6. Escenarios comunes (cuándo usar PaaS) — con ejemplos

1. **MVP / prototipos**

   - Montar una app de demostración rápidamente (ej. e-commerce mínimo).
   - _Ejemplo:_ desplegar una app Node/Express en Heroku para validar mercado.

2. **Aplicaciones web y APIs empresariales**

   - Apps con requisitos estándar, sin necesidad de control a nivel OS.
   - _Ejemplo:_ una API REST para gestión de inventarios desplegada en Azure App Service con Azure SQL como DB.

3. **Microservicios ligeros**

   - Servicios que no requieren orquestación compleja.
   - _Ejemplo:_ varios microservicios en Google App Engine con Cloud SQL para persistencia.

4. **Aplicaciones que necesitan integraciones rápidas**

   - Acceso a add-ons (email, cache, monitorización) de forma inmediata.
   - _Ejemplo:_ usar un add-on de Redis y Sendgrid en Heroku.

5. **Equipos con poca ingeniería de operaciones (DevOps limitado)**

   - Permite focus en producto y lógica de negocio.

-

### 7. Proveedores y ejemplos concretos de PaaS

- **Heroku** — muy usado para startups y MVPs (git push deploy, add-ons).
- **Google App Engine** — PaaS gestionado por Google, con soporte para varios runtimes y escalado automático.
- **Azure App Service** — despliegue de web apps, APIs y mobile backends con integración Azure SQL, DevOps.
- **Red Hat OpenShift (PaaS/Kubernetes managed)** — PaaS empresarial sobre Kubernetes con más control.
- **Platform.sh, Render, Fly.io** — alternativas con modelos modernos (git-based, containers).

### 8. Ejemplo práctico: desplegar una app Node.js en Heroku (paso a paso resumido)

1. Crear app simple `index.js` (Express):

```javascript
const express = require("express");
const app = express();
const port = process.env.PORT || 3000;
app.get("/", (req, res) => res.send("Hola desde PaaS!"));
app.listen(port, () => console.log("Listening on", port));
```

2. `Procfile`:

```
web: node index.js
```

3. Inicializar git y desplegar:

```bash
git init
heroku create mi-app-ejemplo
git add .
git commit -m "Init"
git push heroku main
heroku open
```

Heroku detecta el runtime, construye e inicia la app. Puedes añadir una base de datos:

```bash
heroku addons:create heroku-postgresql:hobby-dev
```

### 9. Pautas y buenas prácticas al usar PaaS

- **Gestiona configuraciones con variables de entorno** (do not hardcode secrets).
- **Usa add-ons gestionados** para DB y caches, pero documenta escape path si quieres migrar.
- **Define límites de recursos y autoscaling** según pruebas de carga.
- **Versiona y automatiza despliegues**: integra PaaS con pipeline CI/CD.
- **Monitorea costos**: PaaS puede escalar y generar factura mayor sin control.
- **Plan de migración**: evita lock-in documentando cómo exportar datos y reconstruir la app en Kubernetes/IaaS si hacen falta.
- **Seguridad**: habilita TLS, revisa roles y accesos, usa back-ups automáticos.

### 10. Costos: qué esperar

- **Modelo de precio:** instancias por unidad (dynos, app service plan), niveles (free/dev/prod), add-ons con precio separado (databases, alerts).
- **Observación:** PaaS reduce el costo de operación humano pero puede ser más caro por unidad de cómputo que IaaS a gran escala; evaluar TCO según uso y equipo.

### 11. Caso real — migración de PaaS a Kubernetes (cuando conviene)

- **Por qué migrar:** crecimiento masivo, necesidad de control fino, optimización de costes, requisitos regulatorios.
- **Qué conservar:** arquitectura por microservicios, pipelines CI/CD e IaC.
- **Cómo hacerlo:** exportar datos, crear Dockerfiles, definir manifests/Helm charts, aprovisionar cluster (EKS/GKE/AKS) y recrear servicios. Mantén un plan de rollback y validación canary.

### 12. Resumen / Conclusión

PaaS es ideal cuando quieres **acelerar el desarrollo**, reducir la carga operativa y lanzar aplicaciones rápidamente sin gestionar servidores. Es especialmente valioso para prototipos, startups y equipos con menos capacidad DevOps. Sin embargo, para cargas masivas altamente optimizadas o necesidades de control extremo, IaaS/Kubernetes o soluciones híbridas pueden ser más apropiadas.

---

[🔼](#índice)

---

## **529. PaaS explicado**

PaaS (Platform as a Service) es un modelo en la nube que te entrega **una plataforma completa** —runtime, middleware, servicios gestionados, herramientas de despliegue y operación— para que te concentres en el **código y la lógica de negocio**, no en servidores ni en la capa OS. El proveedor administra la infraestructura subyacente; tú subes la app y la gestionas a nivel de aplicación.

**¿Cómo funciona PaaS (en la práctica)?**

- Tú desarrollas la aplicación (por ejemplo, una API en Node.js o una web en Python).
- La subes al PaaS (git push / CLI / panel / pipeline).
- El proveedor construye el artefacto (buildpacks/containers), provisiona instancias o contenedores, configura el runtime, aplica certificados TLS y enruta el tráfico.
- El proveedor también ofrece servicios “adjuntos” (DB gestionada, cache, colas, logs) que se conectan fácilmente a tu app.
- Escalado, parches del sistema operativo y alta disponibilidad los maneja el proveedor.

**Componentes típicos que te entrega un PaaS**

- Runtime gestionado (Node, Python, Java, .NET, etc.).
- Buildpacks / builders (construcción automática desde repo).
- Servicios gestionados: bases de datos, Redis, colas, almacenamiento.
- CI/CD (integración con despliegue automático o hooks).
- Gestión de variables de entorno y secretos.
- HTTPS automático y manejo de certificados.
- Consola/CLI para logs, métricas y escalado.

**Ventajas (por qué usar PaaS)**

- Arranque rápido: despliegas un MVP en horas/días.
- Menos operación: no gestionas OS, parches ni provisioning.
- Escalado sencillo: autoscaling transparente.
- Integración fácil con servicios gestionados.
- Buena opción para equipos pequeños o proyectos que buscan velocidad.

**Inconvenientes (qué limitará PaaS)**

- Menor control sobre infraestructura y runtime.
- Posible mayor coste por unidad de cómputo frente a IaaS a gran escala.
- Riesgo de vendor lock-in si usas add-ons propietarios.
- Restricciones en personalización a nivel SO o drivers especiales.

**PaaS vs IaaS vs Serverless (rápido)**

- **IaaS:** tú administras VM/OS (ej. EC2). Máximo control, máxima operación.
- **PaaS:** tú subes código; el proveedor gestiona la plataforma (ej. Heroku, App Service). Balance entre control y simplicidad.
- **Serverless (FaaS):** aún más abstracto; ejecutas funciones event-driven con facturación por ejecución (ej. Lambda). Mejor para tareas cortas y event-driven.

**Casos de uso / ejemplos reales**

1. **MVP o prototipo de startup:** quiero validar mercado sin contratar DevOps → despliego en Heroku/Render.
2. **Aplicación web empresarial estándar:** APIs, panel administrativo, base de datos gestionada → Azure App Service + Azure SQL.
3. **Microservicio ligero no crítico:** desplegar varios servicios con rápida integración a add-ons (Redis, email providers).
4. **Carga variable con poca necesidad de control infra:** blog, tienda pequeña, dashboards internos.

**Ejemplos prácticos — comandos rápidos**

_Desplegar una app Node.js en Heroku (muy resumido):_

```bash
# prep
git init
heroku create mi-app-ejemplo

# push (Heroku detecta runtime, buildpacks y despliega)
git add .
git commit -m "Init"
git push heroku main

# añadir Postgres manejado (addon)
heroku addons:create heroku-postgresql:hobby-dev
```

_Desplegar en Azure App Service (CLI simplificado):_

```bash
# crear resource group y app service plan
az group create -n RG-ejemplo -l eastus
az appservice plan create -n plan-ejemplo -g RG-ejemplo --sku B1

# crear webapp y desplegar desde local git
az webapp create -n mi-app-azure -g RG-ejemplo --plan plan-ejemplo
az webapp deployment source config-local-git -n mi-app-azure -g RG-ejemplo
# la CLI te devuelve una URL git para push
```

**Arquitectura PaaS típica (texto/diagrama sencillo)**

```
Usuarios -> CDN -> PaaS Load Balancer -> App Runtimes (containers managed)
                               ↳ Managed DB (Postgres/Aurora/SQL)
                               ↳ Cache (Redis)
                               ↳ Queue (Rabbit/SQS)
Logs/Metrics -> Observability (provider or 3rd party)
```

**Buenas prácticas al usar PaaS**

- **Variables de entorno y secretos**: no guardar credenciales en código; usar el vault/secret store del PaaS.
- **Despliegues automatizados**: integrar con CI/CD para builds reproducibles y tests previos.
- **Ten en cuenta limits y SLAs**: conoce timeouts, memoria y concurrencia de tu PaaS.
- **Plan de backup y exportación de datos**: diferenciar entre la app (fácil) y datos (exportaciones regulares).
- **Estrategia anti-lock-in**: documenta cómo reconstruir la app en contenedores o en Kubernetes; usa abstractions estándar (containers, env vars, SQL estándar).
- **Observabilidad**: activa logs, tracing y alertas; define SLOs y on-call.

**¿Cuándo elegir PaaS? (criterios prácticos)**

- Necesitas lanzar rápido y con mínimo equipo DevOps → **sí, PaaS**.
- Requieres control extremo de red, kernel o hardware → **no, IaaS/Kubernetes**.
- Tienes funciones event-driven pequeñas → **considera serverless** si encajan límites.
- Quieres escalar pero priorizas tiempo al mercado sobre optimización de costes → **PaaS**.

**Migración de PaaS a Kubernetes / IaaS (cómo y por qué)**

- Motivos: optimización de costes, control de infra, dependencias especiales.
- Pasos:

  1. Dockerizar la app (crear Dockerfile).
  2. Preparar manifiestos k8s/Helm (Deployments, Services, ConfigMaps, Secrets).
  3. Probar en cluster dev (minikube / EKS/GKE/AKS).
  4. Migrar datos y probar canary.
  5. Automatizar IaC y CI/CD.

- Mantén un plan de rollback y pruebas de performance.

**Resumen ejecutivo**

PaaS ofrece una plataforma gestionada que acelera el desarrollo y despliegue de aplicaciones al abstraer la infraestructura subyacente; es ideal para MVPs, aplicaciones web estándar y equipos con poca capacidad operativa, porque reduce la carga de operaciones, permite escalar de forma automática y facilita el acceso a servicios gestionados (DB, cache, colas). Sin embargo, si se necesita control granular, optimización de costes al máximo o requisitos de hardware/latencia extremos, conviene evaluar IaaS o Kubernetes.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 7**          | **Siguiente 9**      |
| ------------------ | -------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./14_7_SaaS.md) | [⏩](./14_9_IaaS.md) |
