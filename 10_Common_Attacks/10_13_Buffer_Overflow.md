| **Inicio**         | **atrás 12**                        | **Siguiente 14**             |
| ------------------ | ----------------------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./10_12_Rogue_Access_Point.md) | [⏩](./10_14_Memory_Leak.md) |

---

## **Índice**

| Temario                                                                                    |
| ------------------------------------------------------------------------------------------ |
| [323. ¿Qué es un desbordamiento de búfer?](#323-qué-es-un-desbordamiento-de-búfer)         |
| [324. Ataque de desbordamiento de búfer](#324-ataque-de-desbordamiento-de-búfer)           |
| [325. Desbordamientos de búfer simplificados](#325-desbordamientos-de-búfer-simplificados) |

# **Desbordamiento de búfer**

## **323. ¿Qué es un desbordamiento de búfer?**

![Desbordamiento de búfert](/img/10_Common_Attacks/Buffer_Overflow.jpg "Desbordamiento de búfer")

### 📌 ¿Qué es un desbordamiento de búfer?

Un **búfer** es una zona de memoria reservada para almacenar datos temporales (ej. un arreglo de caracteres para guardar un texto).

Un **desbordamiento de búfer (Buffer Overflow)** ocurre cuando un programa escribe más datos en ese búfer de los que tiene capacidad, sobrescribiendo memoria adyacente.

👉 Ejemplo simple en C:

```c
#include <stdio.h>
#include <string.h>

int main() {
    char buffer[8];
    strcpy(buffer, "HolaMundo"); // "HolaMundo" tiene 9 caracteres + '\0'
    printf("Buffer: %s\n", buffer);
    return 0;
}
```

Aquí, `buffer` tiene espacio para 8 bytes, pero copiamos 10 → ocurre un **desbordamiento**.

### 📌 ¿Qué es un ataque de desbordamiento de búfer?

Un atacante puede **aprovechar el desbordamiento** para:

- Sobrescribir **variables importantes**.
- Alterar el **flujo de ejecución del programa**.
- Inyectar y ejecutar **código malicioso** (ej. abrir una shell).

Esto convierte al desbordamiento de búfer en una técnica clásica de **ejecución remota de código (RCE)**.

### 📌 Explosiones de desbordamiento de búfer

El término “explosiones” hace referencia a cómo un desbordamiento puede “expandirse” y afectar distintas áreas de la memoria:

- Sobrescribir la **pila de ejecución (stack)**.
- Alterar la **tabla de funciones** o punteros.
- Corromper estructuras del **heap**.

Esto puede provocar desde un **crash** (fallo del programa) hasta una **toma total del sistema**.

### 📌 Consecuencias del desbordamiento del búfer

- **Caída de programas** (DoS).
- **Ejecución de código malicioso** (control del sistema).
- **Escalamiento de privilegios** (ej. pasar de usuario normal a administrador).
- **Pérdida de integridad** (modificación de datos críticos).

Ejemplo famoso: el gusano **Morris (1988)** explotó un buffer overflow para propagarse en sistemas UNIX.

### 📌 Tipos de ataques de desbordamiento de búfer

1. **Stack-based buffer overflow (en la pila):**

   El más común. El atacante sobrescribe el **return address** de una función para redirigir la ejecución a código malicioso.

2. **Heap-based buffer overflow (en el heap):**

   Se explotan estructuras dinámicas (ej. `malloc`). Puede corromper punteros o tablas de asignación de memoria.

3. **Integer overflow → buffer overflow:**

   Un cálculo mal manejado hace que se reserve menos memoria de la necesaria y luego ocurre el desbordamiento.

### 📌 ¿Qué lenguajes de programación son más vulnerables?

- **Vulnerables:** C y C++ → porque permiten manipulación directa de memoria sin comprobaciones de límites.
- **Menos vulnerables:** Java, Python, C#, Go → porque incluyen control de límites en arreglos y strings.

👉 Sin embargo, incluso en lenguajes de “alto nivel”, si llaman librerías en C, pueden heredar la vulnerabilidad.

### 📌 Cómo prevenir desbordamientos de búfer

1. **Buenas prácticas de programación:**

   - Validar siempre el tamaño de entradas (`strncpy` en lugar de `strcpy`).
   - Usar funciones seguras (`fgets` en lugar de `gets`).
   - Comprobar límites antes de copiar o concatenar.

2. **Protecciones a nivel de compilador y sistema:**

   - **Stack canaries:** valores especiales que detectan sobreescritura en la pila.
   - **ASLR (Address Space Layout Randomization):** aleatoriza direcciones de memoria para dificultar exploits.
   - **DEP / NX (No eXecute):** evita ejecutar código en regiones de datos (stack/heap).

3. **Lenguajes más seguros:** usar Java, Python o Rust cuando sea posible.

### 📌 Ejemplos de ataques de desbordamiento de búfer

#### Ejemplo clásico (C vulnerable)

```c
#include <stdio.h>
#include <string.h>

void vulnerable() {
    char buffer[8];
    printf("Escribe tu nombre: ");
    gets(buffer); // ❌ función insegura, no valida tamaño
    printf("Hola %s\n", buffer);
}

int main() {
    vulnerable();
    return 0;
}
```

Si el usuario escribe más de 8 caracteres, se sobrescriben zonas de la memoria → el atacante puede inyectar shellcode.

#### Caso real

- **Blaster Worm (2003):** explotaba un buffer overflow en RPC de Windows.
- **Heartbleed (2014):** bug en OpenSSL causado por falta de validación de límites, permitiendo fuga de datos sensibles.

### 📌 Preguntas frecuentes sobre desbordamiento de búfer

**🔹 ¿Cómo funciona un ataque de desbordamiento de búfer?**

Sobrescribe memoria adyacente al búfer, alterando datos, punteros o el flujo de ejecución.

**🔹 ¿Por qué el desbordamiento del búfer es una vulnerabilidad?**

Porque rompe la separación de memoria y permite a un atacante **inyectar código** o corromper procesos.

**🔹 ¿Qué es un desbordamiento de pila de búfer?**

Es el desbordamiento en la **stack** (pila de ejecución), normalmente usado para sobrescribir la dirección de retorno de funciones.

✅ **En resumen:**

El **desbordamiento de búfer** es una vulnerabilidad grave que ocurre al escribir más datos de los permitidos en memoria. Puede provocar desde fallos de software hasta ejecución remota de código. Los más vulnerables son programas en C/C++ sin validación de entradas. Su prevención depende tanto de **programación segura** como de **mecanismos modernos de defensa** (ASLR, DEP, stack canaries).

---

[🔼](#índice)

---

## **324. Ataque de desbordamiento de búfer**

### 📌 ¿Qué es el desbordamiento de búfer?

Un **búfer** es una porción de memoria reservada para almacenar datos temporales (ejemplo: un arreglo de caracteres).

El **desbordamiento de búfer (Buffer Overflow)** ocurre cuando se intenta escribir **más datos de los que el búfer puede contener**, sobrescribiendo áreas adyacentes de memoria.

👉 Ejemplo en C:

```c
char buffer[8];
strcpy(buffer, "HolaMundo"); // escribe 10 bytes en un espacio de 8
```

Esto sobrescribe memoria contigua y provoca comportamientos inesperados: bloqueos, corrupción de datos o ejecución de código malicioso.

### 📌 ¿Qué es un ataque de desbordamiento de búfer?

Un **ataque de desbordamiento de búfer** consiste en que un atacante **aprovecha la falta de validación de límites** para:

- Sobrescribir memoria crítica (direcciones de retorno, punteros).
- Alterar el flujo normal de ejecución de un programa.
- Inyectar **código malicioso** y lograr ejecución remota (RCE).

Ejemplo:

Un atacante envía una entrada más larga de lo esperado en un formulario vulnerable → la aplicación la copia en un búfer fijo sin validación → se sobrescribe la dirección de retorno de la función → cuando termina, en lugar de regresar al programa legítimo, salta al **código inyectado por el atacante**.

### 📌 Tipos de ataques de desbordamiento de búfer

1. **Stack-based Buffer Overflow (en la pila):**

   - Sobrescribe la pila de ejecución (stack).
   - Objetivo típico: modificar la **dirección de retorno**.

2. **Heap-based Buffer Overflow (en el heap):**

   - Ocurre en memoria dinámica (reservada con `malloc`/`new`).
   - Puede alterar estructuras de gestión de memoria y punteros.

3. **Integer Overflow → Buffer Overflow:**

   - Un cálculo mal validado provoca que se reserve menos memoria de la necesaria.
   - Ejemplo: pedir 256 caracteres, pero por overflow se asignan 0 bytes.

### 📌 ¿Qué lenguajes de programación son más vulnerables?

- **Altamente vulnerables:**

  - **C y C++** → permiten acceso directo a memoria, no validan automáticamente los límites de arrays/strings.

- **Menos vulnerables (tienen protecciones):**

  - **Java, C#, Python, Go, Rust** → realizan verificación de límites en arrays y strings, reduciendo el riesgo.

👉 Aun así, si llaman librerías nativas en C/C++, pueden heredar vulnerabilidades.

### 📌 Cómo prevenir desbordamientos de búfer

1. **Buenas prácticas de programación:**

   - Validar siempre la longitud de las entradas.
   - Usar funciones seguras (`strncpy`, `fgets` en lugar de `strcpy`, `gets`).
   - Aplicar **programación defensiva** y revisiones de código.

2. **Protecciones del compilador y del sistema:**

   - **Stack canaries:** valores de control en la pila que detectan sobrescrituras.
   - **ASLR (Address Space Layout Randomization):** aleatoriza direcciones de memoria para dificultar exploits.
   - **DEP / NX (No eXecute):** evita ejecutar código en áreas de memoria de datos.

3. **Lenguajes seguros:**

   - Usar lenguajes modernos con gestión automática de memoria (Python, Java, Rust).

4. **Auditorías y pruebas de seguridad:**

   - Revisiones de código estático.
   - Fuzzing (entradas aleatorias para descubrir fallos).
   - Pentesting especializado.

### 📌 Cómo **Imperva** ayuda a mitigar los ataques de desbordamiento de búfer

Imperva ofrece soluciones de **seguridad de aplicaciones y datos** que ayudan a reducir el riesgo de ataques basados en buffer overflow:

1. **WAF (Web Application Firewall):**

   - Detecta patrones de ataque en tiempo real (ej. payloads excesivos, entradas anómalas).
   - Bloquea tráfico sospechoso antes de que llegue a la aplicación vulnerable.

2. **Protección DDoS + Seguridad en tiempo real:**

   - Evita que un atacante explote un buffer overflow con tráfico masivo.

3. **Seguridad en bases de datos y API:**

   - Protege aplicaciones legacy escritas en C/C++ que podrían ser vulnerables.
   - Filtra solicitudes maliciosas.

4. **Visibilidad y monitoreo:**

   - Logs centralizados que ayudan a detectar intentos de explotación.
   - Integración con SIEMs para alertas tempranas.

👉 Aunque Imperva no corrige directamente el bug en el código, sí actúa como **barrera preventiva**, bloqueando intentos de explotación antes de que lleguen a la aplicación.

### ✅ Resumen

- **Desbordamiento de búfer:** error al escribir más datos de los permitidos en un bloque de memoria.
- **Ataque de buffer overflow:** el atacante explota este error para ejecutar código malicioso.
- **Tipos:** en pila (stack), en heap y por integer overflow.
- **Lenguajes más vulnerables:** C y C++.
- **Prevención:** validación de entradas, uso de funciones seguras, protecciones del compilador (ASLR, DEP, stack canaries).
- **Imperva:** ayuda a proteger aplicaciones frente a intentos de explotación con un **WAF inteligente, monitoreo y seguridad en tiempo real**.

---

[🔼](#índice)

---

## **325. Desbordamientos de búfer simplificados**

### 1) Idea básica — analogía rápida

Imagina un cajón (búfer) pensado para guardar 8 camisetas. Si metes 20 camisetas, las que no caben se amontonan fuera del cajón y pueden empujar o romper la cajonera contigua. En programación eso significa que los bytes “extras” sobrescriben otras variables o metadatos y el programa se comporta mal.

### 2) ¿Qué ocurre en memoria (visión simple)?

En muchos programas hay áreas contiguas en memoria: el búfer para los datos del usuario, variables, punteros y (en funciones) la **dirección de retorno**. Cuando escribes más de lo que el búfer admite, puedes sobrescribir esas zonas adyacentes.

Diagrama muy simplificado (pila de una función, direcciones crecientes hacia arriba):

```
direcciones altas
+---------------------+
| return address      |  <- donde volverá la función
+---------------------+
| saved frame pointer |
+---------------------+
| buffer[8]           |  <- aquí escribes (solo 8 bytes)
+---------------------+
direcciones bajas
```

Si metes >8 bytes en `buffer`, los bytes extra sobrescriben `saved frame pointer` y `return address` → el comportamiento puede variar desde crash hasta corrupción de datos.

### 3) Tipos simples (explicados en una línea)

- **Stack-based**: overflow en variables locales (pila).
- **Heap-based**: overflow en memoria dinámica (malloc/new).
- **Integer overflow → buffer overflow**: un cálculo erróneo hace que se reserve menos memoria de la necesaria.

### 4) Ejemplo simple en C — **vulnerable** (ilustrativo)

> **AVISO:** esto muestra por qué el código es inseguro. No lo uses en redes públicas.

```c
#include <stdio.h>

int main() {
    char buf[8];
    printf("Escribe algo: ");
    scanf("%s", buf);   // peligro: scanf("%s") no limita la longitud
    printf("Has escrito: %s\n", buf);
    return 0;
}
```

Si el usuario introduce una cadena de más de 7 caracteres (más 1 para `'\0'`), `buf` sobrescribirá memoria adyacente → crash o corrupción.

### 5) Ejemplo seguro en C — corregido

```c
#include <stdio.h>

int main() {
    char buf[8];
    printf("Escribe algo: ");
    if (fgets(buf, sizeof(buf), stdin)) {        // fgets limita la lectura
        buf[strcspn(buf, "\n")] = '\0';          // elimina el salto de línea
        printf("Has escrito: %s\n", buf);
    }
    return 0;
}
```

O usando `scanf("%7s", buf)` que especifica el máximo, aunque `fgets` suele ser preferible.

### 6) Alternativas en otros lenguajes (más seguras por defecto)

- **C++ (moderno):**

  ```cpp
  std::string s;
  std::getline(std::cin, s); // gestión automática de tamaño
  ```

- **Python:**

  ```py
  s = input("Escribe algo: ")  # strings dinámicos, sin overflow típico
  ```

Estos lenguajes gestionan la longitud y evitan el error clásico de C/C++.

### 7) ¿Por qué es grave? (consecuencias simples)

- **Crash** de la aplicación (DoS).
- **Corrupción de datos**: valores internos cambiados.
- **En casos extremos**: si hay otras fallas de seguridad, un atacante podría cambiar el flujo del programa. (Nota: explicar cómo explotarlo es operativo; aquí solo decimos el riesgo.)

### 8) Cómo protegerse — lista práctica y directa

1. **Validar y limitar entradas** (longitud máxima conocida).
2. **Usar funciones seguras**: `fgets`, `snprintf`, `strncpy` con cuidado.
3. **Lenguajes con verificación de límites** o bibliotecas seguras cuando sea posible.
4. **Compilador y OS protections**:

   - **Stack canaries** (`-fstack-protector` en gcc).
   - **ASLR** (aleatorización del layout de memoria).
   - **DEP / NX** (no ejecutar código desde regiones de datos).

5. **Herramientas de detección en desarrollo**:

   - **AddressSanitizer** (`-fsanitize=address`) para compilar y encontrar overflows en pruebas.
   - **Valgrind** para detectar accesos inválidos.

6. **Análisis estático y revisiones de código**, además de fuzzing en entornos autorizados para encontrar entradas peligrosas.

Ejemplo rápido de uso de AddressSanitizer:

```bash
gcc -g -fsanitize=address -o test test.c
./test
```

Esto ayuda a detectar accesos fuera de límites durante pruebas.

### 9) Ejemplo conceptual de heap overflow (muy simple)

Si tienes un bloque `malloc(16)` y copias 40 bytes sin comprobar, se puede sobrescribir metadatos del heap o datos de otros objetos. Resultado: corrupción difícil de depurar y, potencialmente, comportamiento inesperado de memoria dinámica.

### 10) Buenas prácticas para equipos

- Definir límites claros de input en la especificación.
- Escribir pruebas unitarias que incluyan entradas largas y bordes (off-by-one).
- Incluir sanitizers en el pipeline CI en la fase de pruebas (no en prod).
- Priorizar migración a bibliotecas/lenguajes más seguros cuando sea razonable.

### 11) Resumen corto

- **Qué:** escribir más datos de los que un búfer puede contener.
- **Por qué importa:** puede provocar fallos y corrupción; en contextos inseguros puede escalar a compromisos mayores.
- **Prevención:** validar entradas, usar funciones seguras, emplear protecciones del compilador/OS, y probar con herramientas como AddressSanitizer.

---

[🔼](#índice)

---

| **Inicio**         | **atrás 12**                        | **Siguiente 14**             |
| ------------------ | ----------------------------------- | ---------------------------- |
| [🏠](../README.md) | [⏪](./10_12_Rogue_Access_Point.md) | [⏩](./10_14_Memory_Leak.md) |
