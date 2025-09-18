| **Inicio**         | **atrás 6**         | **Siguiente 8**              |
| ------------------ | ------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./10_6_XSS.md) | [⏩](./10_8_VLAN_Hopping.md) |

---

## **Índice**

| Temario                                                                                                                                                                                  |
| ---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [311. ¿Qué es un ataque de gemelo malvado?](#311-qué-es-un-ataque-de-gemelo-malvado)                                                                                                     |
| [312. Cómo los hackers pueden robar tus contraseñas a través de Wi-Fi con ataques Evil Twin](#312-cómo-los-hackers-pueden-robar-tus-contraseñas-a-través-de-wi-fi-con-ataques-evil-twin) |
|                                                                                                                                                                                          |

# **¿Qué es el ataque del gemelo malvado?**

## **311. ¿Qué es un ataque de gemelo malvado?**

![What is Evil Twin attack](/img/10_Common_Attacks/Evil_Twin_attack.webp "What is Evil Twin attack")

Un **ataque de gemelo malvado** ocurre cuando un atacante crea un **punto de acceso Wi-Fi falso** que imita a uno legítimo (por ejemplo, la red del aeropuerto, cafetería o universidad).

El objetivo es engañar a los usuarios para que se conecten al Wi-Fi falso creyendo que es seguro. Una vez conectados:

- El atacante puede **interceptar tráfico** (sniffing).
- Realizar un **ataque Man-in-the-Middle (MitM)**.
- Robar credenciales (correos, bancos, redes sociales).
- Redirigir a sitios falsos de phishing.
- Inyectar malware en descargas.

Se llama "gemelo malvado" porque **parece idéntico** al punto de acceso original (mismo SSID, a veces misma dirección MAC).

### ⚙️ ¿Cómo funciona un ataque de gemelo malvado?

1. **Selección del objetivo**

   - El atacante escanea las redes Wi-Fi disponibles con herramientas como `airmon-ng`, `airodump-ng` o `Kismet`.
   - Identifica el SSID (nombre) de una red legítima muy usada (ej: `Cafeteria_FreeWiFi`).

2. **Creación del Wi-Fi falso**

   - Configura un punto de acceso (con un adaptador Wi-Fi en modo AP) con **el mismo SSID** y, en ocasiones, misma dirección MAC (spoofing).
   - A veces se usa más potencia de señal para que el dispositivo del usuario prefiera la red falsa.

3. **Captura de víctimas**

   - Los dispositivos de los usuarios buscan redes conocidas y se conectan automáticamente.
   - El gemelo malvado “responde” más rápido que el verdadero, logrando que el usuario se conecte a él sin darse cuenta.

4. **Intercepción del tráfico**

   - El atacante puede:

     - Usar un **sniffer** (Wireshark, Ettercap) para espiar tráfico sin cifrar (HTTP, correos, etc.).
     - Forzar al usuario a cargar un **portal cautivo falso** (ej. página que pide credenciales).
     - Realizar **downgrade de cifrado** (ej. quitar HTTPS o inyectar certificados falsos).
     - Redirigir a sitios falsos o phishing.

5. **Robo de información**

   - Credenciales, tokens de sesión, números de tarjeta, correos, mensajes, etc.

### 🛡️ Cómo proteger tu dispositivo de los gemelos malvados

✅ **Usa una VPN confiable**

- Todo tu tráfico se cifra, incluso si estás en una red maliciosa.

✅ **Conéctate solo a redes conocidas**

- Pregunta el nombre exacto de la red oficial en aeropuertos, cafés o universidades.

✅ **Desactiva la conexión automática a Wi-Fi**

- Evita que tu dispositivo se conecte solo a redes guardadas.

✅ **Verifica el certificado HTTPS**

- En sitios web sensibles (banco, correo), revisa que el candado sea válido y emitido a la organización correcta.

✅ **Evita transacciones sensibles en Wi-Fi público**

- No ingreses contraseñas o datos de tarjeta sin VPN.

✅ **Mantén tu sistema actualizado**

- Parches de seguridad reducen vulnerabilidades explotables.

✅ **Usa datos móviles si dudas de la red**

- Es más seguro que un Wi-Fi sospechoso.

✅ **Monitoriza conexiones sospechosas**

- Existen apps de seguridad que avisan si tu dispositivo detecta cambios en el AP (ej. diferente MAC para mismo SSID).

### 📌 Resumen rápido

- Un **gemelo malvado** es un Wi-Fi falso que se hace pasar por legítimo.
- Funciona engañando al usuario y capturando tráfico para robar información.
- La defensa principal es: **VPN + cuidado al conectarse + no confiar en Wi-Fi público sin verificar**.

---

[🔼](#índice)

---

## **312. Cómo los hackers pueden robar tus contraseñas a través de Wi-Fi con ataques Evil Twin**

### 1) Resumen rápido: ¿qué es un Evil Twin y por qué puede robar contraseñas?

Un **Evil Twin** es un punto de acceso Wi-Fi falso que imita a uno legítimo (mismo nombre / SSID). Cuando un usuario se conecta a ese Wi-Fi falso, el atacante puede colocarse en medio de la comunicación (Man-in-the-Middle) y **interceptar o manipular** el tráfico. Si las credenciales viajan sin la protección adecuada (o si el atacante consigue engañar al usuario con una página de inicio de sesión falsa), esas contraseñas pueden quedar en manos del atacante.

### 2) Mecanismos típicos que usan los atacantes (explicación no-operativa)

1. **Portal cautivo falso (phishing local)**

   - El atacante presenta una página de “login” aparentemente legítima (por ejemplo, «Conéctese usando su correo») y captura lo que el usuario escribe.
   - Ejemplo educativo: llegas a un café, te conectas a “Cafetería_FreeWiFi”, aparece una página que pide tu correo y contraseña para “registrar el acceso” — si la ingresas, el atacante la obtiene.

2. **Interceptación de tráfico no cifrado (HTTP)**

   - Si una web usa HTTP (sin HTTPS) o una app envía credenciales sin cifrar, el atacante puede leer directamente esos datos.
   - Ejemplo educativo: en una web antigua de un foro la contraseña se envía en texto; al estar en la red falsa, el atacante ve esa petición y obtiene user/password.

3. **DNS spoofing / redirección a sitios falsos**

   - El atacante altera las respuestas DNS del dispositivo conectado para dirigir al usuario a una copia falsa del sitio (phishing) incluso si el usuario escribió la URL correcta.
   - Ejemplo educativo: intentas abrir `banco-ejemplo.com` y tu navegador te muestra una página casi idéntica pidiendo credenciales — si las introduces, van al atacante.

4. **Explotación de autocompletado / auto-relleno**

   - Los navegadores y gestores de contraseñas rellenan formularios automáticamente cuando la página y el dominio coinciden; una página de phishing bien diseñada puede abusar de esto. Los buenos gestores evitan autocompletar en dominios falsos, pero no todos los usuarios lo notan.

5. **Robo de cookies / tokens de sesión cuando no están protegidos**

   - Si las cookies de sesión no están marcadas `HttpOnly` o viajan sin cifrado, un atacante en la red puede capturarlas y reutilizarlas para secuestrar sesiones (account takeover).

> Importante: hoy en día la mayoría de sitios bancarios y grandes servicios usan HTTPS, HSTS y buenas prácticas que dificultan cosas como el “SSL stripping”. Sin embargo, el **phishing por portal cautivo** y la **ingeniería social** siguen siendo vectores muy efectivos.

### 3) Ejemplos narrativos (casos reales pero didácticos)

#### Ejemplo A — El café y el portal falso

1. Estás en una cafetería y tu teléfono se conecta automáticamente a la red “Cafetería_Publica”.
2. Aparece una página que pide “Correo y contraseña para usar Internet”. La página luce como la del local.
3. Introduces tus credenciales (por comodidad o prisa).
4. El atacante detrás del Evil Twin recibe esos datos y puede intentar usarlos en otros servicios (si reutilizas contraseñas) o venderlos.

**Por qué funciona:** la gente confía, el portal parece legítimo y la prisa o la costumbre de “meter la contraseña rápido” hacen el resto.

#### Ejemplo B — El aeropuerto y la web no segura

1. En un aeropuerto te conectas a una red abierta que crees legítima.
2. Abres una web antigua (sin HTTPS) para iniciar sesión en un servicio.
3. Las credenciales viajan en claro por la red; el atacante las captura y luego intenta acceder a cuentas donde reutilizaste la misma contraseña.

**Por qué funciona:** algunas webs internas o servicios de terceros aún no cifran todo, y la reutilización de contraseñas multiplica el daño.

#### Ejemplo C — La redirección DNS a un clon de banco

1. Te conectas a una red falsa. Cuando accedes al banco, la red te redirige a un dominio falsificado que se ve igual.
2. Ves el candado (a veces el atacante machaca el diseño), metes credenciales.
3. El atacante recibe la entrada y replica la respuesta para que tú no notes la diferencia inmediata.

**Por qué funciona:** diseño idéntico + confianza del usuario + posible uso de ingeniería social (mensajes en el “portal” pidiendo re-login urgente).

### 4) Por qué el robo de contraseñas tiene tanto impacto

- **Reutilización de contraseñas:** si usas la misma contraseña en varios sitios, con una sola captura el atacante puede tomar muchas cuentas (email, redes, banca).
- **Acceso a correo = restablecimiento de contraseñas:** con acceso al correo, el atacante puede pedir “olvidé mi contraseña” en otros servicios.
- **Venta de credenciales:** las credenciales válidas se venden en mercados ilícitos.
- **Sesión activa:** si capturan tokens o cookies, pueden evitar pedir contraseña y actuar como si fueran el usuario.

### 5) Cómo proteger TU dispositivo y contraseñas (lista detallada y accionable)

**Medidas inmediatas y fáciles**

- Desactiva la **conexión automática** a redes abiertas o públicas. (Ajustes > Wi-Fi > redes guardadas > desactivar auto-unirse).
- **Olvida** redes públicas que no uses.
- Ten siempre **VPN** activada cuando uses redes públicas: cifra tu tráfico entre tu dispositivo y el servidor VPN (el MitM no podrá leer el contenido).
- **Evita transacciones sensibles** (banca, compras) en Wi-Fi público; usa datos móviles o VPN.
- **No introduzcas contraseñas** en portales que no reconoces o que te pidan datos innecesarios.
- **Usa gestores de contraseñas**: generan y rellenan contraseñas únicas; la autofill suele ocurrir solo si el dominio coincide, ayudando a detectar phishing.
- **Activa MFA** (2FA) en todas las cuentas importantes — preferiblemente apps de autenticación o llaves físicas (FIDO2), no SMS.
- **Verifica certificados cuando el navegador muestre advertencias**: si el navegador indica certificado inválido, no sigas.
- Mantén **SO y navegador actualizados** y desactiva compartición de archivos en redes públicas.

**Medidas técnicas adicionales (recomendadas)**

- Usa servicios que implementen **HSTS** y **HTTPS obligatorio**; verifica que tus servicios críticos los usen.
- Configura cookies sensibles como `HttpOnly`, `Secure` y `SameSite`.
- Para empresas: despliega **WPA2/WPA3 Enterprise** (802.1X) o SSIDs con autenticación mutua; esto evita que clientes se conecten a APs sin la certificación esperada.
- Implementa sistemas de detección de redes falsificadas en infraestructuras más grandes (monitorización de BSSIDs duplicados, herramientas de seguridad de red).

### 6) ¿Cómo detectar que tu contraseña fue robada o que estás siendo atacado?

- Recibes alertas de inicio de sesión desde ubicaciones desconocidas.
- Notas cambios no autorizados en cuentas (email reenviado, contraseñas modificadas).
- El gestor de contraseñas te notifica que una credencial apareció en filtraciones conocidas.
- Aparecen alertas de seguridad en el propio servicio (p. ej. “hemos detectado inicio desde un nuevo dispositivo”).
- Comportamiento extraño de la red: re-autenticaciones constantes o ventanas de inicio de sesión inesperadas.

Si sospechas:

1. Desconéctate inmediatamente de la red Wi-Fi.
2. Conéctate por datos móviles o una red segura y **cambia** las contraseñas críticas (correo, banca) desde ahí.
3. Activa o refuerza MFA.
4. Revisa sesiones activas y revócalas (muchos servicios permiten “Cerrar sesiones en todos los dispositivos”).
5. Si hay impacto financiero, contacta a tu banco.
6. Revisa tu dispositivo con antivirus/antimalware por si hubiese descarga de software malicioso.

### 7) Buenas prácticas a largo plazo (hábitos de seguridad)

- Usa **contraseñas únicas** por sitio (gestor de contraseñas ayuda mucho).
- Prefiere **llaves de seguridad (FIDO2)** para servicios que lo soporten.
- Evita **SMS** como único factor 2FA (vulnerable a SIM swap).
- Educa a usuarios y familiares sobre phishing y redes públicas.
- Para empresas: políticas que obliguen VPN, MFA y bloqueo de uso de redes abiertas para accesos críticos.

### 8) Resumen práctico y checklist rápido

- ❌ No te conectes automáticamente a redes que no reconoces.
- ✅ Usa **VPN** en redes públicas.
- ✅ Activa **MFA** y usa **gestor de contraseñas**.
- ✅ Evita introducir contraseñas en páginas que aparecen tras conectarte a Wi-Fi público (portales sospechosos).
- ✅ Cambia contraseñas desde una conexión segura si sospechas robo; revoca sesiones.
- ✅ Mantén sistema y navegador actualizados; revisa advertencias de certificados.

### Nota ética y legal

La información anterior es **para defensa y concienciación**. No doy instrucciones prácticas para llevar a cabo ataques. Usa este conocimiento para protegerte y mejorar la seguridad de tus dispositivos y redes.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 6**         | **Siguiente 8**              |
| ------------------ | ------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./10_6_XSS.md) | [⏩](./10_8_VLAN_Hopping.md) |
