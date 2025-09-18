| **Inicio**         | **atrás 11**         | **Siguiente 13**      |
| ------------------ | -------------------- | --------------------- |
| [🏠](../README.md) | [⏪](./12_11_DLP.md) | [⏩](./12_13_HIPS.md) |

---

## **Índice**

| Temario                                                                                                      |
| ------------------------------------------------------------------------------------------------------------ |
| [379. ¿Qué es un firewall?](#379-qué-es-un-firewall)                                                         |
| [380. ¿Qué es un firewall de próxima generación (NGFW)?](#380-qué-es-un-firewall-de-próxima-generación-ngfw) |

# **Firewalls & Next-Generation Firewalls**

## **379. ¿Qué es un firewall?**

![firewall](/img/12_Secure_vs_Unsecure_Protocols/firewall.png "firewall")

Un **firewall** o **cortafuegos** es un sistema de **seguridad de red** que actúa como un **filtro entre una red confiable (como la red interna de una empresa o tu casa) y una red no confiable (como Internet)**.

Su función principal es **permitir o bloquear tráfico de datos** en función de un conjunto de reglas de seguridad previamente configuradas.

👉 Ejemplo: el firewall puede bloquear accesos no autorizados desde Internet hacia tu computadora, pero permitir que tú navegues en la web sin problemas.

### 🛡️ Cortafuegos: significado y definición

El término **cortafuegos** viene de la construcción: un muro que detiene la propagación de incendios entre edificios.
En informática, el concepto es similar: el firewall “detiene el fuego digital”, es decir, **impide que amenazas externas se propaguen hacia tu red interna**.

### ⚙️ ¿Cómo funcionan los firewalls?

El firewall inspecciona todo el **tráfico entrante y saliente** de la red y lo compara con **reglas predefinidas**.

### Métodos de funcionamiento:

1. **Filtrado de paquetes**: analiza encabezados de paquetes (IP, puerto, protocolo).

   - Ejemplo: bloquear todo el tráfico entrante al puerto 23 (Telnet) porque es inseguro.

2. **Firewall de estado (Stateful)**: recuerda conexiones activas y permite solo paquetes que correspondan a una sesión válida.

   - Ejemplo: si inicias conexión con un sitio web, el firewall permite respuestas, pero no conexiones inesperadas.

3. **Firewall de aplicación (Proxy o WAF)**: filtra tráfico específico de aplicaciones como HTTP/HTTPS.

   - Ejemplo: bloquear un intento de inyección SQL en una aplicación web.

4. **Firewall de próxima generación (NGFW)**: incluye inspección profunda de paquetes, detección de malware e integración con IDS/IPS.

### 🚨 ¿Cuáles son los peligros de las noticias falsas?

- Un **firewall no detiene noticias falsas** (eso se combate con alfabetización digital y verificación de fuentes).
- Pero sí puede **bloquear sitios web maliciosos** que difunden _fake news_ junto con malware o phishing.

👉 Ejemplo: un firewall con filtrado web puede impedir el acceso a páginas fraudulentas que buscan robar datos haciéndose pasar por portales de noticias.

### 👨‍💻 ¿Quién inventó los firewalls?

Los **primeros firewalls** aparecieron a finales de los **años 80**.

- **1988 – Digital Equipment Corporation (DEC)** desarrolló el primer firewall comercial.
- **Bill Cheswick y Steve Bellovin** (AT\&T) publicaron investigaciones en los 90 que sentaron las bases de los firewalls modernos.

### 📌 Importancia de los firewalls

Los firewalls son esenciales porque:

- Protegen contra **accesos no autorizados**.
- Reducen el riesgo de **ciberataques** (malware, ransomware, escaneos de puertos).
- Aseguran el cumplimiento de **normativas** (PCI DSS, ISO 27001, HIPAA).
- Permiten **segmentar redes** (ejemplo: separar la red de invitados de la red corporativa).

👉 Sin un firewall, una red está expuesta directamente a Internet y a miles de ataques automatizados diarios.

### 🔒 ¿Qué hace la seguridad del firewall?

Un firewall:

- **Bloquea tráfico sospechoso** antes de que llegue al usuario.
- **Controla accesos internos** (ej. restringir que empleados accedan a ciertos servidores).
- **Monitorea logs y eventos** para detectar intentos de ataque.
- **Protege contra malware conocido** cuando se combina con firmas de seguridad.

👉 Ejemplo: un firewall de empresa puede bloquear que un ransomware contacte con su servidor de comando y control.

### 🖥️ Ejemplos de firewalls

1. **Firewalls de software**: instalados en PCs o servidores.

   - Ejemplo: **Windows Defender Firewall**, **iptables** en Linux.

2. **Firewalls de hardware**: dispositivos dedicados que se colocan entre la red interna y el router.

   - Ejemplo: Cisco ASA, Fortinet FortiGate, Palo Alto Networks.

3. **Firewalls en la nube (Cloud Firewall)**: protegen infraestructuras en AWS, Azure, Google Cloud.

   - Ejemplo: AWS Network Firewall, Azure Firewall.

4. **WAF (Web Application Firewall)**: filtra tráfico HTTP/HTTPS.

   - Ejemplo: Cloudflare WAF, Imperva, F5.

### 🛠️ Cómo utilizar la protección del firewall

1. **Habilitar el firewall del sistema operativo** (ejemplo: Windows Defender o ufw en Linux).
2. **Configurar reglas de acceso**:

   - Permitir solo los puertos necesarios (ej. 80/443 para web).
   - Bloquear puertos no usados (ej. 23, 445).

3. **Implementar firewalls de red** en empresas para segmentar tráfico.
4. **Usar firewalls de próxima generación (NGFW)** para mayor seguridad.
5. **Monitorear logs regularmente** para detectar intentos de intrusión.

👉 Ejemplo práctico:

En una empresa, se configura el firewall para que:

- Solo la red administrativa pueda acceder al servidor de base de datos.
- Los usuarios invitados solo tengan acceso a Internet, no a la red interna.

✅ **Resumen final**

Un **firewall es la primera línea de defensa en ciberseguridad**: actúa como un guardián que decide qué tráfico entra y qué tráfico no. Existen firewalls de software, hardware, aplicación y en la nube. Aunque no bloquea noticias falsas 😅, sí protege contra accesos maliciosos y ataques de red.

---

[🔼](#índice)

---

## **380. ¿Qué es un firewall de próxima generación (NGFW)?**

Un **NGFW (Next-Generation Firewall)** es un tipo avanzado de firewall que combina las funciones de un firewall tradicional (filtrado de paquetes y control de acceso) con **características de seguridad modernas**, como:

- **Inspección profunda de paquetes (DPI)**
- **Prevención de intrusiones (IPS)**
- **Control de aplicaciones**
- **Inteligencia de amenazas en tiempo real**

👉 Ejemplo: mientras un firewall clásico solo bloquearía tráfico por puertos (ej. bloquear puerto 23 de Telnet), un **NGFW puede detectar que dentro del puerto 80 (HTTP) alguien está intentando inyectar un comando malicioso en una aplicación web, y bloquearlo**.

### ⚙️ ¿Qué funciones tiene un NGFW?

1. **Filtrado de paquetes** (nivel básico).
2. **Inspección profunda de paquetes (DPI)**.
3. **Conocimiento y control de aplicaciones (App Control)**.
4. **Prevención de intrusiones (IPS integrado)**.
5. **Protección contra malware avanzado** (sandboxing, análisis en la nube).
6. **VPN seguras** (IPSec o SSL VPN).
7. **Integración con inteligencia de amenazas global**.
8. **Segmentación de red y microsegmentación**.

### 📦 ¿Qué es el filtrado de paquetes y la inspección profunda de paquetes (DPI)?

- **Filtrado de paquetes**: revisa encabezados de tráfico (IP, puerto, protocolo) y decide si permitirlo o no.

  👉 Ejemplo: permitir solo conexiones entrantes en el puerto 443 (HTTPS).

- **Inspección profunda de paquetes (Deep Packet Inspection - DPI)**: analiza también el **contenido del tráfico**, no solo el encabezado.

  👉 Ejemplo: aunque un atacante use HTTPS, el NGFW puede desencriptar (con SSL inspection) y detectar un archivo malicioso en la descarga.

### 📱 ¿Qué es el conocimiento y el control de aplicaciones?

Un NGFW no solo ve **puertos y protocolos**, también identifica y controla **qué aplicaciones están usando la red**.

👉 Ejemplo:

- Diferenciar entre **Skype, Zoom, Microsoft Teams** aunque todos usen el mismo puerto.
- Bloquear **Facebook** en horario laboral, pero permitir **LinkedIn**.
- Limitar **YouTube** solo para ciertos usuarios.

Esto se conoce como **App-ID** en algunos fabricantes como Palo Alto Networks.

### 🛡️ ¿Qué es la prevención de intrusos (IPS)?

Un **IPS (Intrusion Prevention System)** dentro del NGFW detecta y bloquea **ataques conocidos y desconocidos** analizando patrones de tráfico y firmas.

👉 Ejemplo:

- Bloquear un intento de **SQL Injection** contra un servidor web.
- Prevenir un ataque de **fuerza bruta SSH** desde direcciones IP sospechosas.

### 🌍 ¿Qué es la inteligencia de amenazas?

La **inteligencia de amenazas (Threat Intelligence)** es la capacidad del NGFW de conectarse a **bases de datos en la nube** con información actualizada de:

- IPs maliciosas.
- URLs de phishing.
- Dominios usados por malware.
- Patrones de tráfico sospechoso.

👉 Ejemplo: si aparece un nuevo ransomware en Asia, el proveedor del NGFW actualiza sus firmas globalmente y tu firewall ya lo bloquea antes de que llegue a tu red.

### 💻 ¿Los firewalls de nueva generación están basados en hardware o en software?

- **Basados en hardware**: appliances físicos dedicados, muy usados en empresas (Cisco Firepower, Fortinet FortiGate, Palo Alto Networks).
- **Basados en software/virtuales**: funcionan como máquinas virtuales en la nube o en servidores locales (Azure Firewall, AWS Network Firewall, pfSense NG).
- **En la nube (Cloud NGFW)**: ofrecidos como servicio gestionado (ej. Cloudflare Magic Firewall, Prisma Access de Palo Alto).

👉 Ejemplo:

- Una empresa pequeña puede usar **pfSense NGFW** en un servidor.
- Una multinacional puede usar **Fortinet FortiGate hardware** en su sede y **Prisma Access** en la nube para sus oficinas remotas.

### ❓ Preguntas frecuentes sobre NGFW

#### 🔹 ¿Qué es un firewall de última generación (NGFW)?

Es un firewall avanzado que combina **control de red, seguridad de aplicaciones e inteligencia de amenazas** en una sola solución.

#### 🔹 ¿Qué es la inspección profunda de paquetes (DPI)?

Es la capacidad de revisar no solo **encabezados de paquetes**, sino también el **contenido del tráfico**, detectando malware, comandos maliciosos o tráfico no autorizado.

#### 🔹 ¿Qué es el conocimiento y el control de aplicaciones en los NGFW?

Permite identificar y controlar el uso de **aplicaciones específicas** en la red (ej. bloquear TikTok, permitir Teams).

#### 🔹 ¿Qué es un sistema de prevención de intrusiones (IPS) en un NGFW?

Es un componente que detecta y bloquea ataques como **exploit kits, inyecciones SQL, fuerza bruta, malware**, en tiempo real.

#### 🔹 ¿Qué es la inteligencia de amenazas en el contexto de los NGFW?

Es el uso de **información global y actualizada de ciberataques** para bloquear IPs, dominios y patrones de ataque en tiempo real.

#### 🔹 ¿Qué es el filtrado de paquetes y cómo lo utilizan los NGFW?

Es el mecanismo más básico de firewall: revisar IPs, puertos y protocolos. Los NGFW lo utilizan como **primer paso**, y luego aplican inspección avanzada (DPI, IPS, App Control).

✅ **Resumen final**

Un **NGFW es mucho más que un firewall clásico**: combina **filtrado, DPI, control de aplicaciones, IPS e inteligencia de amenazas** en una sola solución. Puede ser físico, virtual o en la nube. Es esencial en entornos modernos donde los ataques son cada vez más sofisticados.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 11**         | **Siguiente 13**      |
| ------------------ | -------------------- | --------------------- |
| [🏠](../README.md) | [⏪](./12_11_DLP.md) | [⏩](./12_13_HIPS.md) |
