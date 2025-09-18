| **Inicio**         | **atrás 17**        | **Siguiente 19**           |
| ------------------ | ------------------- | -------------------------- |
| [🏠](../README.md) | [⏪](./7_17_SSO.md) | [⏩](./7_19_Local_Auth.md) |

---

## **Índice**

| Temario                                                                                    |
| ------------------------------------------------------------------------------------------ |
| [194. ¿Qué es un certificado SSL?](#194-qué-es-un-certificado-ssl)                         |
| [195. ¿Qué es una autoridad de certificación?](#195-qué-es-una-autoridad-de-certificación) |

# **Certificates**

## **194. ¿Qué es un certificado SSL?**

![Certificates](/img/7_Troubleshooting_Tools/Certificates.jpg "Certificates")

### 🔹 1. ¿Qué es un certificado SSL?

Un **certificado SSL (Secure Sockets Layer)** es un archivo digital emitido por una autoridad certificadora (CA, _Certificate Authority_) que se instala en un servidor web y sirve para:

- **Cifrar** la comunicación entre el navegador y el servidor (usando HTTPS en lugar de HTTP).
- **Autenticar** que el sitio web realmente pertenece a la organización que dice ser.
- **Generar confianza** en los usuarios (candado 🔒 en la barra del navegador).

👉 Ejemplo:

Cuando entras a `https://www.banco.com`, el certificado SSL garantiza que:

1. La conexión está cifrada → nadie puede espiar tu tráfico.
2. El dominio pertenece realmente al banco (verificado por la CA).

### 🔹 2. ¿Qué es SSL?

**SSL (Secure Sockets Layer)** fue el primer protocolo que permitió cifrar conexiones web.

Hoy en día está obsoleto y fue reemplazado por **TLS (Transport Layer Security)**, aunque todavía se habla de **SSL/TLS** por costumbre.

👉 Diferencia:

- SSL = versión antigua (insegura).
- TLS = versión actual (segura).

Cuando hablamos de _certificado SSL_, en realidad nos referimos a **certificado TLS**.

### 🔹 3. ¿Cómo funcionan los certificados de SSL?

El proceso se llama **handshake TLS** (apretón de manos). Simplificado:

1. El navegador (cliente) pide conexión segura al servidor (`https://`).
2. El servidor envía su **certificado SSL**, que incluye:

   - El nombre de dominio.
   - Datos de la organización.
   - La clave pública del servidor.
   - La firma digital de la autoridad certificadora.

3. El navegador verifica que el certificado sea válido y confiable.
4. Si es válido, el navegador genera una **clave de sesión** y la cifra con la clave pública del servidor.
5. El servidor descifra con su **clave privada** → ahora ambos tienen una clave compartida.
6. A partir de aquí, toda la comunicación está cifrada.

👉 Ejemplo:

Cuando compras en una tienda online, tu número de tarjeta viaja cifrado. Un atacante que intercepte el tráfico solo verá datos ilegibles.

### 🔹 4. ¿Por qué los sitios web necesitan un certificado SSL?

- 🔐 **Seguridad:** evita que atacantes roben datos (contraseñas, tarjetas, cookies).
- ✅ **Confianza:** los navegadores muestran el candado si el sitio es seguro.
- 📈 **SEO:** Google penaliza sitios sin HTTPS.
- 🚫 **Navegadores bloquean:** muchos navegadores modernos muestran advertencias si entras a un sitio sin certificado SSL.

👉 Ejemplo:

Si tu web es `http://midominio.com`, Chrome mostrará:

⚠️ _"Tu conexión no es privada"_.
Con SSL → `https://midominio.com` aparece el candado 🔒.

### 🔹 5. ¿Cómo obtiene un sitio web un certificado SSL?

1. **Generar una CSR (Certificate Signing Request)** en el servidor.
   Contiene la clave pública y datos del dominio.
2. **Enviar la CSR a una Autoridad Certificadora (CA).**
   Ejemplos: DigiCert, Sectigo, GlobalSign, Let’s Encrypt.
3. La CA verifica que eres dueño del dominio (y en certificados avanzados, la identidad de la empresa).
4. La CA emite el certificado SSL.
5. Se instala en el servidor web y se configura HTTPS (puerto 443).

### 🔹 6. ¿Qué es un certificado SSL autofirmado?

Un certificado que firma **el mismo servidor**, no una CA confiable.

👉 Características:

- Sirve para **pruebas internas** o entornos de desarrollo.
- Los navegadores lo marcarán como **no seguro** porque no está validado por una CA reconocida.
- Ejemplo: `https://192.168.1.100` en una intranet puede usar un SSL autofirmado para cifrar, pero dará advertencia en Chrome.

### 🔹 7. ¿Es posible conseguir un certificado SSL gratuito?

Sí ✅.

- La opción más usada es **Let’s Encrypt**, una CA gratuita y automática.
- Certificados válidos por **90 días**, renovables automáticamente con herramientas como **Certbot**.
- Reconocidos por todos los navegadores modernos.

👉 Ejemplo:

Si tienes un sitio personal en `https://miblog.com`, puedes usar Let’s Encrypt y tener HTTPS gratis en lugar de pagar a una CA.

### 🔹 8. Resumen rápido

- **SSL = protocolo antiguo, TLS = moderno**, pero se sigue llamando "certificado SSL".
- Un **certificado SSL/TLS** cifra el tráfico y valida la identidad de un sitio.
- Son esenciales para proteger datos, generar confianza y evitar advertencias en navegadores.
- Pueden ser:

  - **De pago** (más garantías, validación extendida, seguros).
  - **Gratuitos** (Let’s Encrypt, Cloudflare SSL).

- Un **autofirmado** funciona, pero solo para pruebas.

📌 Ejemplo real:

Cuando entras a `https://facebook.com`, tu navegador descarga el certificado emitido por "DigiCert". Puedes verlo haciendo clic en el candado → _"Certificado válido"_. Allí aparecen el dominio, la CA y la fecha de validez.

---

[🔼](#índice)

---

## **195. ¿Qué es una autoridad de certificación?**

### 🔹 1. ¿Cuál es el papel de una autoridad de certificación (CA)?

Una **CA (Certificate Authority)** es una entidad de confianza que emite certificados digitales para confirmar que una clave pública realmente pertenece a la persona, empresa o sitio web que dice ser el dueño.

👉 **Ejemplo:**

Cuando entras a `https://www.banco.com`, tu navegador confía en el candado 🔒 porque el certificado fue emitido por una CA reconocida (ej. DigiCert). Esa CA garantiza que el sitio realmente pertenece al banco y no a un impostor.

### 🔹 2. ¿Cómo una CA valida y emite certificados?

El proceso varía según el **tipo de validación**:

- **DV (Domain Validation):**

  La CA verifica que el solicitante controla el dominio (ejemplo: responde a un correo [admin@dominio.com](mailto:admin@dominio.com) o coloca un archivo especial en el servidor).

- **OV (Organization Validation):**

  Además de validar el dominio, la CA revisa la existencia legal de la empresa (registros mercantiles, teléfono corporativo).

- **EV (Extended Validation):**

  El nivel más alto → revisa a fondo la existencia, identidad legal y ubicación física de la organización. Antes aparecía la barra verde en los navegadores.

👉 **Ejemplo práctico:**

1. El dueño de `tiendaonline.com` solicita un certificado.
2. Genera una CSR (Certificate Signing Request).
3. Envía la CSR a la CA (ej. SSL.com, GlobalSign).
4. La CA valida que el dueño controla el dominio y que la empresa existe (si OV o EV).
5. La CA firma el certificado digital con su clave privada y lo devuelve al solicitante.
6. El sitio lo instala y habilita **HTTPS**.

### 🔹 3. ¿Para qué se utilizan los certificados que emiten las CA?

Los certificados emitidos por CA sirven para:

- 🔐 **Cifrar comunicaciones** (HTTPS, correo seguro con S/MIME, VPNs, etc.).
- ✅ **Autenticar identidades digitales** (saber con quién hablas en la red).
- 📜 **Firmas digitales** (documentos PDF, código software).
- 🔑 **Acceso seguro** a redes y aplicaciones.

👉 Ejemplo:

- Un certificado SSL en una web → asegura el tráfico del cliente al servidor.
- Un certificado de firma de código → garantiza que un software viene del autor legítimo y no fue alterado.

### 🔹 4. ¿Qué contiene un certificado digital?

Un certificado digital (ejemplo: un archivo `.crt` o `.pem`) incluye:

- **Nombre común (CN)** → el dominio (`www.ejemplo.com`).
- **Organización (O)** y **País (C)** → datos de la empresa (en OV/EV).
- **Clave pública** del servidor.
- **Fecha de validez** (desde / hasta).
- **Número de serie**.
- **Algoritmo de cifrado y firma** (ej. SHA-256 con RSA).
- **Firma digital de la CA emisora**.

👉 Puedes verlo en cualquier navegador → clic en el candado → _"Ver certificado"_.

### 🔹 5. ¿Cómo ayudan las CA a establecer la confianza?

Las CA están en el **root store** de los sistemas operativos y navegadores.

- Windows, macOS, Linux, Chrome, Firefox y otros mantienen una lista de **CA de confianza**.
- Cuando visitas un sitio con HTTPS, el navegador valida que:

  1. El certificado está firmado por una CA de confianza.
  2. El certificado no está caducado ni revocado.
  3. El dominio coincide.

👉 Si algo falla → el navegador muestra la alerta:

⚠️ _"Tu conexión no es privada"_.

### 🔹 6. ¿SSL.com proporciona certificados de confianza?

Sí ✅.

**SSL.com** es una autoridad de certificación pública reconocida globalmente. Sus certificados están incluidos en los navegadores y sistemas operativos, lo que significa que son **de confianza automáticamente**.

Ofrecen:

- Certificados SSL/TLS para sitios web.
- Certificados de firma de código.
- Certificados de firma de documentos.
- Certificados de cliente para autenticación.

### 🔹 Reflexiones finales

- Una **CA** es como una “notaría digital” → valida identidades y firma certificados.
- Los certificados digitales permiten **cifrar, autenticar y generar confianza** en Internet.
- Sin CA confiables, cualquiera podría montar un sitio falso con HTTPS (ej. `https://faceb00k.com`).
- Es posible usar certificados gratuitos (ej. **Let’s Encrypt**), pero para usos más delicados (firmar software, validar empresa) se suelen requerir CA de pago como DigiCert, GlobalSign o SSL.com.

👉 En conclusión:

Las CA son **la raíz de la confianza en Internet**. Gracias a ellas, cuando ves un candado en el navegador puedes estar razonablemente seguro de que el sitio es legítimo y que tu información viaja protegida.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 17**        | **Siguiente 19**           |
| ------------------ | ------------------- | -------------------------- |
| [🏠](../README.md) | [⏪](./7_17_SSO.md) | [⏩](./7_19_Local_Auth.md) |
