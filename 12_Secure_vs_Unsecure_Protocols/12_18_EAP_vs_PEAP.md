| **Inicio**         | **atrás 17**                | **Siguiente 19**     |
| ------------------ | --------------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./12_17_Sandboxing.md) | [⏩](./12_19_WPS.md) |

---

## **Índice**

| Temario                                                                                                                                            |
| -------------------------------------------------------------------------------------------------------------------------------------------------- |
| [390. Protocolo de autenticación extensible (EAP) para el acceso a la red](#390-protocolo-de-autenticación-extensible-eap-para-el-acceso-a-la-red) |
| [391. ¿Qué es el Protocolo de autenticación extensible protegido (PEAP)?](#391-qué-es-el-protocolo-de-autenticación-extensible-protegido-peap)     |

# **Extensible Authentication Protocol (EAP) vs Protected Extensible Authentication Protocol (PEAP)**

## **390. Protocolo de autenticación extensible (EAP) para el acceso a la red**

![EAP vs PEAP](/img/12_Secure_vs_Unsecure_Protocols/EAP_vs_PEAP.webp "EAP vs PEAP")

### 🔹 Protocolo de Autenticación Extensible (EAP)

- Es un marco de autenticación que permite integrar diferentes métodos de validación de identidad en redes cableadas e inalámbricas.
- Se usa en **802.1X**, **Wi-Fi Enterprise (WPA2/WPA3-Enterprise)**, y **VPNs**.
- No define un método en sí, sino que proporciona un marco para varios métodos (ej. contraseñas, certificados, tokens, biometría).

### 🔹 Métodos de autenticación EAP más comunes

1. **EAP-TLS**

   - Basado en certificados digitales (cliente y servidor).
   - Muy seguro, pero requiere una infraestructura de clave pública (PKI).

2. **PEAP (Protected EAP)**

   - Usa un túnel TLS para proteger las credenciales (ej. usuario/contraseña).
   - Común con Active Directory + NPS (Network Policy Server).

3. **EAP-TTLS**

   - Similar a PEAP, pero más flexible: soporta varios protocolos dentro del túnel seguro.

4. **EAP-FAST (Flexible Authentication via Secure Tunneling)**

   - Creado por Cisco, evita necesidad de certificados usando un **PAC (Protected Access Credential)**.

### 🔹 Configuración de las propiedades de EAP

En Windows o equipos gestionados se configuran propiedades como:

- **Método de autenticación predeterminado** (ej. EAP-TLS o PEAP).
- **Uso de certificados** (cliente, CA raíz de confianza).
- **Políticas de validación del servidor** (ej. comprobar que el certificado sea de la CA esperada).

### 🔹 Perfiles XML para EAP

- En entornos empresariales, las configuraciones EAP se pueden exportar/importar como **archivos XML** (ej. con `netsh wlan export profile`).
- Esto facilita la distribución de configuraciones de red inalámbrica con EAP a múltiples dispositivos.
- Contienen información de SSID, método EAP, validación de certificados y autenticación.

### 🔹 Configuración de seguridad

Incluye definir:

- **Cifrado de datos** (ej. WPA2-Enterprise/AES, WPA3-Enterprise).
- **Servidor de autenticación** (RADIUS/NPS).
- **Método EAP** y credenciales aceptadas.

### 🔹 Configuración de seguridad avanzada > IEEE 802.1X

- **802.1X** es el estándar que usa EAP para control de acceso en red.
- Implica 3 componentes:

  - **Supplicant** (cliente, ej. laptop).
  - **Authenticator** (switch o AP).
  - **Servidor de autenticación** (ej. RADIUS).

### 🔹 Configuración de seguridad avanzada > Inicio de sesión único (SSO)

- Permite que las credenciales usadas en el inicio de sesión de Windows (ej. Active Directory) también se usen para la autenticación EAP.
- Evita que el usuario deba autenticarse dos veces.

### 🔹 Configuración del método de autenticación

- Selección del tipo de EAP (TLS, PEAP, TTLS, etc.).
- Configuración de certificados o credenciales necesarias.

### 🔹 Validación del certificado del servidor

- Protege contra ataques de **falsificación de servidor RADIUS**.
- El cliente comprueba:

  - Que el certificado está emitido por una CA de confianza.
  - Que el nombre del servidor coincide con el esperado.
  - Que no esté caducado o revocado.

### 🔹 Solicitud de validación del servidor para el usuario

- Es la opción que hace que Windows u otro cliente **pregunte al usuario** si confía en el certificado presentado por el servidor, en caso de que no se pueda validar automáticamente.
- En entornos corporativos normalmente **se desactiva**, porque la validación se configura para que sea automática (para no depender de la decisión del usuario).

---

[🔼](#índice)

---

## **391. ¿Qué es el Protocolo de autenticación extensible protegido (PEAP)?**

- **PEAP (Protected Extensible Authentication Protocol)** es un protocolo de autenticación que encapsula **EAP** dentro de un túnel seguro creado con **TLS (Transport Layer Security)**.
- Fue desarrollado por **Microsoft, Cisco y RSA** como alternativa a EAP-TLS, para evitar la necesidad de certificados en los clientes.
- Se usa mucho en redes **Wi-Fi empresariales (WPA2/WPA3-Enterprise)** y en **802.1X**.

👉 En resumen: **PEAP protege las credenciales del usuario (como usuario/contraseña) transmitiéndolas dentro de un canal cifrado**.

### 🔹 ¿Cómo funciona PEAP? (flujo simplificado)

El proceso ocurre en **dos fases principales**:

#### **Fase 1 – Establecimiento del túnel TLS**

1. El **cliente (supplicant)** se conecta al **punto de acceso/switch (authenticator)**, que redirige la autenticación al **servidor RADIUS/NPS**.
2. El servidor presenta su **certificado digital** (como en HTTPS).
3. El cliente valida el certificado:

   - Que la CA sea de confianza.
   - Que el nombre del servidor sea correcto.
   - Que el certificado no esté caducado/revocado.

4. Si la validación es correcta, se establece un **túnel TLS seguro** entre cliente y servidor.

#### **Fase 2 – Autenticación del usuario dentro del túnel seguro**

1. Ahora, dentro del túnel cifrado, el cliente envía sus **credenciales** (ejemplo: usuario/contraseña de Active Directory).
2. El servidor valida las credenciales con el directorio corporativo (ej. LDAP/AD).
3. Si las credenciales son correctas, el servidor envía un mensaje de **éxito EAP**.
4. El switch o AP abre el puerto y el cliente obtiene acceso a la red.

### 🔹 Métodos comunes dentro de PEAP

- **PEAP-MSCHAPv2** → El más usado. Utiliza usuario/contraseña contra Active Directory.
- **PEAP-GTC (Generic Token Card)** → Usa tokens o credenciales OTP (ej. RSA SecurID).

### 🔹 Ventajas de PEAP

✅ Solo requiere **certificado en el servidor**, no en cada cliente (más fácil de administrar).

✅ Protege las credenciales con un túnel cifrado (TLS).

✅ Integración nativa con Active Directory y RADIUS.

### 🔹 Desventajas de PEAP

❌ Menos seguro que **EAP-TLS** (que usa certificados cliente-servidor).

❌ Depende de la solidez de las contraseñas (vulnerable a ataques de fuerza bruta si se interceptan hashes MSCHAPv2).

❌ Requiere buena gestión de certificados en el servidor.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 17**                | **Siguiente 19**     |
| ------------------ | --------------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./12_17_Sandboxing.md) | [⏩](./12_19_WPS.md) |
