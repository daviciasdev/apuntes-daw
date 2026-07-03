# Tema 4 — Arrays

> Bloque 1 · IES Fernando Wirtz · DAW a distancia
> Apunte completo del tema.

Hasta ahora, para guardar N valores hacían falta N variables sueltas. Un **array**
guarda muchos valores del mismo tipo en una sola estructura, accesibles por posición.
Es la primera estructura de datos del curso.

---

## 1. Qué es un array

Colección de **tamaño fijo** que almacena **valores del mismo tipo**, cada uno en una
**posición numerada** (índice).

```
índice:   0     1     2     3     4
        ┌─────┬─────┬─────┬─────┬─────┐
        │ 10  │  3  │  8  │  5  │ 12  │
        └─────┴─────┴─────┴─────┴─────┘
```

- **Tamaño fijo**: se decide al crearlo y no cambia. (Para tamaño dinámico está
  `ArrayList`, Tema 8.)
- **Un solo tipo**: `int[]`, `double[]`, `String[]`... no se mezclan.
- **Índices desde 0**: en un array de N, las posiciones válidas van de `0` a `N-1`.
  El último elemento es `array[length - 1]`. Acceder fuera de rango lanza
  `ArrayIndexOutOfBoundsException`.

---

## 2. Declaración, creación e inicialización

```java
// Declarar + crear (relleno con valores por defecto)
int[] numeros = new int[5];        // {0, 0, 0, 0, 0}

// Con valores desde el inicio (Java cuenta el tamaño solo)
int[] b = {10, 3, 8, 5, 12};

// Forma explícita (menos usada)
int[] c = new int[]{10, 3, 8};
```

**Valores por defecto** al crear con `new`:

| Tipo               | Por defecto     |
|--------------------|-----------------|
| enteros (int...)   | `0`             |
| decimales (double) | `0.0`           |
| `boolean`          | `false`         |
| `char`             | `'\u0000'`      |
| objetos / `String` | `null`          |

---

## 3. Acceso por índice y recorrido

```java
int x = numeros[0];      // LEER
numeros[2] = 99;         // ESCRIBIR

for (int i = 0; i < numeros.length; i++) {
    System.out.println(numeros[i]);
}
```

> **Error clásico: `<` vs `<=`.** La condición es `i < length`, NO `i <= length`. Si
> el array tiene 5 elementos, `length` es 5 pero el último índice válido es 4. Con
> `<=` accederías a `numeros[5]` → `ArrayIndexOutOfBoundsException`.

---

## 4. `length` y el `for-each`

**`length`** — número de elementos. Es un **atributo, sin paréntesis**:
`numeros.length` (¡ojo! en `String` era método con paréntesis: `texto.length()`).
Usar siempre `length` en la condición del `for`, nunca el tamaño "a mano".

**`for-each`** — recorrido simplificado cuando solo se **lee**:

```java
for (int n : numeros) {       // "para cada int n dentro de numeros"
    System.out.println(n);
}
```

> **`for-each` no modifica el array.** La variable (`n`) es una **copia** del valor.
> Hacer `n = 0` cambia la copia, no el array. Para modificar → `for` clásico con
> índice (`numeros[i] = ...`). Conexión con el paso por valor.

**Cuándo cada uno:**
- `for-each`: solo leer, en orden, de principio a fin. Más limpio.
- `for` clásico: cuando necesitas el índice, modificar, recorrer al revés, saltar
  posiciones o no recorrer entero.

---

## 5. Arrays y métodos (paso por referencia)

Un array es un **objeto**. Al pasarlo a un método se copia la **referencia** (no el
array entero), así que ambos apuntan al **mismo array**: **si el método lo modifica,
el cambio se ve fuera.**

```java
public static void duplicar(int[] a) {
    for (int i = 0; i < a.length; i++) {
        a[i] = a[i] * 2;
    }
}

int[] valores = {1, 2, 3};
duplicar(valores);
System.out.println(valores[2]);   // 6, ¡NO 3! El array real se modificó
```

> Con **primitivos** se copia el valor (el original no cambia). Con **objetos**
> (arrays) se copia la referencia (el original sí puede cambiar). Técnicamente Java
> siempre pasa por valor; con objetos ese valor es la referencia. Es la otra mitad
> de la charla "valor vs. referencia" del primer día.

Un método puede **devolver** un array (`return`), con tipo de retorno `int[]`:

```java
public static int[] crearRango(int n) {
    int[] r = new int[n];
    for (int i = 0; i < n; i++) r[i] = i;
    return r;
}
```

---

## 6. Arrays bidimensionales (matrices)

Tabla de filas y columnas. Dos pares de corchetes:

```java
int[][] matriz = new int[3][4];   // 3 filas, 4 columnas

int[][] tabla = {
    {1, 2, 3},
    {4, 5, 6},
    {7, 8, 9}
};

matriz[1][2] = 9;   // fila 1, columna 2
```

**Recorrido con bucles anidados:**

```java
for (int f = 0; f < tabla.length; f++) {          // filas
    for (int c = 0; c < tabla[f].length; c++) {   // columnas de esa fila
        System.out.print(tabla[f][c] + " ");
    }
    System.out.println();
}
```

- `tabla.length` → número de **filas**.
- `tabla[f].length` → número de **columnas** de la fila `f`.

---

## Ejercicios resueltos del tema

- **4.1 — Análisis de temperaturas.** Array `double` de tamaño dinámico, relleno con
  `for` clásico. Cálculos en **métodos separados** que reciben el array:
  `calcularMedia`, `calcularMaxima`, `calcularMinima`, `contarSobreMedia`.
  Lecciones:
  - Inicializar máx/mín con `temperaturas[0]` y recorrer **desde `i = 1`** (no desde
    0): cubre el caso de un solo elemento sin guardas extra, y evita comparar el
    primero consigo mismo.
  - **`Double.MIN_VALUE` NO es el más negativo** (es el positivo más pequeño ≈ 0);
    el más negativo es `-Double.MAX_VALUE`. Por eso inicializar en `[0]` es más
    seguro.
  - **Desconfiar de condiciones "por si acaso"** (`length > 1` dentro del `for`):
    casi siempre es mejor reestructurar para que el caso especial deje de serlo
    (empezar en `i = 1`) que parchearlo con una guarda.
  - Refactorizar código que ya funciona hacia métodos: legibilidad y reutilización
    ganan a ahorrar pasadas.

---

## Principios transversales reforzados

1. **Índices desde 0**; último = `length - 1`; `i < length` en el bucle.
2. **`for-each` para leer, `for` clásico para modificar** o cuando necesitas índice.
3. **Arrays por referencia**: un método puede modificar el array original.
4. **Inicializar búsquedas de máx/mín con el primer elemento**, recorrer desde 1.
5. **Reestructurar > parchear** los casos especiales.
