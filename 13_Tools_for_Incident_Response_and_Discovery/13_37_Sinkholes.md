| **Inicio**         | **atrás 36**                  | **Siguiente 38**      |
| ------------------ | ----------------------------- | --------------------- |
| [🏠](../README.md) | [⏪](./13_36_Group_Policy.md) | [⏩](./13_38_ACLs.md) |

---

## **Índice**

| Temario                                                                                                             |
| ------------------------------------------------------------------------------------------------------------------- |
| [483. Sumideros de DNS: ¿Qué son y cómo empezar a usarlos?](#483-sumideros-de-dns-qué-son-y-cómo-empezar-a-usarlos) |

# **Sinkholes**

## **483. Sumideros de DNS: ¿Qué son y cómo empezar a usarlos?**

![Sinkholes](/img/13_Tools_for_Incident_Response_and_Discovery/Sinkholes.png "Sinkholes")

### 1. ¿Qué es DNS?

El **DNS (Domain Name System)** es como la **agenda telefónica de Internet**.

Convierte los nombres de dominio (ej. `www.google.com`) en direcciones IP (ej. `142.250.72.14`) que los ordenadores entienden.

👉 Ejemplo práctico:

Cuando escribes `facebook.com` en tu navegador:

1. Tu equipo pregunta al servidor DNS: _“¿Cuál es la IP de facebook.com?”_
2. El servidor DNS responde con la IP, y tu navegador se conecta a esa dirección.

### 2. Arquitectura DNS (simplificada)

DNS es **jerárquico** y funciona en niveles:

- **Nivel raíz (Root Servers)**

  Son los “primeros puntos de contacto” del DNS. No saben todas las IP, pero saben quién sabe.

  Ejemplo: el root server te dice a qué TLD (`.com`, `.org`, `.net`, etc.) preguntar.

- **Dominios de nivel superior (TLD)**

  Como `.com`, `.org`, `.pe`. Cada uno tiene sus propios servidores.

  Ejemplo: preguntas por `facebook.com`, el TLD `.com` te responde: _“Pregunta a los servidores de facebook”_.

- **Dominios de segundo nivel**

  Son los nombres principales registrados bajo un TLD.

  Ejemplo: `facebook` en `facebook.com`.

- **Subdominio**

  Son divisiones dentro del dominio.

  Ejemplo: `mail.google.com` → `mail` es un subdominio.

- **Anfitrión (host)**

  El nombre final que apunta a una IP.

  Ejemplo: `www.ejemplo.com` apunta a `203.0.113.10`.

### 3. ¿Qué es un sumidero de DNS?

Un **DNS Sinkhole** (sumidero) es un servidor DNS configurado para **responder con una IP falsa o controlada** cuando un dispositivo intenta resolver un dominio **malicioso** o **no autorizado**.

👉 Ejemplo:

Un malware intenta conectarse a `bad-malware.com`.

- El DNS normal devolvería la IP del servidor del atacante.
- El **DNS Sinkhole** devuelve otra IP, por ejemplo `127.0.0.1` (localhost) o la IP de un servidor de seguridad interno.
  Resultado: la conexión maliciosa **se corta o se redirige** a un lugar seguro.

### 4. ¿Por qué utilizar un sumidero de DNS?

Los sumideros son muy útiles en **seguridad empresarial**. Sus beneficios:

- 🚫 **Bloqueo de malware**: evita que los equipos se comuniquen con servidores de comando y control (C2).
- 🔍 **Detección temprana**: si un equipo consulta un dominio malicioso, probablemente está infectado.
- 🎯 **Contención de amenazas**: limita el alcance de ataques como ransomware, troyanos o botnets.
- 🛡️ **Protección proactiva**: ayuda a bloquear phishing y spyware.

👉 Ejemplo real:

Un ransomware intenta descargar su clave de cifrado desde `evil-hacker.net`.
El DNS Sinkhole redirige esa petición a un servidor interno → el ransomware nunca recibe la clave → la infección no progresa.

### 5. Cómo empezar a utilizar los sumideros DNS

Existen varias formas de implementar un **DNS Sinkhole**:

1. **A nivel de servidor DNS corporativo**

   - Configurar tu propio **BIND, Unbound o Windows DNS Server** con listas negras de dominios maliciosos.
   - Cuando se intente resolver un dominio de esa lista, el DNS responde con una IP controlada.

2. **Con appliances o firewalls de red**

   - Algunos firewalls (Fortinet, Palo Alto, Cisco Umbrella) ya traen DNS Sinkhole integrado.
   - Se actualizan automáticamente con listas de amenazas (threat intelligence).

3. **Herramientas gratuitas/open-source**

   - **Pi-hole**: bloquea anuncios y malware en redes domésticas/pequeñas empresas.
   - **OpenDNS** (Cisco): DNS filtrado en la nube.
   - **Fakenet-NG**: usado en análisis de malware.

👉 Mini ejemplo de configuración en BIND:

```conf
zone "bad-malware.com" {
    type master;
    file "/etc/bind/db.null";
};

# db.null file content
@   IN  SOA localhost. root.localhost. (
            1 ; Serial
            604800 ; Refresh
            86400 ; Retry
            2419200 ; Expire
            604800 ) ; Negative Cache TTL
    IN  NS  localhost.
    IN  A   127.0.0.1
```

Esto hace que cualquier petición a `bad-malware.com` se resuelva como `127.0.0.1`.

### 6. Limitaciones de los sumideros de DNS

Aunque útiles, no son mágicos:

- ❌ No bloquean tráfico si el malware usa **IPs directas** (sin DNS).
- ❌ No evitan infecciones iniciales (solo cortan comunicaciones posteriores).
- ❌ Pueden generar **falsos positivos** si un dominio legítimo entra en una blacklist.
- ❌ Requieren **mantenimiento constante** (listas de dominios maliciosos actualizadas).

👉 Ejemplo:

Un atacante usa la IP `203.0.113.50` directamente en lugar de `malware.com`.
El DNS Sinkhole no interviene, porque no hubo resolución de nombre.

### 7. Detección automatizada de infracciones de DNS con IA (ejemplo: Evolve)

Hoy en día, hay plataformas de **SIEM con IA** (ejemplo: _Evolve_ o _Splunk con Machine Learning_) que:

- Detectan patrones de consultas de DNS sospechosos.
- Identifican comportamientos anómalos (ej. cientos de peticiones a dominios generados aleatoriamente — típico de DGA malware).
- Integran el Sinkhole con respuesta automática.

👉 Ejemplo:

Un endpoint infectado hace peticiones a `asd1234.biz`, `xkqwe9.com`, `mzll99.info` →
la IA detecta patrón **DGA (Domain Generation Algorithm)** → bloquea automáticamente → alerta al SOC.

### 8. Conclusión

- El **DNS Sinkhole** es una técnica clave en **defensa en profundidad**.
- Bloquea, detecta y redirige tráfico malicioso basado en DNS.
- Fácil de implementar en firewalls, servidores DNS o con herramientas open-source como Pi-hole.
- No es infalible → debe usarse junto con IDS/IPS, antivirus y monitoreo de red.

✅ Resumen con analogía:

Imagina que DNS es como un **GPS** que te guía hacia direcciones.
Un **DNS Sinkhole** es como una **desviación controlada** que evita que tu coche entre en un callejón peligroso y en su lugar te manda a un estacionamiento seguro. 🚦

---

[🔼](#índice)

---

| **Inicio**         | **atrás 36**                  | **Siguiente 38**      |
| ------------------ | ----------------------------- | --------------------- |
| [🏠](../README.md) | [⏪](./13_36_Group_Policy.md) | [⏩](./13_38_ACLs.md) |
