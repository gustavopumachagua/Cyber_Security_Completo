| **Inicio**         | **atrás 8**                  | **Siguiente 10**               |
| ------------------ | ---------------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./10_8_VLAN_Hopping.md) | [⏩](./10_10_Deauth_Attack.md) |

---

## **Índice**

| Temario                                                                                                                             |
| ----------------------------------------------------------------------------------------------------------------------------------- |
| [315. Que es DNS Cache Poisoning? DNS spoofing](#315-que-es-dns-cache-poisoning--dns-spoofing)                                      |
| [316. Que es DNS Poisoning?](#316-que-es-dns-poisoning)                                                                             |
| [317. DNS Poisoning (DNS Spoofing): Definition, Technique & Defense](#317-dns-poisoning-dns-spoofing-definition-technique--defense) |
|                                                                                                                                     |

# **DNS Poisoning DNS Spoofing DNS Cache Poisoning**

## **315. Que es DNS Cache Poisoning? DNS spoofing**

![DNS spoofing](/img/10_Common_Attacks/DNS_Spoofing.png "DNS spoofing")

### 🔹 ¿Qué es el envenenamiento de caché de DNS?

El **envenenamiento de caché de DNS** es un ataque en el que un atacante introduce **registros DNS falsos** en la caché de un **resolver DNS** (servidor o cliente).

El resultado es que, cuando un usuario intenta visitar un sitio legítimo, el resolver devuelve una **dirección IP falsa**, normalmente controlada por el atacante.

👉 Ejemplo sencillo:

- Tú escribes `www.banco.com` en tu navegador.
- En condiciones normales, el DNS resuelve el nombre y responde con la IP legítima del banco (por ejemplo, `200.45.23.10`).
- Si el resolver ha sido **envenenado**, devuelve una IP falsa (por ejemplo, `185.66.77.88`), que en realidad apunta a un **sitio malicioso que imita al banco**.
- Tú no notas la diferencia, porque la URL en el navegador sigue siendo `www.banco.com`, pero estás en un sitio falso → robo de credenciales.

### 🔹 ¿Cómo funciona el almacenamiento en caché de DNS?

Para entender el ataque, hay que ver cómo opera la caché:

1. **Resolución normal de DNS**

   - El cliente pregunta al **resolver** (ej.: DNS de tu ISP o de Google 8.8.8.8).
   - Si la respuesta no está en la caché, el resolver pregunta a otros servidores (root → TLD → autoritativo).
   - Una vez obtenida la IP legítima, la guarda en caché un tiempo (`TTL` – Time To Live).

2. **Caché DNS**

   - Es una memoria temporal en el resolver (y a veces en el sistema operativo o router local).
   - Sirve para responder más rápido y reducir tráfico.
   - Cualquier registro DNS malicioso que entre en esa caché afectará a **todos los usuarios que la usen**.

👉 Ejemplo:

- Resolver DNS del ISP guarda en caché que `www.ejemplo.com` → `123.45.67.89`.
- Cualquier usuario que pregunte por `www.ejemplo.com` recibirá esa IP **sin necesidad de consultar al servidor autoritativo** hasta que expire el TTL.

### 🔹 ¿Cómo pueden los atacantes envenenar un caché de DNS?

El ataque busca que el resolver guarde **información falsa**. Algunas técnicas históricas y modernas:

#### 1. **Respuesta falsa antes que la legítima**

- El atacante envía una **respuesta DNS falsificada** al resolver **antes** de que llegue la respuesta legítima del servidor autoritativo.
- Si coincide el **ID de transacción** de la consulta DNS, el resolver acepta la respuesta falsa y la guarda en caché.

👉 Ejemplo histórico:

- Resolver pregunta: “¿Cuál es la IP de `www.banco.com`?”
- Atacante inyecta respuesta falsa: "`www.banco.com` = 185.66.77.88".
- Resolver acepta y cachea → usuarios van al sitio falso.

_(Esto fue muy común antes de que los resolvers randomizaran el ID de consulta y el puerto de origen.)_

#### 2. **Compromiso de servidor o router local**

- Malware en el router o sistema operativo puede modificar directamente la caché DNS local.
- Algunos atacantes cambian la **configuración DNS** en el router para que use resolvers controlados por ellos.

#### 3. **Ataques a servidores vulnerables**

- Exploits contra software de DNS mal configurado o desactualizado.
- Ejemplo: vulnerabilidades como la descubierta por Dan Kaminsky en 2008, que mostró que muchos resolvers eran fáciles de engañar con ataques de predicción de ID.

### 🔹 ¿Qué impacto puede tener?

- **Suplantación de sitios web**: phishing perfecto (la URL es legítima, pero la IP es falsa).
- **Intercepción de tráfico**: el atacante puede redirigir a servidores intermediarios.
- **Distribución de malware**: usuarios descargan software desde un dominio legítimo pero redirigido.
- **Ataques masivos**: si se envenena la caché de un DNS público o ISP, miles de usuarios pueden ser afectados.

### 🔹 ¿Cómo ayuda DNSSEC a prevenir el envenenamiento de DNS?

**DNSSEC (Domain Name System Security Extensions)** agrega **firmas digitales** a las respuestas DNS.

Funcionamiento:

1. Cada zona DNS (ej. `.com`, `banco.com`) tiene un **par de claves criptográficas**.
2. Los registros DNS (A, AAAA, MX, etc.) se firman digitalmente con la clave privada.
3. El resolver que soporta DNSSEC valida la respuesta con la **clave pública** publicada en los servidores DNS autoritativos.
4. Si la respuesta no tiene firma válida → se descarta.

👉 Ejemplo:

- Usuario consulta `www.banco.com`.
- El servidor autoritativo devuelve IP **+ firma digital**.
- Resolver valida la firma.
- Si un atacante intenta inyectar una respuesta falsa, **fallará la validación** porque no puede falsificar la firma sin la clave privada.

✅ Así, DNSSEC **evita que respuestas falsificadas sean aceptadas y cacheadas**.

### 🔹 Resumen

- **Envenenamiento de caché de DNS:** ataque donde se introducen registros falsos en el cache DNS de un resolver.
- **Objetivo:** redirigir tráfico a servidores falsos (phishing, malware, espionaje).
- **Cómo ocurre:** suplantando respuestas DNS, comprometiendo routers/servidores o aprovechando vulnerabilidades en resolvers.
- **Defensa principal:** **DNSSEC**, junto con otras medidas como:

  - Randomización de IDs y puertos en consultas.
  - Uso de resolvers confiables (ej. Google, Cloudflare).
  - Monitoreo de anomalías DNS.
  - Configuración segura en routers locales.

---

[🔼](#índice)

---

## **316. Que es DNS Poisoning?**

### 🔹 ¿Qué es el envenenamiento de DNS? (Definición)

El **envenenamiento de DNS** (también llamado _DNS spoofing_ o _DNS cache poisoning_) es un ataque en el que un ciberdelincuente introduce **registros falsos de resolución de nombres** en un sistema DNS (en la caché del cliente, en un servidor DNS o en un router).

El objetivo es que cuando un usuario escriba un dominio legítimo, sea redirigido a un sitio malicioso sin darse cuenta.

👉 Ejemplo sencillo:

- El usuario escribe `www.banco.com`.
- En lugar de ir a la IP real del banco (`200.45.23.10`), el DNS envenenado responde con una IP falsa (`185.66.77.88`).
- El navegador muestra `www.banco.com` en la barra de direcciones, pero en realidad abre una **página fraudulenta** diseñada para robar contraseñas.

### 🔹 ¿Qué es un DNS y qué es un servidor DNS?

- **DNS (Domain Name System)**: el “listín telefónico” de Internet. Convierte nombres de dominio (`www.google.com`) en direcciones IP (`142.250.190.78`) para que las computadoras puedan conectarse.
- **Servidor DNS**: un equipo que recibe solicitudes de resolución de nombres y devuelve las IP correspondientes. Ejemplos:

  - DNS de tu proveedor de Internet (ISP).
  - DNS públicos (Google 8.8.8.8, Cloudflare 1.1.1.1).

### 🔹 ¿Cómo funciona el envenenamiento de DNS?

Los atacantes buscan que el sistema resuelva un nombre de dominio con una dirección falsa. Algunas formas:

#### 1. Ataques Man-in-the-Middle (MITM)

- El atacante se sitúa entre el usuario y el servidor DNS (ej. en una red Wi-Fi pública).
- Intercepta las peticiones DNS y responde antes que el servidor legítimo.
- Resultado: el usuario va a un sitio controlado por el atacante.

👉 Ejemplo:

En un café con Wi-Fi abierta, un hacker hace que todas las peticiones a `www.redsocial.com` resuelvan hacia su servidor falso.

#### 2. Secuestro del servidor DNS

- El atacante compromete directamente un **servidor DNS** (por malware, explotación de vulnerabilidades o mala configuración).
- Modifica los registros para que dominios legítimos apunten a IP falsas.
- Este ataque afecta a **todos los usuarios que usen ese servidor DNS**.

👉 Ejemplo:

Si un servidor DNS de un ISP es secuestrado, todos sus clientes pueden ser redirigidos a sitios de phishing.

#### 3. Envenenamiento de caché DNS por spam

- El atacante envía respuestas falsas al resolver antes de que llegue la legítima.
- Si el resolver acepta la respuesta, la guarda en su **caché DNS** y todos los usuarios posteriores reciben esa dirección falsa.

👉 Ejemplo clásico (2008, vulnerabilidad descubierta por Dan Kaminsky):
Un atacante logra adivinar el ID de transacción de la consulta DNS y manda una respuesta falsa con una IP maliciosa.

### 🔹 ¿Cuáles son los riesgos del envenenamiento de DNS?

1. **Phishing invisible**: el usuario cree que está en el sitio real, pero está en uno falso.
2. **Robo de credenciales y datos**: contraseñas, correos, datos bancarios.
3. **Distribución de malware**: descargas “legítimas” que en realidad son troyanos.
4. **Espionaje y vigilancia**: el atacante redirige tráfico a servidores controlados para espiar.
5. **Ataques masivos**: si el DNS envenenado es público o de un ISP, miles de usuarios pueden ser afectados.

### 🔹 Cómo prevenir el envenenamiento de DNS

#### ✅ Para usuarios finales

- Usar **DNS confiables**: Cloudflare (1.1.1.1), Google (8.8.8.8).
- Evitar redes Wi-Fi públicas sin VPN.
- Mantener actualizado el sistema operativo y el navegador.
- Usar una **VPN**: cifra las consultas DNS y dificulta la manipulación.
- Activar **DNS sobre HTTPS (DoH)** o **DNS sobre TLS (DoT)** en el navegador o sistema operativo.

#### ✅ Para propietarios de sitios web y proveedores de servicios DNS

- Implementar **DNSSEC (Domain Name System Security Extensions)**: añade firmas digitales a los registros DNS.
- Configurar correctamente TTLs y servidores autoritativos.
- Asegurar y monitorear los servidores DNS contra intrusiones.
- Usar redundancia (múltiples servidores DNS en diferentes ubicaciones).
- Implementar **monitoring de integridad de DNS**: comprobar periódicamente si el dominio resuelve a la IP correcta.

### 🔹 Preguntas frecuentes sobre el envenenamiento de DNS

#### ❓ ¿Qué es el envenenamiento de DNS?

Es una técnica en la que un atacante manipula el sistema DNS para redirigir a los usuarios a sitios falsos aunque escriban la dirección correcta.

#### ❓ ¿Qué es un DNS y un servidor DNS?

DNS es el sistema que traduce nombres de dominio en IPs. El servidor DNS es el que hace esta traducción (por ejemplo, los de Google, tu ISP o Cloudflare).

#### ❓ ¿Cuáles son los riesgos?

- Robo de credenciales.
- Infección por malware.
- Espionaje y manipulación de tráfico.
- Ataques a gran escala contra miles de usuarios.

#### ❓ ¿Cómo prevenir el envenenamiento de DNS?

- Usuarios: usar DNS confiables, VPN, DoH/DoT, sistemas actualizados.
- Propietarios de sitios: implementar DNSSEC, monitoreo, hardening de servidores DNS.

📌 **Resumen final**:

El **envenenamiento de DNS** es un ataque que manipula las respuestas DNS para redirigir tráfico a sitios falsos. El riesgo es alto porque el usuario no sospecha: la barra del navegador sigue mostrando la URL correcta. La defensa clave es **DNSSEC**, combinado con buenas prácticas de seguridad en servidores DNS y el uso de **DNS cifrado** y confiable por parte de los usuarios.

---

[🔼](#índice)

---

## **317. DNS Poisoning (DNS Spoofing): Definition, Technique & Defense**

### 🔹 ¿Qué es el envenenamiento de DNS?

El **envenenamiento de DNS** (también llamado _DNS spoofing_ o _DNS cache poisoning_) es una técnica en la que un atacante manipula el **sistema de resolución de nombres de dominio** para redirigir al usuario a un sitio malicioso aunque escriba la dirección correcta.

👉 Ejemplo:

Un usuario escribe `www.banco.com`.

- El DNS legítimo debería devolver la IP real: `200.45.23.10`.
- Pero el DNS envenenado devuelve una IP falsa: `185.77.66.99`.
- El navegador muestra `www.banco.com`, pero en realidad carga una **página falsa de phishing**.

### 🔹 ¿Cómo funciona un DNS?

El **Sistema de Nombres de Dominio (DNS)** funciona como la “guía telefónica de Internet”. Convierte un **nombre de dominio** en una **dirección IP** para que los dispositivos puedan conectarse entre sí.

📌 Proceso simplificado:

1. El usuario escribe `www.google.com`.
2. El navegador pregunta al **resolver DNS local** (el de tu router o ISP).
3. Si la respuesta no está en caché, el resolver consulta a los **servidores raíz**, luego a los **servidores TLD (.com, .org, etc.)** y finalmente al **servidor autoritativo** de Google.
4. El DNS devuelve la IP correcta (ejemplo: `142.250.190.78`).
5. El navegador conecta al servidor y muestra la página.

### 🔹 ¿Cómo se ve el envenenamiento de la caché DNS?

El **envenenamiento de caché DNS** ocurre cuando se almacenan **respuestas falsas** en la memoria caché del resolver DNS.

👉 Ejemplo visual:

- ✅ Caso normal:

  `www.tubanco.com` → **DNS legítimo** → `201.20.30.40` → Servidor del banco.

- ❌ Caso envenenado:

  `www.tubanco.com` → **DNS envenenado** → `185.66.77.88` → Servidor del atacante (página falsa idéntica a la del banco).

El usuario no nota la diferencia porque la **URL en el navegador no cambia**, solo la IP detrás de ella.

### 🔹 Su plan de suplantación de DNS (cómo actúa un atacante)

Un atacante que quiere envenenar DNS puede seguir este flujo:

1. **Preparación del sitio malicioso**

   - Crea una copia idéntica del sitio objetivo (ejemplo: `www.banco.com`).
   - Lo aloja en un servidor bajo su control.

2. **Ataque a la caché DNS**

   - Inyecta entradas falsas en el **resolver DNS** (por spam de respuestas, MITM o explotación de vulnerabilidades).
   - Engaña al DNS para que guarde la dirección IP falsa.

3. **Redirección de usuarios**

   - Cuando las víctimas escriben el dominio legítimo, son enviadas al sitio fraudulento.

4. **Explotación**

   - El atacante roba credenciales (usuario, contraseña, tokens de Okta o MFA).
   - También puede instalar malware o redirigir tráfico para espionaje.

👉 En el contexto de **Okta** (gestión de identidades), un atacante podría:

- Crear un sitio falso de inicio de sesión (`login.okta.com`).
- Usar un DNS envenenado para redirigir a empleados.
- Capturar sus credenciales de acceso corporativo y comprometer la seguridad de toda la empresa.

### 🔹 Defensa contra el envenenamiento de DNS

#### ✅ Para usuarios

- Usar **DNS confiables y cifrados** (Google 8.8.8.8, Cloudflare 1.1.1.1).
- Activar **DNS sobre HTTPS (DoH)** o **DNS sobre TLS (DoT)**.
- Navegar con **VPN** en redes públicas.
- Mantener el navegador y SO actualizados.
- Revisar siempre el **certificado SSL/TLS (candado)** en sitios sensibles como bancos o login corporativos.

#### ✅ Para administradores y empresas

- Implementar **DNSSEC**: añade firmas digitales a los registros DNS para verificar su autenticidad.
- Configurar correctamente los resolvers DNS y minimizar el tiempo de vida (TTL) de la caché.
- Monitorear tráfico DNS para detectar anomalías.
- Asegurar servidores DNS contra ataques y mantenerlos actualizados.
- Usar autenticación fuerte en aplicaciones críticas como Okta (MFA, claves FIDO2).

📌 **Resumen final**

El **envenenamiento de DNS** es un ataque en el que un hacker manipula el sistema DNS para redirigir tráfico hacia sitios falsos. Aprovecha la confianza que los usuarios tienen en los nombres de dominio. La mejor defensa es implementar **DNSSEC** a nivel de servidores y usar **DoH/DoT, VPN y DNS confiables** a nivel de usuarios.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 8**                  | **Siguiente 10**               |
| ------------------ | ---------------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./10_8_VLAN_Hopping.md) | [⏩](./10_10_Deauth_Attack.md) |
