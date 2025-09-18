| **Inicio**         | **atrás 29**                                            |
| ------------------ | ------------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_29_Penetration_Testing_Rules_of_Engagement.md) |

---

## **Índice**

| Temario                                                                                                                      |
| ---------------------------------------------------------------------------------------------------------------------------- |
| [262. Mejores prácticas para la segmentación de la red](#262-mejores-prácticas-para-la-segmentación-de-la-red)               |
| [263. Hoja de referencia para la segmentación de red de OWASP](#263-hoja-de-referencia-para-la-segmentación-de-red-de-owasp) |

# **Perímetro vs DMZ vs Segmentación**

## **262. Mejores prácticas para la segmentación de la red**

![Perímetro vs DMZ vs Segmentación](/img/8_Security_Skills_and_Knowledge/Perimetro_vs_DMZ_vs_Segmentación.jpg "Perímetro vs DMZ vs Segmentación")

### 1. Objetivos y principios de diseño

- **Reducir el blast radius**: si un host es comprometido, que el atacante no llegue libremente al resto.
- **Aplicar el principio de menor privilegio en la red**: solo permitir el tráfico necesario entre componentes.
- **Defensa en profundidad**: la segmentación es una capa más junto a firewalls, EDR, WAF, IDS/IPS y MFA.
- **Identidad sobre IP cuando sea posible**: políticas basadas en servicio/etiquetas/identidad (ABAC, security groups) en lugar de confiar sólo en IPs.
- **Visibilidad y monitoreo**: cada segmento debe generar logs/flow para detección.
- **Automatización y versionado**: IaC para reglas de red (Terraform/Ansible) y revisión en CI/CD.
- **Operabilidad**: no tanto segmentación por capricho; evita políticas imposibles de mantener.

### 2. Tipos de segmentación (resumen)

- **Perimetral / North–South**: tráfico entre usuario/internet y tus recursos (firewall perimetral, WAF).
- **Este–Oeste (East–West)**: tráfico entre servidores/servicios internos — donde la microsegmentación aporta más valor.
- **VLANs / Subredes**: separación lógica a nivel L2/L3.
- **Microsegmentación**: políticas a nivel de host o proceso (host-based firewall, NSX, Illumio, Cilium).
- **Segmentación en la nube**: VPCs, subnets públicas/privadas, security groups, NACLs.
- **Segmentación de contenedores / Kubernetes**: NetworkPolicies, service mesh (mTLS, authn).
- **Segmentación física**: separar switches, VRFs, enlaces dedicados para sistemas críticos (OT/ICS).

### 3. Pasos para diseñar la segmentación (metodología)

1. **Inventario y clasificación de activos**

   - CMDB actualizada: IPs, servicios, dueños, criticidad, cumplimiento (PCI, HIPAA).

2. **Modelado de amenazas y prioridades**

   - ¿Qué activos son más atractivos para un atacante? (DBs, AD, certificados).

3. **Definir zonas/trust levels**

   - Ej.: Internet → DMZ → App Tier → DB Tier → Management.

4. **Definir políticas (what’s allowed)**

   - Lista explícita: origen, destino, puerto/protocolo, justificación.

5. **Elegir puntos de aplicación**

   - Perimeter NGFW, internal firewall, ACLs en routers, host-based firewall, SGs cloud.

6. **Implementar en fases y probar**

   - Piloto en non-prod → recolección de telemetría → ajustar.

7. **Monitorear y mantener**

   - Flow logs, IDS/EDR, alertas por reglas de desviación.

### 4. Patrones y ejemplos de arquitectura (con ejemplos)

#### Patrón clásico 3-tier (web → app → db)

- **VLAN 10 — Web** (10.1.10.0/24) — servidores frontales en DMZ.
- **VLAN 20 — App** (10.1.20.0/24) — lógica de negocio.
- **VLAN 30 — DB** (10.1.30.0/24) — base de datos con acceso restringido.
- **Reglas**:

  - Web → App: permitir TCP/443 y TCP/8080 hacia IPs de App.
  - App → DB: permitir TCP/5432 (Postgres) solo desde App tier.
  - Todo lo demás entre VLANs: DENY.

**Ejemplo (matriz simplificada)**

| Origen             | Destino            | Puerto/protocolo | Acción                | Justificación         |
| ------------------ | ------------------ | ---------------: | --------------------- | --------------------- |
| Web (10.1.10.0/24) | App (10.1.20.0/24) |    TCP 443, 8080 | ALLOW                 | Tráfico HTTP(S) a app |
| App (10.1.20.0/24) | DB (10.1.30.10)    |         TCP 5432 | ALLOW                 | DB desde app servers  |
| Any                | DB                 |               \* | DENY                  | Bloqueo por defecto   |
| Management         | All                |          SSH/RDP | ALLOW desde jump-host | Acceso gestionado     |

#### Ejemplo de reglas iptables (ilustrativo, entorno controlado)

```bash
# permitir web -> app (8080)
iptables -A FORWARD -s 10.1.10.0/24 -d 10.1.20.10/32 -p tcp --dport 8080 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT

# permitir app -> db (5432)
iptables -A FORWARD -s 10.1.20.0/24 -d 10.1.30.10/32 -p tcp --dport 5432 -m conntrack --ctstate NEW,ESTABLISHED -j ACCEPT

# denegar todo lo demás entre zonas (policy deny-by-default)
iptables -A FORWARD -s 10.1.10.0/24 -d 10.1.30.0/24 -j DROP
```

> **Nota:** usa estas reglas como ejemplo de principio; en producción aplica políticas gestionadas y versionadas (no edites directamente en caja sin control).

### 5. Segmentación en la nube — prácticas y ejemplos (AWS/Azure/GCP)

- **VPC / Subnets**: separar subnets públicas (load balancers) y privadas (app, db).
- **Security Groups (SG)**: _stateful_ por instancia, referencia por SG (mejor que IPs). Ejemplo: App-SG permite tráfico entrante desde Web-SG en puerto 8080.
- **Network ACLs (NACLs)**: _stateless_, útiles para capa adicional (bloqueos a nivel subnet).
- **Flow logs**: habilitar VPC Flow Logs para visibilidad east-west.
- **Bastion / Jump hosts** para acceso gestionado.
- **Zero trust y IAM**: combine network segmentation con IAM roles y políticas.

**Ejemplo Security Group (conceptual)**

- `web-sg`: permite 80/443 desde 0.0.0.0/0 (internet)
- `app-sg`: permite 8080 desde web-sg (referencia)
- `db-sg`: permite 5432 desde app-sg

### 6. Microsegmentación y host-based policies

- **Host-based firewalls** (Windows Defender Firewall, iptables/ufw) y EDR pueden imponer reglas por host/proceso.
- **VM/hypervisor enforcement**: VMware NSX, Illumio, Guardicore permiten políticas centradas en workload.
- **Benefits**: control fino por proceso, etiqueta/identity-based, reduce dependencia en la topología IP.

**Ejemplo de microsegmentación**:

- Política: _solo el binario `/usr/bin/myapp` en app-server-A puede conectarse a db:5432_.
- Implementación: control a nivel de host (firewall + process whitelisting) o mediante agente de microsegmentación.

### 7. Segmentación en Kubernetes y contenedores

- **NetworkPolicies**: controlan qué pods pueden comunicarse.
- **Service Mesh (Istio/Linkerd)**: mutual TLS entre servicios, políticas de autorización y observabilidad.
- **Namespaces**: aislan por equipo/entorno pero no sustituyen a NetworkPolicies.

**Ejemplo NetworkPolicy (Kubernetes)**:

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
  name: allow-web-to-app
  namespace: prod
spec:
  podSelector:
    matchLabels:
      app: app-server
  ingress:
    - from:
        - podSelector:
            matchLabels:
              app: web-server
      ports:
        - protocol: TCP
          port: 8080
```

### 8. Autenticación/Identidad + segmentación (Identity-aware)

- Cuando sea posible, **políticas por identidad** (principal/service account) son más resilientes que por IP.
- En la nube: use IAM roles, security group by role, y atributos (tags) para organizar y aplicar políticas.
- En redes corporativas: NAC (Network Access Control) para colocar endpoints en VLANs según posture (MDM/EDR posture).

### 9. Monitorización y detección por segmento

- Habilita **flow logs** (NetFlow, sFlow, VPC Flow Logs).
- Sensorizar tráfico East–West con **Zeek/Suricata** y correlación en SIEM (Splunk/Elastic/Azure Sentinel).
- Genera alertas por **retries inusuales**, conexiones a puertos no permitidos o movimientos fuera de patrón.
- Mantén dashboards por zona (traffic volume, rejected flows, anomalies).

### 10. Pruebas, validación y mantenimiento

- **Pruebas controladas (authorized)**: red-team/purple-team o pentest para verificar que las políticas impiden movimiento lateral.
- **Automated policy validation**: herramientas que simulan flows (policy simulation) y pruebas unitarias de reglas.
- **Revisión periódica**: cada cambio de arquitectura obliga a revisar reglas (policy drift).
- **Documentación**: matriz de comunicación entre zonas, owner y justificación.

### 11. Errores comunes y cómo evitarlos

- **Flat network**: sin zonas, facilita movimiento lateral. → Solución: definir zonas críticas y empezar por ahí.
- **Segmentation por IP estática**: difícil de mantener con DHCP/Cloud/Containers. → Usar etiquetas/SGs/identidad.
- **Reglas demasiado permisivas** (“ALLOW \* \* \*”) → usar deny-by-default.
- **Falta de visibilidad**: no ver logs East–West → habilitar flow logs/IDS.
- **No involucrar operaciones**: cambios que rompen servicios → pilot y coordinar con owners.
- **Over-segmentation** que complica soporte → balance entre seguridad y operatividad.

### 12. Plan de migración (implementación por fases)

1. **Discovery**: inventario, dependencias, mapa de comunicaciones (network mapping / application dependency mapping).
2. **Diseño**: zonas, políticas de acceso mínimo, matrix de allowed flows.
3. **Piloto**: aplicar en entorno no-prod o una aplicación no crítica.
4. **Iterar y ajustar**: recoger telemetría, corregir falsos positivos.
5. **Despliegue por oleadas**: por BU o por stack (web→app→db).
6. **Operación continua**: auditorías periódicas, control de cambios y revisión de políticas.

### 13. Checklist rápido de buenas prácticas

- [ ] Inventario y clasificación de activos (CMDB) actualizado.
- [ ] Zonas definidas (DMZ, App, DB, Management, Dev).
- [ ] Regla de deny-by-default entre zonas.
- [ ] Políticas basadas en identidad/tags cuando sea posible.
- [ ] Microsegmentación para activos críticos.
- [ ] Flow logs y sensorización este–oeste habilitada.
- [ ] Automatización de reglas y versionado (IaC).
- [ ] Pruebas de validación (red/purple team) y playbooks de incident response.
- [ ] Revisión periódica y owner asignado para cada regla.

### 14. Métricas y KPI que medir

- **% de hosts en zonas con microsegmentación aplicadas.**
- **Número de reglas ALLOW excesivas** (por revisión).
- **Número de flows denegados y su tendencia** (posible detección).
- **MTTR (mean time to contain)** — tiempo desde detección hasta aislamiento.
- **Drift rate**: número de cambios no documentados en políticas.

### 15. Runbook corto para incidente (aislamiento vía segmentación)

1. Detectada actividad sospechosa en host X.
2. Identificar owner y contexto (servicio, criticalidad).
3. Aplicar **temporary ACL**: bloquear todo el tráfico desde host X excepto management desde jump-host.
4. Aislar físicamente o cambiar SGs/host-based firewall.
5. Capturar evidencia (pcap, memory dump) y notificar IR.
6. Remediar y reaplicar reglas normales cuando se valide.
7. Post-mortem y ajuste de políticas.

---

[🔼](#índice)

---

## **263. Hoja de referencia para la segmentación de red de OWASP**

### 1. ¿Qué es la segmentación de red según OWASP?

Es la práctica de **dividir la infraestructura en diferentes zonas lógicas** según la función y el nivel de confianza de cada componente (por ejemplo: servidores web, aplicaciones, bases de datos).
El objetivo es:

- **Separar capas de la aplicación** (presentación, lógica, datos).
- **Restringir comunicaciones innecesarias**.
- **Proteger los activos críticos** mediante controles de acceso en red.

👉 En palabras simples: **cada capa debe hablar solo con la que necesita** y nada más.

### 2. Principios clave de la hoja de referencia

- **Zonas separadas por firewalls o ACLs**: Internet, DMZ, capa de aplicación, capa de datos.
- **Denegar por defecto**: solo permitir tráfico explícitamente autorizado.
- **Restricción de puertos/protocolos**: abrir solo lo necesario (ejemplo: 443 para HTTPS, 3306 para MySQL desde la app).
- **Separación de privilegios**: las máquinas de administración (jump servers) deben estar en su propia red.
- **Monitoreo y logging**: cada segmento debe registrar actividad de red para detectar accesos anómalos.

### 3. Ejemplo típico recomendado por OWASP (arquitectura 3-tier)

#### 🔹 Zonas

1. **Zona de usuario/Internet** → tráfico público.
2. **DMZ (zona desmilitarizada)** → balanceadores de carga, servidores web.
3. **Capa de aplicación** → lógica del negocio (APIs, microservicios).
4. **Capa de datos** → bases de datos, almacenes sensibles.
5. **Red de administración** → gestión, monitoreo, backup.

#### 🔹 Reglas de comunicación (permitir solo lo necesario)

| Origen     | Destino   | Puerto         | Acción | Justificación          |
| ---------- | --------- | -------------- | ------ | ---------------------- |
| Internet   | DMZ (Web) | 80/443         | ALLOW  | Acceso web público     |
| DMZ (Web)  | App Tier  | 443/8080       | ALLOW  | Comunicación web → API |
| App Tier   | DB Tier   | 1433/3306/5432 | ALLOW  | App → Base de datos    |
| Admin Zone | All Tiers | SSH/RDP, SNMP  | ALLOW  | Gestión y monitoreo    |
| Any        | Any       | \*             | DENY   | Bloqueo por defecto    |

### 4. Ejemplo práctico (escenario con e-commerce)

Supongamos un **sitio web de e-commerce**:

- Los **clientes** acceden a `https://tienda.com` → llega al **servidor web en DMZ**.
- El **servidor web** se comunica con el **servidor de aplicación** (API que maneja carrito, usuarios, pagos).
- El **servidor de aplicación** consulta datos en la **base de datos de clientes y pedidos**.
- Los **administradores** no entran directamente al servidor: primero acceden a un **jump server** en la red de administración.

⚡ **Qué pasaría sin segmentación:**

Un atacante que explota una vulnerabilidad en el servidor web podría acceder directamente a la base de datos → robo de tarjetas.

⚡ **Con segmentación (OWASP):**

El servidor web solo puede hablar con la capa de aplicación, y esta solo con la base de datos → el atacante tendría que saltar varias barreras adicionales.

### 5. Recomendaciones adicionales de OWASP

- **Segmentación en entornos cloud**: usar VPCs, subnets privadas/públicas, security groups y NACLs.
- **Segmentación en Kubernetes**: usar _NetworkPolicies_ para limitar tráfico entre pods.
- **Segregación de entornos**: producción, pruebas y desarrollo en redes diferentes.
- **IDS/IPS y WAF**: colocar controles adicionales en las fronteras de cada segmento.
- **Zero Trust**: no asumir confianza solo por estar en la misma red, aplicar autenticación y cifrado.

### 6. Ejemplo de configuración (AWS inspirado en OWASP)

- **VPC con 3 subnets**:

  - `PublicSubnet` → balanceador de carga (ELB) y servidores web.
  - `PrivateSubnet-App` → instancias con API y lógica de negocio.
  - `PrivateSubnet-DB` → instancias RDS (accesibles solo desde la app).

- **Security Groups**:

  - `Web-SG`: permite 443 desde `0.0.0.0/0`.
  - `App-SG`: permite 8080 solo desde `Web-SG`.
  - `DB-SG`: permite 5432 solo desde `App-SG`.

Esto refleja la misma **arquitectura en capas** que OWASP recomienda.

### 7. Beneficios clave

- 🔒 **Reduce el impacto de un ataque** (movimiento lateral más difícil).
- ✅ **Cumplimiento** con PCI DSS, HIPAA, ISO 27001 (todos exigen segmentación).
- 📉 **Menor superficie de ataque** al exponer menos servicios.
- 🕵️ **Mejor visibilidad y detección** de anomalías.

### 8. Resumen rápido (mini-cheat sheet OWASP)

- Divide tu red en zonas lógicas (DMZ, App, DB, Admin).
- Aplica _deny by default_ en cada frontera.
- Permite solo los puertos y protocolos estrictamente necesarios.
- Separa redes de administración del tráfico de usuarios.
- Monitorea y registra el tráfico entre zonas.
- Extiende la segmentación a cloud, contenedores y entornos Dev/Prod.

👉 En resumen: La **Hoja de referencia de OWASP para segmentación de red** propone una **arquitectura en capas bien definidas** (Internet → DMZ → App → DB → Administración), con reglas mínimas y explícitas, aplicando _principio de menor privilegio_ en el nivel de red.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 29**                                            |
| ------------------ | ------------------------------------------------------- |
| [🏠](../README.md) | [⏪](./8_29_Penetration_Testing_Rules_of_Engagement.md) |
