# Tema 5 — Clases y objetos (POO)

> Bloque 1 · IES Fernando Wirtz · DAW a distancia
> Apunte completo del tema.

El gran salto del bloque: de la **programación estructurada** (datos por un lado,
métodos sueltos por otro) a la **programación orientada a objetos**, que agrupa datos
y comportamiento en una sola unidad: el objeto.

---

## 1. Qué es la POO y por qué

Antes: los datos (variables, arrays) y el comportamiento (métodos que los reciben por
parámetro) estaban separados. Con muchos elementos, esto lleva a arrays paralelos
frágiles (`nombres[]`, `edades[]`, `alturas[]`... rezando que el índice cuadre).

La **POO** agrupa en una sola unidad —el objeto— los datos y el comportamiento que van
juntos. Un objeto tiene:
- **Estado** (atributos): los datos que lo describen.
- **Comportamiento** (métodos): las acciones que sabe hacer, que operan sobre su
  propio estado.

Ventajas: organización (cada cosa del mundo real es un objeto), reutilización (defines
una vez, creas muchos) y protección (encapsulación).

---

## 2. Clase vs. objeto

- **Clase**: el molde/plano. Define cómo son los objetos de ese tipo. Se define una vez.
- **Objeto**: una instancia concreta creada del molde con `new`, con sus propios valores.

```
CLASE Movil (molde)  ──>  objeto: miMovil (Samsung, batería 80%)
                     ──>  objeto: tuMovil (iPhone, batería 45%)
```

Cada objeto tiene **su propia copia del estado** y es independiente: modificar uno no
afecta a los demás. `new` crea el objeto en memoria (igual que con arrays, que también
son objetos → por eso van por referencia).

---

## 3. Atributos y métodos de instancia

```java
public class CuentaBancaria {
    String titular;          // ATRIBUTOS: estado de cada objeto
    double saldo;

    public void ingresar(double cantidad) {   // MÉTODO DE INSTANCIA
        saldo = saldo + cantidad;             // usa el atributo directamente
    }
}
```

Clave del cambio de mentalidad: **los métodos usan los atributos directamente**, sin
recibirlos por parámetro. `cuenta1.ingresar(...)` opera sobre el `saldo` de `cuenta1`.

**Operador punto (`.`)**: accede a lo que hay dentro de un objeto:
`cuenta1.saldo` (atributo), `cuenta1.ingresar(...)` (método). Ya se usaba sin saberlo:
`sc.nextLine()`, `array.length`.

---

## 4. Constructores

Método especial que se ejecuta automáticamente al hacer `new`, para inicializar el
objeto y que nazca **completo y válido**.

Reglas: **mismo nombre que la clase** y **sin tipo de retorno** (ni `void`).

```java
public CuentaBancaria(String titular, double saldo) {
    this.titular = titular;
    this.saldo = saldo;
}

CuentaBancaria c = new CuentaBancaria("David", 1000.0);  // nace completo
```

- Si no defines constructor, Java da uno **por defecto** vacío. Pero **al definir uno
  propio, el por defecto desaparece**.
- Puedes tener **varios constructores** (sobrecarga: misma firma-nombre, distintos
  parámetros).
- **Constructor vacío**: NO por norma. Solo si el objeto es válido "vacío", si una
  tecnología lo exige (JPA/Hibernate lo pedirá), o para valores por defecto. Un objeto
  debe nacer en estado válido; un constructor vacío "por si acaso" permite objetos
  inválidos.

---

## 5. `this`

Referencia al **propio objeto** desde dentro de sus métodos. Uso principal:
desambiguar cuando un parámetro y un atributo se llaman igual.

```java
this.titular = titular;   // this.titular = ATRIBUTO ; titular = PARÁMETRO
```

- No es obligatorio si no hay ambigüedad (`saldo = saldo + cantidad` funciona), pero
  **como aprendiz, usar `this` siempre que toques un atributo** entrena la consciencia
  de estado.
- Segundo uso: `this(...)` como llamada = un constructor llama a otro de la misma clase
  (evita repetir código).

---

## 6. Encapsulación (la pieza clave del tema)

**Ocultar el estado interno y obligar a acceder a él por métodos controlados.**

