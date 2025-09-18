| **Inicio**         | **atrás 21**                                | **Siguiente 23**                |
| ------------------ | ------------------------------------------- | ------------------------------- |
| [🏠](../README.md) | [⏪](./12_21_WPA_vs_WPA2_vs_WPA3_vs_WEP.md) | [⏩](./12_23_Identification.md) |

---

## **Índice**

| Temario                                                                                                                            |
| ---------------------------------------------------------------------------------------------------------------------------------- |
| [396. ¿Qué es la respuesta a incidentes?](#396-qué-es-la-respuesta-a-incidentes)                                                   |
| [397. Explicación de la respuesta a incidentes de ciberseguridad](#397-explicación-de-la-respuesta-a-incidentes-de-ciberseguridad) |

# **Preparation**

## **396. ¿Qué es la respuesta a incidentes?**

![Preparation](/img/12_Secure_vs_Unsecure_Protocols/Preparation.webp "Preparation")

La **respuesta a incidentes** es el conjunto de **procesos, políticas y tecnologías** que una organización aplica para **detectar, analizar, contener, erradicar y recuperarse** de incidentes de seguridad informática.

👉 En otras palabras: es el plan de acción que sigue una empresa cuando ocurre un ataque o un evento inesperado que afecta la seguridad de sus sistemas.

### 📌 Definición de respuesta a incidentes

- Es la disciplina que permite a una organización **gestionar incidentes de seguridad de manera estructurada**.
- Busca **minimizar el impacto** (pérdida de datos, tiempo de inactividad, daños reputacionales).
- Permite **aprender de cada incidente** para prevenir que vuelva a ocurrir.

Ejemplo: si un empleado descarga un archivo malicioso que instala un ransomware, la respuesta a incidentes es el conjunto de pasos que la empresa sigue para detener la propagación, recuperar datos y reforzar controles.

### 📌 ¿Cómo funciona la respuesta a incidentes?

Funciona siguiendo un **ciclo estructurado** (como el modelo NIST o SANS):

1. **Preparación:** tener un plan, equipo y herramientas listas.
2. **Detección y análisis:** identificar el incidente y evaluar su gravedad.
3. **Contención:** limitar el alcance (ej. aislar máquinas comprometidas).
4. **Erradicación:** eliminar la causa raíz (ej. malware).
5. **Recuperación:** restaurar servicios afectados y reforzar seguridad.
6. **Lecciones aprendidas:** documentar lo ocurrido para mejorar.

### 📌 Tipos de incidentes de seguridad

Los incidentes más comunes incluyen:

#### 1. **Suplantación de identidad (phishing)**

- Ataques que engañan al usuario para que entregue credenciales o información confidencial.
- Ejemplo: correo falso de “banco” pidiendo que confirmes tu contraseña.

#### 2. **Malware**

- Programas diseñados para dañar sistemas o robar datos.
- Ejemplo: troyano que instala un backdoor en el equipo.

#### 3. **Ransomware**

- Tipo de malware que cifra archivos y exige pago por su liberación.
- Ejemplo: WannaCry afectando hospitales en 2017.

#### 4. **Denegación de servicio (DoS/DDoS)**

- Ataques que sobrecargan un servidor para dejarlo inaccesible.
- Ejemplo: miles de solicitudes falsas contra un sitio web.

#### 5. **El hombre en el medio (MitM)**

- El atacante intercepta la comunicación entre dos partes.
- Ejemplo: sniffing en una red Wi-Fi pública para capturar contraseñas.

#### 6. **Amenaza interna**

- Empleado o contratista que usa indebidamente sus privilegios.
- Ejemplo: un ex empleado copiando datos confidenciales antes de irse.

#### 7. **Acceso no autorizado**

- Cuando un atacante logra entrar en un sistema sin permiso.
- Ejemplo: explotación de una vulnerabilidad en un servidor web.

### 📌 ¿Qué es un plan de respuesta a incidentes?

Es un **documento formal** que define:

- Cómo se **detectan, reportan y gestionan** los incidentes.
- Quiénes son los responsables de cada acción.
- Qué **procedimientos técnicos y de comunicación** seguir.
- Cómo coordinar con terceros (ej. proveedores, fuerzas de seguridad).

👉 Piensa en él como el **manual de emergencias** de una empresa frente a ciberataques.

### 📌 Importancia de un plan de respuesta a incidentes

- **Reduce el impacto** económico y operativo.
- **Acelera la recuperación** de sistemas críticos.
- **Cumple con normativas** (ej. ISO 27001, GDPR, HIPAA).
- **Protege la reputación** de la empresa.
- **Mejora la resiliencia** frente a ataques futuros.

Ejemplo: una empresa sin plan puede tardar semanas en recuperarse de un ransomware; con plan, puede restaurar copias de seguridad en horas.

### 📌 Pasos de respuesta a incidentes (modelo NIST/SANS)

1. **Preparación** → entrenar al personal, definir roles, implementar monitoreo.
2. **Detección y análisis** → identificar anomalías en logs, alertas SIEM, reportes de usuarios.
3. **Contención** → detener la propagación (aislar redes, revocar credenciales).
4. **Erradicación** → eliminar la amenaza (borrar malware, parchar vulnerabilidades).
5. **Recuperación** → restaurar operaciones (copias de seguridad, verificar integridad).
6. **Lecciones aprendidas** → documentar, actualizar políticas, entrenar nuevamente.

### 📌 ¿Qué es un equipo de respuesta a incidentes?

Es el grupo de especialistas encargado de gestionar incidentes, conocido como **CSIRT (Computer Security Incident Response Team)**.

Incluye:

- **Líder del equipo** (coordina acciones y comunicación).
- **Analistas forenses** (investigan causas).
- **Ingenieros de red/sistemas** (aplican contención y recuperación).
- **Legal y RRPP** (manejo de comunicación y cumplimiento).

Ejemplo: en un banco, el CSIRT debe actuar en minutos ante un ataque de phishing masivo a clientes.

### 📌 Automatización de la respuesta a incidentes

La automatización se logra con herramientas **SOAR (Security Orchestration, Automation and Response)** que permiten:

- Bloquear automáticamente una IP maliciosa detectada.
- Aislar un endpoint infectado de la red.
- Generar tickets y alertas sin intervención manual.

Ejemplo: un SIEM detecta intento de fuerza bruta → el sistema SOAR bloquea la IP en el firewall de forma automática.

### 📌 Cómo implementar un plan de respuesta a incidentes

1. **Definir roles y responsabilidades.**
2. **Inventariar activos críticos** (saber qué proteger).
3. **Establecer canales de comunicación** internos y externos.
4. **Configurar detección** (SIEM, IDS/IPS, monitoreo de logs).
5. **Documentar procedimientos** para cada tipo de incidente.
6. **Realizar simulacros** (ej. tabletop exercises).
7. **Actualizar el plan** tras cada incidente real o prueba.

### 📌 Soluciones de respuesta a incidentes

- **SIEM (Security Information and Event Management):** Splunk, QRadar, ELK.
- **EDR/XDR (Endpoint/Extended Detection & Response):** CrowdStrike, SentinelOne, Microsoft Defender.
- **SOAR (Security Orchestration, Automation and Response):** Palo Alto Cortex XSOAR, IBM Resilient.
- **Servicios de CSIRT externos:** equipos especializados que ofrecen soporte 24/7.

✅ **En resumen:**

La respuesta a incidentes es un **pilar esencial de la ciberseguridad**. Un plan bien diseñado y un equipo entrenado permiten a las organizaciones **detectar ataques rápido, contenerlos, recuperarse con mínimo impacto y aprender de cada evento**.

---

[🔼](#índice)

---

## **397. Explicación de la respuesta a incidentes de ciberseguridad**

### ¿Qué es la respuesta a incidentes?

La **respuesta a incidentes (IR)** es el conjunto de procesos, personas, herramientas y procedimientos que activas cuando ocurre un evento de seguridad que afecta tus sistemas, datos o servicios.

Objetivos principales: **detectar rápido, contener el daño, erradicar la amenaza, recuperar operaciones y aprender** para no repetirlo.

Beneficios: reduce tiempo de inactividad, minimiza pérdidas, cumple requisitos legales y protege la reputación.

### Fases del ciclo de respuesta (modelo NIST / SANS) — con tareas concretas

#### 1) Preparación

Qué incluye:

- Definir el **plan de respuesta a incidentes**, playbooks y políticas.
- Crear y entrenar el **CSIRT / equipo IR** (roles: líder, analistas, forense, redes, legal, PR).
- Inventario de activos críticos, diagramas de red y accesos.
- Implementar herramientas: **SIEM, EDR/XDR, IDS/IPS, backups, registro centralizado, MFA.**
- Procedimientos legales y de comunicación (notificaciones, reguladores, prensa).

Resultado: cuando ocurre algo, sabes quién hace qué y tienes herramientas listas.

### 2) Detección e identificación

Qué haces:

- Recolectas **alertas** del SIEM/EDR/IDS, reportes de usuarios, logs de firewalls o logs de aplicaciones.
- Clasificas el evento: ¿incidente real o falso positivo? ¿qué sistemas/usuarios están afectados?

Artefactos a capturar: timestamps, alert ID, logs relevantes (auth, firewall, proxy, aplicación), capturas de pantalla, hash de archivos sospechosos.

Indicadores de compromiso (IoC) comunes: IPs maliciosas, dominios, hashes SHA256, usernames, rutas de archivos, claves de registro.

### 3) Contención (inmediata y a largo plazo)

Objetivo: **evitar que el incidente se propague** mientras se preserva evidencia.

Acciones inmediatas (primeros 15–60 minutos):

- Aislar el endpoint comprometido de la red (switch port disable / bloquear en NAC / EDR isolate).
- Cambiar credenciales expuestas (si procede), aplicar bloqueo de cuentas sospechosas.
- Implementar reglas temporales en firewall (bloquear IPs, puertos).
- Mantener la integridad de la evidencia (no reiniciar sistemas si vas a forensearlos).

Acciones de contención a corto plazo:

- Patching rápido de la vulnerabilidad explotada si se conoce la técnica.
- Crear reglas de detección adicionales para identificar más víctimas.

#### 4) Erradicación

Objetivo: **eliminar la causa raíz** (malware, cuenta comprometida, credencial robada).

Ejemplos de tareas:

- Eliminar malware con EDR, restaurar binarios limpios.
- Parchar vulnerabilidades explotadas y actualizar contraseñas.
- Eliminar persistencias (servicios, tareas programadas, claves de registro).

Siempre documenta qué se hizo y conserva copias forenses de sistemas antes de cambiar demasiado.

### 5) Recuperación

Objetivo: **restaurar servicios de forma segura** y monitorear.

Pasos típicos:

- Restaurar desde backups sanos (verificados) en sistemas aislados.
- Reintroducir hosts a la red gradualmente mientras se monitoriza comportamiento.
- Validar integridad y rendimiento de servicios.

### 6) Lecciones aprendidas (post-incident)

- Reunión formal (post-mortem) con cronología, decisiones, sugerencias.
- Actualizar playbooks, parches, reglas SIEM y formación.
- Si aplica, notificar a reguladores/clientes (según leyes y SLA).

#### Roles y responsabilidades (CSIRT básico)

- **CISO / Sponsor**: decisiones estratégicas y comunicación ejecutiva.
- **Team Lead / Incident Manager**: dirige la respuesta diaria.
- **Analistas SOC**: triage y correlación de alertas.
- **Forense digital**: preservación y análisis de evidencia.
- **Ingeniería de redes/sistemas**: contención y remediación técnica.
- **Legal / Compliance**: evalúa obligaciones regulatorias y documentación.
- **Comms / PR**: mensajes a clientes/medios.
- **Recursos Humanos**: en incidentes con empleados (amenaza interna).

### Ejemplos prácticos (4 escenarios reales, con pasos concretos)

#### Escenario A — Phishing → credenciales robadas → acceso a SaaS corporativo

Detección:

- Usuario reporta un email sospechoso; SIEM muestra login desde IP nueva.
  Acciones:

1. **Detectar/Identificar**: revisar logs de autenticación, identificar hora, IP, user agent.
2. **Contención**: forzar bloqueo de la cuenta, invalidar sesiones activas, requerir MFA revalidación.
3. **Erradicación**: reset de credenciales, revisar permisos y tokens API asociados, revocar OAuth tokens.
4. **Recuperación**: restaurar acceso con MFA obligatorio y revisar actividad del usuario en SaaS (descargas, exports).
5. **Post-mortem**: entrenar al empleado, ajustar filtros de correo, añadir reglas de detección de geolocalización inusual.

Artefactos: headers del correo, URL del phishing, captura de pantalla, logs de autenticación, hashes de archivos adjuntos.

#### Escenario B — Ransomware en servidor de archivos

Detección:

- EDR detecta cifrado masivo de archivos; usuarios reportan archivos con extensión inusual y nota de rescate.
  Acciones:

1. **Contención inmediata**: aislar servidor afectado, desconectar comparticiones SMB, bloquear cuentas usadas.
2. **Preservación**: tomar imagen forense del servidor (bit-for-bit) antes de cualquier manipulación.
3. **Erradicación**: identificar y cerrar vector (vulnerabilidad RDP, credenciales comprometidas), eliminar puerta trasera.
4. **Recuperación**: restaurar desde backups conocidos limpios; verificar integridad y restaurar en segmento aislado; cambiar credenciales.
5. **Comunicación**: activar equipo legal y PR; decidir notificación según normativa (p. ej. si hay datos personales afectados).
6. **Lecciones aprendidas**: endurecer accesos remotos, segmentación de red, probar backups.

Notas: **no pagar rescates por defecto**; decisión legal/ético/operativa.

#### Escenario C — DDoS contra web pública

Detección:

- Tráfico volumétrico anómalo; aumento de latencia y errores 503.
  Acciones:

1. **Contención**: activar mitigación en el proveedor CDN/WAF (rate limiting, challenge pages).
2. **Identificación**: diferenciar tráfico legítimo (picos) vs botnet; revisar patrones de IP y encabezados.
3. **Recuperión**: escalar capacidad con el proveedor o aplicar reglas de bloqueo IP/geo.
4. **Post-mortem**: ajustar WAF, guard rails para escalado automático y runbooks para proveedores.

#### Escenario D — Amenaza interna (exfiltración de datos)

Detección:

- Un empleado descarga grandes volúmenes fuera de horario y usa servicios cloud personales.
  Acciones:

1. **Identificación**: revisar logs DLP, proxy y auditoría de archivos.
2. **Contención**: suspender cuenta y acceso del empleado vía IAM/NAC (coordinar con RRHH).
3. **Evidencia**: preservar logs, exportar registros de SSO y actividad de archivo.
4. **Investigación**: determinar si hubo intención maliciosa; cuantificar datos exfiltrados.
5. **Recuperación/Legal**: acciones disciplinarias, notificaciones legales si fue exposición de datos personales.

### Artefactos y evidencia — qué recolectar y cómo preservarla

Prioridad: **preservar la cadena de custodia** y minimizar cambios en el sistema afectado.

Elementos clave:

- **Imágenes forenses** (disco completo, RAM en caliente si es necesario).
- **Logs** (auth, sistema, red, proxy, aplicación, SIEM raw events).
- **Dump de memoria** (para detectar malware en RAM).
- **Capturas de procesos y conexiones** (ps, netstat/lsof, tasklist).
- **Hashes** de archivos sospechosos (SHA256).
- **Capturas de pantalla** y metadatos.
- **Metadata de archivos** (timestamps, permisos).

Buenas prácticas:

- Hacer imagen antes de limpiar.
- Usar herramientas forenses confiables (FTK Imager, EnCase, Velociraptor, OSForensics).
- Registrar cadena de custodia: quién tomó la evidencia, cuándo y dónde se almacenó.

Ejemplos de comandos útiles (para recolección — **uso defensivo**):

- Linux: `ps aux`, `ss -tulpn`, `lsof -i`, `journalctl -u <servicio>`, `tar/copy` logs.
- Windows: `tasklist /v`, `netstat -ano`, `wevtutil qe System /q:"*[System[TimeCreated]]"`.
- No reiniciar ni apagar si necesitas análisis de memoria (si es posible, toma volcado RAM).

### Herramientas habituales en IR

- **SIEM**: Splunk, Elastic SIEM, QRadar — correlación y búsquedas históricas.
- **EDR/XDR**: CrowdStrike Falcon, Microsoft Defender for Endpoint, SentinelOne — detección y aislamiento de endpoints.
- **Forense**: FTK Imager, Autopsy, Volatility (análisis memoria), Velociraptor.
- **SOAR**: Cortex XSOAR, Demisto — orquestación y automatización (playbooks).
- **DLP / Proxy / WAF / IDS**: para detectar y mitigar fugas, ataques web y tráfico malicioso.

### Playbook / checklist inmediato (primeras 60 min — “first 60”)

1. Activar respuesta (CSIRT notification).
2. Recolectar alertas iniciales y artefactos (logs, pantalla, alert id).
3. Clasificar severidad (S1 crítica — servicios abajo / S2 alta — datos sensibles, etc.).
4. Contención primaria (aislar host, bloquear IP, revocar sesión).
5. Preservar evidencia (imagen/disco, volcado memoria si aplica).
6. Comunicación interna (CISO, IT, Legal, RRHH, PR).
7. Registrar todas las acciones en el cronograma del incidente.

Checklist 24–72 horas:

- Ampliar identificación (buscar lateral movement).
- Coordinar recuperación y validación de backups.
- Iniciar reporte a autoridades si corresponde.
- Revisión de medidas preventivas (parches, MFA, segmentación).

### Clasificación de severidad (ejemplo simple)

- **S1 — Crítico:** servicios esenciales caídos, datos sensibles expuestos masivamente.
- **S2 — Alto:** compromisos con impacto significativo en negocio.
- **S3 — Medio:** acceso no autorizado limitado, posible exfiltración limitada.
- **S4 — Bajo:** intento frustrado, falso positivo.

La clasificación guía el nivel de respuesta y la comunicación.

### Comunicación y notificación

- **Interna:** informar a ejecutivos, IT, legal y operaciones según severidad.
- **Externa:** clientes, partners y reguladores — sigue plantillas aprobadas por legal/PR.
- **Regulatoria:** notificaciones GDPR, HIPAA, o leyes locales si hay exposición de datos personales (plazos y contenido varían según jurisdicción).

Sugerencia: tener plantillas de correo y mensajes ya redactados para distintos niveles de severidad.

### Automatización (SOAR) y orquestación

Usar SOAR para:

- Enriquecer alertas automáticamente (whois, reputation).
- Ejecutar acciones automáticas seguras ( bloquear IPs con thresholds, aislar endpoint tras validación).
- Generar tickets y ejecutar playbooks repetibles.

Importante equilibrar automatización con supervisión humana: no desatar acciones destructivas sin verificación.

### Métricas y KPIs útiles

- Tiempo medio de detección (MTTD).
- Tiempo medio de respuesta/remediación (MTTR).
- Número de incidentes por categoría.
- % de incidentes detectados por automatización.
- Tiempo hasta contención completa.

Estas métricas ayudan a justificar inversiones y mejorar procesos.

### Plantilla breve de reporte de incidente (para entregar a dirección)

- ID del incidente
- Fecha/hora de detección
- Clasificación (S1–S4)
- Resumen ejecutivo (qué pasó, impacto)
- Alcance (hosts/servicios/usuarios afectados)
- Acciones tomadas (contención, erradicación, recuperación)
- Estado actual y próximos pasos
- Recomendaciones inmediatas y a largo plazo

### Buenas prácticas y prevención (resumen práctico)

- **Backup + test de restores** frecuentes y offline.
- **Segmentación de red** y principio de mínimo privilegio.
- **MFA obligatorio** en accesos sensibles.
- **Patching y gestión de vulnerabilidades** constante.
- **EDR + SIEM** para detección avanzada y correlación.
- **Simulacros (tabletop y red-team)** para afinar playbooks.
- **Políticas de seguridad y formación** para reducir phishing y errores humanos.

### Limitaciones y consideraciones legales/éticas

- Conserva cadena de custodia; trabajo forense puede ser evidencia legal.
- En algunos países, ciertas acciones (seguir a un atacante fuera del perímetro, hack-back) son ilegales. Coordina con Legal.
- Balances: mantener evidencia vs restaurar operaciones rápidas — documenta decisiones.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 21**                                | **Siguiente 23**                |
| ------------------ | ------------------------------------------- | ------------------------------- |
| [🏠](../README.md) | [⏪](./12_21_WPA_vs_WPA2_vs_WPA3_vs_WEP.md) | [⏩](./12_23_Identification.md) |
