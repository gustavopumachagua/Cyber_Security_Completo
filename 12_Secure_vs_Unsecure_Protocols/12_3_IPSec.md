| **Inicio**         | **atrás 2**                | **Siguiente 4**        |
| ------------------ | -------------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./12_2_SSL_vs_TLS.md) | [⏩](./12_4_DNSSEC.md) |

---

## **Índice**

| Temario                                                          |
| ---------------------------------------------------------------- |
| [362. ¿Qué es IPSec?](#362-qué-es-ipsec)                         |
| [363. Fundamentos de IP Sec VPN](#363-fundamentos-de-ip-sec-vpn) |

# **IPSec**

## **362. ¿Qué es IPSec?**

![IPSec](/img/12_Secure_vs_Unsecure_Protocols/IPSec.png "IPSec")

### 📌 1. ¿Qué es IPsec?

**IPsec (Internet Protocol Security)** es un conjunto de protocolos que asegura las comunicaciones IP mediante:

- **Autenticación** 🔑 → verifica identidad de los extremos.
- **Confidencialidad** 🔒 → cifra los datos transmitidos.
- **Integridad** ✅ → evita modificaciones de datos en tránsito.
- **Antirreplay** ⏱️ → evita que un atacante reutilice paquetes interceptados.

👉 IPsec trabaja en **capa 3 (red)** del modelo OSI, lo que significa que puede proteger cualquier tráfico IP (HTTP, DNS, VoIP, etc.), no solo aplicaciones específicas.

### 📌 2. ¿Por qué es importante IPsec?

- Es el **estándar más usado para VPN corporativas**.
- Protege datos sensibles en redes públicas (ej. Internet).
- Permite **interconectar sucursales, oficinas, servidores y usuarios remotos** de manera segura.
- Es requerido en sectores como banca, gobierno y salud por temas regulatorios.

### 📌 3. ¿Qué es una VPN? ¿Qué es una VPN IPsec?

- **VPN (Virtual Private Network)**: tecnología que permite crear un “túnel seguro” entre dos puntos a través de Internet u otra red insegura.
- **VPN IPsec**: es una VPN que usa **IPsec como protocolo de seguridad** para cifrar y autenticar el tráfico.

👉 Ejemplo: un empleado desde su casa abre una **VPN IPsec** para acceder a los servidores internos de la empresa como si estuviera en la oficina.

### 📌 4. ¿Cómo se conectan los usuarios a una VPN IPsec?

Existen dos escenarios principales:

1. **Site-to-Site (puente entre redes):**

   - Se conectan **dos redes completas** (ej. sucursal ↔ sede central).
   - Routers/firewalls negocian el túnel automáticamente.

2. **Client-to-Site (acceso remoto):**

   - Cada usuario usa un **cliente de VPN IPsec** en su laptop/móvil.
   - El cliente se conecta al **gateway VPN** de la empresa con credenciales + claves compartidas o certificados.

Ejemplo de cliente: Cisco AnyConnect, StrongSwan (Linux), Windows nativo, macOS nativo.

### 📌 5. ¿Cómo funciona IPsec?

IPsec establece un **túnel seguro** mediante dos fases:

1. **Fase I (IKE SA - Internet Key Exchange):**

   - Negocia algoritmos de cifrado, autenticación y establece un canal seguro.
   - Usa puertos **UDP 500 (IKEv1/IKEv2)**.

2. **Fase II (IPsec SA):**

   - Crea el canal de datos real (ESP o AH).
   - Protege el tráfico IP definido en las políticas.

### 📌 6. ¿Qué protocolos se utilizan en IPsec?

IPsec utiliza varios protocolos según lo que quieras asegurar:

- **IKE (Internet Key Exchange):** gestiona las claves y la negociación.
- **AH (Authentication Header):** garantiza integridad y autenticidad (pero no cifra).
- **ESP (Encapsulating Security Payload):** cifra los datos + provee integridad y autenticación.

  👉 En la práctica, **ESP** es el más usado.

### 📌 7. Diferencia: modo túnel vs modo transporte

- **Modo Transporte:**

  - Solo cifra el **payload (datos)** del paquete IP.
  - Cabecera original IP se mantiene.
  - Usado entre hosts individuales.

- **Modo Túnel:**

  - Cifra **todo el paquete IP original** y lo encapsula en un nuevo paquete IP.
  - Usado en VPNs (site-to-site o client-to-site).

👉 **Ejemplo:**

- Transporte: PC ↔ Servidor cifran tráfico directo.
- Túnel: Router A ↔ Router B crean un túnel que encapsula todo el tráfico de sus redes.

### 📌 8. ¿Qué puerto utiliza IPsec?

Depende de la fase y protocolo:

- **UDP 500** → IKE (fase inicial de negociación).
- **UDP 4500** → NAT-T (cuando hay NAT entre cliente y servidor).
- **Protocolo 50 (ESP)** → encapsulación de datos (no es puerto, es protocolo IP).
- **Protocolo 51 (AH)** → autenticación de cabeceras.

👉 En firewalls, hay que permitir:

- UDP 500
- UDP 4500
- IP protocolo 50 (ESP)
- IP protocolo 51 (AH, opcional).

### 📌 9. ¿Es Cloudflare compatible con IPsec?

👉 **No directamente.**

Cloudflare no ofrece **VPN IPsec** como servicio (su foco es seguridad de aplicaciones web, DNS y Zero Trust).

En su lugar:

- Cloudflare ofrece **Cloudflare Tunnel (Argo Tunnel)** y **Cloudflare Zero Trust Access**, que funcionan en capa de aplicación (similar a un VPN pero para apps específicas).
- Para VPN corporativas tradicionales con IPsec, se usan servicios de nube como **AWS VPN, Azure VPN Gateway o GCP Cloud VPN**.

### 📌 10. Resumen rápido

| Aspecto     | IPsec                                                  |
| ----------- | ------------------------------------------------------ |
| ¿Qué es?    | Conjunto de protocolos para asegurar IP                |
| Funciona en | Capa 3 (red)                                           |
| Usos        | VPNs site-to-site, acceso remoto seguro                |
| Protocolos  | IKE, AH, ESP                                           |
| Modos       | Transporte (solo datos) / Túnel (paquete completo)     |
| Puertos     | UDP 500, UDP 4500, IP proto 50 (ESP), IP proto 51 (AH) |
| Importancia | Cifrado, autenticación, integridad en redes públicas   |
| Cloudflare  | No soporta IPsec, ofrece alternativas Zero Trust       |

---

[🔼](#índice)

---

## **363. Fundamentos de IP Sec VPN**

### 1 — ¿Qué es una VPN IPsec (resumen rápido)?

Una **VPN IPsec** es una VPN que utiliza el conjunto de protocolos **IPsec** para **autenticación, confidencialidad e integridad** del tráfico IP. IPsec opera en **capa 3 (IP)**, por lo que protege cualquier aplicación que use IP (HTTP, RDP, DNS, etc.). Se usa tanto para **site-to-site** (red↔red) como para **client-to-site** (usuario remoto ↔ red corporativa).

### 2 — Componentes y conceptos clave

- **SA (Security Association)**: contrato unidireccional que define algoritmos, claves y parámetros para proteger tráfico. Una VPN completa usa pares de SAs (una por cada dirección).
- **IKE (Internet Key Exchange)**: protocolo de control para negociar SAs y claves. Versiones: **IKEv1** (antiguo) e **IKEv2** (recomendado).
- **ESP (Encapsulating Security Payload)**: protocolo que cifra y autentica los datos (el más usado).
- **AH (Authentication Header)**: solo proporciona autenticación/integridad (sin cifrado). Casi en desuso para VPN de datos.
- **Modos**:

  - **Tunnel mode**: encapsula todo el paquete IP original dentro de uno nuevo (útil para site-to-site).
  - **Transport mode**: protege solo el payload del paquete IP (útil entre hosts).

- **Autenticación**: puede ser **PSK (pre-shared key)**, **certificados X.509** (recomendado para escala/seguridad) o **EAP** (para usuarios).
- **NAT-T (NAT Traversal)**: cuando hay NAT entre extremos, IKE usa UDP 4500 y encapsula ESP en UDP para atravesar NATs.
- **Traffic selectors / políticas**: determinan qué subredes o IPs deben cifrarse (p. ej. `10.1.0.0/24 ↔ 10.2.0.0/24`).

### 3 — Flujo de establecimiento (alto nivel)

**Fase 1 (IKE SA)** — autenticación y establecimiento de canal seguro:

1. Cliente/peer envía `ClientHello` (algoritmos propuestos).
2. Servidor responde `ServerHello` (elige parámetros).
3. Se autentican (PSK o certificados) y se derivan claves maestras.

**Fase 2 (IPsec / Child SA)** — se crean las SAs que protegerán el tráfico real:

4\. Negociación de transform (ESP/AUTH, cifrado, DH).
5\. Se instalan SAs y empiezan a enviarse paquetes cifrados (ESP, proto 50 o encapsulado en UDP si NAT-T).

Puertos/protocolos a tener en cuenta:

- **UDP 500** → IKE initial.
- **UDP 4500** → IKE/NAT-T (cuando hay NAT).
- **IP proto 50 (ESP)** → datos cifrados.
- **IP proto 51 (AH)** → autenticación cabecera (poco usado).

### 4 — Ejemplo conceptual: site-to-site simple

**Topología**

- Sitio A: IP pública `203.0.113.1`, LAN `10.0.1.0/24`
- Sitio B: IP pública `198.51.100.2`, LAN `10.0.2.0/24`

**Idea:** todo tráfico entre `10.0.1.0/24` y `10.0.2.0/24` se encapsula y cifra.

**Selector / política:** `src=10.0.1.0/24 dst=10.0.2.0/24` (y la inversa).

### 5 — Ejemplo práctico (strongSwan / Linux) — site-to-site con PSK

`/etc/ipsec.conf` (fragmento)

```text
conn site-to-site
    keyexchange=ikev2
    left=203.0.113.1
    leftid=@siteA.example.com
    leftsubnet=10.0.1.0/24
    right=198.51.100.2
    rightid=@siteB.example.com
    rightsubnet=10.0.2.0/24
    authby=psk
    ike=aes256-sha256-modp2048
    esp=aes256-sha256
    rekey=yes
    auto=start
```

`/etc/ipsec.secrets`

```text
@siteA.example.com @siteB.example.com : PSK "MiSecretaMuyFuerte_y_Larga!"
```

Iniciar / chequear:

```bash
sudo ipsec restart
sudo ipsec statusall        # ver estado de SAs y conexiones
sudo ipsec up site-to-site  # forzar levantado manual
```

**Qué esperar:** después de `ipsec up`, verás SAs instaladas y podrás hacer `ping` entre hosts `10.0.1.x` ↔ `10.0.2.x`.

### 6 — Ejemplo práctico: roadwarrior (cliente móvil) con certificados — pasos esenciales

1. Crear CA y firmar certificados (ejemplo OpenSSL):

```bash
# Crear CA
openssl genpkey -algorithm RSA -out ca.key -pkeyopt rsa_keygen_bits:4096
openssl req -x509 -new -nodes -key ca.key -sha256 -days 3650 -out ca.crt -subj "/CN=MiCA"

# Servidor VPN
openssl genpkey -algorithm RSA -out server.key -pkeyopt rsa_keygen_bits:2048
openssl req -new -key server.key -out server.csr -subj "/CN=vpn.ejemplo.com"
openssl x509 -req -in server.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out server.crt -days 365 -sha256

# Cliente
openssl genpkey -algorithm RSA -out client.key -pkeyopt rsa_keygen_bits:2048
openssl req -new -key client.key -out client.csr -subj "/CN=cliente1"
openssl x509 -req -in client.csr -CA ca.crt -CAkey ca.key -CAcreateserial -out client.crt -days 365 -sha256
```

2. Configuras el servidor para exigir certificados firmados por esa CA (más seguro que PSK para usuarios).
3. El cliente importa `client.crt` + `client.key` + `ca.crt` y se conecta con IKEv2 (por ejemplo, clientes nativos de Windows/macOS o strongSwan en Android).

### 7 — Comandos útiles para diagnóstico

- `sudo ipsec statusall` — estado general (strongSwan).
- `sudo swanctl --list-sas` — ver SAs (swanctl-based).
- `sudo journalctl -u strongswan -f` — logs en vivo.
- `sudo tcpdump -n -i eth0 'udp port 500 or udp port 4500 or proto 50'` — ver paquetes IKE / ESP / NAT-T.
- `ip xfrm` (Linux):

  - `ip xfrm state` — ver SAs instaladas.
  - `ip xfrm policy` — ver políticas/selector.

Errores comunes que verás:

- `NO_PROPOSAL_CHOSEN` → parámetros de cifrado (ciphers/DH/lifetimes) no coinciden.
- `AUTH_FAILED` → PSK o certificado incorrecto.
- `HOST_UNREACHABLE` / `CONNREFUSED` → puerto bloqueado por firewall (UDP 500/4500) o IKE no escuchando.

### 8 — Problemas prácticos y soluciones

- **NAT entre peers**: activa NAT-T (UDP 4500). Muchos stacks lo hacen automáticamente.
- **MTU/MSS y fragmentación**: encapsulación agrega overhead → MTU menor. Si tienes problemas de HTTP lento o conexiones que no se establecen, reduce MTU en la interfaz (p. ej. `ip link set dev tun0 mtu 1400`) o usa el ajuste MSS en el firewall:

```bash
iptables -t mangle -A FORWARD -p tcp --tcp-flags SYN,RST SYN -j TCPMSS --clamp-mss-to-pmtu
```

- **Rendimiento CPU**: cifrado intensivo → usa AES-NI en CPUs modernas, o ChaCha20 para dispositivos sin AES hardware.
- **Políticas malformadas**: mismatch en `leftsubnet/rightsubnet` produce que no se encripte tráfico esperado.

### 9 — Buenas prácticas de seguridad (resumidas)

- **Usar IKEv2** (mejor, más robusto y sencillo que IKEv1).
- **Autenticación por certificados** en vez de PSK para entornos productivos.
- **Cifras modernas**: AEAD (AES-GCM o ChaCha20-Poly1305), ECDHE para PFS (p. ej. X25519, P-256).
- **Limitar lifetimes razonables** y forzar rekeying periódico.
- **Registrar y auditar** todo (logs de IKE, IPsec, conexión de usuarios).
- **Restringir fuentes** en firewalls (permitir solo IPs publicadas si es posible).
- **Monitoreo y pruebas** con `tcpdump`, `ip xfrm`, y controles regulares (test de caída/reconexión, pruebas de throughput).

### 10 — ¿IPsec vs alternativas?

- **OpenVPN** (TLS-based) → flexible, atraviesa NAT fácilmente, pero mayor overhead en handshake.
- **WireGuard** → muy simple, rápido y moderno; usa criptografía moderna y es más fácil de auditar.
- **IPsec** → estándar de la industria para interconexión de redes, soporte nativo en routers/firewalls empresariales y sistemas operativos.

### 11 — Checklist rápido para desplegar una IPsec VPN

1. Decidir **tipo**: site-to-site o client-to-site.
2. Elegir **IKEv2** + **certificados** (recomendado).
3. Definir **selectors** (qué redes se cifran).
4. Configurar **firewalls** (UDP 500, UDP 4500, ESP).
5. Seleccionar **cifras** modernas (AEAD, ECDHE).
6. Probar con `ipsec up`, `ipsec statusall`, `tcpdump`.
7. Ajustar MTU/MSS si hay problemas de fragmentación.
8. Habilitar logs y monitoreo.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**                | **Siguiente 4**        |
| ------------------ | -------------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./12_2_SSL_vs_TLS.md) | [⏩](./12_4_DNSSEC.md) |
