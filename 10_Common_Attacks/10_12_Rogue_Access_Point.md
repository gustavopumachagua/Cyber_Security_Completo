| **Inicio**         | **atrás 11**                   | **Siguiente 13**                 |
| ------------------ | ------------------------------ | -------------------------------- |
| [🏠](../README.md) | [⏪](./10_11_Replay_Attack.md) | [⏩](./10_13_Buffer_Overflow.md) |

---

## **Índice**

| Temario                                                                                        |
| ---------------------------------------------------------------------------------------------- |
| [321. Puntos de acceso no autorizados](#321-puntos-de-acceso-no-autorizados)                   |
| [322. ¿Qué es un punto de acceso no autorizado?](#322-qué-es-un-punto-de-acceso-no-autorizado) |

# **Punto de acceso fraudulento**

## **321. Puntos de acceso no autorizados**

![Rogue Access Point](/img/10_Common_Attacks/Rogue_Access_Point.png "Rogue Access Point")

### 1) ¿Qué es un punto de acceso no autorizado (Rogue AP)?

Un **Rogue AP** es cualquier punto de acceso inalámbrico conectado a (o que interactúa con) la infraestructura de red de una organización **sin autorización del equipo de TI**. Puede ser:

- **Accidental/benigno**: un empleado conecta un router doméstico a un puerto Ethernet para “mejorar Wi-Fi”.
- **Malicioso**: un atacante conecta un AP al switch o crea un _evil-twin_ (AP que imita un SSID legítimo) para espiar clientes o hacer phishing.
  Estas unidades rompen las políticas de control de acceso y evitan las protecciones planificadas para la red.

### 2) ¿Por qué son peligrosos? (impacto)

Un rogue AP puede:

- **Permitir acceso directo a la red interna** si el AP está enchufado a un puerto con privilegios.
- **Facilitar ataques Man-in-the-Middle (MitM)** y captura de tráfico (HTTP no cifrado, tokens, etc.).
- **Servir para phishing** (captura de credenciales mediante captive portals) — forma típica de _evil twin_.
- **Exponer dispositivos IoT** o permitir pivotaje lateral dentro de la red corporativa.
- **Crear puntos ciegos de seguridad** que no son visibles para los controles centralizados.

  Estos riesgos están documentados por agencias y guías de seguridad de redes inalámbricas.

### 3) Tipos y escenarios (ejemplos reales y didácticos)

- **Empleado ingenuo**: un trabajador conecta su “router de casa” al puerto de escritorio; lo configura como abierto o con WPA personal; deja la red interna accesible. (Riesgo: puerto con VLAN errónea → acceso a recursos internos).
- **Evil-Twin / phishing**: atacante monta un AP con el mismo SSID que el de la cafetería corporativa y prepara un captive portal que pide login; usuarios despistados entregan credenciales. (Casos similares han sido reportados en aeropuertos y vuelos).
- **AP clandestino conectado por intruso**: alguien accede físicamente a un armario de telecomunicaciones y enchufa un AP para acceso remoto persistente.
- **AP de terceros mal configurado**: proveedor o contratista deja su equipo conectado sin las políticas adecuadas.

### 4) Cómo detectarlos (estrategias defensivas)

Detección por capas — combina varias fuentes para fiabilidad:

**a) Detección inalámbrica (WIDS/WIPS)**

- Sistema de detección inalámbrica pasivo (WIDS) o solución WIPS que monitoree el espectro y liste APs y clientes. Buscan: SSID desconocidos, BSSID con mismo SSID que APs autorizados, APs fuera de la base de datos de inventario. Muchos controladores empresariales incluyen detección automática.

**b) Detección en la red cableada**

- Monitorear switches: detectar puertos que anuncian LLDP/802.1X fallos, o puertos con MACs de APs no esperadas.
- DHCP logs: verás leases otorgados por MACs/DHCP servers inesperados.
- NetFlow/traffic baselines: picos o patrones inusuales asociados a sub-redes inalámbricas.

**c) Inventario y correlación**

- Mantener una lista autoritativa de APs (BSSID, canal, ubicación). Correlacionar lo que reporta el WIDS con esa base de datos.

**d) Señales RF y físicas**

- Detección de SSID duplicados, APs en canales no previstos, potencias inusuales o APs ubicados fuera de zonas autorizadas (triangulación RF).
- Inspecciones físicas y sitio RF surveys periódicos.

### 5) Prevención y endurecimiento (mejores prácticas)

Aplica controles en **capas**: políticas, red cableada, autenticación y monitoreo.

#### Políticas y gobernanza

- Política estricta que prohíba APs no autorizados y sancione conexiones no autorizadas. Comunica qué dispositivos están permitidos.
- Inventario único y registro (BSSID, canal, ubicación, responsable).

#### Controles de red cableada

- **Cerrar puertos de switch no usados** (administrative shutdown).
- **Port-security**: limitar el número de MACs por puerto, sticky MAC y violación en `restrict` o `shutdown` según política.
- **802.1X / NAC (Network Access Control)**: autenticar dispositivos antes de otorgar acceso a la red; asignar VLANs en función del resultado (guest, corp). Esto evita que un AP sin credenciales obtenga acceso a VLANs internas.
- **DHCP Snooping / Dynamic ARP Inspection / IP Source Guard**: evita que un rogue actúe como servidor DHCP o haga ARP spoofing.