**Problema** (atributos "al aire"): `cuenta1.saldo = -5000;` corrompe el estado
saltándose todo control.

**Solución:**
1. Atributos `private` → no accesibles desde fuera de la clase.
2. Métodos públicos controlados: **getters** (leer), **setters** (escribir con
   control) y métodos de negocio.

```java
private double saldo;

public double getSaldo() { return this.saldo; }

public void setSaldo(double saldo) {
    if (saldo >= 0) { this.saldo = saldo; }      // con CONTROL
}
```

> **Lo importante NO es "poner getters/setters a todo".** Eso es casi como tener el
> atributo público. La clave es el **information hiding**: el objeto controla su propio
> estado y decide cómo se puede modificar (el `if (saldo >= 0)` es encapsulación de
> verdad). El objeto es guardián de su coherencia: nadie puede dejarlo en estado
> inválido.

> **Datos fijos vs. datos que cambian**: los que no deben cambiar tras crearse (imei,
> nº de cuenta, nombre de producto) llevan getter pero **NO setter**. Los que cambian,
> ambos (con control en el setter).

---

## 7. `static` revisitado

Distinción: ¿algo pertenece a **cada objeto** o a **la clase entera**?

- **De instancia** (sin `static`): una copia por objeto. `titular`, `saldo`, `ingresar`.
  Necesitas un objeto: `cuenta1.ingresar()`.
- **`static`**: único y compartido por toda la clase. Se accede por la clase, sin objeto.

```java
private static int numeroCuentas = 0;   // UNO para toda la clase
// en el constructor: numeroCuentas++;   → cuenta el total de cuentas creadas
CuentaBancaria.numeroCuentas;            // acceso por la CLASE
```

**El misterio resuelto:**
- `public static void main` es `static` porque Java lo ejecuta **sin haber creado
  ningún objeto** todavía (es el punto de entrada).
- Los métodos `static` del Tema 3 (`sumar`...) lo eran porque se llamaban directo desde
  el `main`, sin objetos: métodos sueltos de utilidad.

> **Regla:** desde un método `static` no puedes acceder directo a atributos de
> instancia (no sabe de qué objeto). Lo static vive en el mundo de la clase; lo de
> instancia, en el de los objetos. Para pasar de uno a otro: crear objeto con `new`.

**Cuándo usar `static`**: constantes compartidas (`static final double IVA`),
contadores/datos globales de la clase, métodos de utilidad sin estado
(`Math.random()`, `Integer.parseInt()`). Por defecto en POO: **de instancia**.

---

## Ejercicios resueltos del tema

- **5.1 — Clase Producto (inventario).** Atributos privados, constructor con `this`,
  getters sin setters libres (nombre y stock no se modifican arbitrariamente),
  setter con control (precio ≥ 0), métodos de negocio (`anadirStock`,
  `venderUnidades` con control de stock suficiente). Lección: encapsulación real
  (proteger la coherencia, no poner setters mecánicos). Bug detectado: vender/añadir
  cantidades ≤ 0 (vender −3 inflaría el stock) → validar cantidades positivas.
- **5.2 — Clase Sala (cine).** Aplica **patrón boolean + separación de
  responsabilidades**: los métodos devuelven `true`/`false` y NO imprimen; el `main`
  decide el mensaje según el resultado. Lecciones:
  - **No duplicar estado**: `butacasLibres` se **calcula**
    (`butacasTotales - entradasVendidas`), no se guarda. Una sola fuente de verdad;
    imposible descuadrar. Regla: no guardes lo que puedes calcular (salvo cálculo caro).
  - El `boolean` comunica "no se pudo" pero **no el porqué** → limitación que
    resolverán las **excepciones** (Tema 9).

---

## Principios transversales del tema

1. **Datos + comportamiento juntos** en el objeto (vs. estructurada).
2. **Encapsulación = information hiding**: el objeto protege su propio estado.
3. **Objeto válido siempre**: nace completo (constructor), no se corrompe (control).
4. **Separación de responsabilidades**: la clase gestiona estado y devuelve resultado;
   la presentación (mensajes) es de quien la usa.
5. **No duplicar estado**: calcular datos derivados en vez de almacenarlos.
6. **Minimizar el estado guardado** = minimizar formas de corromperlo.
