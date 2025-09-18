| **Inicio**         | **atrás 13**                     | **Siguiente 15**               |
| ------------------ | -------------------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./10_13_Buffer_Overflow.md) | [⏩](./10_15_Pass_the_Hash.md) |

---

## **Índice**

| Temario                                                                  |
| ------------------------------------------------------------------------ |
| [326. ¿Qué son las fugas de memoria?](#326-qué-son-las-fugas-de-memoria) |

# **Memory Leak**

## **326. ¿Qué son las fugas de memoria?**

![Memory Leak](/img/10_Common_Attacks/MemoryLeak.jpg "Memory Leak")

### 1. 📌 ¿Qué es una pérdida de memoria?

Una **fuga de memoria** ocurre cuando un programa **reserva memoria** (heap, stack dinámico u otros recursos) pero **no la libera** correctamente después de usarla.

- Con el tiempo, la memoria ocupada queda **inutilizada** pero no disponible para el sistema.
- Si la aplicación sigue ejecutándose, consume cada vez más memoria hasta que el sistema se **ralentiza o falla**.

👉 Analogía: imagina que alquilas una serie de casilleros en un gimnasio para guardar tus cosas. Cada vez que vas ocupas un nuevo casillero, pero nunca devuelves la llave. Con el tiempo, ocupas todos los casilleros aunque ya no los uses.

### 2. 📌 Conceptos básicos de fugas de memoria

- **Reserva de memoria (allocation):** el programa pide memoria (ej. `malloc`, `new`, etc.).
- **Liberación (deallocation):** el programa devuelve la memoria (ej. `free`, `delete`).
- **Fuga:** ocurre cuando se hace lo primero pero no lo segundo.

Ejemplo en **C** (vulnerable):

```c
#include <stdlib.h>

int main() {
    int *p = malloc(100 * sizeof(int)); // reservar memoria para 100 enteros
    // uso de p...
    // falta free(p); -> fuga de memoria
    return 0;
}
```

Ejemplo **correcto**:

```c
int *p = malloc(100 * sizeof(int));
// uso de p...
free(p); // liberamos la memoria
```

### 3. 📌 Fugas de memoria en acción

#### Ejemplo en un bucle infinito (C):

```c
while (1) {
    char *buf = malloc(1024); // reserva 1 KB
    // hacemos algo...
    // pero nunca se libera
}
```

🔴 Este programa sigue ocupando memoria hasta que el sistema se queda sin RAM.

#### Ejemplo en Python:

Aunque Python tiene **recolector de basura (GC)**, también pueden darse fugas si hay **referencias circulares** o **objetos que nunca se liberan**.

```python
class Nodo:
    def __init__(self):
        self.ref = None

a = Nodo()
b = Nodo()

a.ref = b
b.ref = a   # referencia circular

# Aunque no usemos más a ni b, la memoria no se libera inmediatamente
```

### 4. 📌 Fugas de memoria bajo el capó

Cuando el sistema operativo asigna memoria:

- La aplicación recibe un **puntero** a un bloque del **heap**.
- Si nunca se libera:

  - El **heap se fragmenta**.
  - La memoria se **reserva permanentemente** mientras la app esté activa.

- El sistema puede volverse lento o colapsar por falta de memoria.

Ejemplo de efectos:

- Aplicaciones que **se congelan** tras horas/días.
- Servidores que **se caen** bajo alta carga.

### 5. 📌 Mitigación de fugas de memoria

#### 🔹 Buenas prácticas de programación

- **Liberar memoria siempre** después de usarla.
- Usar funciones seguras: `free` (C), `delete` (C++).
- Evitar referencias circulares en lenguajes con GC (Python, Java).
- Usar _smart pointers_ en C++ (`std::unique_ptr`, `std::shared_ptr`).

#### 🔹 Herramientas de detección

- **C/C++:**

  - [Valgrind](https://valgrind.org/) → detecta fugas y accesos indebidos.
  - `AddressSanitizer` (`-fsanitize=address`).

- **Java:**

  - VisualVM, Eclipse MAT (Memory Analyzer Tool).

- **Python:**

  - `objgraph` para detectar objetos no liberados.
  - `gc` (garbage collector module).

#### 🔹 Estrategias arquitectónicas

- Reiniciar servicios periódicamente si son muy críticos.
- Monitorizar consumo de memoria con métricas (Prometheus, Grafana, CloudWatch).
- Uso de lenguajes con gestión automática de memoria cuando sea viable.

### ✅ Resumen

- Una **fuga de memoria** ocurre cuando un programa reserva memoria pero nunca la libera.
- Puede llevar a **ralentización**, **caídas del sistema** o **consumo excesivo de recursos**.
- Lenguajes como **C/C++** son más vulnerables (requieren gestión manual).
- Lenguajes con **recolector de basura** (Java, Python, C#) también pueden tener fugas por referencias circulares.
- **Prevención:** buenas prácticas de programación, uso de smart pointers, análisis de memoria y herramientas como Valgrind.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 13**                     | **Siguiente 15**               |
| ------------------ | -------------------------------- | ------------------------------ |
| [🏠](../README.md) | [⏪](./10_13_Buffer_Overflow.md) | [⏩](./10_15_Pass_the_Hash.md) |
