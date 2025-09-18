| **Inicio**         | **atrás 2**                              | **Siguiente 4**          |
| ------------------ | ---------------------------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./10_2_Man_in_the_middle_attack.md) | [⏩](./10_4_Spoofing.md) |

---

## **Índice**

| Temario                                                                                |
| -------------------------------------------------------------------------------------- |
| [302. Cross-Site Request Forgery](#302-cross-site-request-forgery)                     |
| [303. Cross-Site Request Forgery Explained](#303-cross-site-request-forgery-explained) |

# **Cross-Site Request Forgery (CSRF)**

## **302. Cross-Site Request Forgery**

![Cross-Site Request Forgery](/img/10_Common_Attacks/Cross_Site_Request_Forgery.jpeg "Cross-Site Request Forgery")

### 🔹 ¿Qué es CSRF?

Es un ataque que **engaña a un usuario autenticado** para que, sin darse cuenta, **ejecute una acción no deseada** en una aplicación web en la que ya tiene sesión abierta.

👉 El atacante **no roba las credenciales**, sino que **abusa de la confianza que el servidor tiene en la sesión del usuario** (cookies, tokens de sesión, cabeceras automáticas).

Ejemplo simple:

- Estás logueado en tu banco.
- Mientras navegas en otra página, el atacante te hace cargar una URL oculta como:

  ```
  https://banco.com/transferir?destino=123&cantidad=1000
  ```

- Como tu navegador **adjunta automáticamente la cookie de sesión**, el servidor cree que la petición viene de ti y ejecuta la transferencia.

### 🔹 ¿Cómo funciona el ataque? (flujo general)

1. **Víctima autenticada**: el usuario inicia sesión en una aplicación web y el navegador guarda la cookie de sesión.
2. **Engaño**: el atacante induce al usuario a visitar una página o abrir un enlace malicioso (email, post en foro, imagen incrustada, JS en otra web).
3. **Petición automática**: el navegador de la víctima hace la petición HTTP hacia el sitio objetivo **adjuntando cookies/session tokens**.
4. **Servidor confía**: como la petición viene con cookies válidas, el servidor ejecuta la acción pensando que fue legítima.

### 🔹 Ejemplos de CSRF

#### Ejemplo 1 — Transferencia bancaria (GET)

```html
<img src="https://banco.com/transferir?cuenta=123&cantidad=1000" />
```

La imagen se carga automáticamente → el navegador hace la petición GET al banco con la cookie de sesión.

#### Ejemplo 2 — Cambio de contraseña (POST oculto)

```html
<form action="https://victima.com/cambiarPassword" method="POST">
  <input type="hidden" name="nuevaPassword" value="hackeado123" />
</form>
<script>
  document.forms[0].submit();
</script>
```

El navegador envía el formulario automáticamente con la cookie activa → el servidor cambia la contraseña.

#### Ejemplo 3 — Redes sociales

- Usuario autenticado en Facebook/Twitter.
- Entra a un foro con código malicioso que hace `POST` para seguir a una cuenta o dar "like" a una página.
- La acción se ejecuta con el perfil real del usuario.

### 🔹 Medidas de prevención que **NO funcionan** ❌

- **Autenticación por cookie o sesión únicamente**: precisamente lo que se explota.
- **Ocultar botones o URLs sensibles**: el atacante puede forzar la petición directo al endpoint.
- **Usar solo métodos POST en vez de GET**: no previene CSRF (formularios y JS pueden mandar POST sin que el usuario lo note).
- **Comprobar únicamente la cabecera `Referer`**: fácilmente manipulable o puede venir vacía por proxies/firewalls.
- **Usar HTTPS**: cifra el tráfico, pero no impide que el navegador envíe la cookie en una petición forzada.

### 🔹 Actividades de seguridad relacionadas (defensivas ✔️)

- **Tokens CSRF (anti-CSRF tokens)**

  - Se generan de forma única por sesión o por formulario.
  - Se envían como campo oculto en formularios o como cabecera personalizada.
  - El servidor valida que el token coincida antes de procesar la acción.
  - Ejemplo: frameworks como **Django, Spring, Laravel** ya incluyen protección CSRF.

- **Cabeceras SameSite en cookies**

  - `Set-Cookie: sessionid=XYZ; SameSite=Strict`
  - Evitan que las cookies se envíen en peticiones cross-site.
  - Muy efectivo contra CSRF moderno.

- **Doble submit de cookies**

  - Enviar un valor de token tanto en cookie como en cuerpo/cabecera de la petición; el servidor valida que coincidan.

- **Reautenticación en acciones sensibles**

  - Ejemplo: pedir la contraseña de nuevo al transferir dinero o cambiar credenciales.

- **MFA (Multi-Factor Authentication)**

  - No previene el envío de la petición, pero mitiga consecuencias si se requiere confirmación extra.

### 🔹 Resumen rápido

- **CSRF** explota la **confianza del servidor en el navegador del usuario autenticado**.
- El atacante induce a la víctima a enviar peticiones maliciosas **con su propia cookie de sesión**.
- **Ejemplos típicos**: transferencias, cambios de email/contraseña, interacciones en redes sociales.
- **Medidas que no sirven**: solo usar POST, solo HTTPS, esconder formularios, confiar en `Referer`.
- **Defensas efectivas**: tokens anti-CSRF, cookies `SameSite`, reautenticación, MFA, frameworks seguros.

---

[🔼](#índice)

---

## **303. Cross-Site Request Forgery Explained**

### 1) ¿Qué es CSRF? (definición breve)

CSRF (Cross-Site Request Forgery) es un ataque por el que **un atacante induce a un usuario autenticado** a enviar una petición HTTP a una aplicación en la que ya tiene sesión, con el objetivo de que el servidor **ejecute una acción legítima pero no deseada** (ej.: transferir dinero, cambiar email, publicar en nombre del usuario). El ataque se aprovecha de que el navegador del usuario **adjunta automáticamente credenciales (cookies de sesión, cabeceras de autenticación gestionadas por el navegador)** a las peticiones.

### 2) ¿Por qué funciona CSRF? (prerrequisitos)

Para que un CSRF tenga éxito normalmente deben cumplirse estas condiciones:

- El usuario está **autenticado** en la aplicación objetivo (hay cookie o token de sesión válido).
- El navegador del usuario **envía automáticamente** la cookie o credencial cuando se hace la petición (eso es comportamiento estándar del navegador).
- La aplicación **confía** en la cookie como prueba de que la petición fue iniciada por el propio usuario y **no exige una verificación adicional** (token único, comprobación de Origin/Referer, reautenticación).

### 3) Flujo conceptual del ataque (alto nivel)

1. Víctima inicia sesión en `bank.example` y obtiene una cookie de sesión.
2. Víctima visita una página maliciosa (p. ej. `attacker.example`) o hace clic en un enlace.
3. La página maliciosa provoca que el navegador envíe una petición hacia `bank.example` (por ejemplo, cargando una imagen, enviando un formulario o ejecutando un `fetch`) — el navegador incluye la cookie de sesión.
4. `bank.example` recibe la petición con cookie válida y **ejecuta** la acción (porque no tenía forma de distinguir si la petición la inició el usuario desde su propia interfaz o desde una web externa).

### 4) Ejemplos conceptuales (ilustrativos — para entender el vector)

- **GET forzada (imagen):**

  ```html
  <img src="https://bank.example/transfer?to=acct123&amount=1000" />
  ```

  El navegador pide la URL para obtener la imagen; si `bank.example` procesa esa URL como una acción (mala práctica), la acción se ejecuta con la cookie del usuario.

- **POST automático (form + auto-submit):**

  ```html
  <form action="https://site.example/change-email" method="POST">
    <input type="hidden" name="email" value="attacker@evil" />
  </form>
  <script>
    document.forms[0].submit();
  </script>
  ```

  El formulario se envía silenciosamente desde la página atacante; la cookie se adjunta y la aplicación podría cambiar el email.

> Estos fragmentos son **conceptuales**: se usan en documentación y laboratorios para aprender cómo detectar y proteger. Nunca los uses contra sistemas sin autorización.

### 5) Vectores y variantes modernas

- **Formularios e imágenes** (clásico).
- **AJAX/Fetch JSON**: diferencias importantes — las peticiones `fetch` con cabeceras personalizadas requieren CORS y normalmente no pueden ser enviadas cross-origin sin permiso, por lo que diseñar APIs que **requieran un header personalizado** es una defensa práctica.
- **APIs REST con cookies**: si tu API usa cookies para autenticación, sigue siendo vulnerable a CSRF si no hay protección adicional.
- **CSRF “dinámico”**: explotación combinada con otras técnicas (p. ej. XSS que facilita generar payloads específicos)

### 6) Medidas de prevención **efectivas** (qué implementar)

#### a) **Synchronizer Token Pattern (anti-CSRF token)**

- Generar un token aleatorio por sesión o por formulario, almacenarlo en servidor y enviarlo al cliente dentro del HTML (campo oculto). El servidor valida el token en cada petición sensible. Es la defensa clásica y fiable.

#### b) **SameSite cookies** (`Lax`/`Strict`)

- Configurar la cookie de sesión con `SameSite=Lax` o `Strict` reduce mucho el riesgo porque el navegador **no enviará la cookie** en muchas peticiones cross-site normales. Muy recomendable como defensa adicional. Atención: compatibilidad con navegadores antiguos y efectos en enlaces externos deben evaluarse.

#### c) **Cabeceras personalizadas + CORS** para APIs AJAX

- Si tu frontend envía un header personalizado (p. ej. `X-CSRF-Token`) al backend, los navegadores **no** permiten enviar esos headers cross-origin sin que el servidor permita explícitamente CORS. Esto evita que una página atacante haga peticiones AJAX con dichos headers. Es una defensa práctica para APIs.

#### d) **Comprobar `Origin` o `Referer`**

- Validar `Origin` (mejor) o `Referer` puede ayudar, pero no es infalible (proxies, algunas políticas corporativas y fallos en clientes pueden eliminar o alterar `Referer`). Úsalo en combinación con tokens.

#### e) **Reautenticación / Confirmaciones para acciones críticas**

- Para operaciones sensibles (transferencias, cambios de correo, alta de administradores) exigir re-entrada de contraseña, código 2FA o confirmación explícita reduce el impacto.

#### f) **Evitar cookies para autenticación en APIs públicas**

- Usar tokens en `Authorization: Bearer` (enviado por JS) evita CSRF (porque el navegador no envía automáticamente ese header en solicitudes cross-origin) — pero **ojo**: almacenar tokens en `localStorage` los hace vulnerables a XSS, así que balancear riesgos.

### 7) Medidas **que NO funcionan** (o son insuficientes)

- **Sólo usar `HttpOnly` en las cookies**: `HttpOnly` evita que JavaScript lea la cookie, pero **no** evita que el navegador la envíe automáticamente en peticiones CSRF.
- **Cambiar a POST en todos los endpoints**: usar POST no evita CSRF (formularios y JS pueden enviar POST).
- **Ocultar URLs, ofuscar parámetros o “no documentar” endpoints**: seguridad por oscuridad no protege contra CSRF.
- **Confiar sólo en `Referer`**: es útil pero no 100% fiable (puede faltar o ser filtrado).
  Estas medidas pueden **completar** la defensa pero no deben ser la única protección.

### 8) Detección y pruebas (para pentesters / defensores — **siempre con permiso**)

Si tienes autorización, para verificar CSRF en un entorno controlado revisa:

- ¿Los endpoints que realizan cambios requieren un token sincronizado o validación de `Origin`/`Referer`?
- ¿Las cookies de sesión tienen `SameSite` configurado?
- ¿Las APIs requieren cabeceras personalizadas o `Authorization` en lugar de depender únicamente de cookies?
- Usar entornos deliberadamente inseguros como **OWASP WebGoat** o **OWASP Juice Shop** para practicar exploitation/mitigación en laboratorio. (útiles para aprender sin riesgo legal).

### 9) Buenas prácticas resumidas (checklist rápido)

- Habilitar tokens anti-CSRF (frameworks modernos ya los entregan).
- Configurar `SameSite` en cookies (Lax o Strict según el caso).
- Para APIs, favorecer autenticación basada en headers (y aplicar CORS correctamente).
- Requerir reautenticación / MFA para operaciones críticas.
- Mantener auditoría y monitoreo de patrones anómalos (peticiones cross-origin a endpoints sensibles).
- Testear en laboratorios (WebGoat, Juice Shop) y seguir OWASP WSTG/cheatsheets.

### Conclusión

CSRF es una clase clásica pero todavía relevante de ataque porque explota el **comportamiento legítimo del navegador** (envío automático de cookies) y la **confianza del servidor** en esa información. Hoy en día la defensa efectiva mezcla **tokens anti-CSRF**, **SameSite cookies**, validaciones de `Origin/Referer`, y diseño de APIs que no dependan únicamente de cookies. Siempre practica en entornos controlados y con permiso.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 2**                              | **Siguiente 4**          |
| ------------------ | ---------------------------------------- | ------------------------ |
| [🏠](../README.md) | [⏪](./10_2_Man_in_the_middle_attack.md) | [⏩](./10_4_Spoofing.md) |
