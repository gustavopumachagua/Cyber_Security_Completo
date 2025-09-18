| **Inicio**         | **atrás 5**                                   | **Siguiente 7**                             |
| ------------------ | --------------------------------------------- | ------------------------------------------- |
| [🏠](../README.md) | [⏪](./2_5_Installation_and_Configuration.md) | [⏩](./2_7_Navigating_using_GUI_and_CLI.md) |

---

## **Índice**

| Temario                                                              |
| -------------------------------------------------------------------- |
| [48. Importancia de las versiones](#48-importancia-de-las-versiones) |
| [49. Comprender las diferencias](#49-comprender-las-diferencias)     |

---

# **Diferentes versiones y diferencias**

## **48. Importancia de las versiones**

### ¿Por qué importan las versiones? (resumen práctico)

- **Reproducibilidad:** recuperar exactamente el binario / container / entorno que funcionó.
- **Compatibilidad:** gestionar cambios sin romper a usuarios/otras piezas del sistema.
- **Trazabilidad / auditoría:** saber qué código/config estuvo en producción en un incidente.
- **Rollbacks seguros:** volver a versiones anteriores verificadas.
- **Coordinación entre equipos:** frontend/ backend/infra sincronizan por versiones.
- **Gestión de parches y seguridad:** distinguir hotfixes de cambios mayores.
- **Automatización:** CI/CD, deployment pipelines y scanners usan versiones para decisiones.

### Esquemas de versionado comunes (qué son y cuándo usarlos)

#### 1. **Semantic Versioning (SemVer)** — el más usado

Formato: `MAJOR.MINOR.PATCH` (ej.: `2.4.1`)

- **MAJOR**: incompatible con versiones previas (breaking changes).
- **MINOR**: añade funcionalidad hacia atrás compatible.
- **PATCH**: arreglos y parches compatibles.

**Ejemplo:**

- `1.4.2` → `1.5.0` agrega una API nueva compatible.
- `1.5.0` → `2.0.0` rompe endpoints antiguos → requiere coordinación.

Pre-releases y metadata: `1.2.0-alpha.1`, `2.0.0+build.20250829`.

#### 2. **Calendar Versioning (CalVer)**

Formato: `YYYY.MM.DD` o `YYYY.MINOR` (ej.: `2025.08`, `2025.08.29`). Útil para releases regulares (monthly/quarterly).

**Ejemplo:** Ubuntu usa `22.04` (Año.Mes).

#### 3. **Sequential / Build numbers**

Simple contador `build-1534` o `r2025-123`. Fácil en pipelines CI.

#### 4. **Hash-based**

Referirse al commit: `git sha1` (`a1b2c3d`). Útil para reproducir builds exactos; lo combinamos con tags semánticos.

### Deep dive: Semantic Versioning (práctico)

Reglas clave y ejemplos:

- Cambio compatible menor/patch: `1.2.3` → `1.2.4` (fix) o `1.3.0` (feature).
- Breaking change: `1.2.3` → `2.0.0`.
- Pre-release: `2.0.0-rc.1`, `2.0.0-beta.2`.
- Build metadata (no afecta prioridad): `2.0.0+20250829`.

**Ejemplo real de breaking change (API):**

- v1 `GET /users` devolvía `{id,name}`
- v2 cambia a `{userId,fullName}` → subir MAJOR a `2.0.0` y documentar migración.

### Versiones de dependencias (por qué cuidarlas)

#### Ranges vs pinning

- `^1.2.3` (npm): permite `>=1.2.3 <2.0.0` — acepta minor/patch.
- `~1.2.3`: permite `>=1.2.3 <1.3.0` — acepta patch solo.
- `1.2.3` (exacto): solo esa versión.

**Problema común:** `^` en producción puede introducir cambios inesperados.

**Buenas prácticas:** usar _lockfiles_ (`package-lock.json`, `poetry.lock`, `Pipfile.lock`) y CI que pruebe actualizaciones automáticas.

**Ejemplo pip:**

- `requirements.txt` con pines: `Django==4.2.1`
- Para desarrollo: `Django>=4.2,<5.0`

### Versionado de APIs — estrategias y ejemplos

Opciones comunes:

1. **URI versioning**: `GET /v1/users` vs `/v2/users`. (claro y visible)
2. **Header versioning**: `Accept: application/vnd.myapi.v2+json` (menos "sucio" en URLs).
3. **Query param**: `/users?version=2` (útil para pruebas).
4. **Content negotiation / media type**.

**Recomendación:** para cambios rompientes, usa `v2` en la URI o mediante header y mantén documentación y migración guiada.

**Ejemplo:** documenta en el changelog campos removidos/renombrados y proporciona un adapter de compatibilidad `v1-client` → `v2-backend`.

### Versionado de bases de datos — migraciones

Herramientas: Flyway, Liquibase, Alembic, Rails migrations.

**Convención Flyway:** `V1__create_users.sql`, `V2__add_email_index.sql`

- Aplica migraciones ordenadas y guarda checksum.
- Incluye _down/rollback_ cuando sea posible.

**Buenas prácticas:**

- Migraciones pequeñas y atomizadas.
- Evita ALTERs bloqueantes en tablas grandes; usa _online schema change_ o estrategia de doble escritura/lectura.
- Prueba rollback y migración en staging con producción-like data.

**Ejemplo (Flyway):**

```text
V1__init.sql
V2__add_user_email.sql
```

### Versionado de infraestructura (IaC)

- Versiona módulos Terraform: `v1.2.0` del módulo `network`.
- Usa registries y semver para módulos compartidos.
- Controla el estado (`state`) y cambia con plan/review.

**Ejemplo:** actualizar módulo `db` de `v1.3.0` a `v2.0.0` → revisar breaking changes (nuevos parámetros).

### Reproducible builds e imágenes inmutables

- **No uses `latest`** en Docker para producción. `myapp:1.2.3` → imagen inmutable.
- Incluye `git commit` + semver en la etiqueta: `myapp:1.2.3-a1b2c3`.
- Genera y publica artefactos con checksum (SHA256) para verificar integridad.

**Comando ejemplo:**

```bash
# tag y push
docker build -t myapp:1.2.3 .
docker tag myapp:1.2.3 registry.example.com/myapp:1.2.3
docker push registry.example.com/myapp:1.2.3
```

### Workflows y control de versiones de código (prácticas)

- **Tag releases** en Git:

```bash
git tag -a v1.2.3 -m "Release v1.2.3 - fixes x"
git push origin v1.2.3
```

- **Branching models**: Git Flow (feature/release/hotfix) o Trunk-Based (short-lived branches).
- **Automatizar release**: `semantic-release` o GitHub Actions que crean tags y changelogs.

**Ejemplo release simple (manual):**

```bash
# después de merge a main
npm version patch         # actualiza package.json y crea tag vX.Y.Z
git push && git push --tags
```

### Seguridad y parches (gestión de versiones críticas)

- Clasificar releases: `stable`, `maintenance`, `EOL`.
- Parches críticos → `PATCH` o `hotfix` con backports a ramas soportadas.
- Automatizar alertas de vulnerabilidades (Dependabot, Renovate, Snyk).

**Ejemplo proceso de hotfix:**

1. Branch `hotfix/1.2.4-fix` desde `release/1.2.x`.
2. Aplicar fix, tests, merge a `main` y `release/1.2.x`.
3. Tag `v1.2.4` y desplegar.

### Changelog y comunicación (plantilla práctica)

**Formato minimal (Keep a Changelog style):**

```
## [Unreleased]

### Added
- Nueva ruta GET /stats

### Fixed
- Corrige error en validación de email

### Breaking
- Se renombra `user.name` a `user.fullName` (migración necesaria)

## [1.2.3] - 2025-08-29
- Release notes...
```

Publica **guías de migración** para cambios mayores y un resumen de riesgos.

### Herramientas útiles

- **Control de versiones**: Git (tags), GitHub Releases.
- **Semver automation**: semantic-release.
- **Dependency management**: npm/yarn/pnpm (lockfile), pip + `requirements.txt`, poetry.
- **Security updates**: Dependabot / Renovate.
- **Migrations**: Flyway / Liquibase / Alembic.
- **Containers**: Docker tags, container registries.
- **IaC versioning**: Terraform modules + provider versions.

### Comandos/ejemplos prácticos (cheat-sheet)

**Git: tag y describe**

```bash
git tag -a v2.0.0 -m "Release v2.0.0"
git push origin v2.0.0
git describe --tags $(git rev-parse HEAD)
```

**npm: version**

```bash
npm version minor   # incrementa minor y crea tag vX.Y.0
git push && git push --tags
```

**Docker: build/tag/push**

```bash
docker build -t myapp:1.2.3 .
docker tag myapp:1.2.3 registry/myapp:1.2.3
docker push registry/myapp:1.2.3
```

**Pip: freeze (para reproducibility)**

```bash
pip freeze > requirements.txt
# instalar exactamente esas versiones
pip install -r requirements.txt
```

**Flyway: ejemplo**

```bash
# archivos en sql: V1__init.sql, V2__add_index.sql
flyway migrate
flyway info
```

### Errores comunes y cómo evitarlos

- **No versionar infra/config** → usar Git + IaC.
- **Usar `latest` en producción** → no saber qué se está ejecutando.
- **Permitir ranges demasiado permisivos** (`^` sin lockfile) → regresiones por dependencia.
- **Romper API sin bump de MAJOR** → clientes rotos.
- **Olvidar changelog/migration guide** → soporte saturado.
- **No probar migraciones** → downtime o pérdida de datos.

### Checklist rápido para implementar una política de versiones (equipo)

1. Elegir esquema principal (SemVer recomendado para librerías/API).
2. Forzar uso de _lockfiles_ + CI que verifique builds.
3. Etiquetado automático de releases en CI (tags semánticos).
4. Publicar changelog y guía de migración en cada MAJOR.
5. Pinnear imágenes Docker con tags y checksums.
6. Usar herramientas para actualizaciones de dependencia automáticas (Dependabot).
7. Mantener ramas de soporte y política de backports para seguridad.
8. Testear rollback y migraciones en staging.
9. Documentar EOL y ventanas de soporte.
10. Revisar dependencias por vulnerabilidades periódicamente.

### Plan de acción corto para empezar (3 pasos)

1. **Decidan esquema** (SemVer + lockfile + tags).
2. **Automatizar**: CI que haga `npm version` / `git tag` / build & push image con tag semántico.
3. **Documentar**: plantilla de release + políticas de EOL + playbook de hotfix.

#### Resumen final

El versionado no es solo poner números: es **la columna vertebral** de coordinación técnica, seguridad y confianza operativa. Un plan claro de versiones (qué significa cada cambio), automatizado y comunicado reduce riesgos, facilita rollbacks y acelera la entrega segura.

---

[🔼](#índice)

---

## **49. Comprender las diferencias**

### 1) Marco de trabajo paso a paso (qué hacer y por qué)

1. **Define el objetivo** — ¿qué decisión necesitas tomar? (ej.: elegir VM vs contenedor para producción).
2. **Acota el alcance** — tiempo, presupuesto, restricciones regulatorias, equipo.
3. **Elige criterios de comparación** (técnicos y no técnicos): rendimiento, seguridad, coste, facilidad de operación, compatibilidad, tiempo de arranque, capacidad de escalado, soporte.
4. **Pondera los criterios** según importancia (pesos que sumen 100).
5. **Reúne datos**: documentación, benchmarks, pruebas propias, referencias de terceros.
6. **Evalúa**: cualitativa, cuantitativa o mixta. Usa matrices ponderadas para decisiones objetivas.
7. **Prueba en laboratorio** (staging) con cargas representativas.
8. **Documenta resultados** (changelog, playbooks, runbooks).
9. **Decisión + plan de adopción** (piloto → rollout por fases → monitoreo).

### 2) Tipos de comparaciones y métodos

- **Cualitativa**: entrevistas, checklists, pros/cons (útil rápido).
- **Cuantitativa**: métricas medibles (latencia en ms, IOPS, coste \$/mes).
- **Mixta**: convierte juicios cualitativos en puntajes y aplica una **matriz ponderada**.
- **Pruebas A/B / canary**: despliegas ambas opciones con usuarios reales y comparas métricas clave.
- **Análisis de sensibilidad**: ¿qué pasa si el peso de "coste" aumenta 10 puntos? ¿cambia la decisión?

### 3) Cómo construir una **matriz ponderada** (plantilla y ejemplo práctico)

#### Pasos

1. Lista las **opciones** (A, B, C).
2. Lista los **criterios** relevantes y asigna **pesos** (ej. suman 100).
3. Puntúa cada opción en cada criterio (escala 1–10).
4. Calcula `puntaje_criterio = puntuación * peso`.
5. Suma los puntajes para obtener `puntaje_total`.
6. Normaliza si quieres (dividir por máximo posible = 10 \* suma de pesos).

> Importante: documenta justificaciones de cada puntuación (evita sesgos).

#### Ejemplo completo y calculado paso a paso — **Contenedores vs VMs**

**Criterios y pesos (suman 100):**

- Boot time (arranque): 15
- Densidad / overhead: 20
- Aislamiento / Seguridad: 25
- Gestión / Operación: 20
- Compatibilidad / Ecosistema: 20

**Puntuaciones (1–10):**

- **Contenedores**: Boot 9, Densidad 9, Aislamiento 6, Gestión 8, Compatibilidad 7
- **VMs**: Boot 4, Densidad 5, Aislamiento 9, Gestión 7, Compatibilidad 9

Ahora calculamos **cada multiplicación** paso a paso (digit-by-digit como práctica recomendada):

##### Contenedores — cálculo detalle

- Boot time: 9 × 15

  - 9×10 = 90
  - 9×5 = 45
  - Suma = 90 + 45 = **135**

- Densidad: 9 × 20

  - 9×20 = (9×2)×10 = 18×10 = **180**

- Aislamiento: 6 × 25

  - 6×20 = 120
  - 6×5 = 30
  - Suma = 120 + 30 = **150**

- Gestión: 8 × 20

  - 8×20 = (8×2)×10 = 16×10 = **160**

- Compatibilidad: 7 × 20

  - 7×20 = (7×2)×10 = 14×10 = **140**

Ahora sumamos los subtotales (hazlo paso a paso):

- 135 + 180 = 315
- 315 + 150 = 465
- 465 + 160 = 625
- 625 + 140 = **765**

Puntaje total contenedores = **765**

##### VMs — cálculo detalle

- Boot time: 4 × 15

  - 4×10 = 40
  - 4×5 = 20
  - Suma = 40 + 20 = **60**

- Densidad: 5 × 20

  - 5×20 = (5×2)×10 = 10×10 = **100**

- Aislamiento: 9 × 25

  - 9×20 = 180
  - 9×5 = 45
  - Suma = 180 + 45 = **225**

- Gestión: 7 × 20

  - 7×20 = (7×2)×10 = 14×10 = **140**

- Compatibilidad: 9 × 20

  - 9×20 = (9×2)×10 = 18×10 = **180**

Sumamos subtotales:

- 60 + 100 = 160
- 160 + 225 = 385
- 385 + 140 = 525
- 525 + 180 = **705**

Puntaje total VMs = **705**

##### Normalización (opcional)

Máximo posible = 10 × suma_pesos = 10 × 100 = **1000**

- Contenedores: 765 / 1000 = 0.765 → **76.5%**
  (Cálculo: 765 ÷ 1000 = 0.765 → multiplicar por 100 = 76.5)
- VMs: 705 / 1000 = 0.705 → **70.5%**

**Interpretación:** con estos pesos y puntuaciones, los contenedores puntúan más alto (76.5% vs 70.5%). Conclusión: para un caso donde arranque rápido y densidad importan, contenedores suelen preferirse; si aislamiento fuerte y compatibilidad con software legado pesan más, las VMs pueden ganar.

> Nota: cambia pesos según tu contexto (por ejemplo en fintech seguridad tendrá mayor peso y la decisión podría invertirse).

### 4) Tres comparaciones prácticas rápidas (qué mirar para decidir)

#### A — **Windows vs Linux vs macOS** (cuándo elegir cada uno)

- **Windows**: compatibilidad comercial (MS Office, software corporativo), soporte hardware amplio. Elige Windows si dependes de software exclusivo o particulares integraciones corporativas.
- **Linux**: flexibilidad, servidores, control, contenedorización, ahorro de licencias y herramientas DevOps. Elige Linux para servidores, infra y desarrollo.
- **macOS**: integración hardware-software, entorno de desarrollo Apple (iOS/macOS), experiencia de usuario. Ideal para desarrolladores Apple y creativos.
  **Ejemplo**: para un servidor web en producción → Linux; para desarrollo iOS → macOS; para escritorios en dominio empresarial con Office dependiente → Windows.

#### B — **SemVer vs CalVer (versionado)**

- **SemVer** (MAJOR.MINOR.PATCH): mejor para librerías/APIs donde romper compatibilidad es importante. Usa SemVer si external clients dependen de tus APIs.
- **CalVer** (YYYY.MM): mejor si tu producto se libera periódicamente y quieres que la versión muestre fecha; ejemplo: distribuciones/os.
  **Ejemplo**: librería de JavaScript → SemVer; distribución Linux o SaaS con releases mensuales → CalVer.

#### C — **Encriptación en tránsito vs en reposo**

- **En tránsito**: TLS/HTTPS — protege datos mientras viajan. Obligatorio para comunicaciones públicas.
- **En reposo**: BitLocker/LUKS/FileVault o cifrado de objetos en cloud — protege datos si alguien obtiene acceso físico o copia de backups.
  **Ejemplo práctico**: para una base de datos con PII, implementa TLS para conexiones y cifrado en reposo en discos + gestión de claves centralizada.

### 5) Cómo diseñar pruebas reproducibles (plan de benchmarking)

1. Define métricas (latencia p50/p95, throughput, IOPS, memoria, coste).
2. Crea escenario representativo (dataset, número de usuarios, carga).
3. Usa herramientas: `iperf`, `sysbench`, `wrk`, `ab`, `fio`, `k6`.
4. Automáticamente instrumenta (Prometheus + Grafana) y guarda resultados.
5. Ejecuta varias corridas, cambia sólo una variable a la vez.
6. Analiza distribución (mediana, percentiles), no solo la media.
7. Documenta toda la configuración (hardware, kernel params, versiones).

**Comandos de ejemplo** (benchmarks básicos):

- CPU: `sysbench cpu --threads=4 run`
- HTTP load: `wrk -t2 -c100 -d60s http://myapp/endpoint`
- Disk I/O (fio): crear job file y `fio job.fio`

### 6) Errores habituales y cómo evitarlos

- **Comparar manzanas con naranjas**: asegúrate de que los escenarios son equivalentes.
- **Sesgo de confirmación**: no pongas más peso a criterios que favorecen tu opción preferida.
- **Ignorar costos ocultos**: capacitación, licencias, soporte, compatibilidad con legado.
- **Basarse solo en benchmarks de marketing**: reproduce en tu entorno.
- **No planear rollback**: siempre tener ruta de vuelta antes de cambiar.

### 7) Plantilla rápida para tu propia comparación (cópiala y usa)

**1. Objetivo:**

**2. Opciones:** A / B / C

**3. Criterios (peso 0–100):** (ej.: Seguridad 30, Coste 20, Rendimiento 20, Operación 15, Compatibilidad 15) — suma = 100

**4. Puntuaciones (1–10) por opción y criterio**

**5. Cálculo (puntuación × peso)** → sumar → normalizar si quieres.

**6. Resultados y riesgos** (lista de riesgos por opción)

**7. Plan de prueba** (herramientas, casos, cuándo ejecutar)

**8. Recomendación** y plan de rollout

### 8) Herramientas útiles para comparar y documentar

- **Hoja de cálculo** (Excel/Google Sheets) para la matriz ponderada.
- **Jupyter / Python** (pandas + matplotlib) para análisis y gráficos.
- **Prometheus + Grafana** para comparar métricas en pruebas.
- **Git repos** para versionar scripts y resultados.
- **Benchmarks reproducibles** en CI (GitHub Actions / GitLab CI) que ejecuten tests periódicos.

### 9) Resumen — cómo aplicar esto ahora mismo

1. Define **1 decisión** que necesitas (por ejemplo: “¿contenedores o VMs para microservicios?”).
2. Usa la **matriz ponderada** de la sección 3 con tus pesos.
3. Monta **2 entornos** (staging) y ejecuta los tests de la sección 5.
4. Analiza resultados, documenta y decide con rollout por fases.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 5**                                   | **Siguiente 7**                             |
| ------------------ | --------------------------------------------- | ------------------------------------------- |
| [🏠](../README.md) | [⏪](./2_5_Installation_and_Configuration.md) | [⏩](./2_7_Navigating_using_GUI_and_CLI.md) |