#### Seguridad Wi-Fi y Endurecimiento inalámbrico

- **WPA2/WPA3-Enterprise** con EAP-TLS (certificados) — evita contraseñas compartidas que facilitan conexión a APs maliciosos.
- **Controlador y gestión centralizada** (CAPWAP) — APs gestionados por controller reducen la probabilidad de APs desconectados o mal configurados.
- **Segmentación**: VLANs separadas para Guest vs Corporate; aplicar firewall entre VLANs.
- **Blindaje físico**: sellar o restringir acceso a racks y puntos de conexión.

### 6) Reacción e investigación (qué hacer si detectas un Rogue AP)

1. **No actuar a ciegas**: documenta evidencia (WIDS alerts, SSID, BSSID, canal, RSSI, DHCP leases, switch port info).
2. **Localiza físicamente**: triangulación RF o site survey para identificar ubicación; inspecciona puertos de switch asociados.
3. **Aísla**: si el AP está conectado por Ethernet, desconecta el puerto del switch o aplica port-security/errdisable; si es un AP malicioso en RF, usa herramientas WIPS para alertar y, según políticas, _contención_ (advertencia: el envío de desautenticaciones puede ser problemático legalmente y debe usarse con precaución).
4. **Forense**: captura tráfico relevante, logs DHCP, registros de autenticación; preserva evidencia.
5. **Mitiga y corrige**: parchea la brecha (cerrar puerto, reforzar 802.1X, revocar credenciales), rota credenciales si fue posible compromiso de cuentas.
6. **Informe y lecciones**: notifica stakeholders, ajusta políticas y formación.

### 7) Lista práctica rápida — Checklist de defensa

- Inventario de APs (BSSID/canales/ubicación).
- Habilitar WIDS/WIPS y alertas.
- 802.1X + NAC para puertos de red y acceso Wi-Fi.
- Cerrar puertos no usados / aplicar port-security.
- DHCP Snooping, DAI, IP Source Guard en switches.
- Separar tráfico Guest en VLANs y aplicar firewall entre segmentos.
- Requerir WPA2/WPA3-Enterprise (EAP-TLS preferido).
- Realizar RF site surveys y auditorías periódicas.
- Formar empleados sobre no conectar routers/domésticos.

(Varios de estos controles son recomendados por fabricantes y guías NIST/Cisco).

### 8) Comandos y ejemplos defensivos (ejemplo Cisco — solo configuraciones que aumentan seguridad)

> _Ejemplo ilustrativo para endurecer puertos de acceso en un switch Cisco (solo la parte defensiva):_

```text
interface GigabitEthernet1/0/5
  switchport mode access
  switchport access vlan 20
  switchport port-security
  switchport port-security maximum 2
  switchport port-security mac-address sticky
  switchport port-security violation restrict
  spanning-tree bpduguard enable
  shutdown unused interfaces   ! (hacer en interfaces físicas sin uso)
```

Y para trunkes:

```text
interface GigabitEthernet1/0/48
  switchport trunk encapsulation dot1q
  switchport mode trunk
  switchport trunk allowed vlan 10,20,30
  switchport trunk native vlan 999  ! native VLAN no usada por hosts
```

Estos son ejemplos defensivos típicos; ajústalos a tu política de red y hardware.

### 9) Herramientas útiles (solo para detección / inventario)

- Soluciones empresariales WIPS/WIDS (controladores Cisco, Aruba, Ruckus, etc.).
- Escaneo pasivo / análisis RF: **Kismet**, herramientas de site survey (Ekahau, AirMagnet) para localizar APs.
- Correlación con logs de switches / DHCP / SIEM.

### 10) Conclusión — ¿qué recordar?

- Un **Rogue AP** puede ser tan inofensivo como la intención de un empleado, o tan peligroso como la puerta de entrada de un atacante. La protección real requiere **políticas claras**, **controles en la red cableada (802.1X, port-security, DHCP snooping)** y **monitorización inalámbrica** (WIDS/WIPS y site surveys).
- Combina gobernanza, técnica y educación: eso reduce drásticamente la probabilidad de que un rogue AP provoque una brecha.

---

