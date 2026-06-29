# Tema 1 — Conceptos básicos de Java

> Bloque 1 · IES Fernando Wirtz · DAW a distancia
> Apunte completo del tema.

---

## 1. Tipos primitivos

Java tiene **exactamente 8** tipos primitivos. Almacenan el valor directamente en
memoria: no son objetos, no tienen métodos y no pueden ser `null`.

| Tipo      | Tamaño  | Rango / uso                       |
|-----------|---------|-----------------------------------|
| `byte`    | 8 bits  | −128 a 127                        |
| `short`   | 16 bits | −32.768 a 32.767                  |
| `int`     | 32 bits | ~±2.100 millones · el más usado   |
| `long`    | 64 bits | Números muy grandes · sufijo `L`  |
| `float`   | 32 bits | Decimal simple precisión · sufijo `f` |
| `double`  | 64 bits | Decimal estándar                  |
| `char`    | 16 bits | Un carácter Unicode               |
| `boolean` | —       | `true` / `false`                  |

> **Primitivo vs. objeto.** `int` guarda el valor directamente. `Integer` es un
> objeto en el heap, con métodos, puede ser `null` y es **inmutable**.
> Java **siempre pasa por valor**; con objetos, el valor que se copia es la
> referencia (la dirección de memoria), no el objeto en sí.

---

## 2. Variables y declaración

Sintaxis: `tipo nombre = valor;`

```java
int edad = 25;
double precio = 9.99;
char letra = 'A';        // comillas simples
boolean activo = true;
String nombre = "David"; // String es objeto, no primitivo
```

Convenciones de nombres:
- Variables → **camelCase**: `miVariable`
- Clases → **PascalCase**: `MiClase`
- Constantes → **MAYÚSCULAS_CON_GUION**: `PRECIO_MAXIMO`

---

## 3. Entrada por teclado (Scanner)

Clase `Scanner` del paquete `java.util`.

```java
import java.util.Scanner;

Scanner sc = new Scanner(System.in);

String nombre = sc.nextLine();   // lee línea completa
int edad      = sc.nextInt();    // lee entero
double altura = sc.nextDouble(); // lee decimal
```

> **Precaución `nextInt()` + `nextLine()`.** `nextInt()` no consume el salto de
> línea. Si un `nextLine()` va justo **después** de un `nextInt()`, lee el salto
> pendiente y parece "saltarse" la entrada.
> **Solución robusta:** leer todo con `nextLine()` y convertir con
> `Integer.parseInt(sc.nextLine())` / `Double.parseDouble(...)`. Evita el problema
> de raíz.

---

## 4. Salida por consola

```java
System.out.println("Hola");              // con salto de línea
System.out.print("Hola");                // sin salto
System.out.printf("Hola, %s%n", nombre); // formateado
```

| Especificador | Para qué             |
|---------------|----------------------|
| `%s`          | String               |
| `%d`          | Entero (`int`)       |
| `%f`          | Decimal              |
| `%.2f`        | Decimal con 2 cifras |
| `%n`          | Salto de línea (portable, mejor que `\n`) |

---

## 5. Locale en entrada y salida (separador decimal)

En un Ubuntu en español, el separador decimal del sistema es la **coma**. Afecta a
`Scanner` y a `printf` por separado: **entrada y salida son mundos independientes**,
cada uno con su propio locale.

- `Scanner.nextDouble()` espera `1,80` y lanza `InputMismatchException` con `1.80`.
- `printf` saca `1,80` aunque el Scanner esté en inglés.

### Tres formas de controlarlo

**a) Local, en cada uso** (explícita pero repetitiva):
```java
Scanner sc = new Scanner(System.in);
sc.useLocale(Locale.ENGLISH);
System.out.printf(Locale.ENGLISH, "Mides %.2f m.%n", altura);
```

**b) Global, una sola vez** (recomendada):
```java
Locale.setDefault(Locale.ENGLISH); // primera línea del main
```

**c) Aceptar el locale del sistema** (meter decimales con coma).

> **Patrón general:** configuración *local repetida* vs. configuración *global
> única*. Reaparece en muchos contextos, no solo con locales.

📖 Doc oficial (Java 21):
https://docs.oracle.com/en/java/javase/21/docs/api/java.base/java/util/Scanner.html

---

## 6. Operadores

### Aritméticos

| Operador | Operación      | `7 op 3` |
|----------|----------------|----------|
| `+`      | Suma           | `10`     |
| `-`      | Resta          | `4`      |
| `*`      | Multiplicación | `21`     |
| `/`      | División       | `2` ⚠️   |
| `%`      | Módulo (resto) | `1`      |

