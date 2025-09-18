| **Inicio**         | **atrás 7**                              | **Siguiente 9**                                                |
| ------------------ | ---------------------------------------- | -------------------------------------------------------------- |
| [🏠](../README.md) | [⏪](./10_7_What_is_Evil_Twin_attack.md) | [⏩](./10_9_DNS_Poisoning_DNS_Spoofing_DNS_Cache_Poisoning.md) |

---

## **Índice**

| Temario                                                        |
| -------------------------------------------------------------- |
| [313. ¿Qué es el salto de VLAN?](#313-qué-es-el-salto-de-vlan) |
| [314. Salto de VLAN](#314-salto-de-vlan)                       |
|                                                                |

# **Salto de VLAN**

## **313. ¿Qué es el salto de VLAN?**

![VLAN Hopping](/img/10_Common_Attacks/Salto_de_VLAN.png "VLAN Hopping")

### 🔹 Introducción a la segmentación de red

La **segmentación de red** es una técnica para dividir una red física en múltiples redes lógicas más pequeñas llamadas **VLANs (Virtual Local Area Networks)**.

Beneficios:

- **Seguridad:** separa tráfico sensible (ej. VLAN de servidores vs VLAN de invitados).
- **Eficiencia:** reduce broadcast y congestión.
- **Gestión:** facilita control de acceso y políticas de red.

👉 Ejemplo:

- VLAN 10: Área de Finanzas
- VLAN 20: Recursos Humanos
- VLAN 30: Visitantes

  Cada VLAN está aislada y no puede comunicarse con otra sin pasar por un **router o firewall**, donde se aplican reglas de seguridad.

### 🔹 Segmentación lógica (más profunda)

No depende de conexiones físicas, sino de **configuración de switches**.

- **Puertos de acceso:** asignados a una VLAN fija (ej. el puerto 1 = VLAN 10).
- **Puertos de troncal (trunk):** transportan tráfico de múltiples VLANs entre switches usando **etiquetas VLAN (IEEE 802.1Q)**.

👉 En un **trunk**, cada trama lleva una etiqueta que indica a qué VLAN pertenece. Así, un solo cable puede transportar tráfico de muchas VLANs.

### 🔹 ¿Qué es el salto de VLAN (VLAN hopping)?

El **VLAN hopping** es un ataque donde un atacante logra enviar tráfico desde una VLAN a otra, **rompiendo el aislamiento lógico**.

Impacto:

- Acceso a segmentos restringidos.
- Interceptar tráfico de usuarios en otras VLANs.
- Posible pivotaje hacia servidores críticos.

### 🔹 Técnicas comunes de VLAN hopping

#### 1. Doble etiquetado (Double Tagging Attack)

El atacante envía una trama con **dos etiquetas VLAN**:

- La **primera etiqueta** (outer tag) es retirada por el primer switch (porque cree que el tráfico es local a esa VLAN).
- La **segunda etiqueta** (inner tag) queda y es usada por el siguiente switch en el troncal, haciendo que la trama termine en una VLAN diferente.

👉 Ejemplo narrativo:

- Atacante en VLAN 10 quiere llegar a VLAN 20.
- Envía una trama con etiquetas VLAN 10 y VLAN 20.
- El primer switch quita la etiqueta VLAN 10 (porque es el puerto de acceso).
- La trama llega al troncal aún con la etiqueta VLAN 20 → el switch la reenvía a la VLAN 20.

⚠️ Limitación: funciona mejor si el atacante está en la VLAN nativa del troncal.

#### 2. Suplantación de Switch (Switch Spoofing / Rogue Switch Attack)

El atacante conecta un dispositivo que **finge ser un switch legítimo**.

- Usa protocolos como **DTP (Dynamic Trunking Protocol)** para negociar un **enlace troncal** con el switch real.
- Una vez establecido el troncal, el atacante recibe tráfico de **todas las VLANs** permitidas en ese troncal.

👉 Ejemplo narrativo:

- Un atacante conecta un portátil con software que simula un switch.
- El switch de la empresa, con DTP activado, establece un troncal con el dispositivo.
- Ahora el atacante tiene acceso a múltiples VLANs (ej. Finanzas, Recursos Humanos, Servidores).

### 🔹 Defensa contra ataques de salto de VLAN

✅ **Contra doble etiquetado**

- No uses VLAN nativa en troncales (dejarla vacía o en VLAN dedicada).
- Etiquetar explícitamente todas las tramas en los puertos troncales.
- Usar VLANs distintas para usuarios y troncales.

✅ **Contra suplantación de switch (Rogue Switch)**

- Deshabilitar **DTP** en todos los puertos (`switchport mode access`).
- Configurar puertos manualmente como **access** o **trunk**, nunca “dynamic”.
- Limitar VLANs permitidas en cada troncal (`switchport trunk allowed vlan`).
- Usar **802.1X** y seguridad de puerto para controlar quién se conecta.

✅ **Buenas prácticas generales**

- Segmentar VLANs sensibles (servidores, administración).
- Aplicar ACLs o firewalls entre VLANs críticas.
- Monitorear redes en busca de anomalías (ej. múltiples etiquetas en tramas).
- Implementar IDS/IPS para detectar intentos de VLAN hopping.

### 🔹 Resumen

- **Segmentación de red**: divide la red en VLANs para seguridad y eficiencia.
- **Salto de VLAN (VLAN hopping)**: ataque que rompe el aislamiento de VLANs.
- **Doble etiquetado**: inserta 2 etiquetas VLAN para engañar al switch.
- **Suplantación de Switch**: un atacante finge ser un switch y crea un troncal para acceder a múltiples VLANs.
- **Defensa**: deshabilitar DTP, configurar VLAN nativa vacía, restringir VLANs en troncales, aplicar ACLs y seguridad en switches.

---

[🔼](#índice)

---

## **314. Salto de VLAN**

### 1) ¿Qué es el salto de VLAN?

El **salto de VLAN** (VLAN hopping) es una técnica por la cual un atacante que tiene acceso a un puerto en una VLAN consigue hacer llegar tráfico a otra VLAN que debería estar aislada. El objetivo es **romper el aislamiento lógico** entre VLANs para interceptar tráfico o acceder a recursos no autorizados.

Es una amenaza a la **segmentación** de red: si un atacante consigue saltar de la VLAN de usuarios a la VLAN de servidores o administración, el impacto puede ser crítico.

### 2) Conceptos de base (necesarios para entenderlo)

- **VLAN (802.1Q):** Red lógica. Las tramas que viajan por un _trunk_ llevan una etiqueta 802.1Q que indica la VLAN (VLAN ID).
- **Puerto de acceso:** puerto asignado a una sola VLAN; envía y recibe tramas **untagged** (sin etiqueta).
- **Puerto trunk:** transporta tramas de múltiples VLANs; las tramas normalmente están **tagged** (etiquetadas) con 802.1Q, excepto la _native VLAN_ que suele ir sin etiqueta en muchos equipos.
- **Native VLAN:** VLAN que, por convención en muchos switches, viaja sin etiqueta en el troncal. Esta característica es clave para el ataque de doble etiquetado.

### 3) Principales vectores de VLAN hopping (resumen)

1. **Doble etiquetado (Double Tagging)** — aprovecha la native VLAN y el stripping (remoción) de la primera etiqueta por el primer switch.
2. **Suplantación de switch / Switch spoofing / DTP attack** — el atacante negocia un trunk con un switch (p. ej. vía DTP) y así recibe tráfico de múltiples VLANs.

   (Otras técnicas históricas como CAM overflow no son exactamente VLAN hopping pero también permiten pivotar en switches antiguos; aquí nos centramos en los dos vectores más relevantes.)

### 4) Cómo funciona _conceptualmente_ el doble etiquetado (Double Tagging)

#### Idea básica (conceptual, no operacional)

- El atacante en una VLAN A (por ejemplo VLAN 10) envía una trama Ethernet que contiene **dos etiquetas 802.1Q**:

  - **Outer tag** (etiqueta externa): VLAN igual a la _native VLAN_ del troncal (por ejemplo VLAN 1).
  - **Inner tag** (etiqueta interna): VLAN objetivo a la que quiere llegar (por ejemplo VLAN 20).

- El primer switch (el que está conectado al atacante) recibe la trama en un **puerto de acceso** y, al enviar la trama al troncal, **no** re-ingresa la outer tag (porque la native VLAN no se etiqueta en la salida trunk). Al pasar por el troncal hacia el siguiente switch, la **primera etiqueta ha sido removida** y la siguiente etiqueta (la inner) queda visible para el siguiente switch del camino. Ese switch ve una trama con una etiqueta que corresponde a VLAN 20 y la entrega a esa VLAN → salto conseguido.

#### Representación simplificada de la trama

(orden conceptual de campos)

```
[Dst MAC][Src MAC][0x8100][TCI: VLAN=1] [0x8100][TCI: VLAN=20] [Ethertype][Payload]
```

- El primer switch _quita_ la etiqueta VLAN=1 al reenviar al trunk; así la etiqueta VLAN=20 "surge" en el troncal y el segundo switch la interpreta como tráfico de VLAN 20.

#### Limitaciones del ataque

- Requiere que la **native VLAN sea usada** en el troncal y que el primer switch permita pasar tramas que terminan llegando sin outer tag.
- Funciona mejor cuando el atacante está en la _VLAN nativa_ o en una VLAN cuya salida termine siendo tratada como native en el primer salto.
- Muchos equipos modernos y buenas configuraciones de red (ver mitigaciones abajo) bloquean o reducen la efectividad de este ataque: cambiar native VLAN a una VLAN no usada, evitar VLAN nativa sin etiqueta, control de puertos, etc.

> Importante: aquí hemos explicado el **concepto** para que puedas reconocer el patrón y defenderte. No se están dando instrucciones prácticas para atacarlo.

### 5) Cómo funciona _conceptualmente_ la suplantación de switch (Switch spoofing / DTP)

#### Idea básica

- Muchos switches Cisco (y compatibles) soportan **DTP (Dynamic Trunking Protocol)** u otros mecanismos para negociar automáticamente si un puerto debe ser _access_ o _trunk_.
- Un atacante conecta un dispositivo que **finge ser un switch** y participa en la negociación DTP. Si el switch de la red está configurado para negociar troncales, puede establecerse un trunk entre el switch legítimo y el dispositivo del atacante.
- Una vez establecido el trunk, el atacante tiene acceso a las VLANs permitidas en ese trunk (potencialmente muchas).

#### Por qué es peligroso

- Permite al atacante recibir/inyectar tráfico de múltiples VLANs sin necesidad de explotar etiquetas dobles.
- Es especialmente efectivo si los puertos del switch están en modo **dynamic desirable** o similar (es decir, aceptan negociaciones).

### 6) Ejemplo narrativo (cómo se ve en la práctica, a nivel conceptual)

- Topología: Switch A (core) — trunk — Switch B (edge). Host atacante en puerto del Switch B (acceso VLAN 10). VLAN 20 es la VLAN objetivo (servidores).
- **Doble etiquetado:** el atacante envía trama doble-tagged; Switch B saca outer tag y pasa inner tag por el trunk hacia Switch A; Switch A ve etiqueta VLAN 20 y entrega la trama a hosts en VLAN 20.
- **Switch spoofing:** el atacante consigue que el puerto entre Switch B y su dispositivo se convierta en trunk; ahora el atacante recibe tráfico de varias VLANs.

### 7) Cómo detectar intentos de VLAN hopping

(actividades defensivas y comandos útiles en entornos Cisco-like)

**Señales a monitorear**

- Tramas con tags inesperadas o múltiples etiquetas (detección a nivel de switch/IDS).
- Negociaciones DTP o mensajes de negociación en puertos que deberían ser _access_.
- Aparición repentina de una MAC desconocida en múltiples VLANs.
- Cambios inusuales en la tabla MAC (`show mac address-table`) o en STP (mensajes BPDU inusuales).
- Tráfico desde puertos que no deberían ver ciertas VLANs.

**Comandos útiles (lectura solamente, no ofensivos)**

- `show interfaces trunk` — lista los troncales y VLANs permitidas.
- `show vlan brief` — ver asignación de puertos a VLANs.
- `show mac address-table dynamic` — ver MACs y puertos.
- `show logging` / revisar syslog — buscar mensajes de negociación o errores.
- SNMP/NetFlow/IDS: configurar alertas para tramas doble-etiquetadas o negociaciones DTP en puertos de acceso.

### 8) Mitigaciones y configuraciones recomendadas (prácticas seguras)

Estas medidas deben aplicarse en switches de borde y núcleo. Prioriza la **reducción de la superficie** (no permitir negociación automática) y el **principio de menor privilegio**.

#### A. Evita la negociación automática de troncales (DTP)

- Configure puertos de usuario **siempre** como `access`:

```text
interface Gi0/1
  switchport mode access
  switchport access vlan 10
  switchport nonegotiate      ! evita DTP
```

- En el lado del switch que debe ser trunk, configúralo explícitamente:

```text
interface Gi0/24
  switchport trunk encapsulation dot1q
  switchport mode trunk
  switchport trunk allowed vlan 10,20,30
  switchport trunk native vlan 999   ! native VLAN no usada por hosts
```

#### B. No usar VLAN 1 / native VLAN estándar para usuarios

- Cambia la **native VLAN** en troncales a una VLAN **no usada por hosts** (por ejemplo VLAN 999) y asegúrate de que la misma configuración esté en ambos extremos del trunk:

```text
switchport trunk native vlan 999
```

- Considerar **etiquetar** todas las VLANs en el trunk si el equipo soporta esa opción (evitar tráfico untagged).

#### C. Restringir VLANs permitidas en troncales

- Permite solo las VLANs necesarias por cada trunk:

```text
switchport trunk allowed vlan 10,20,30
```

#### D. Seguridad de puerto (Port Security)

- Limita número de MACs por puerto, establece acción en violación:

```text
interface Gi0/1
  switchport mode access
  switchport port-security
  switchport port-security maximum 2
  switchport port-security violation restrict
  switchport port-security mac-address sticky
```

#### E. Protecciones adicionales

- **BPDU Guard / Root Guard**: protege contra dispositivos que envían BPDU inesperados.
- **UDLD (Unidirectional Link Detection)**: detecta enlaces unidireccionales.
- **DHCP Snooping** + **Dynamic ARP Inspection (DAI)** + **IP Source Guard**: previenen ataques de suplantación IP/MAC y ARP en la red de usuarios.
- **802.1X (dot1x)**: autentica dispositivos en el puerto antes de otorgar acceso; muy fuerte para entornos corporativos.
- **Private VLANs** para aislar hosts dentro de la misma VLAN en entornos donde convenga.

#### F. Segmentación y firewalling entre VLANs

- No confíes exclusivamente en VLANs para seguridad lógica — aplica **ACLs/VLAN ACLs** o firewalls entre segmentos sensibles (servidores, administración).

### 9) Procedimiento recomendado para endurecer puertos (resumen práctico)

1. Configura puertos de usuario: `switchport mode access`, `no switchport trunk`/`nonegotiate`.
2. Establece `switchport trunk native vlan` a VLAN no usada (coincidir en ambos extremos).
3. Limita VLANs permitidas en troncales.
4. Activa port-security, BPDU guard, DHCP snooping y DAI.
5. Implementa 802.1X donde sea posible.
6. Monitorea con IDS/SNMP/NetFlow y revisa logs regularmente.

### 10) Pruebas y auditoría (enfoque defensivo)

- **Entornos de laboratorio:** prueba mitigaciones en un lab controlado (switches de pruebas, VLANs, hosts de test).
- **Auditoría de configuración:** revisa automáticamente plantillas de switch para detectar puertos en `dynamic`/`auto` o `native VLAN` igual a VLAN de usuarios.
- **Monitoreo continuo:** alertas si se detectan negociaciones DTP en puertos que deben ser access.
- **Pen-tests autorizados:** contrata pruebas de red autorizadas para validar el hardening, pero solo en entornos con permiso.

### 11) Resumen (qué debes recordar)

- **VLAN hopping** permite romper aislamiento entre VLANs; las formas más comunes son **double tagging** y **switch spoofing (DTP)**.
- La raíz del riesgo es la **negociación automática de troncales** y la existencia de una **native VLAN** sin control.
- **Mitigaciones efectivas:** desactivar negociación (DTP), fijar puertos a access/trunk explícitamente, usar native VLAN no usada, limitar VLANs permitidas en troncales, activar port-security, DHCP snooping, DAI, 802.1X y segmentación adicional con ACLs/firewalls.
- **Detecta** negociaciones inesperadas y actividad de MAC anómala; **prueba** las defensas en un lab controlado.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 7**                              | **Siguiente 9**                                                |
| ------------------ | ---------------------------------------- | -------------------------------------------------------------- |
| [🏠](../README.md) | [⏪](./10_7_What_is_Evil_Twin_attack.md) | [⏩](./10_9_DNS_Poisoning_DNS_Spoofing_DNS_Cache_Poisoning.md) |
