| **Inicio**         | **atrás 4**         | **Siguiente 6**        |
| ------------------ | ------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./5_4_SFTP.md) | [⏩](./5_6_SSL_TLS.md) |

---

## **Índice**

| Temario                                                                      |
| ---------------------------------------------------------------------------- |
| [141. Una descripción general de HTTP](#141-una-descripción-general-de-http) |
| [142. ¿Qué es HTTPS?](#142-qué-es-https)                                     |

# **HTTP HTTPS**

## **141. Una descripción general de HTTP**

![HTTP HTTPS](/img/5_Network_Topologies/HTTP_HTTPS.jpg "HTTP HTTPS")

### 1. Generalidades del protocolo HTTP

- **Definición:** HTTP (_HyperText Transfer Protocol_) es un protocolo de comunicación de la capa de aplicación usado para la transmisión de información en la web.
- **Rol principal:** Permite que los navegadores (clientes) soliciten recursos (páginas, imágenes, videos, APIs) a los servidores.
- **Cliente-Servidor:** Funciona bajo un modelo **request/response** → el cliente pide, el servidor responde.
- **Versiones más usadas:**

  - **HTTP/1.1** → más extendida.
  - **HTTP/2** → más rápida, soporta multiplexación (varias peticiones en la misma conexión).
  - **HTTP/3** → usa QUIC (sobre UDP) para mejorar la latencia y la seguridad.

📌 **Ejemplo simple:**

Cuando escribes en tu navegador:

```
https://www.wikipedia.org/
```

El navegador envía una petición HTTP al servidor de Wikipedia, que responde con el HTML de la página.

### 2. Arquitectura de los sistemas basados en HTTP

- **Modelo Cliente-Servidor:**

  - Cliente: navegador, app móvil, Postman, curl.
  - Servidor: Apache, Nginx, Node.js, etc.

- **Stateless (sin estado):** Cada petición HTTP es independiente; el servidor no recuerda datos de la anterior (a menos que se use _cookies_, _tokens_ o _sesiones_).
- **Capas intermedias:**

  - **Proxies:** caché intermedia (ej. Cloudflare).
  - **Gateways:** traducen protocolos.
  - **Balanceadores:** reparten carga entre varios servidores.

📌 **Ejemplo:**

Un usuario entra a un sitio de e-commerce:

1. El navegador pide el HTML → servidor responde.
2. El navegador pide las imágenes → servidor o CDN responde.
3. Cuando paga, la petición pasa por un **gateway** hacia el sistema de pagos.

### 3. Características clave del protocolo HTTP

- **Basado en texto:** Mensajes legibles (aunque HTTP/2 y 3 usan binario).
- **Stateless:** No guarda estado por sí solo.
- **Extensible:** Permite nuevos métodos, cabeceras, códigos de estado.
- **Seguro con HTTPS:** Al combinarse con TLS, cifra la comunicación.
- **Flexible:** Puede transportar no solo HTML, también JSON, XML, imágenes, videos, etc.
- **Códigos de estado estándar:** (200 OK, 404 Not Found, 500 Internal Server Error…).

### 4. ¿Qué se puede controlar con HTTP?

- **Métodos de acción:**

  - `GET` → obtener datos.
  - `POST` → enviar datos (ej. formulario).
  - `PUT` → actualizar recursos.
  - `DELETE` → eliminar recursos.

- **Cabeceras (headers):**

  - Autenticación (`Authorization: Bearer <token>`).
  - Tipo de contenido (`Content-Type: application/json`).
  - Caché (`Cache-Control: no-cache`).

- **Cookies y sesiones:** control de identidad y estado del usuario.
- **Control de seguridad:** mediante HTTPS, CORS, autenticación, etc.

📌 **Ejemplo real:**

Un request HTTP a una API REST puede incluir:

```
GET /api/productos HTTP/1.1
Host: tienda.com
Authorization: Bearer abc123
Accept: application/json
```

### 5. Flujo de HTTP

El ciclo típico es:

1. **Resolución DNS:** navegador obtiene la IP del dominio.
2. **Conexión TCP/QUIC:** cliente abre conexión con el servidor.
3. **Petición HTTP:** cliente envía `GET /recurso`.
4. **Procesamiento:** el servidor busca el recurso.
5. **Respuesta HTTP:** servidor responde con `200 OK` y el contenido.
6. **Renderizado:** navegador muestra la información.

📌 **Ejemplo práctico (navegador → servidor):**

```
Cliente: GET /index.html HTTP/1.1
         Host: ejemplo.com

Servidor: HTTP/1.1 200 OK
          Content-Type: text/html
          <html> ... </html>
```

### 6. Mensajes HTTP

Los mensajes HTTP son de **dos tipos**:

#### 6.1. Request (petición del cliente)

- **Estructura:**

  1. Línea de petición: método, recurso, versión (ej. `GET /index.html HTTP/1.1`)
  2. Cabeceras (ej. `Host`, `User-Agent`, `Accept`)
  3. Cuerpo (opcional, usado en POST/PUT con datos).

📌 **Ejemplo de request POST:**

```
POST /login HTTP/1.1
Host: ejemplo.com
Content-Type: application/json
Content-Length: 42

{"usuario": "gustavo", "password": "1234"}
```

#### 6.2. Response (respuesta del servidor)

- **Estructura:**

  1. Línea de estado: versión, código, descripción (ej. `HTTP/1.1 200 OK`)
  2. Cabeceras (ej. `Content-Type`, `Set-Cookie`)
  3. Cuerpo (HTML, JSON, archivo, etc.).

📌 **Ejemplo de response:**

```
HTTP/1.1 200 OK
Content-Type: application/json
Content-Length: 27

{"mensaje": "Login exitoso"}
```

✅ **Resultado final:**

HTTP es el **protocolo base de la web**, permite la comunicación entre clientes y servidores mediante un flujo de peticiones y respuestas. Su arquitectura es cliente-servidor y _stateless_, pero puede enriquecerse con cabeceras, cookies y seguridad (HTTPS). Los mensajes HTTP son simples de entender y manipular, lo que hace posible todo el ecosistema web actual.

---

[🔼](#índice)

---

## **142. ¿Qué es HTTPS?**

### 🔹 1. ¿Qué es **HTTPS**?

- **HTTPS (HyperText Transfer Protocol Secure)** es la **versión segura de HTTP**.
- La diferencia principal es que **usa cifrado mediante SSL/TLS** para proteger la comunicación entre el cliente (navegador, app) y el servidor.
- Es el estándar actual de la web: todos los sitios modernos deberían usarlo.

📌 Ejemplo:

- HTTP → `http://ejemplo.com`
- HTTPS → `https://ejemplo.com` (con el candado en el navegador).

### 🔹 2. ¿Cómo funciona **HTTPS**?

Funciona igual que HTTP (peticiones y respuestas), pero añade una **capa de seguridad con TLS (Transport Layer Security)**:

1. **Handshake TLS (apretón de manos):**

   - El cliente pide conectarse de forma segura.
   - El servidor envía su **certificado digital SSL/TLS** (emitido por una autoridad de confianza).
   - Se negocia un algoritmo de cifrado y una clave compartida.

2. **Cifrado de datos:**

   - Toda la información (formularios, contraseñas, cookies, etc.) viaja encriptada.
   - Aunque un atacante intercepte el tráfico, no podrá leerlo.

📌 Ejemplo práctico:

Si envías un login en HTTP:

```
usuario=gustavo&password=1234
```

➡️ un atacante en la misma red lo puede ver.

En **HTTPS**, la misma información viaja cifrada, algo así como:

```
A91F#5&3asdasd==...
```

### 🔹 3. ¿Por qué es importante HTTPS?

#### ✅ Ventajas

- **Confidencialidad:** protege los datos frente a espionaje (_man-in-the-middle attacks_).
- **Integridad:** asegura que el contenido no sea modificado en tránsito.
- **Autenticidad:** confirma que el servidor es legítimo (gracias al certificado).
- **Confianza del usuario:** los navegadores muestran un candado 🔒 cuando el sitio es seguro.
- **SEO:** Google favorece sitios con HTTPS en los resultados de búsqueda.

#### ❌ Si un sitio NO usa HTTPS:

- Los datos pueden ser interceptados (ej. en un WiFi público).
- Se pueden inyectar anuncios o malware en el tráfico.
- Los navegadores modernos (Chrome, Firefox) muestran alertas como:

  > ⚠️ _"Este sitio no es seguro"_

### 🔹 4. ¿Qué puerto utiliza HTTPS?

- **HTTPS usa el puerto 443** por defecto.
- HTTP usa el puerto 80.

📌 Ejemplo:

- HTTP → `http://ejemplo.com:80`
- HTTPS → `https://ejemplo.com:443`

(En la práctica, los navegadores asumen los puertos por defecto, así que no se muestran).

### 🔹 5. ¿De qué otra forma se diferencia HTTPS de HTTP?

| Característica         | HTTP           | HTTPS                 |
| ---------------------- | -------------- | --------------------- |
| Puerto por defecto     | 80             | 443                   |
| Seguridad              | No cifrado     | Cifrado con SSL/TLS   |
| Integridad de datos    | No garantizada | Garantizada           |
| Autenticación servidor | No             | Sí, con certificado   |
| Navegador lo marca     | “No seguro” ⚠️ | Candado 🔒 y “Seguro” |

### 🔹 6. ¿Cómo comienza un sitio web a utilizar HTTPS?

Un sitio empieza a usar HTTPS cuando obtiene e instala un **certificado SSL/TLS válido**.

**Pasos típicos:**

1. **Adquirir un certificado SSL/TLS** (gratuito con _Let’s Encrypt_ o de pago con una CA como DigiCert, GlobalSign, etc.).
2. **Instalar el certificado en el servidor web** (Apache, Nginx, IIS, etc.).
3. **Configurar el servidor para forzar HTTPS** → redirigir automáticamente todo el tráfico de HTTP a HTTPS.
4. **Actualizar enlaces internos** y probar que todo funciona bien.

📌 Ejemplo real:

En Apache, puedes redirigir todo a HTTPS con:

```apache
RewriteEngine On
RewriteCond %{HTTPS} off
RewriteRule ^(.*)$ https://%{HTTP_HOST}%{REQUEST_URI} [L,R=301]
```

✅ **Resultado final:**

- **HTTPS** es la evolución segura de HTTP.
- Funciona en el **puerto 443** y usa **TLS** para cifrar la comunicación.
- Es esencial para proteger datos, ganar confianza y cumplir estándares modernos de la web.
- Si un sitio no usa HTTPS, los datos quedan expuestos y los navegadores alertan de inseguridad.
- Un sitio comienza a usar HTTPS obteniendo e instalando un **certificado digital SSL/TLS**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 4**         | **Siguiente 6**        |
| ------------------ | ------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./5_4_SFTP.md) | [⏩](./5_6_SSL_TLS.md) |