> **Trampa 1 — división entera.** `int / int` da `int`: descarta decimales.
> `7 / 3` = `2`, no `2.33`. Para decimal, un operando debe ser `double`:
> `7.0 / 3` o `(double) 7 / 3`.
>
> **Trampa 2 — módulo.** `%` da el resto. Clave para par/impar (`n % 2 == 0`),
> múltiplos y ciclado de valores.

### Relacionales (devuelven `boolean`)

`==` (igual), `!=` (distinto), `>`, `<`, `>=`, `<=`

> `==` es comparación; `=` es asignación. No confundir.
> Con **objetos** (`String`), `==` compara identidad en memoria, no contenido →
> usar `.equals()`.

### Lógicos

| Operador | Significado        |
|----------|--------------------|
| `&&`     | Y (ambas verdaderas) |
| `\|\|`   | O (al menos una)   |
| `!`      | NO (invierte)      |

### Ternario

Forma compacta de un if/else que **devuelve un valor**:

```java
String par = numero % 2 == 0 ? "par" : "impar";
```

**Anidado** para más de dos casos (se evalúa en cascada):

```java
String signo = numero == 0 ? "cero" : numero > 0 ? "positivo" : "negativo";
```

---

## 7. Casting (conversión de tipos)

**Implícito (automático)** — de tipo pequeño a grande, sin pérdida:
```java
int n = 5;
double d = n;   // 5.0, automático
```

**Explícito (manual)** — de grande a pequeño, con posible pérdida; lo pides con `(tipo)`:
```java
double precio = 9.99;
int entero = (int) precio;   // 9 — ¡trunca, NO redondea!
```

> Cuidado con la precedencia: `(double) numero / 2` convierte y *luego* divide
> (→ `3.5`). `(double)(numero / 2)` divide entero primero (→ `3.0`).

---

## 8. Constantes (`final`)

Variable que solo se asigna **una vez**; reasignarla es error de compilación.

```java
final double IVA = 0.21;
final int ANIO_ACTUAL = 2026;
```

- Nombre en **MAYÚSCULAS_CON_GUION**.
- Ventajas: seguridad (no se cambia por error), legibilidad (el nombre explica el
  valor) y mantenimiento (un único punto de cambio).
- Evitan los **"números mágicos"**: valores sueltos sin nombre repartidos por el
  código.

---

## 9. Cadenas (`String`)

`String` es un **objeto** (no primitivo), por eso tiene métodos.

```java
String saludo = "Hola, " + nombre;   // + concatena, no suma
String s = "Edad: " + 25;            // "Edad: 25" (número → texto)
```

| Método              | Qué hace                         | `s = "David"`        |
|---------------------|----------------------------------|----------------------|
| `s.length()`        | Longitud                         | `5`                  |
| `s.toUpperCase()`   | A mayúsculas                     | `"DAVID"`            |
| `s.toLowerCase()`   | A minúsculas                     | `"david"`            |
| `s.charAt(i)`       | Carácter en posición `i`         | `charAt(0)` → `'D'`  |
| `s.substring(i, j)` | Trozo de `i` a `j` (sin incluir `j`) | `substring(0,2)` → `"Da"` |
| `s.equals(otro)`    | ¿Mismo contenido?                | `true`               |
| `s.trim()`          | Quita espacios de los extremos   | —                    |

> **Aviso 1 — comparar con `.equals()`, no `==`.** `String` es objeto; `==` mira
> identidad en memoria, no contenido.
>
> **Aviso 2 — posiciones desde 0.** En `"David"`, `D` es `charAt(0)`, `d` final es
> `charAt(4)`.
>
> **Aviso 3 — inmutables.** Los métodos NO modifican el original, **devuelven uno
> nuevo**. Hay que recoger el resultado: `s = s.toUpperCase();`

---

## Ejercicios resueltos del tema

- **1.1 — Presentación personal.** `Scanner` + `printf` con `String`/`int`/`double`.
  Descubrimiento del **locale** en entrada/salida.
- **1.2 — Analizador de número.** Par/impar (`%`), signo con tres casos (ternario
  anidado), doble y mitad (casting `(double)`). Principio: **un caso especial debe
  ser tan local como sea posible**, no envolver más de lo que le corresponde.
- **1.3 — Generador de carné.** `final` (centro y año actual), `substring` +
  `toUpperCase` para el código, concatenación. Detectado: calcular edad como resta
  de años es una aproximación (el cálculo correcto necesita fechas, tema posterior).

---

## Principios transversales aprendidos

1. **Verificar la documentación oficial** ante dudas de sintaxis o nombres de
   métodos, en vez de fiarse de memoria.
2. **Caso especial lo más local posible.**
3. **Evitar números mágicos** con constantes `final`.
4. **Local repetido vs. global único** como decisión de diseño (locale, config…).
