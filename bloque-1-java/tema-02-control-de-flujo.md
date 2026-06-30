# Tema 2 — Control de flujo

> Bloque 1 · IES Fernando Wirtz · DAW a distancia
> Apunte completo del tema.

Hasta ahora los programas iban en línea recta, de arriba abajo. El control de
flujo permite **tomar decisiones** (condicionales) y **repetir** (bucles).

---

## 1. Condicionales `if` / `else if` / `else`

```java
if (condicion) {
    // si condicion es true
} else if (otraCondicion) {
    // si la primera es false y esta es true
} else {
    // si ninguna anterior se cumplió
}
```

- La condición va entre paréntesis y evalúa a un `boolean`. Aquí se usan los
  operadores relacionales y lógicos del Tema 1.
- `else if` y `else` son opcionales.
- Se evalúan **en orden, de arriba abajo**; en cuanto una se cumple, las demás se
  ignoran.

> **El orden importa: de lo más específico/restrictivo a lo más general.** Si pones
> lo general primero, "se traga" los casos que deberían caer en lo específico.
> Ejemplo del fallo: `if (nota >= 5) ... else if (nota >= 9)` nunca llega a
> "Sobresaliente", porque `nota >= 5` atrapa antes el 9.

> **Aprovechar el "ya filtrado":** en un `else if`, cada rama tiene garantizado que
> las condiciones anteriores fallaron. No hace falta repetir límites superiores:
> basta `else if (nota >= 5)`, no `else if (nota >= 5 && nota < 9)`.

### Relación con el ternario (Tema 1)

El ternario es un if/else compacto que **devuelve un valor** (bueno para
asignaciones cortas). El if/else es más legible con varias instrucciones por rama o
muchos casos.

---

## 2. `switch`

Para comparar **una misma variable** contra **varios valores concretos**. Funciona
con `int`, `char`, `String`… (no con `double`).

### Clásico (con `:` y `break`)

```java
switch (dia) {
    case 1:
        nombre = "Lunes";
        break;
    case 2:
        nombre = "Martes";
        break;
    default:
        nombre = "Otro";
}
```

> **Trampa del fall-through.** Sin `break`, Java sigue ejecutando los `case`
> siguientes (aunque no coincidan) hasta encontrar un `break` o acabar el switch.
> El fall-through arranca **desde el case que coincide**, hacia abajo; los `case`
> anteriores al que machea se saltan. Regla: cada `case` con su `break`.
>
> Fall-through *intencionado* para agrupar: `case 1: case 2: case 3:` seguidos
> comparten bloque.

### Moderno (con `->`, Java 14+)  ← preferido para código nuevo

```java
switch (letra) {
    case 'A' -> System.out.println("Excelente");
    case 'B' -> System.out.println("Bien");
    default  -> System.out.println("Otro");
}
```

- Cada case ejecuta **solo su bloque**: no hay fall-through, no hace falta `break`.
- Bloque multilínea con llaves: `case 2 -> { ... }`.
- Agrupar casos con comas: `case 1, 2, 3 -> ...`.
- Puede **devolver un valor** (expresión asignable):

```java
String mes = switch (n) {
    case 1 -> "Enero";
    default -> "Desconocido";
};   // ← punto y coma: es una expresión
```

> El clásico hay que conocerlo (aparece en código ajeno y en el material del Wirtz).
> Para escribir código nuevo, el moderno `->` es más seguro y limpio.

---

## 3. Bucles `while` y `do-while`

**`while`** — comprueba la condición **antes** de cada vuelta. Puede ejecutarse
**0 veces**.

```java
int i = 1;
while (i <= 5) {
    System.out.println(i);
    i++;   // imprescindible para acercar la condición a false
}
```

**`do-while`** — comprueba la condición **al final**. Ejecuta **al menos 1 vez**.

```java
do {
    System.out.print("Edad (0-120): ");
    edad = Integer.parseInt(sc.nextLine());
} while (edad < 0 || edad > 120);
```

> **Trampa: bucle infinito.** Dentro del bucle algo debe acercar la condición a
> `false` (ej. `i++`). Si se olvida, se repite para siempre. Se para con el botón
> rojo de stop en IntelliJ.
>
> **Elección:** `do-while` cuando algo debe pasar mínimo una vez (pedir un dato y
> repetir hasta que sea válido, mostrar un menú). `while` cuando puede no
> ejecutarse ninguna.

---

## 4. Bucle `for`

Junta en una línea las tres piezas del bucle. Preferido cuando se conoce (o se
controla) el número de repeticiones.

```java
for ( inicialización ; condición ; incremento ) {
    // cuerpo
}

for (int i = 1; i <= 5; i++) { ... }   // 1..5
for (int i = 10; i > 0; i--) { ... }   // cuenta atrás
for (int i = 0; i < 100; i += 10) { ... } // de 10 en 10
```

1. **Inicialización**: una sola vez, al empezar.
2. **Condición**: antes de cada vuelta; si es false, sale.
3. **Incremento**: al final de cada vuelta.

> **Scope:** la variable declarada en el `for` solo existe dentro del bucle. Si se
> necesita después, declararla antes. Tener la contadora "encerrada" es bueno.

---

## 5. `break` / `continue` y la disciplina estructurada

- `break` — sale del bucle inmediatamente.
- `continue` — salta al siguiente ciclo del bucle.

### Postura "evitar break/continue" (programación estructurada)

Principio **"una entrada, una salida"**: un bucle debería terminar por un solo
sitio (su condición), no por puntos de escape internos. Con un `boolean` de
control, la condición cuenta toda la verdad sobre cuándo para:

```java
boolean encontrado = false;
while (!encontrado && i < n) {
    if (algo) {
        encontrado = true;   // en vez de break
    }
    i++;
}
```

> **Regla práctica:** es un buen *valor por defecto*, no un dogma. Se acepta
> `break`/`continue` cuando *mejoran* la legibilidad (p. ej. `break` obligatorio en
> switch clásico, o un *early exit* claro en una búsqueda). La regla real:
> "no uses break/continue si oscurecen el flujo; un boolean bien nombrado suele ser
> más claro".

---

## Ejercicios resueltos del tema

- **2.1 — Adivina el número.** `do-while` (pedir al menos una vez), condicional con
  pistas alto/bajo, contador de intentos. `Math.random()` para el secreto.
- **2.2 — Estadísticas de una serie.** `for` con número conocido de vueltas,
  acumuladores (suma, contadores pares/impares), mayor/menor inicializados con el
  primer número (`i == 0`). Lección: **dos `if` independientes** comunican mejor
  que un `else if` cuando ambas comprobaciones deben hacerse siempre. Y: **verificar
  trazando** las afirmaciones sobre el propio código, no creerlas a ciegas.
- **2.3 — Menú de cajero.** `switch` moderno con `->`, bucle controlado con boolean
  (`while (!fin)`) a lo "una salida", validación de lógica de negocio (saldo
  insuficiente). Lección: **coherencia de tipos** — si el saldo es `double`, leer
  con `Double.parseDouble`, no con `Integer.parseInt`.

---

## Principios transversales reforzados

1. **Orden de condiciones**: de lo específico a lo general.
2. **Elegir el bucle según si debe ejecutarse al menos una vez** (`do-while`) o no
   (`while`), o si el número de vueltas es conocido (`for`).
3. **Acumuladores**: declarar fuera, actualizar dentro.
4. **Coherencia de tipos** entre lectura, almacenamiento y salida.
5. **Una entrada, una salida** como disciplina por defecto en bucles.
