| **Inicio**         | **atrás 1**             | **Siguiente 3**        |
| ------------------ | ----------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./9_1_Phishing.md) | [⏩](./9_3_Whaling.md) |

---

## **Índice**

| Temario                                                                          |
| -------------------------------------------------------------------------------- |
| [268. Explicación del phishing en Wi-Fi](#268-explicación-del-phishing-en-wi-fi) |

# **Whishing**

## **268. Explicación del phishing en Wi-Fi**

![Whishin](/img/9_Attack_Types_and_Differences/phishing_en_Wi_Fi.jpeg "Whishin")

El **phishing en Wi-Fi** es una variante del phishing donde el atacante aprovecha redes inalámbricas (o crea redes falsas) para **engañar a usuarios y obtener credenciales, datos personales o inyectar contenido malicioso**. En vez de enviar un correo, el engaño se produce cuando la víctima se conecta — o intenta conectarse — a una red inalámbrica aparentemente legítima.

### Tipos comunes de phishing en Wi-Fi (resumen conceptual)

- **Evil Twin / Punto de acceso falso**: el atacante crea una red con un nombre (SSID) muy similar al real (por ejemplo: `AirportWiFi` vs `Airport_WiFi`) para que los usuarios se conecten a su router y así interceptar tráfico o mostrar un portal falso.
- **Captive portal fraudulento**: tras conectarte, apareces en una página de “login” o “aceptación de términos” que pide correo/contraseña, número de tarjeta o datos personales; esa página es controlada por el atacante.
- **Karma / Reponse-to-Probe**: el atacante responde a las búsquedas de redes que hace tu dispositivo y “se hace pasar” por una red conocida para que el dispositivo se conecte automáticamente.
- **Man-in-the-middle (MitM) sobre una red abierta**: el atacante intercepta y manipula el tráfico (por ejemplo, redirigiéndote a páginas falsas o insertando anuncios/malware).
- **DNS spoofing / DHCP maliciosos**: en la red el atacante ofrece respuestas DNS falsas o una puerta de enlace maligna que redirige dominios legítimos a páginas de phishing.

### ¿Cómo funciona el ataque? (flujo conceptual)

1. El atacante crea o compromete una red Wi-Fi accesible — muchas veces con un SSID convincente.
2. Tú te conectas (por confianza o porque tu dispositivo se autoconecta a nombres memorados).
3. El atacante fuerza una **interacción**: muestra un captive portal, inyecta un aviso que pide iniciar sesión o redirige solicitudes web a páginas que parecen reales.
4. Si introduces credenciales o datos, el atacante los captura. Si solo navegas, puede espiar tráfico sin cifrar y robar cookies/sesiones si las defensas fallan.
5. Con la información obtenida el atacante puede acceder a cuentas, realizar fraudes o vender datos.

### Ejemplos ilustrativos (hipotéticos y seguros)

**Ejemplo A — Café**

- SSID real: `CafeCentral_WiFi`
- SSID falso: `CafeCentral_FreeWiFi`
  Al conectarte, se abre una página que dice:

> **Bienvenido a Café Central**
>
> Para continuar, por favor valida tu cuenta introduciendo tu email y contraseña.
>
> Si introduces tu correo y contraseña (o usas rellenado automático del navegador), esa info va a parar al atacante.

**Ejemplo B — Aeropuerto (captiva “oficial”)**

- Tras conectarte a `Airport_Free_WiFi` aparece:

> Para usar este Wi-Fi, confirme su identidad. Ingrese su número de teléfono y el código de su tarjeta para verificar su cuenta.
> Esto es una trampa: servicios legítimos nunca piden la tarjeta para “verificar” acceso gratuito.

**Ejemplo C — Red de Hotel (MitM + DNS spoofing)**

- Te conectas a la red del hotel. El atacante, en la misma red, responde las consultas DNS y te redirige `servicio-banco.com` a una web idéntica pero controlada por el atacante. Si no verificas el certificado TLS o si el sitio no fuerza HTTPS estricto, puedes filtrar credenciales.

> Nota: en los ejemplos anterior **no** doy instrucciones técnicas para montar la red — solo explico el flujo y los señuelos que usan los atacantes.

### ¿Por qué es efectivo el phishing en Wi-Fi?

- Muchos dispositivos **se conectan automáticamente** a redes conocidas.
- Las personas confían en redes “oficiales” (cafés, aeropuertos, hoteles).
- Es fácil mostrar una **página de inicio** (captive portal) que aparenta ser legítima.
- El cifrado de extremo a extremo no siempre protege si la víctima acepta certificados falsos o si el atacante logra forzar conexiones inseguras.

### Señales de alerta (cómo reconocer que algo no va bien)

- El dispositivo se conecta a una red con nombre **parecido** pero no exactamente igual.
- Aparece un **portal que pide credenciales de servicios externos** (Google, banco, correo) en lugar de pedir solo aceptar términos.
- **Avisos de certificado** en el navegador (advertencias de HTTPS).
- Pide información sensible innecesaria (número de tarjeta para conexión Wi-Fi).
- El navegador muestra cadenas de redirección extrañas o dominios que no coinciden con la compañía que supuestamente provee el servicio.
- Conexiones muy lentas, redirecciones a publicidad masiva o descargas inesperadas.

### Cómo protegerte — medidas prácticas (usuario)

**Antes de conectarte**

- No te conectes automáticamente a redes abiertas. Desactiva “auto-join” o “conectar automáticamente” en redes públicas.
- Verifica el **nombre exacto del SSID** con el establecimiento (pregunta al bar/recepción cuál es el SSID oficial).
- Si un dispositivo solicita conectarse a una red que no conoces, **no la aceptes**.

**Mientras estés en la red**

- Usa siempre **VPN** de confianza para cifrar todo tu tráfico cuando estés en redes públicas.
- Abrir solo sitios con **HTTPS** y revisar el candado del navegador. Si el navegador muestra una alerta de certificado, no procedas.
- Evita operaciones sensibles (banca, compras) en Wi-Fi público; usa datos móviles o VPN.
- Desactiva el intercambio de archivos y el descubrimiento de redes en tu dispositivo (p. ej. “compartir archivos” o “AirDrop” en modo abierto).
- No ingreses credenciales en páginas de captive portal que pidan tu usuario/contraseña de servicios externos (Google, Microsoft, banco). Los portales legítimos piden generalmente solo aceptar términos o usar un token temporal.

**Después de conectarte (si sospechas)**

- Cierra sesión y cambia contraseñas desde una red segura.
- Desconecta el Wi-Fi y analiza el equipo con antivirus/antimalware.
- Habilita 2FA/MFA en tus cuentas vulnerables.
- Revisa sesiones activas en servicios y cierra sesiones desconocidas.

### Medidas organizacionales (empresas)

- Implementar **WPA2/WPA3 con 802.1X (EAP-TLS)** en lugar de redes abiertas o PSK.
- **Network Access Control (NAC)** y políticas de dispositivos gestionados (MDM) para evitar que dispositivos no autorizados se conecten.
- **Segmentación de red** y restricciones a recursos sensibles para redes de invitados.
- **Detección de APs falsos**: escaneos regulares del espectro y herramientas de inventariado de puntos de acceso.
- **Hardened captive-portal**: cuando exista, evitar pedir credenciales de terceros y mostrar con claridad el dominio y la política.
- Formación para empleados: campañas y simulacros (pero ejecutados por equipos de seguridad autorizados, sin poner en riesgo a usuarios reales).

### Detección y respuesta (qué hacer si sospechas que fuiste víctima)

1. **Desconectar inmediatamente** del Wi-Fi sospechoso (apagar Wi-Fi o modo avión).
2. Desde una red segura, **cambiar contraseñas** críticas y habilitar MFA.
3. Revocar sesiones activas en servicios (configuración → cerrar sesiones).
4. Ejecutar análisis antimalware en el dispositivo.
5. Si es equipo corporativo: **avisar al equipo de seguridad / TI** con captura del SSID, hora y capturas de la página del portal.
6. Revisar movimientos bancarios y notificar al banco si se compartió información financiera.
7. Reportar el incidente a la entidad administradora del lugar (aeropuerto, hotel) y, si procede, a las autoridades.

### Escenario narrativo (ejemplo para formación — sin instrucciones técnicas)

> María está en un aeropuerto y ve dos redes: `Airport_WiFi` y `Airport_Free_WiFi`. El cartel del aeropuerto indica que la red oficial es `Airport_WiFi`. Por prisa, María se conecta a `Airport_Free_WiFi`. Al hacerlo, se abre una página que pide iniciar sesión con su correo y contraseña “para verificar identidad”. María usa el autocompletado del navegador y su cuenta queda comprometida.
>
> Lección: confirmar always el SSID, evitar autocompletar en redes públicas y usar VPN.

### Lista rápida — Do / Don’t (pegable)

**DO**

- Preguntar el SSID oficial.
- Usar VPN en redes públicas.
- Verificar certificados HTTPS.
- Desactivar auto-join.
- Usar 2FA en todas las cuentas importantes.

**DON’T**

- No introduzcas credenciales de servicios externos en captive portals.
- No aceptes certificados o advertencias de seguridad.
- No uses Wi-Fi público para banca sin VPN.

### Consideraciones legales y éticas

Si detectas o sospechas de un punto de acceso malicioso, **no intentes “hackearlo” o desactivarlo**: eso puede ser ilegal. Documenta evidencias (SSID, hora, capturas) y notifícalo a la administración del lugar y a tu equipo de seguridad o autoridades.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 1**             | **Siguiente 3**        |
| ------------------ | ----------------------- | ---------------------- |
| [🏠](../README.md) | [⏪](./9_1_Phishing.md) | [⏩](./9_3_Whaling.md) |