[🔼](#índice)

---

## **322. ¿Qué es un punto de acceso no autorizado?**

### 📌 ¿Qué es un punto de acceso no autorizado?

Un **punto de acceso no autorizado (Rogue AP)** es cualquier dispositivo de red inalámbrico que se conecta a tu infraestructura de forma **no controlada o sin autorización del equipo de TI**.

Puede ser:

- Un **router doméstico** conectado por accidente a la red corporativa.
- Un **AP malicioso** colocado por un atacante para robar credenciales o espiar tráfico.
- Un **AP no gestionado** instalado por un tercero (proveedor, empleado, contratista).

👉 El problema es que estos dispositivos **abren una puerta trasera** a tu red interna y evaden las medidas de seguridad oficiales.

### 📌 Tipos de puntos de acceso no autorizados

1. **Rogue AP accidental (benigno):** empleado conecta su router Wi-Fi personal para “mejorar señal”.
2. **Rogue AP malicioso:** atacante instala un AP para tener acceso remoto y lanzar ataques internos.
3. **Evil Twin:** atacante crea un AP con el mismo nombre (SSID) que el legítimo para engañar a usuarios y capturar credenciales.
4. **Mal configurado:** AP legítimo pero con contraseñas débiles, firmware desactualizado o cifrado inseguro (ej. WEP).

### 📌 Cómo los atacantes explotan los puntos de acceso no autorizados

- **Captura de tráfico**: recolectan datos no cifrados (usuarios, contraseñas, cookies de sesión).
- **Phishing inalámbrico**: redirigen a portales falsos para robar credenciales.
- **Acceso interno**: si el AP está conectado a la red corporativa, permite entrada a recursos internos.
- **Ataques MitM (Man-in-the-Middle):** interceptan y modifican comunicaciones.
- **Pivotaje**: usan el AP para entrar a la red y luego moverse lateralmente hacia servidores críticos.

### 📌 Ejemplos reales de ataques con AP no autorizados

1. **Aeropuertos y cafeterías**: ciberdelincuentes crean Wi-Fi “gratis” con SSID parecido al oficial (“FreeAirportWiFi”) → capturan credenciales bancarias.
2. **Empresa financiera**: un empleado conecta un router en su oficina para conectar su celular → el router tenía WPS activado y un atacante lo explotó para entrar en la red.
3. **Ataque Evil Twin en conferencias**: AP con el mismo SSID de la Wi-Fi oficial del evento → robo masivo de credenciales de correo.

### 📌 Cómo detectar puntos de acceso no autorizados

- **Monitoreo inalámbrico (WIDS/WIPS):** detecta SSIDs, BSSIDs y APs no registrados.
- **Auditorías de red cableada:** revisar puertos de switch para APs desconocidos.
- **Inspección de DHCP:** ver si aparecen servidores DHCP extraños.
- **Análisis RF (site surveys):** identificar señales sospechosas y su ubicación física.
- **Logs centralizados (SIEM):** alertas ante dispositivos no reconocidos.

### 📌 Cómo mitigar y prevenir incidentes con AP no autorizados

✅ **Políticas claras:** prohibir que empleados conecten APs sin autorización.

✅ **Cerrar puertos de switch no usados.**

✅ **802.1X / NAC:** autenticar dispositivos antes de dar acceso a la red.

✅ **DHCP Snooping / ARP Inspection:** bloquear APs que actúen como servidores DHCP.

✅ **Segmentación:** separar redes corporativas, invitados y dispositivos IoT.

✅ **WPA2/WPA3-Enterprise:** usar certificados en lugar de contraseñas compartidas.

✅ **Inspecciones físicas y site surveys periódicos.**

### 📌 Herramientas y tecnologías recomendadas para detección y protección

- **WIDS/WIPS empresariales:** Cisco, Aruba, Ruckus, Fortinet.
- **Escaneo y análisis Wi-Fi:**

  - **Kismet** (detección pasiva de APs).
  - **Ekahau / AirMagnet** (site surveys profesionales).

- **Monitoreo de red:**

  - **NAC (Cisco ISE, Aruba ClearPass).**
  - **SIEM (Splunk, ELK, QRadar)** para correlacionar eventos.

- **Controles de switch (Cisco IOS, Juniper, HP Aruba):** port-security, DHCP snooping, Dynamic ARP Inspection.

### 📌 Conclusión

Los **puntos de acceso no autorizados** representan una amenaza silenciosa pero muy peligrosa:

- Pueden abrir puertas traseras a la red interna.
- Permiten ataques de espionaje, phishing y pivotaje.
- Son difíciles de detectar sin herramientas específicas.

La **defensa en capas** es clave: políticas claras, monitoreo inalámbrico (WIDS/WIPS), controles en la red cableada (802.1X, port-security, DHCP snooping), segmentación y auditorías periódicas.

### 📌 Preguntas frecuentes (FAQ)

**🔹 ¿Un router doméstico conectado por un empleado cuenta como Rogue AP?**

Sí. Aunque no sea malicioso, introduce vulnerabilidades graves.

**🔹 ¿Cuál es la diferencia entre Rogue AP y Evil Twin?**

- _Rogue AP_: conectado dentro de tu red (por empleado o atacante).
- _Evil Twin_: imita tu red legítima para engañar usuarios desde fuera.

**🔹 ¿Cómo sé si tengo un Rogue AP en la red?**

Con WIDS/WIPS, auditorías de switch y site surveys RF.

**🔹 ¿El WPA3 evita los Rogue AP?**

No directamente. WPA3 protege la comunicación, pero si el AP es no autorizado dentro de la red, sigue siendo un riesgo.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 11**                   | **Siguiente 13**                 |
| ------------------ | ------------------------------ | -------------------------------- |
| [🏠](../README.md) | [⏪](./10_11_Replay_Attack.md) | [⏩](./10_13_Buffer_Overflow.md) |
