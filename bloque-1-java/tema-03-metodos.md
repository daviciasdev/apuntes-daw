# Tema 3 — Métodos

> Bloque 1 · IES Fernando Wirtz · DAW a distancia
> Apunte completo del tema.

Hasta ahora todo el código vivía dentro del `main`. Los **métodos** permiten partir
el programa en piezas con nombre, cada una con una tarea, reutilizables. Es el primer
paso hacia organizar programas de verdad y la antesala de la POO (Tema 5).

Ya usabas métodos sin definirlos: `Integer.parseInt()`, `Math.random()`,
`sc.nextLine()`, `System.out.println()`. Ahora aprendes a escribir los tuyos.

---

## 1. Qué es un método y por qué

Un bloque de código con nombre que realiza una tarea y puede reutilizarse.

```java
public static void saludar(String nombre) {
    System.out.println("Hola, " + nombre);
}

saludar("Ana");
saludar("Luis");
```

Ventajas (el *porqué* del tema):
1. **No repetir código** (DRY, *Don't Repeat Yourself*): la lógica en un solo sitio.
2. **Legibilidad**: el `main` se lee como un índice (`calcularMedia()`, `mostrarMenu()`).
3. **Mantenimiento**: si hay un fallo, sabes dónde mirar.

> Misma filosofía que las constantes `final` (Tema 1): un único punto de cambio,
> pero para **comportamiento** en vez de para **valores**.

---

## 2. Anatomía de un método

```java
public static void saludar(String nombre) {
    // cuerpo
}
```

- **`public static`** — modificadores. Por ahora, fórmula fija. El significado real
  (`public` = visibilidad, `static` = pertenece a la clase) se ve en el Tema 5.
- **`void`** — tipo de retorno: qué devuelve. `void` = no devuelve nada.
- **`saludar`** — nombre, en camelCase; suele ser un verbo.
- **`(String nombre)`** — parámetros: datos de entrada.
- **`{ }`** — cuerpo: código que se ejecuta al llamarlo.

> **Firma** = nombre + parámetros (`saludar(String)`). Identifica el método de forma
> única. Clave para entender la sobrecarga.

---

## 3. Métodos que devuelven valor (`return`)

`return` termina el método y **envía un valor de vuelta**. El tipo de retorno indica
qué tipo devuelve.

```java
public static int sumar(int a, int b) {
    return a + b;   // el tipo devuelto (int) coincide con el declarado (int)
}

int total = sumar(5, 3);           // 8
System.out.println(sumar(10, 20)); // 30
```

> El tipo de retorno declarado y lo que devuelve el `return` **deben coincidir**.

**Dos detalles de `return`:**

1. **Termina el método inmediatamente.** Sirve para "salir antes":
   ```java
   if (nota < 5) {
       return "Suspenso";   // sale ya
   }
   return "Aprobado";       // solo si nota >= 5
   ```
2. **`void` vs. con retorno:** un `void` puede llevar `return;` a secas (salir sin
   valor). Un método con tipo de retorno está **obligado** a devolver un valor por
   todos los caminos.

> Distinción esencial del tema: métodos que **calculan y devuelven** (`parseInt`,
> `nextLine`) vs. métodos que **solo hacen una acción** (`println`, que es `void`).
> Un método reutilizable calcula y devuelve; deja que quien lo llama decida qué
> hacer con el resultado. Mezclar cálculo con impresión (`void` + `println` dentro)
> rompe la reutilización.

---

## 4. Sobrecarga (*overloading*)

Varios métodos con el **mismo nombre**, diferenciados por sus **parámetros**.

```java
public static int sumar(int a, int b) { return a + b; }
public static int sumar(int a, int b, int c) { return a + b + c; }
public static double sumar(double a, double b) { return a + b; }

sumar(2, 3);      // primero
sumar(2, 3, 4);   // segundo
sumar(2.5, 1.5);  // tercero
```

Java elige según los argumentos (cuántos y de qué tipo). Para sobrecargar, la firma
debe diferir en:
- **Número** de parámetros, o
- **Tipo** de parámetros, o
- **Orden** de los tipos.

> **El tipo de retorno NO cuenta para la firma.** Dos métodos con mismos parámetros
> pero distinto retorno NO compilan:
> ```java
> public static int calcular(int a) { ... }
> public static double calcular(int a) { ... }  // ERROR: misma firma
> ```

---

## 5. Alcance de variables (*scope*)

Una variable solo existe dentro del bloque `{ }` donde se declara. Fuera, no existe.

Con métodos: **cada método tiene sus propias variables, aisladas de los demás.**

```java
public static void metodoA() {
    int x = 10;              // x solo vive aquí
}
public static void metodoB() {
    System.out.println(x);   // ERROR: x no existe aquí
}
```

Variables con el mismo nombre en métodos distintos **son variables distintas**:

```java
public static int sumar(int a, int b) { return a + b; }

public static void main(String[] args) {
    int a = 5, b = 3;
    int total = sumar(a, b);  // se pasa una COPIA de los valores (5 y 3)
}
```

> **Conexión con el primer día (paso por valor).** Los primitivos se pasan por copia
> del valor. Si `sumar` modificara su `a`, la `a` del `main` no cambiaría: son
> variables independientes en scopes distintos. "Alcance" + "paso por valor" son la
> misma idea vista desde dos ángulos.

---

## Ejercicios resueltos del tema

- **3.1 — Calculadora con métodos.** Cuatro métodos (`sumar`, `restar`,
  `multiplicar`, `dividir`) con retorno; el `main` solo los llama. Lección central:
  un método **calcula y devuelve**; quien llama decide cómo mostrar. No mezclar
  cálculo con impresión.
- **3.2 — Utilidades de números.** `esPar` (return booleano directo), `esPrimo`
  (bucle con boolean de control, reutiliza `esPar`), `mayor` sobrecargado (2 y 3
  parámetros, la de 3 reutiliza la de 2). Lección: **colaboración entre métodos** y
  el caso matemático del **1 no es primo** (definición: >1 con exactamente dos
  divisores distintos; se excluye el 1 para la unicidad del Teorema Fundamental de
  la Aritmética).

---

## Principios transversales reforzados

1. **Separar "calcular" de "mostrar"**: los métodos devuelven, el llamador presenta.
2. **DRY**: un método reutilizable evita repetir lógica; los métodos pueden llamarse
   entre sí.
3. **La firma** (nombre + parámetros) identifica el método; el retorno no cuenta.
4. **Scope y paso por valor**: cada método es un mundo aislado.
