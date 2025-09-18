| **Inicio**         | **atrás 1**                                                        | **Siguiente 3**                            |
| ------------------ | ------------------------------------------------------------------ | ------------------------------------------ |
| [🏠](../README.md) | [⏪](./10_1_Denial_of_Service_vs_Distributed_Denial_of_Service.md) | [⏩](./10_3_Cross_Site_Request_Forgery.md) |

---

## **Índice**

| Temario                                                        |
| -------------------------------------------------------------- |
| [301. Man-in-the-middle attack](#301-man-in-the-middle-attack) |

# **Man-in-the-middle attack**

## **301. Man-in-the-middle attack**

![Man-in-the-middle ](/img/10_Common_Attacks/Man_in_the_middle_attack.png "Man-in-the-middle ")

Un **MITM** es un ataque en el que un tercero se sitúa entre dos interlocutores (por ejemplo: cliente ↔ servidor) para **escuchar, manipular o redirigir** la comunicación sin que las partes se den cuenta. El atacante puede actuar de forma **pasiva** (solo escucha) o **activa** (modifica, inyecta o bloquea mensajes).

### Concepto técnico (resumen)

- La comunicación original se “divide” en dos conexiones: cliente→atacante y atacante→servidor. Desde la perspectiva del cliente y del servidor, la sesión parece válida aunque pase por el atacante.
- Objetivos típicos: **robar credenciales/sesiones**, inyectar contenido malicioso, alterar transacciones o recolectar información sensible para pasos posteriores (espionaje, fraude, lateral movement).

### Tipos / vectores comunes (qué técnicas aparece con más frecuencia)

> _Nota:_ describo cada vector conceptualmente — **no** doy comandos ni pasos explotables.

- **ARP spoofing / ARP poisoning (LAN)**

  El atacante envía mensajes ARP falsos en una red local para engañar a los hosts y hacerles creer que su MAC corresponde a la IP del gateway (o de otro equipo). Resultado: el tráfico de la LAN puede pasar por el atacante. Es un método clásico en redes Ethernet.

- **Evil-twin / rogue Wi-Fi (punto de acceso falso)**

  Se crea un punto de acceso inalámbrico que imita el SSID legítimo; las víctimas se conectan y todo su tráfico pasa por el atacante. Muy usado en lugares públicos.

- **DNS spoofing / cache poisoning**

  El atacante contamina respuestas DNS para que un dominio (ej. `banco.com`) resuelva a una IP controlada por el atacante, redirigiendo tráfico hacia servidores maliciosos o proxies. DNSSEC está pensado para mitigar este vector.

- **SSL/TLS downgrade / SSL-stripping**

  Ataques que impiden o revierten el uso de HTTPS, forzando comunicaciones en texto claro (HTTP) o presentando certificados engañosos; Moxie Marlinspike describió estas ideas y las herramientas que las demostraron públicamente, lo que impulsó contramedidas como HSTS.

- **Rogue / mal configurated proxies y certificados**

  Si un atacante (o una entidad intermedia) logra que el cliente confíe en un certificado falso (por ejemplo instalando un root CA malicioso en el equipo), puede interceptar y descifrar TLS legalmente a nivel de empresa o ilegalmente si es mal usado.

- **BGP/IP hijacking (Internet-level MITM)**

  A nivel de enrutamiento, anuncios BGP falsos pueden desviar tráfico global hacia redes controladas por el atacante (o por error), permitiendo intercepción a gran escala. Un ejemplo famoso fue el incidente que desvió tráfico de YouTube en 2008.

- **Man-in-the-Browser / malware en el cliente**

  Un troyano o extensión maliciosa dentro del navegador intercepta y modifica las páginas o formularios antes de que el usuario vea/mande su contenido (muy usado para fraude bancario).

### Ejemplos prácticos (escenarios narrativos)

1. **Café con Wi-Fi abierto (side-jacking):** un usuario inicia sesión en una red social usando HTTP para la sesión (o la web no fuerza HTTPS). Un atacante en la misma AP captura la cookie de sesión y la usa para tomar el control de la cuenta. La herramienta _Firesheep_ (demo de 2010) mostró cuánto riesgo había cuando sitios no cifraban sesiones completas.

2. **Evil twin en aeropuerto:** el atacante lanza un AP con el mismo nombre del Wi-Fi del aeropuerto; las víctimas se conectan y todo el tráfico va por el atacante, que puede redirigir a páginas falsas de inicio de sesión o capturar credenciales.

3. **Empresa con proxy TLS:** en entornos corporativos el proxy de seguridad puede interceptar TLS (con certificado empresarial instalado en máquinas corporativas) para inspeccionar contenido. Es legítimo en muchos casos, pero si la clave o el proxy se comprometen, constituye un vector MITM.

4. **Desvío de rutas (BGP hijack):** un AS anuncia rutas que atraen tráfico hacia su red, posibilitando la captura o modificación masiva de tráfico antes de re-encaminarlo. Este tipo de problemas ha ocurrido por error y por abuso intencional.

### Señales de que podrías estar sufriendo (detectando un MITM)

- **Advertencias de certificado** en el navegador (certificado no coincidente, emisor extraño).
- **Conexiones HTTPS que cambian a HTTP** o páginas que no muestran el candado donde antes sí lo hacían. (HSTS ayuda a evitar este problema).
- **IPs de destino/puerta de enlace cambiantes** en tu ARP table o duplicidad de IP/MAC en la LAN.
- **Certificados con emisores desconocidos** o certificados nuevos para dominios que nunca los tuvieron (comprobar CT logs / Certificate Transparency).
- **Picos de tráfico a servidores intermediarios** o rutas inusuales en traceroute.
- Registros de aplicaciones mostrando datos que no coinciden con la geolocalización habitual de usuarios.

### Mitigaciones y mejores prácticas (defensivas)

- **Cifrado extremo a extremo / TLS en todo el sitio**: obligar HTTPS en todas las páginas (no solo login), aplicar HSTS, usar TLS moderna y OCSP stapling.
- **DNSSEC / validación de DNS / DoT / DoH**: dificulta el DNS spoofing si la resolución está firmada y validada.
- **Evitar redes Wi-Fi abiertas** o usar **VPN** en redes públicas para proteger la confidencialidad frente a sniffers/rogue AP.
- **802.1X / segmentación de la red** y control de acceso en capas de enlace para reducir el riesgo de ARP spoofing dentro de una LAN.
- **Monitoreo de red** (NetFlow, detección de ARP anomalies) y alertas sobre cambios en tablas ARP o rutas BGP.
- **Reducir confianza en CA**: revisar emisores de certificados, usar Certificate Transparency y, cuando proceda, pinning o mTLS en servicios internos.
- **Autenticación fuerte / MFA**: mitiga el impacto si credenciales son capturadas.
- **Parcheo y hardening** de routers, APs y servidores DNS para evitar ser usados como amplificadores o puntos de ataque.

### Cómo practicar **legalmente y de forma segura** (hacking ético)

1. **Siempre — permiso explícito.** No pruebes ataques contra redes o sistemas ajenos sin autorización escrita. Hacerlo es delito.
2. **Laboratorio aislado:** monta VMs en una red interna que no tenga salida a Internet (virtual network aislada). Crea al menos 3 máquinas: cliente, servidor, e "intermediario" para **estudiar y observar** (p. ej. captura de paquetes con Wireshark). Esto te permite ver en detalle cómo cambian paquetes/sesiones sin afectar a terceros. OWASP y otros proyectos publican guías para pruebas en entornos controlados.
3. **Herramientas de análisis pasivo:** usa analizadores (Wireshark, tcpdump) para estudiar cómo se ven las sesiones y los certificados; aprender a leer TLS handshakes y cookies es valiosísimo.
4. **Laboratorios y retos autorizados:** plataformas de laboratorio (containers/VMs vulnerables, CTFs corporativos, laboratorios de pentesting) permiten practicar escenarios sin riesgo legal.
5. **Simula ataques solo para entender la defensa:** por ejemplo, en tu laboratorio puede ser instructivo observar qué pasa si un servidor no tiene HSTS o si DNS no está firmado — siempre con máquinas cerradas. Nunca publiques ni uses esas técnicas contra terceros.

### Recursos recomendados (lectura técnica)

- OWASP: manipulator / man-in-the-middle & Web Security Testing Guide.
- MITRE ATT\&CK: técnicas Adversary-in-the-Middle (AiTM).
- Artículos de Cloudflare sobre DNSSEC y prevención de SSL stripping / HSTS.
- Historia y casos reales (Firesheep, BGP hijacks) para contexto histórico y lecciones aprendidas.

### Advertencia legal y ética (breve)

La información técnica sirve para **defensa, detección y aprendizaje**. Ejecutar MITM contra sistemas sin autorización es ilegal y éticamente reprobable. En _hacking ético_ el marco es: **autorización explícita, alcance definido y reporte responsable**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 1**                                                        | **Siguiente 3**                            |
| ------------------ | ------------------------------------------------------------------ | ------------------------------------------ |
| [🏠](../README.md) | [⏪](./10_1_Denial_of_Service_vs_Distributed_Denial_of_Service.md) | [⏩](./10_3_Cross_Site_Request_Forgery.md) |
