| **Inicio**         | **atrás 24**         | **Siguiente 26**    |
| ------------------ | -------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./4_24_Ring.md) | [⏩](./4_26_Bus.md) |

---

## **Índice**

| Temario                                                                   |
| ------------------------------------------------------------------------- |
| [127. ¿Qué es la topología de malla?](#125-qué-es-la-topología-de-anillo) |
| [128. Topología de malla](#126-topologías-de-red---anillo)                |

# **Mesh**

## **127. ¿Qué es la topología de malla?**

![Mesh](/img/4_IP_Terminology/Mesh.webp "Mesh")

### 🔹 1. ¿Qué es la topología de malla?

La **topología de malla** es aquella en la que **cada dispositivo de la red está conectado a varios otros dispositivos**, creando múltiples rutas para que los datos viajen.

👉 Es como una ciudad con muchas calles y avenidas que conectan los mismos puntos: si una ruta se bloquea, puedes ir por otra.

📌 Ejemplo visual (malla parcial):

```
PC1 ─── PC2
 │    ╱ │ ╲
 │   ╱  │  ╲
PC3 ─── PC4 ─── PC5
```

### 🔹 2. Ventajas de utilizar una topología de malla

✅ **Alta tolerancia a fallos:** si un enlace se cae, los datos encuentran otra ruta.

✅ **Rutas múltiples:** mejora la velocidad y confiabilidad.

✅ **Escalabilidad:** se pueden añadir más nodos sin que toda la red caiga.

✅ **Buen rendimiento en redes inalámbricas dinámicas (como IoT o ciudades inteligentes).**

### 🔹 3. ¿Cómo viajan los datos en una red en malla?

Los datos **pueden viajar por múltiples rutas**. Los nodos usan algoritmos de enrutamiento para elegir la mejor opción:

1. El nodo A quiere enviar datos al nodo D.
2. Puede enviarlo directo (A → D) o por rutas alternativas (A → B → D o A → C → D).
3. Si un camino falla, el mensaje se redirige automáticamente por otro.

👉 Esto se llama **autorrecuperación**.

### 🔹 4. Malla completa vs. Malla parcial

- **Malla completa:** todos los dispositivos están conectados directamente entre sí.

  📌 Ejemplo: 5 dispositivos → 10 enlaces (crece exponencialmente).

  - Muy robusta, pero costosa y compleja.

- **Malla parcial:** cada dispositivo está conectado a varios, pero no a todos.

  📌 Ejemplo: Wi-Fi en malla en casa → el router principal se conecta con 2 repetidores, y estos con otros.

  - Más económica y común en la práctica.

### 🔹 5. Redes que utilizan topología de malla

- **Redes inalámbricas Wi-Fi Mesh (hogar y oficina).**
- **Redes de sensores IoT** (casas inteligentes, agricultura de precisión).
- **Redes militares** (resilientes a fallos).
- **Telecomunicaciones**: fibra óptica en anillo + malla para redundancia.
- **Vehículos autónomos** (V2V: comunicación entre autos).

### 🔹 6. Protocolos de enrutamiento para redes en malla

Sí ✅, existen protocolos diseñados para elegir rutas dinámicas:

- **OLSR (Optimized Link State Routing).**
- **AODV (Ad hoc On-Demand Distance Vector).**
- **B.A.T.M.A.N. (Better Approach To Mobile Adhoc Networking).**
- **802.11s (Wi-Fi mesh estándar).**

### 🔹 7. Desafíos de gestionar una red de malla a gran escala

⚠️ **Complejidad**: muchas rutas y enlaces hacen difícil la administración.

⚠️ **Costos altos** si se usa cableado para una malla completa.

⚠️ **Latencia**: demasiados saltos pueden retrasar la comunicación.

⚠️ **Seguridad**: más puntos de entrada implican más riesgos.

### 🔹 8. ¿Se pueden combinar topologías de red?

Sí ✅.

- Una empresa puede tener un **núcleo en malla** (conexiones redundantes entre switches principales) y **redes en estrella** hacia los usuarios.
- Ejemplo: **Cisco en campus universitarios** → backbone en malla + LAN en estrella.

### 🔹 9. Tipos de topologías de malla

1. **Malla completa** (todos con todos).
2. **Malla parcial** (solo algunos nodos interconectados).
3. **Malla inalámbrica** (Wi-Fi Mesh, IoT, redes urbanas).
4. **Malla cableada** (más común en centros de datos y telecomunicaciones).

### 🔹 10. Consideraciones al diseñar una red en malla

- Número de nodos: ¿es viable conectarlos todos?
- Costos de cableado o dispositivos.
- Protocolos de enrutamiento dinámico.
- Seguridad (cifrado WPA3, VPN, firewalls).
- Balanceo de carga (evitar que un nodo se sature).

### 🔹 11. Congestión en la red en malla

Las redes en malla suelen manejar la congestión con:

- **Enrutamiento dinámico** → eligen la ruta menos cargada.
- **Balanceo de carga** → distribuyen tráfico entre nodos.
- **Saltos múltiples** → si un nodo está saturado, se evita.

### 🔹 12. Seguridad en una red de malla inalámbrica

Sí, es posible protegerla:

🔒 **Cifrado fuerte** (WPA3, IPSec).

🔒 **Autenticación de dispositivos** (solo nodos autorizados entran).

🔒 **VPNs** para proteger datos en tránsito.

🔒 **Segmentación de red** para separar tráfico sensible.

### 📌 Ejemplo real

- En tu **casa**, si tienes Wi-Fi Mesh:

  - Router principal en la sala.
  - Repetidor en el pasillo.
  - Otro en la habitación.
  - Si uno falla, tu celular se conecta al más cercano y la red sigue funcionando.

- En una **ciudad**, los semáforos inteligentes pueden comunicarse en malla.

  - Si una calle pierde señal, los datos viajan por otro semáforo cercano hasta llegar al centro de control.

### ✅ Resumen rápido

- La **topología en malla** conecta dispositivos con múltiples rutas.
- Puede ser **completa** (todos con todos) o **parcial**.
- Se usa en **Wi-Fi Mesh, IoT, telecomunicaciones y entornos críticos**.
- Ventajas: alta tolerancia a fallos, flexibilidad, resiliencia.
- Desafíos: costos, complejidad y seguridad.

---

[🔼](#índice)

---

## **128. Topología de malla**

### 🔹 1. ¿Qué es la topología de malla?

La **topología de malla** es una forma de organizar una red en la que **cada dispositivo (nodo) se conecta a varios otros dispositivos**, permitiendo múltiples caminos para que los datos viajen.

👉 Imagina una ciudad con muchas calles y avenidas conectadas entre sí: si una se bloquea, siempre puedes usar otra para llegar a tu destino.

### 🔹 2. Tipos de topología de malla

1. **Malla completa**

   - Todos los dispositivos están conectados entre sí directamente.
   - Muy confiable, pero muy costosa (porque requiere muchos cables o enlaces).
   - 📌 Ejemplo: una red militar crítica donde la comunicación no puede fallar.

2. **Malla parcial**

   - Los dispositivos se conectan a varios otros, pero no a todos.
   - Menos costosa y más común en la práctica.
   - 📌 Ejemplo: una red Wi-Fi Mesh en el hogar (router principal conectado a repetidores).

### 🔹 3. ¿Cómo funciona la topología de malla?

Los datos viajan de un nodo a otro **siguiendo la mejor ruta disponible**.

- Si la ruta principal falla, los datos se redirigen automáticamente por otro camino.
- Esto se llama **autorrecuperación**.

📌 Ejemplo:

- En una red de 4 nodos (A, B, C, D), si A quiere enviar datos a D:

  - Puede ir directo (A → D).
  - O usar otra ruta (A → B → D).
  - Si B falla, puede ir por (A → C → D).

### 🔹 4. Ventajas de la topología de malla

✅ Alta **tolerancia a fallos**: si un enlace cae, la red sigue funcionando.

✅ **Rendimiento confiable**: múltiples rutas mejoran la velocidad y la redundancia.

✅ **Escalabilidad**: se pueden agregar nodos sin afectar a toda la red.

✅ Ideal para **redes inalámbricas dinámicas** como IoT o ciudades inteligentes.

### 🔹 5. Desventajas de la topología de malla

⚠️ **Costosa**: en malla completa, los cables/enlaces aumentan de forma exponencial.

⚠️ **Complejidad de gestión**: mantener muchas rutas puede ser complicado.

⚠️ **Latencia**: demasiados saltos pueden ralentizar los datos en redes grandes.

### 🔹 6. Ejemplos reales de uso

1. **Hogar / Oficina**:

   - Sistemas **Wi-Fi Mesh** (como Google Nest, TP-Link Deco).
   - Permiten que tu celular cambie de un repetidor a otro sin perder conexión.

2. **Ciudades inteligentes**:

   - Los semáforos o sensores de tráfico se comunican entre sí en malla.
   - Si un nodo falla, los datos van por otro camino.

3. **Telecomunicaciones**:

   - Las compañías de Internet usan mallas de fibra óptica para asegurar que, si un cable principal se corta, la red siga operando.

4. **Redes militares**:

   - Usan mallas porque la redundancia es crítica en campo de batalla.

### 🔹 7. Protocolos de enrutamiento para malla

Existen protocolos especiales que ayudan a que los datos encuentren el mejor camino:

- **OLSR (Optimized Link State Routing).**
- **AODV (Ad hoc On-Demand Distance Vector).**
- **B.A.T.M.A.N. (Better Approach To Mobile Adhoc Networking).**
- **802.11s** (estándar Wi-Fi Mesh).

### 🔹 8. Seguridad en redes de malla

- 🔒 **Cifrado WPA3** en redes Wi-Fi Mesh.
- 🔒 **VPNs** para asegurar la transmisión de datos.
- 🔒 **Control de acceso** para que solo nodos autorizados participen.

📌 Ejemplo: en un **Wi-Fi Mesh doméstico**, puedes configurar que solo tus dispositivos (PC, celular, Smart TV) puedan conectarse.

### 📌 Ejemplo ilustrativo

Imagina que tienes una **casa grande**:

- Router principal en la sala.
- Repetidor 1 en el pasillo.
- Repetidor 2 en la habitación.

Tu celular se conecta al repetidor más cercano. Si el **Repetidor 1 falla**, el celular no se queda sin Internet: se conecta al **Repetidor 2**, porque todos los nodos están interconectados en una **malla parcial**.

### ✅ Resumen rápido

- La **topología de malla** conecta dispositivos con múltiples rutas.
- Puede ser **completa** (todos conectados entre sí) o **parcial** (solo algunos).
- Se usa en **Wi-Fi Mesh, IoT, telecomunicaciones y redes críticas**.
- Ventajas: confiabilidad, tolerancia a fallos, redundancia.
- Desventajas: costo y complejidad.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 24**         | **Siguiente 26**    |
| ------------------ | -------------------- | ------------------- |
| [🏠](../README.md) | [⏪](./4_24_Ring.md) | [⏩](./4_26_Bus.md) |
