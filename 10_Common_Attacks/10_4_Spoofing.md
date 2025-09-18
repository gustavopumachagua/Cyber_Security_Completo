| **Inicio**         | **atrás 3**                                | **Siguiente 5**                       |
| ------------------ | ------------------------------------------ | ------------------------------------- |
| [🏠](../README.md) | [⏪](./10_3_Cross_Site_Request_Forgery.md) | [⏩](./10_5_What_is_SQL_Injection.md) |

---

## **Índice**

| Temario                                                                                                                        |
| ------------------------------------------------------------------------------------------------------------------------------ |
| [304. Definición y explicación de la suplantación de identidad](#304-definición-y-explicación-de-la-suplantación-de-identidad) |
| [305. Que es spoofing?](#305-que-es-spoofing)                                                                                  |

# **Spoofing**

## **304. Definición y explicación de la suplantación de identidad**

![Spoofing](/img/10_Common_Attacks/Spoofing.jpg "Spoofing")

### 🔹 Definición de suplantación de identidad

La **suplantación de identidad** o **spoofing** es una técnica usada por atacantes para **hacerse pasar por otra entidad legítima** (usuario, dispositivo o servicio) con el fin de engañar, robar información, interceptar comunicaciones o ganar acceso no autorizado.

En el ámbito digital, el atacante **falsifica información de identificación** (IP, direcciones MAC, emails, llamadas, DNS, etc.) para que la víctima confíe en que se trata de una fuente real.

### 🔹 ¿Qué es la suplantación de identidad?

En ciberseguridad, **spoofing** significa manipular datos de autenticación o identificación para hacerse pasar por otro.

- Puede ser a nivel de **persona** (phishing por email o llamadas),
- o a nivel **técnico** (suplantación de IP, DNS, ARP, etc.).

### 🔹 ¿Cómo funciona la suplantación de identidad?

1. **Preparación:** el atacante selecciona el tipo de identidad a falsificar (una dirección IP, un servidor DNS, un correo, etc.).
2. **Falsificación:** manipula cabeceras, protocolos o identificadores para aparentar ser la fuente legítima.
3. **Engaño:** la víctima (humana o sistema) confía en la fuente falsificada.
4. **Explotación:** el atacante intercepta, modifica o redirige comunicaciones, roba datos o ejecuta fraudes.

### 🔹 Tipos de suplantación de identidad

#### 1. **Suplantación de IP**

- El atacante falsifica la dirección IP de origen de los paquetes.
- Usado en ataques DoS/DDoS para ocultar el origen real.
- Puede servir también para evadir controles de acceso basados en IP.

#### 2. **Identificador de llamadas (Caller ID spoofing)**

- Se falsifica el número telefónico mostrado en la pantalla de la víctima.
- Usado en fraudes telefónicos o estafas (ej.: fingir ser un banco).

#### 3. **Suplantación de ARP (ARP Spoofing / ARP Poisoning)**

- En redes locales, el atacante envía mensajes ARP falsos para asociar su dirección MAC con la IP de otro dispositivo (como el router).
- Resultado: el tráfico de la víctima pasa por el equipo del atacante, permitiendo un ataque **Man-in-the-Middle (MitM)**.

#### 4. **Suplantación de DNS (DNS Spoofing o DNS Cache Poisoning)**

- El atacante manipula respuestas DNS para redirigir a la víctima a sitios maliciosos (ej.: la víctima escribe `banco.com` pero es redirigida a un servidor controlado por el atacante).

#### 5. **Suplantación de correo electrónico (Email Spoofing)**

- Alteración del campo _From_ en emails para que parezcan enviados desde una dirección confiable.
- Muy común en **phishing**.

### 🔹 Cómo prevenir la suplantación de IP (para propietarios de sitios web)

- **Filtros de paquetes** en routers/firewalls que bloqueen direcciones IP privadas o no enrutables.
- **Sistemas de detección de intrusos (IDS/IPS)** para detectar tráfico sospechoso.
- **Autenticación multifactor (MFA):** que no dependa solo de la IP del usuario.
- **Implementación de protocolos de seguridad (TLS/SSL):** protege la integridad de los datos aunque la IP sea falsificada.

### 🔹 Cómo prevenir la suplantación de identidad (en general)

✅ **En redes:**

- Usar **ARP inspection** y segmentación VLAN para evitar ARP spoofing.
- Configurar **DNSSEC** para validar respuestas DNS.
- Monitorear tráfico en busca de anomalías.

✅ **En sistemas web:**

- Implementar **SPF, DKIM y DMARC** para evitar suplantación de correos.
- Usar **certificados TLS válidos** para evitar phishing con páginas falsas.
- Proteger cuentas con **MFA**.

✅ **En usuarios finales:**

- Desconfiar de llamadas o emails que piden información sensible.
- Verificar siempre direcciones web y certificados antes de ingresar credenciales.
- Mantener actualizado el software de seguridad.

### 🔹 Resumen

- La **suplantación de identidad (spoofing)** es hacerse pasar por otra entidad legítima falsificando datos de identificación.
- Puede ser técnica (IP, ARP, DNS, email) o social (phishing, llamadas).
- Se usa para interceptar, redirigir o engañar a víctimas.
- La prevención depende de **tecnologías de seguridad en red**, **protocolos de autenticación robustos** y **conciencia del usuario**.

---

[🔼](#índice)

---

## **305. Que es spoofing?**

**Spoofing** es el término general para cualquier técnica en la que un atacante **finge ser otra entidad** (dirección, dispositivo, servicio, persona) manipulando datos de identificación —por ejemplo cabeceras de paquetes, registros DNS, direcciones MAC, remitentes de email o el identificador de llamada— con el objetivo de **engañar**, **interceptar**, **redirigir** o **fraudar**.

En pocas palabras: el atacante presenta credenciales o identificadores falsos para que la víctima (humana o sistema) confíe en algo que no es legítimo.

### ¿Cómo funciona (explicación conceptual)?

A nivel conceptual el spoofing se basa en **alterar o falsificar información de identificación** que el receptor usa para decidir “¿es fiable esto?”. Dependiendo del protocolo o sistema, esto puede significar:

- Cambiar la **dirección IP** origen de un paquete para ocultar el emisor o evadir filtros.
- Responder con una **respuesta DNS** manipulada para que un dominio apunte a una IP controlada por el atacante.
- Enviar mensajes **ARP falsos** para que los equipos en una LAN crean que la MAC del atacante es la del router (MitM).
- Forjar el campo **From** en un e-mail para que parezca venir de una entidad confiable.
- Falsificar el **Caller ID** para suplantar un número de teléfono.

El objetivo puede ser distinto: ocultar el origen de un ataque (por ejemplo en ciertos DDoS), redirigir tráfico para phishing o MITM, robar credenciales, o engañar a usuarios para que realicen acciones.

### Tipos principales de spoofing — explicación y ejemplos

#### 1. IP spoofing

**Qué es:** falsificar la dirección IP de origen en paquetes IP.

**Ejemplo:** un atacante envía paquetes con IP falsificada para que la víctima reciba respuestas o para ocultar el origen de tráfico en un ataque volumétrico. También se usa en ataques por reflexión/amplificación cuando el atacante falsifica la IP de la víctima para que las respuestas lleguen a ésta.

**Impacto típico:** dificultad para rastrear el atacante, facilitan ciertos DoS/reflectores.

#### 2. ARP spoofing / ARP poisoning

**Qué es:** enviar entradas ARP falsas en una LAN para asociar la IP de otro host (p. ej. la puerta de enlace) con la MAC del atacante.

**Ejemplo:** en una red Wi-Fi pública el atacante hace que los paquetes de los clientes pasen por su máquina, pudiendo capturarlos o modificarlos (MITM).

**Mitigación típica:** Dynamic ARP Inspection, 802.1X, segmentación de red.

#### 3. DNS spoofing / DNS cache poisoning

**Qué es:** forjar o envenenar respuestas DNS para que un nombre de dominio resuelva a una IP maliciosa.

**Ejemplo:** el usuario teclea `banco.com` pero la resolución DNS ha sido manipulada y apunta a una web falsa que roba credenciales.

**Mitigación típica:** DNSSEC, validación de respuestas, DoT/DoH, asegurar resolvers.

#### 4. Email spoofing

**Qué es:** falsificar el campo “From” o las cabeceras de un correo para que parezca enviado por otra persona/organización.

**Ejemplo:** phishing que aparenta venir del departamento de TI solicitando credenciales.

**Mitigación típica:** SPF, DKIM y DMARC en dominio y verificación en el receptor.

#### 5. Caller ID spoofing (suplantación telefónica)

**Qué es:** mostrar un número falso en el identificador de llamadas.

**Ejemplo:** un estafador hace aparecer el número del banco para pedir datos sensibles.

**Mitigación típica (telecom):** STIR/SHAKEN para llamadas sobre IP; educación del usuario.

#### 6. BGP / enrutamiento (IP prefix hijacking)

**Qué es:** anuncios de rutas BGP falsos que hacen que partes del tráfico se encaminen por redes del atacante.

**Ejemplo:** un AS anuncia prefijos que no le pertenecen y “captura” tráfico de destino, posibilitando espionaje o manipulación.

**Mitigación típica:** RPKI, filtros de peering, buenas prácticas de enrutado.

#### 7. GPS / GNSS spoofing

**Qué es:** emitir señales GNSS falsas para engañar a receptores GPS (p. ej. de drones, barcos).

**Ejemplo:** desviar la navegación de un vehículo autónomo si su receptor acepta señales no verificadas.

**Mitigación típica:** receptores con anti-spoofing, correlación con sensores inerciales.

#### 8. Web / URL spoofing (phishing de sitios)

**Qué es:** crear páginas o URLs muy parecidas al sitio legítimo o manipular indicadores visibles (favicon, subdominios engañosos).

**Ejemplo:** un dominio similar (`b@nc0.com` o `banco.example-login.com`) usado para capturar credenciales.

**Mitigación típica:** certificados TLS validos, HSTS, navegación segura y educación.

### Señales de detección (qué monitorear)

- **Anomalías en tablas ARP** (IP duplicada, MAC cambiada).
- **TTL, IPID o patrones de cabecera inusuales** que indiquen paquetes con IP falsificada.
- **Resoluciones DNS que apuntan a IPs inusuales** o cambios repentinos en las respuestas.
- **Aumento de tráfico de respuesta no solicitado** (indicador de reflexión/amplificación).
- **Emails con dominios legítimos pero fallos en DKIM/SPF/DMARC.**
- **Usuarios reportando números de teléfono extraños o comunicaciones sospechosas.**

Herramientas de monitoreo (IDS/IPS, NetFlow/IPFIX, Zeek/Bro, sistemas de telemetría) ayudan a detectar patrones.

### Cómo prevenir y mitigar spoofing (por categoría)

#### Prevención de IP spoofing (red/ISP)

- **Egress / Ingress filtering (BCP38 / RFC 2827):** evitar que hosts salientes envíen paquetes con IPs que no les pertenecen.
- **uRPF (Unicast Reverse Path Forwarding):** filtra paquetes con origen no alcanzable por la ruta inversa.
- **Listas de control de acceso en routers** y monitoreo del tráfico.

#### Prevención de ARP spoofing (LAN)

- **Dynamic ARP Inspection (DAI)** en switches, **DHCP snooping** y **port security**.
- Segmentación de red y uso de **802.1X** para autenticar dispositivos.

#### Prevención de DNS spoofing

- **DNSSEC** para firmar respuestas y detectar manipulaciones.
- Usar resolvers confiables, monitorizar cache poisoning y emplear DoT/DoH cuando proceda.

#### Prevención de email spoofing

- Implementar **SPF, DKIM y DMARC** y configurar políticas de rechazo/monitoreo.
- Filtrado y entrenamiento de usuarios.

#### Prevención de Caller ID spoofing

- Adopción de **STIR/SHAKEN** en entornos VoIP y controles en proveedores telefónicos.
- Verificación adicional para transacciones críticas.

#### Prevención de BGP hijack

- **RPKI / ROA** y filtros de prefijos en routers de peering; controles de confianza entre AS.
- Auditoría de anuncios de rutas y alertas en cambios inusuales.

#### Prevención de GPS spoofing

- Receptores GNSS con anti-spoofing, fusión con sensores internos y validación temporal/geográfica.

#### En general (aplicación y usuario)

- **TLS/mTLS** para autenticidad de servicios.
- **Autenticación multifactor (MFA)** y checks adicionales para operaciones sensibles.
- **Educación** del usuario sobre phishing y verificación de canales.
- **Registro y monitoreo continuo**, playbooks de respuesta.

### Cómo estudiar/practicar legalmente (hacking ético)

- **Siempre** pide permiso escrito antes de probar técnicas contra sistemas que no sean tuyos.
- Monta un **laboratorio aislado**: VMs/containers en una red virtual cerrada (no conectada a Internet) para experimentar con tráfico, resolver DNS, simular ARP, etc.
- Usa plataformas educativas: **OWASP Juice Shop**, **WebGoat**, laboratorios de redes (GNS3, EVE-NG) y CTFs para escenarios controlados.
- Practica **detección y mitigación** (p. ej. analiza con Wireshark, Zeek, NetFlow) en lugar de técnicas ofensivas contra terceros.
- Estudia buenas prácticas, RFCs y guías (BCP38, RPKI, DNSSEC, SPF/DKIM/DMARC, STIR/SHAKEN).

> Nota ética: no proporciono comandos ni instrucciones para realizar spoofing en sistemas reales.

### Resumen práctico (chuleta rápida)

- **Spoofing = fingir identidad** para engañar a sistemas o personas.
- **Tipos claves:** IP, ARP, DNS, Email, Caller ID, BGP, GPS, URL/web.
- **Detección:** anomalías en tablas de red, TTL, patrones DNS y fallos en firmas/ DKIM/SPF.
- **Prevención:** ingress/egress filtering, DNSSEC, SPF/DKIM/DMARC, DAI/802.1X, RPKI, STIR/SHAKEN, TLS/MFA.
- **Aprende en laboratorio** y enfócate en **defensa y detección**: eso es hacking ético responsable.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 3**                                | **Siguiente 5**                       |
| ------------------ | ------------------------------------------ | ------------------------------------- |
| [🏠](../README.md) | [⏪](./10_3_Cross_Site_Request_Forgery.md) | [⏩](./10_5_What_is_SQL_Injection.md) |
