| **Inicio**         | **atrás 32**                   | **Siguiente 34**           |
| ------------------ | ------------------------------ | -------------------------- |
| [🏠](../README.md) | [⏪](./13_32_Firewall_Logs.md) | [⏩](./13_34_NAC_based.md) |

---

## **Índice**

| Temario                                                                                                  |
| -------------------------------------------------------------------------------------------------------- |
| [475. ¿Qué es el control de acceso obligatorio?](#475-qué-es-el-control-de-acceso-obligatorio)           |
| [476. Modelos de control de acceso obligatorio (MAC)](#476-modelos-de-control-de-acceso-obligatorio-mac) |

# **MAC-based**

## **475. ¿Qué es el control de acceso obligatorio?**

![MAC-based](/img/13_Tools_for_Incident_Response_and_Discovery/MAC_based.png "MAC-based")

### 1. Control de acceso obligatorio definido

El **control de acceso obligatorio (MAC)** es un modelo de seguridad donde **el sistema operativo (o la política centralizada)** decide qué usuarios pueden acceder a qué recursos.

- Los usuarios **no pueden modificar** estas políticas de acceso.
- Los permisos se basan en **clasificación de seguridad** (ej. Confidencial, Secreto, Top Secret) y en **niveles de autorización** de los usuarios.

👉 A diferencia de DAC (Control de Acceso Discrecional), donde el propietario decide quién entra, en MAC **todo está definido por reglas estrictas centralizadas**.

### 2. ¿Cómo funciona el control de acceso obligatorio?

El sistema asigna **etiquetas de seguridad** tanto a:

- **Usuarios / procesos** → clearance (nivel de autorización).
- **Recursos (archivos, bases de datos, dispositivos)** → clasificación (nivel de sensibilidad).

El acceso se determina mediante **reglas obligatorias**:

- Un usuario solo puede acceder a un recurso **si su autorización es igual o superior al nivel de clasificación**.

📌 Ejemplo:

- Usuario: "Empleado con autorización **Secreto**".
- Documento: Clasificación **Confidencial** → ✅ acceso permitido.
- Documento: Clasificación **Top Secret** → ❌ acceso denegado.

### 3. Beneficios del control de acceso obligatorio

- **Alta seguridad**: Protege datos sensibles (ej. militares, gubernamentales).
- **Consistencia**: Los permisos no dependen del criterio de cada usuario.
- **Resiliencia contra errores humanos**: Un empleado no puede compartir archivos restringidos aunque quiera.
- **Cumplimiento normativo**: Adecuado para entornos que requieren certificaciones (ISO 27001, DoD, etc.).

### 4. Desafíos y limitaciones del control de acceso obligatorio

- **Rigidez**: Difícil adaptarse a entornos dinámicos o colaborativos.
- **Complejidad en la administración**: Etiquetar usuarios y recursos requiere gestión constante.
- **Curva de aprendizaje**: Los administradores y usuarios deben entender las políticas de seguridad.
- **Baja flexibilidad**: Ejemplo: un proyecto nuevo puede necesitar permisos temporales, pero MAC no permite excepciones fácilmente.

### 5. Implementación del control de acceso obligatorio

El MAC se implementa en sistemas que soportan **etiquetado obligatorio**:

1. **Definir niveles de clasificación de la información**

   - Público, Interno, Confidencial, Secreto, Top Secret.

2. **Asignar autorizaciones a usuarios y procesos**

   - Ej. “Empleado A” autorizado hasta **Secreto**.

3. **Configurar el sistema operativo / base de datos** con MAC habilitado:

   - **SELinux** (Linux con Security-Enhanced Linux).
   - **AppArmor** (Linux).
   - **Trusted Solaris** (versión de Solaris con MAC).
   - **Windows con Mandatory Integrity Control (MIC)**.
   - **Bases de datos Oracle** con etiquetado de seguridad (Oracle Label Security).

4. **Monitorear y auditar** → registros de accesos permitidos/denegados.

### 6. Ejemplos de control de acceso obligatorio en la práctica

#### Ejemplo 1 — Militar

- Documento clasificado como **Top Secret**.
- Solo usuarios con autorización **Top Secret** pueden abrirlo.
- Un coronel con autorización "Secreto" no podrá leerlo, aunque tenga altos privilegios administrativos.

#### Ejemplo 2 — SELinux (Linux)

Un proceso como `httpd` (Apache) está etiquetado para solo leer archivos en `/var/www/html`.

Si intenta acceder a `/etc/shadow` (contraseñas), el sistema lo bloquea automáticamente, aunque el usuario root haya iniciado el proceso.

#### Ejemplo 3 — Bases de datos

En una base de datos gubernamental:

- Registro de un ciudadano = **Confidencial**.
- Datos de un espía = **Secreto**.
  Un analista con credenciales de "Confidencial" no podrá consultar al espía aunque tenga permisos SQL de SELECT.

#### Ejemplo 4 — Empresas privadas

Un banco clasifica datos:

- Transacciones = Confidencial.
- Estrategias de inversión = Secreto.
- Planes de expansión = Top Secret.

  Solo el comité de dirección con autorización máxima puede leer el último nivel.

✅ **Resumen final**:

El **MAC** garantiza **seguridad estricta y centralizada**. Es ideal en entornos donde la **confidencialidad es prioritaria sobre la flexibilidad**, como gobiernos, militares, bancos y grandes corporaciones. Sin embargo, requiere una **administración cuidadosa** para no obstaculizar la productividad.

---

[🔼](#índice)

---

## **476. Modelos de control de acceso obligatorio (MAC)**

### 1 — ¿Qué es MAC (visión general)?

**MAC (Mandatory Access Control)** es un modelo de control de acceso en el que **las reglas de acceso las define la política del sistema** (o un administrador central), no el propietario del recurso.

Elementos clave:

- **Etiquetas/labels** asociadas a sujetos (usuarios/procesos) y objetos (archivos, registros).
- **Política central** que decide si un sujeto puede leer/escribir un objeto según la relación entre sus etiquetas.
- Se usa cuando la **confidencialidad o la integridad** deben preservarse incluso frente a errores humanos o privilegios de usuario.

Ejemplos de aplicación: entornos militares, gobierno, banca, salud, núcleos de sistemas operativos (sandboxing), contenedores.

### 2 — Lattice-based access control (LBAC) — el marco matemático

MAC se suele formalizar con una **álgebra de lattice** (retículo):

- Cada etiqueta (nivel) pertenece a un **lattice** con una relación de dominancia `≤` (p. ej. Unclass ≤ Confidential ≤ Secret ≤ TopSecret).
- Una regla típica: un sujeto con etiqueta `s` puede leer objeto `o` **si y solo si** `label(s) ≥ label(o)` (domina) — esto expresa “no read up” cuando se busca confidencialidad.

El lattice permite combinar jerarquías y categorías (ej.: `Secret:{Nuclear, Crypto}`), y expresar políticas complejas.

### 3 — Modelos clásicos (reglas y ejemplos)

#### 3.1 Bell–LaPadula (confidencialidad)

Enfocado en **confidencialidad** (aplicaciones militares).

Reglas principales:

- **Simple security property (no read up)**: un sujeto no puede leer un objeto de nivel estrictamente superior.

  - Ejemplo: usuario con nivel _Confidential_ **no** puede leer _Secret_.

- **★-property (no write down)**: un sujeto no puede escribir en un objeto de nivel inferior.

  - Objetivo: evitar que información secreta “baje” a niveles menos protegidos.

- (Opcional) **strong★** (ni read up ni write down simultáneamente).

**Ejemplo práctico:**

- Usuario A (Clearance = Secret) puede leer documentos Confidential o Secret, pero **no** leer TopSecret. Si A escribe, no puede escribir en files marcados Confidential (para no “filtrar” secretos).

#### 3.2 Biba (integridad)

Es el dual de Bell-LaPadula; protege **integridad** (evitar que datos no confiables corrompan datos confiables).

Reglas principales (forma simple):

- **Simple integrity axiom (no read down)**: un sujeto no lee objetos de integridad menor (evita contaminarse).
- **★-integrity property (no write up)**: un sujeto no escribe en objetos de integridad mayor.

**Ejemplo práctico:**

Un proceso con integridad _Low_ (p. ej. navegador en sandbox) **no** puede escribir en ficheros _High_ (configuración del sistema). Tampoco debería leer ficheros _High_ (para evitar usar datos confiables mezclados con datos dudosos).

> Resumen Bell-LaPadula vs Biba: BLP = **no leer hacia arriba / no escribir hacia abajo** (confidencialidad). Biba = **no leer hacia abajo / no escribir hacia arriba** (integridad).

#### 3.3 Clark–Wilson (integridad en entornos comerciales)

No se basa en niveles solamente; usa **transacciones controladas**:

Componentes:

- **CDI (Constrained Data Items)** — datos que requieren integridad.
- **UDI (Unconstrained Data Items)** — datos de entrada sin confianza.
- **TP (Transformation Procedures)** — procedimientos autorizados que modifican CDIs (p. ej. una función `transferir()` en un banco).
- **IVP (Integrity Verification Procedures)** — verifican integridad (checksums, balances).

Reglas clave:

- Los usuarios sólo acceden a CDIs a través de TPs autorizadas (evita acceso directo).
- Separación de funciones y auditoría.

**Ejemplo práctico (banco):**

Operación `Transferir(Origen, Destino, Monto)` es un TP validado por IVP. Ningún empleado puede modificar directamente la tabla de saldos; los cambios se hacen sólo mediante la TP y se registra auditoría.

#### 3.4 Brewer–Nash (Chinese Wall) — conflictos de interés (dinámico)

Política dinámica para **conflictos de interés** (consultoría, auditoría):

- Si un consultor accede a datos confidenciales del Cliente A (en la industria X), se le **impide** acceder a datos confidenciales de otros clientes en la misma industria (B) para evitar conflicto.
- Es **dinámico**: las permisiones cambian en base al historial de accesos del sujeto.

**Ejemplo práctico:**

Un auditor que revisa la contabilidad de Banco A (sector financiero) no puede después revisar Banco B (competidor) si ambos están en el mismo conflicto class.

### 4 — Implementaciones prácticas de MAC en sistemas reales

#### 4.1 SELinux (Type Enforcement + MLS/MCS)

- SELinux implementa **Type Enforcement (TE)** como base (cada proceso y objeto tiene un `type`), y opcionalmente **MLS** o **MCS** para multilevel/category enforcement.
- **Contextos**: `system_u:system_r:httpd_t:s0:c123,c456`

  - `httpd_t` = dominio (proceso Apache), `s0` = sensitivity, `c123` = MCS category.

**Comandos básicos (ejemplo):**

- `ls -Z /var/www` → ver contextos SELinux.
- `ps -eZ | grep httpd` → ver contexto de procesos.
- Para marcar un directorio:

  ```bash
  semanage fcontext -a -t httpd_sys_content_t '/srv/www(/.*)?'
  restorecon -Rv /srv/www
  ```

**Caso real:** en OpenShift/Kubernetes SELinux MCS se usa para asignar categorías únicas a contenedores (aislamiento entre contenedores).

#### 4.2 Windows Mandatory Integrity Control (MIC)

- Windows usa **integrity levels**: `Low`, `Medium`, `High`, `System`.
- Procesos con `Low` (ej. navegador) no pueden escribir en objetos `Medium` o `High`.
- Se nota en UAC y procesos de navegador que se ejecutan en low integrity.

**Ejemplo:** Internet Explorer en `Low` no puede modificar `Program Files`.

#### 4.3 Bases de datos con etiquetado (Oracle Label Security, SQL Server AIP)

- RDBMS que soportan etiquetas por fila/columna y políticas de acceso según clearance.
- Útil para datos clasificados en un mismo esquema (militar, salud).

**Ejemplo:** cada fila de la tabla `pacientes` puede llevar una etiqueta `Confidentiality=PHI:level=Restricted`, y la política filtra las filas según la etiqueta del usuario.

### 5 — Ventajas de los modelos MAC

- **Fuerte garantía** de confidencialidad/integridad por diseño.
- **Política centralizada y consistente** (evita errores de propietarios).
- **Resiliencia**: reduce riesgo de exposición por cuentas privilegiadas equivocadas.
- **Apto para cumplimiento** y entornos de alta seguridad.

### 6 — Desafíos y limitaciones del MAC

- **Rigidez**: puede impedir la colaboración ágil.
- **Complejidad de políticas y etiquetas**: etiquetar correctamente objetos y sujetos es laborioso.
- **Sobrecarga administrativa**: cambios frecuentes requieren reetiquetado.
- **Falsos positivos / bloqueo de procesos legítimos** si políticas mal diseñadas.
- **Operatividad**: performance/overhead y necesidad de herramientas que soporten MAC (no todos los apps lo hacen).

### 7 — Cómo diseñar/implementar MAC en una organización (pasos prácticos)

1. **Clasificar la información**: definir niveles (Public, Internal, Confidential, Secret, TopSecret) y categorías (proyectos, clientes).
2. **Mapear roles y clearances**: qué perfiles necesitan qué niveles.
3. **Elegir tecnología**: SELinux/AppArmor, Windows MIC, DB etiquetado, DLP, IAM.
4. **Definir política central** (reglas read/write, excepciones justificadas).
5. **Piloto**: aplicar en un dominio controlado (por ejemplo, servidores de pruebas).
6. **Auditoría y logging**: habilitar registros de denegaciones y revisar (tuning).
7. **Formación**: enseñar a admins y a los usuarios los impactos.
8. **Operación y gobernanza**: gobernanza de etiquetas, POA\&M para cambios.

### 8 — Ejemplos concretos (escenarios)

#### Escenario A — Ministerio de Defensa

- Documentos con etiquetas `Unclassified < Confidential < Secret < TopSecret`.
- Analistas con clearance `Secret` pueden leer Secret/Confidential, pero **no** TopSecret.
- Políticas preventivas: logs de accesos y alertas SIEM si un usuario intenta repetidamente "read up".

#### Escenario B — Banco (integridad con Clark-Wilson)

- Transferencias se realizan sólo mediante TP (aplicación bancaria); auditoría IVP ejecuta conciliaciones diarias.
- Empleados no pueden editar balances directamente.

#### Escenario C — Empresa de consultoría (Brewer-Nash)

- Consultor que abre datos de Cliente A (sector X) queda temporalmente **bloqueado** para abrir datos de Cliente B (competidor) en la misma clase de conflicto.

#### Escenario D — Kubernetes/OpenShift

- Usan SELinux MCS para aislar contenedores: cada pod recibe categorías MCS únicas `s0:cXYZ`, previniendo escrutinio cruzado de volúmenes y sockets.

### 9 — Buenas prácticas y consejos de operación

- **Empieza por lo crítico**: aplica MAC a sistemas de mayor riesgo (bases de datos sensibles, servidores con PHI/PII).
- **Automatiza etiquetado** cuando sea posible (por metadatos, clasificación por flujo).
- **Registro y dashboards**: monitoriza denegaciones (false positives) y ajusta.
- **Gestión de excepciones**: procesos que requieran mayor flexibilidad deben pasar por un control change/request con auditoría.
- **Prueba** en entornos no productivos y realizar rollback plan.
- **Documenta** la política y la lógica (por qué una etiqueta permite/deniega acceso).

### 10 — Comparación rápida (cuando elegir MAC vs DAC vs RBAC)

- **MAC**: elegir si la prioridad es **máxima seguridad** (confidencialidad/integridad), entornos regulados o militares.
- **DAC (Discretionary)**: más flexible; propietario controla; útil en colaboración normal de oficina.
- **RBAC (Role-based)**: buena para gestión de privilegios por rol (aplicable en combinación con MAC).
  En la práctica, **combinan**: por ejemplo, SELinux (MAC) + Unix ACLs (DAC) + AD/RBAC para roles.

### 11 — Checklist rápido para empezar con MAC

- Clasificar activos y datos.
- Seleccionar tecnología soportada (SELinux, Windows MIC, DB labels).
- Pilotar en pequeño dominio con logging.
- Revisar logs y ajustar reglas.
- Documentar proceso de cambio y gobernanza.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 32**                   | **Siguiente 34**           |
| ------------------ | ------------------------------ | -------------------------- |
| [🏠](../README.md) | [⏪](./13_32_Firewall_Logs.md) | [⏩](./13_34_NAC_based.md) |
