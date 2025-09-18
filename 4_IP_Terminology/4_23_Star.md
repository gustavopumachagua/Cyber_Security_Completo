| **Inicio**         | **atrás 22**         | **Siguiente 24**     |
| ------------------ | -------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./4_22_IPAM.md) | [⏩](./4_24_Ring.md) |

---

## **Índice**

| Temario                                                  |
| -------------------------------------------------------- |
| [124. Topología en estrella](#124-topología-en-estrella) |

# **Star**

## **124. Topología en estrella**

![Star](/img/4_IP_Terminology/start.jpg "Star")

### 🔹 1. ¿Qué es la **topología en estrella**?

Es una forma de organizar una red en la que **todos los dispositivos (PC, impresoras, laptops, etc.) se conectan a un dispositivo central**, que normalmente es un **switch** o un **hub** (hoy en día más switch).

📌 Ejemplo visual:

```
          PC1
           |
           |
PC2 ---- [SWITCH] ---- PC3
           |
         Servidor
```

- El **switch** es el centro de la estrella.
- Todos los dispositivos pasan por él para comunicarse entre sí o salir a internet.

### 🔹 2. Ventajas de la topología en estrella

✅ **Fácil de administrar**

- Si una computadora se desconecta o falla, el resto de la red sigue funcionando.

  Ejemplo: si tu laptop se apaga, los demás equipos de la oficina siguen con internet.

✅ **Escalabilidad**

- Es fácil **agregar más dispositivos**: solo conectas otro cable al switch.

✅ **Diagnóstico de fallas**

- Es más sencillo detectar dónde está el problema porque cada dispositivo tiene su propio cable.

✅ **Rendimiento mejorado**

- Al usar un switch, cada conexión es independiente y no hay tantas colisiones de datos como en topologías antiguas (ejemplo: bus).

### 🔹 3. Desventajas de la topología en estrella

❌ **Dependencia del dispositivo central**

- Si el switch (o hub) falla, **toda la red deja de funcionar**.

  Ejemplo: si en una oficina se quema el switch, nadie puede conectarse.

❌ **Costo de cableado**

- Requiere más cables porque cada dispositivo necesita su propio cable hacia el switch.

❌ **Costo del dispositivo central**

- El switch debe ser de buena calidad, especialmente en redes grandes → mayor inversión inicial.

❌ **Complejidad en redes grandes**

- Si tienes cientos de dispositivos, el switch central se puede saturar.

✅ **Resumen rápido para ti:**

- **Topología en estrella** = todos los equipos conectados a un switch.
- **Ventajas** = fácil de manejar, falla aislada, escalable.
- **Desventajas** = dependencia total del switch, más cables, costo más alto.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 22**         | **Siguiente 24**     |
| ------------------ | -------------------- | -------------------- |
| [🏠](../README.md) | [⏪](./4_22_IPAM.md) | [⏩](./4_24_Ring.md) |
