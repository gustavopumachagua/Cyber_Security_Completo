| **Inicio**         | **atrás 25**                 | **Siguiente 27**                  |
| ------------------ | ---------------------------- | --------------------------------- |
| [🏠](../README.md) | [⏪](./12_25_Eradication.md) | [⏩](./12_27_Known_vs_Unknown.md) |

---

## **Índice**

| Temario                                                                                                 |
| ------------------------------------------------------------------------------------------------------- |
| [404. Plan de respuesta a incidentes: marco y pasos](#404-plan-de-respuesta-a-incidentes-marco-y-pasos) |
| [405. Proceso de respuesta a incidentes](#405-proceso-de-respuesta-a-incidentes)                        |

# **Recuperación**

## **404. Plan de respuesta a incidentes: marco y pasos**

![Recuperación](/img/12_Secure_vs_Unsecure_Protocols/contencion_erradicacion_y_recuperacion.jpg "Recuperación")

Un **plan de respuesta a incidentes (IRP, Incident Response Plan)** es un conjunto de procedimientos estructurados que guían cómo una organización detecta, contiene, erradica y se recupera de un incidente de ciberseguridad.
El objetivo es **minimizar el impacto, restaurar operaciones y aprender del ataque**.

### 📚 Marcos de respuesta a incidentes

Los dos marcos más utilizados son:

1. **NIST (National Institute of Standards and Technology)** → ampliamente adoptado por gobiernos y empresas.
2. **SANS Institute** → usado como guía práctica en el sector privado y en equipos de respuesta (CSIRT/CSOC).

### 🛠️ Pasos de respuesta a incidentes del NIST (SP 800-61r2)

El **NIST** define 4 fases:

1. **Preparación**

   - Entrenamiento de personal.
   - Implementación de herramientas (SIEM, IDS/IPS, EDR).
   - Definición de roles y políticas.
   - Ejemplo: definir que el CSIRT se activa al detectar ransomware en un servidor crítico.

2. **Detección y análisis**

   - Monitorear logs, alertas y anomalías.
   - Clasificar el incidente (phishing, malware, DoS, insider threat).
   - Ejemplo: detectar un pico anormal de tráfico en un firewall que indica un ataque DDoS.

3. **Contención, erradicación y recuperación**

   - **Contención:** limitar el alcance (aislar máquinas infectadas).
   - **Erradicación:** eliminar la amenaza (borrar malware, revocar credenciales).
   - **Recuperación:** restaurar servicios (reinstalar servidores, aplicar parches).
   - Ejemplo: desconectar un servidor comprometido, limpiar el malware y restaurarlo desde un backup seguro.

4. **Actividad posterior al incidente (post-mortem)**

   - Documentar el incidente y el tiempo de respuesta.
   - Evaluar lo que funcionó y lo que falló.
   - Implementar mejoras.
   - Ejemplo: tras un ataque de phishing, capacitar a empleados y reforzar filtros de correo.

### 🛠️ Pasos de respuesta a incidentes de SANS

El **SANS Institute** propone un modelo de **6 pasos** (más detallado que NIST):

1. **Preparación**

   - Políticas, herramientas, entrenamiento.

2. **Identificación (detección y análisis)**

   - Reconocer si un evento es un incidente.

3. **Contención**

   - Corto plazo: frenar daños inmediatos.
   - Largo plazo: asegurar que el atacante no vuelva.

4. **Erradicación**

   - Eliminar la causa raíz del ataque.

5. **Recuperación**

   - Volver a la operación normal.

6. **Lecciones aprendidas**

   - Documentar y mejorar el plan.

👉 Como ves, el **NIST combina contención, erradicación y recuperación en un solo paso**, mientras que **SANS los separa en fases distintas** para mayor detalle.

### 🔎 Ejemplo práctico (comparando NIST vs SANS)

Supongamos un ataque de **ransomware en servidores de archivos**:

- **NIST**:

  - Fase 2: Detección (se identifican archivos cifrados).
  - Fase 3: Contención/Erradicación/Recuperación (aislar el servidor, eliminar ransomware, restaurar backups).
  - Fase 4: Post-mortem (mejorar segmentación de red y aplicar EDR).

- **SANS**:

  - Paso 2: Identificación (confirmar que es ransomware).
  - Paso 3: Contención (aislar el servidor).
  - Paso 4: Erradicación (eliminar ransomware y cuentas comprometidas).
  - Paso 5: Recuperación (restaurar servicio con backups).
  - Paso 6: Lecciones aprendidas (implementar MFA y simulacros).

### 🛡️ Respuesta a incidentes de CrowdStrike

**CrowdStrike**, empresa líder en ciberseguridad, combina el enfoque **NIST + SANS**, apoyado en su tecnología **Falcon (EDR/XDR)**.

Su metodología incluye:

1. **Detección rápida con IA + EDR** → anomalías de endpoints, comportamiento sospechoso.
2. **Contención inmediata** → “isolate host” para desconectar una máquina comprometida de la red en segundos.
3. **Erradicación asistida** → eliminación remota de malware y backdoors con Falcon.
4. **Recuperación guiada** → restauración de sistemas críticos desde backups.
5. **Informe detallado** → documentación para clientes, cumplimiento legal y auditorías.

👉 Ejemplo:

Si un atacante compromete credenciales en un servidor de AWS:

- **Falcon** detecta un proceso anómalo.
- CrowdStrike **aísla el host** automáticamente.
- Se **eliminan cuentas y accesos del atacante**.
- Se **restaura el servicio** con seguridad reforzada.
- Se entrega un **informe post-incidente** al cliente.

✅ **En resumen:**

- El **NIST** propone 4 fases: preparación, detección/análisis, contención/erradicación/recuperación y post-incidente.
- El **SANS** lo amplía a 6 pasos, separando contención, erradicación y recuperación.
- Proveedores como **CrowdStrike** aplican estos marcos con tecnología avanzada de **detección y respuesta (EDR/XDR)**.

---

[🔼](#índice)

---

## **405. Proceso de respuesta a incidentes**

La **respuesta a incidentes (IR)** es el conjunto ordenado de actividades, personas, procesos y herramientas que una organización aplica para **detectar, contener, erradicar, recuperar y aprender** tras un incidente de seguridad. Su objetivo es minimizar impacto (operativo, legal, reputacional) y volver a condiciones seguras con evidencia útil para auditoría/forense.

### Marcos y fases (resumen rápido)

Dos marcos muy usados:

**NIST (SP 800-61)** — 4 fases:

1. Preparación
2. Detección y análisis
3. Contención, erradicación y recuperación
4. Actividad posterior al incidente

**SANS** — 6 pasos (más granular):

1. Preparación
2. Identificación
3. Contención (corto y largo plazo)
4. Erradicación
5. Recuperación
6. Lecciones aprendidas

Ambos coinciden en la idea: planificar antes, detectar pronto, actuar rápido, restaurar y aprender.

### Fase 0 — Preparación (antes de un incidente)

**Qué incluye**

- Política IR, playbooks y runbooks por tipo de incidente.
- Equipo CSIRT/IR: roles y contactos (Incident Manager, Forense, SOC, Legal, PR, RRHH).
- Herramientas: SIEM, EDR/XDR, IDS/IPS, backups verificables, gestión de parches, MFA, logging centralizado (CloudTrail, etc.).
- Inventario de activos y clasificación de datos.
- Acuerdos con proveedores/forenses externos.

**Salida esperada:** playbooks listos, detección configurada, simulacros (tabletop) realizados.

### Fase 1 — Detección e identificación

**Objetivos**

- Confirmar que un evento es un incidente.
- Clasificar severidad (S1 crítico → S4 bajo).
- Recolectar artefactos iniciales: logs, alertas, IDs, capturas de pantalla.

**Fuentes típicas**

- Alertas SIEM/EDR/IDS, usuarios, monitoreo de red, logs de aplicaciones, honeypots.

**Salida:** ticket/INC con clasificación, primeros IoC (IP, hashes, usuarios), timeline inicial.

### Fase 2 — Triage y análisis

**Qué se hace**

- Correlacionar evidencias (SIEM, proxies, IAM logs, CloudTrail, VPC Flow Logs).
- Determinar vector de entrada (phishing, RDP expuesto, vulnerabilidad, credenciales robadas).
- Decidir contención inmediata o investigar más.

**Herramientas:** Splunk/ELK, EDR (CrowdStrike, SentinelOne), herramientas forenses (FTK, Volatility), queries Athena / SQL para grandes logs.

### Fase 3 — Contención (short-term y long-term)

**Short-term (primeras horas)**

- Aislar hosts comprometidos (EDR “isolate host”, Security Group de cuarentena, cordon en k8s).
- Bloquear IPs maliciosas, revocar sesiones, inhabilitar cuentas comprometidas.
- Crear snapshots / copias forenses antes de modificar sistemas.

**Long-term**

- Aplicar reglas firewall, cambiar configuraciones, despliegue de parches masivos, segmentación temporal.

**Check rápido (First 60)**

1. Identificar recursos afectados.
2. Crear snapshot/log copy.
3. Aislar el endpoint/servicio.
4. Inhabilitar credenciales comprometidas.
5. Notificar CSIRT y Legal.

### Fase 4 — Erradicación

**Qué hacer**

- Eliminar malware, backdoors y persistencias (cronjobs, servicios, claves SSH).
- Rotar/invalidar todas las credenciales y certificados potencialmente comprometidos.
- Parchear la vulnerabilidad explotada.
- Rebuild desde imágenes limpias si procede.

**Validación:** re-escaneo con antivirus/EDR/inspector y búsqueda de IoC residuales.

### Fase 5 — Recuperación

**Objetivo**

- Restaurar servicios desde backups limpios o imágenes verificadas.
- Reintegrar hosts gradualmente con monitoreo reforzado.
- Verificar integridad funcional y rendimiento.

**Pruebas:** validación de restores, test de integridad, monitoreo intensivo por al menos 7–30 días.

### Fase 6 — Lecciones aprendidas (post-incident)

**Actividades**

- Reunión formal post-mortem: cronología, decisiones, brechas.
- Actualizar playbooks, reglas SIEM, políticas y formación.
- Reporte ejecutivo y cierre de ticket con acciones pendientes.

**Entrega típica:** informe técnico + executive summary + lista de mitigaciones implementadas.

### Roles y responsabilidades (mínimo)

- **Incident Manager / Team Lead**: coordina y comunica.
- **SOC / Analista**: detección, triage, correlación.
- **Forense**: preservación y análisis de evidencia.
- **Ingeniería (redes/sistemas)**: contención y remediación.
- **Legal / Compliance**: obligaciones regulatorias y custodia de evidencia.
- **PR / Comunicación**: mensajes a clientes/medios.
- **Recursos Humanos**: cuando hay amenazas internas.

### Artefactos y preservación de evidencia

Siempre preservar antes de borrar:

- Imágenes de disco/volúmenes, volcado de RAM si aplica.
- Copia de logs (SIEM raw, firewall, proxy, AD).
- Hashes (SHA256) de archivos sospechosos.
- Capturas pantalla y metadatos.

Comando útiles defensivos (ejemplos):

```bash
# Linux - recolección rápida
ps aux
ss -tulpn
lsof -i
journalctl -u apache2 -S "2025-09-14"

# Windows - recolección rápida (PowerShell/ CMD)
tasklist /v
netstat -ano
wevtutil qe Security /q:"*[System[TimeCreated[@SystemTime>='2025-09-14T12:00:00Z']]]"
```

(Usar con criterio forense: no reiniciar si necesitas volcado RAM.)

### Comunicación y notificaciones

**Interna:** alerta a CISO, IT, Legal y negocio.
**Externa:** clientes/partners/reguladores según leyes y SLA. Mensaje mínimo: qué pasó, alcance, acciones tomadas, acciones recomendadas al receptor y contacto.

**Plantilla breve para notificación a cliente**

- Asunto: Incidente de seguridad — impacto en \[servicio]
- Resumen ejecutivo: qué ocurrió y cuándo
- Alcance: qué datos/servicios afectos
- Acciones tomadas: contención y mitigación
- Recomendaciones al cliente: cambiar credenciales, monitorizar accesos
- Contacto: CSIRT / soporte / portal de estado

### Automatización y orquestación (SOAR)

- Orquestar acciones repetitivas: bloqueo de IP, snapshot automático, aislamiento de host (con aprobaciones para acciones destructivas).
- Mantener balances: automatizar tareas seguras y dejar intervención humana para cambios críticos.

### Métricas y KPIs (lo que medir)

- **MTTD** — tiempo medio de detección.
- **MTTR** — tiempo medio de respuesta/remediación.
- Número de incidentes por categoría.
- % incidentes cubiertos por playbooks automáticos.
- Tiempo desde detección hasta contención.

### Ejemplo 1 — Phishing que roba credenciales (pasos resumidos)

1. Detección: usuario reporta correo; SIEM muestra login inusual.
2. Triage: confirmar login desde IP extraña; identificar servicios accedidos.
3. Contención: forzar cambio de contraseña, invalidar sesiones, revocar tokens, bloquear IP.
4. Erradicación: comprobar si hubo elevación de privilegios o creación de cuentas; eliminar sesiones OAuth comprometidas.
5. Recuperación: restablecer accesos con MFA y monitoreo.
6. Post-mortem: enviar campaña de concienciación y ajustar filtros de correo.

### Ejemplo 2 — Ransomware en servidor de archivos

1. Detección: EDR detecta cifrado masivo.
2. Contención inmediata: aislar servidor, bloquear comparticiones SMB. Crear snapshot forense.
3. Erradicación: identificar vector (RDP expuesto / credencial), parchear, eliminar persistencias, rotar credenciales.
4. Recuperación: restaurar desde backup verificado en entorno aislado, validar integridad.
5. Post-incident: segmentación de red, pruebas de backups y mejora de EDR/IDS.

### Playbook / plantilla mínima (para codificar)

```
Playbook: [Tipo de incidente]
- Alcance: [sistemas, datacenters, cloud]
- Detección: [fuente, criterios de alerta]
- Triage: [checks iniciales, commands]
- Contención inmediata: [acciones 0-60min]
- Contención larga: [SG/NACL/SCP, bloqueo IP]
- Erradicación: [pasos, rotación de credenciales, parcheo]
- Recuperación: [restore steps, validate]
- Comunicación: [quién informar y cómo]
- Evidencia: [qué snapshot, logs]
- Criterio de cierre: [tests pasados]
```

### Buenas prácticas finales

- Practica con **tabletop exercises** y simulacros reales.
- Mantén backups **offline y verificados**.
- Define runbooks por tipo de incidente (phishing, ransomware, DDoS, insider).
- Documenta todo durante la respuesta (registro de acciones).
- Involucra Legal/Compliance desde el inicio en incidentes con datos personales.
- Revisa y mejora continuamente (post-mortem accionable).

---

[🔼](#índice)

---

| **Inicio**         | **atrás 25**                 | **Siguiente 27**                  |
| ------------------ | ---------------------------- | --------------------------------- |
| [🏠](../README.md) | [⏪](./12_25_Eradication.md) | [⏩](./12_27_Known_vs_Unknown.md) |
