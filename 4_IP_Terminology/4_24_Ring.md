| **Inicio**         | **atrás 23**         | **Siguiente 25**     |
| ------------------ | -------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./4_23_Star.md) | [⏩](./4_25_Mesh.md) |

---

## **Índice**

| Temario                                                                    |
| -------------------------------------------------------------------------- |
| [125. ¿Qué es la topología de anillo?](#125-qué-es-la-topología-de-anillo) |
| [126. Topologías de red - Anillo](#126-topologías-de-red---anillo)         |

# **Ring**

## **125. ¿Qué es la topología de anillo?**

![Ring](/img/4_IP_Terminology/Ring.jpg "Ring")

### 🔹 1. ¿Qué es la topología de anillo?

Es un tipo de red en la que **cada dispositivo está conectado al siguiente en forma de círculo cerrado**.

- Los datos viajan en un único sentido (unidireccional) o en ambos sentidos (bidireccional, en los anillos modernos).

📌 Ejemplo visual:

```
PC1 ─── PC2 ─── PC3 ─── PC4
  └──────────────────────┘
```

### 🔹 2. ¿Cómo funciona la topología de anillo?

- Cada dispositivo actúa como un **repetidor**: recibe los datos y los reenvía al siguiente.
- La información da vueltas en el anillo hasta llegar al destinatario correcto.
- Se suele usar el **paso de token** para evitar colisiones.

### 🔹 3. Ventajas de la topología de anillo

✅ Menos colisiones que en la topología bus (gracias al token).

✅ Rendimiento predecible porque el acceso está regulado.

✅ Adecuada para redes con tráfico constante.

### 🔹 4. Ejemplos de dispositivos que usan topología de anillo

- **Token Ring** de IBM (muy usado en los 80s y 90s).
- **FDDI (Fiber Distributed Data Interface)**: redes de fibra óptica en anillo.
- Algunas **redes de transporte metropolitanas (MAN)** usan anillos de fibra.

### 🔹 5. Pasos en la transmisión de datos

1. Un dispositivo espera su turno (cuando tiene el **token**).
2. Inserta los datos en el anillo.
3. Cada dispositivo repite la señal y revisa si el mensaje es para él.
4. Cuando llega al destino, este copia los datos y libera el token.

### 🔹 6. ¿Qué es el **paso de token**?

- El **token** es como un "boleto" que da permiso para enviar datos.
- Solo el dispositivo con el token puede transmitir.
- Cuando termina, pasa el token al siguiente.

  👉 Esto evita que dos computadoras transmitan al mismo tiempo (no hay colisiones).

### 🔹 7. ¿Qué pasa si falla un dispositivo?

- En un **anillo clásico**, si un dispositivo o un cable falla, **se rompe toda la red**.
- Para solucionarlo, se desarrollaron **anillos duales** (bidireccionales), que redirigen el tráfico por el otro lado.

### 🔹 8. Diferencias con otras topologías

- **Anillo vs. Bus**: en bus, todos los dispositivos comparten un solo cable central; en anillo, los datos circulan en cadena.
- **Anillo vs. Estrella**: en estrella hay un dispositivo central (switch), en anillo no hay un centro.

### 🔹 9. Alternativas al anillo

- **Estrella** (la más usada hoy en día).
- **Malla** (usada en telecomunicaciones y Wi-Fi Mesh).
- **Árbol** (combinación de varias estrellas).

### 🔹 10. ¿Se usa en redes modernas?

No mucho en LANs domésticas o de oficina.

👉 Pero sí se usa en **telecomunicaciones y proveedores de internet**, con tecnologías como **SDH, SONET y FDDI**, porque permiten redundancia.

### 🔹 11. ¿Se puede ampliar una red en anillo?

Sí, se puede agregar más nodos, pero **cada nuevo nodo aumenta la latencia** y hace más frágil la red si no es bidireccional.

### 🔹 12. ¿Cómo fluyen los datos?

- **Unidireccional**: siempre en una sola dirección (más simple pero más frágil).
- **Bidireccional**: pueden ir en ambos sentidos (más confiable).

### 🔹 13. Aplicaciones comunes

- Redes metropolitanas (MAN).
- Sistemas de fibra óptica.
- Algunas redes industriales que necesitan tráfico ordenado.

✅ **Resumen rápido para ti**:

La **topología en anillo** conecta dispositivos en círculo. Usa **paso de token** para evitar colisiones. Fue muy popular con **Token Ring** de IBM, hoy en día está en desuso en LANs, pero todavía se usa en telecomunicaciones y redes de fibra.

---

[🔼](#índice)

---

## **126. Topologías de red - Anillo**

### 🔹 1. ¿Qué es la topología de anillo?

La **topología en anillo** es un tipo de red donde **cada dispositivo está conectado con el siguiente y el último se conecta con el primero**, formando un **círculo cerrado**.

- Los datos viajan en el anillo pasando por cada dispositivo hasta llegar al destino.
- No hay un "centro" como en estrella, sino que todos forman parte del mismo circuito.

📌 **Ejemplo visual:**

```
PC1 ─── PC2 ─── PC3 ─── PC4
  └──────────────────────┘
```

Aquí, el mensaje que envía **PC1 a PC3** pasa primero por **PC2** y luego llega a **PC3**.

### 🔹 2. ¿Cómo funciona?

Los dispositivos en un anillo actúan como **repetidores**:

1. Un equipo quiere enviar un dato.
2. Ese dato viaja en un sentido (horario o antihorario).
3. Cada dispositivo revisa si el dato es para él.

   - Si no lo es → lo reenvía.
   - Si lo es → lo copia y lo procesa.

👉 Para evitar que varios hablen al mismo tiempo, se utiliza el **paso de token**.

### 🔹 3. El **paso de token**

El **token** es como un “boleto de permiso” para transmitir datos.

- Solo el dispositivo con el token puede enviar.
- Cuando termina, libera el token para que otro lo use.

📌 Ejemplo:

- PC1 recibe el token → envía datos a PC3.
- PC3 recibe el mensaje → PC1 suelta el token.
- Ahora PC2 puede tomar el token y enviar lo suyo.

Esto **elimina colisiones** (problema común en topologías de bus).

### 🔹 4. Ventajas del anillo

✅ Ordenado: solo uno transmite a la vez.

✅ Rendimiento estable en redes con mucho tráfico.

✅ Cada dispositivo ayuda a mantener la señal (función de repetidor).

### 🔹 5. Desventajas

❌ Si un dispositivo o cable falla, toda la red puede caer (en anillos unidireccionales).

❌ Dificultad para agregar o quitar equipos sin interrumpir.

❌ Menos flexible que estrella (que hoy es la más común).

### 🔹 6. Tipos de anillo

- **Unidireccional**: los datos van en un solo sentido → más simple, pero frágil.
- **Bidireccional (Doble anillo)**: los datos pueden ir en ambos sentidos → más tolerante a fallos.

📌 Ejemplo:

En telecomunicaciones, si un corte de fibra ocurre en un lado del anillo, el tráfico se redirige por el otro.

### 🔹 7. Ejemplos de uso

- **Token Ring de IBM (años 80-90):** usaba esta topología en oficinas.
- **FDDI (Fiber Distributed Data Interface):** redes de fibra en universidades y empresas.
- **Redes Metropolitanas (MAN):** proveedores de Internet usan anillos de fibra para asegurar redundancia.
- **Industria:** algunas fábricas usan anillos para controlar máquinas con comunicación constante.

### 🔹 8. Comparación con otras topologías

- **Anillo vs. Bus**:

  - Bus: todos en un solo cable, más barato pero más colisiones.
  - Anillo: tráfico más ordenado con token.

- **Anillo vs. Estrella**:

  - Estrella: un dispositivo central (switch), más fácil de escalar.
  - Anillo: no tiene un centro, cada nodo depende de los demás.

### 🔹 9. Aplicaciones modernas

Aunque en redes de oficina (LAN) ya no se usa, sigue viva en:

- **Telecomunicaciones (anillos de fibra óptica)** para asegurar que Internet llegue aunque haya cortes.
- **Redes eléctricas inteligentes (Smart Grid)** para comunicación redundante.
- **Transporte ferroviario y fábricas** para control de sistemas críticos.

### 🔹 10. Resumen rápido

- La **topología de anillo** conecta dispositivos en círculo.
- Usa el **token** para transmitir datos sin colisiones.
- **Ventajas**: orden, tráfico controlado, redundancia (en doble anillo).
- **Desventajas**: sensible a fallos y difícil de ampliar.
- Hoy en día se usa más en **infraestructuras grandes (telecom, fibra, industria)** que en oficinas o casas.

📌 Ejemplo real:

Cuando tu operador de Internet conecta varias ciudades con **fibra óptica en forma de anillo**, si un cable se corta en una ciudad, los datos viajan por el otro lado y **nadie se queda sin Internet**.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 23**         | **Siguiente 25**     |
| ------------------ | -------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./4_23_Star.md) | [⏩](./4_25_Mesh.md) |
