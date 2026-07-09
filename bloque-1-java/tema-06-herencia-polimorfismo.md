# Tema 6 — Herencia y polimorfismo

> Bloque 1 · IES Fernando Wirtz · DAW a distancia
> Apunte completo del tema.

Construye sobre la POO del Tema 5. Cuando tienes clases **parecidas pero no iguales**,
la herencia pone lo común en una clase padre y deja que las hijas añadan/especialicen.
El polimorfismo permite tratarlas de forma uniforme pero que cada una se comporte a su
manera. Es el principio DRY (Tema 3) elevado al nivel de clases.

---

## 1. Qué es la herencia y por qué

Crear una clase nueva **a partir de otra existente**, heredando sus atributos y
métodos y añadiendo/modificando lo necesario. Evita duplicar el código común.

Relación **"es un" (is-a)**: un `Programador` **es un** `Empleado`. Si puedes decir
"X es un Y" con naturalidad, X puede heredar de Y.

> **Herencia ("es un") vs. composición ("tiene un").** No todo lo relacionado se
> modela con herencia:
> - `Coche` **es un** `Vehiculo` → herencia (`extends`).
> - `Casa` **tiene** habitaciones, `Biblioteca` **tiene** libros → composición (un
>   atributo que contiene al otro), NO herencia.
> Error clásico: usar herencia donde tocaba composición solo porque dos clases están
> relacionadas. El filtro es "es un".

---

## 2. `extends`, superclase y subclase

- **Superclase** (padre/base): la que se hereda.
- **Subclase** (hija/derivada): la que hereda, con `extends`.

```java
public class Persona {
    protected String nombre;
    protected int edad;
    public void presentarse() { System.out.println("Soy " + nombre); }
}

public class Estudiante extends Persona {   // hereda nombre, edad, presentarse()
    private String titulacion;
    public void estudiar() { System.out.println(nombre + " estudia " + titulacion); }
}
```

La subclase usa lo heredado **sin redefinirlo**, más lo suyo propio.

**Niveles de acceso** (relevantes para herencia):
- `private`: solo dentro de la propia clase (NO cruza a subclases).
- `protected`: la clase **y sus subclases**.
- `public`: desde cualquier sitio.

Si un atributo del padre es `private`, la subclase no puede accederlo directamente;
con `protected`, sí. (Alternativa más estricta: `private` + getters/setters heredados.)

> **Herencia simple:** en Java una clase solo puede heredar de UNA superclase. No
> existe `extends B, C`. Sí cadenas: `C extends B extends A`.

---

## 3. `super`

Referencia a la superclase (como `this` lo es al propio objeto).

**Uso 1 — llamar al constructor del padre: `super(...)`**

```java
public Estudiante(String nombre, int edad, String titulacion) {
    super(nombre, edad);            // inicializa la parte heredada (Persona)
    this.titulacion = titulacion;   // inicializa lo propio
}
```

Reglas críticas:
1. Si se usa, **debe ser la PRIMERA línea** del constructor (el objeto se construye de
   arriba abajo).
2. Si NO lo pones, Java llama automáticamente al **constructor vacío** del padre. Si el
   padre no tiene constructor vacío (definió uno con parámetros), **error de
   compilación**. Por eso con herencia casi siempre hay que poner `super(...)` explícito.

**Uso 2 — acceder a métodos del padre: `super.metodo()`** (para reutilizar la versión
del padre al sobrescribir).

Paralelismo: `this` = "yo mismo"; `super` = "mi padre".

---

## 4. Sobrescritura (`@Override`)

Una subclase **redefine** un método heredado con la misma firma, dándole otro
comportamiento.

```java
public abstract class Animal {
    public void hacerSonido() { System.out.println("hace un sonido"); }
}
public class Perro extends Animal {
    @Override
    public void hacerSonido() { System.out.println("¡Guau!"); }
}
```

**`@Override`**: anotación opcional pero **muy recomendable**. Activa una red de
seguridad: el compilador verifica que de verdad sobrescribes un método del padre. Sin
ella, una errata en la firma crea silenciosamente un método nuevo que no sobrescribe
nada (bug clásico). Ponla SIEMPRE que sobrescribas, incluso al reutilizar el padre con
`super.metodo()` (sigue siendo sobrescritura).

> **Sobrecarga (overload) vs. sobrescritura (override):**
> - **Sobrecarga** (Tema 3): mismo nombre, **distintos parámetros**, misma clase.
>   Varias versiones conviviendo.
> - **Sobrescritura** (ahora): **misma firma exacta**, clases distintas (padre/hijo).
>   El hijo reemplaza la versión del padre.

---

## 5. Polimorfismo

*Poli* (muchas) + *morfos* (formas): un mismo tipo se comporta de muchas maneras según
el objeto real que contenga.

**Una variable del tipo padre puede contener un objeto de cualquier subclase** (porque
"es un"):

```java
Animal a1 = new Perro("Toby");    // variable Animal, objeto Perro
Animal a2 = new Gato("Michi");
a1.hacerSonido();   // "¡Guau!" — versión de Perro
a2.hacerSonido();   // "¡Miau!" — versión de Gato
```

**Enlace dinámico:** en tiempo de ejecución, Java mira el **objeto real** (no el tipo
de la variable) y llama a su versión del método. Esto NO lo causa `@Override` (que solo
verifica); lo causa el objeto real.

Permite tratar objetos distintos de forma **uniforme**:

```java
Animal[] animales = { new Perro("Toby"), new Gato("Michi") };
for (Animal a : animales) { a.hacerSonido(); }   // cada uno el suyo
```

Añadir una `Vaca extends Animal` no obliga a tocar el bucle: diseño extensible.

> **Regla clave:** el **tipo de la variable** decide QUÉ métodos puedes llamar; el
> **objeto real** decide CUÁL versión se ejecuta. Con `Animal a = new Perro()`, solo
> puedes llamar métodos que existan en `Animal`; para lo específico de `Perro`, hace
> falta casting de objetos.

---

## 6. Clases abstractas

Clase **pensada para heredarse pero que NO se puede instanciar**. Molde incompleto:
define lo común y deja "huecos" que las subclases rellenan.

```java
public abstract class Animal {
    protected String nombre;
    public Animal(String nombre) { this.nombre = nombre; }

    public abstract void hacerSonido();   // ABSTRACTO: sin cuerpo, obliga a implementar

    public void dormir() {                // CONCRETO: se hereda tal cual
        System.out.println(nombre + " duerme.");
    }
}
```

- `abstract class` → `new Animal(...)` da **error**. Solo se instancian las subclases
  concretas.
- **Método abstracto** (`abstract void hacerSonido();`): sin cuerpo; toda subclase
  concreta está **obligada** a implementarlo o no compila. Para comportamiento que cada
  subclase hace distinto.
- **Método concreto**: con cuerpo, común a todas.

> **Patrón potente:** un método concreto del padre puede llamar a un método abstracto.
> El código del padre es único, pero se comporta distinto por subclase (polimorfismo).
> Ej: `esConsumoLargo()` concreto que llama a `minutosConsumo()` abstracto.

Usar cuando: concepto general no instanciable (`Figura`, `Empleado`, `Material`), que
comparte código y obliga a implementar ciertos métodos.

---

## 7. `Object` y `toString()`

**Toda clase hereda (implícitamente) de `Object`**, la raíz del árbol de Java. Aunque
no escribas `extends`, `class X` es `class X extends Object`. Por eso toda clase hereda
métodos como `toString()`, `equals()`, `hashCode()`.

**El misterio del `Producto@1b6d3586`:** al imprimir un objeto, Java llama a su
`toString()`. La versión heredada de `Object` devuelve `Clase@hashHex`, inútil. Solución:
**sobrescribir `toString()`**.

```java
@Override
public String toString() {
    return String.format("%s - %.2f€", nombre, precio);   // DEVUELVE texto
}

System.out.println(p);   // Java llama a tu toString() automáticamente
```

- `@Override` porque sobrescribes un método de `Object`.
- `String.format` = como `printf` pero **devuelve** en vez de imprimir (separar
  construir texto de imprimirlo; quien llama decide).
- `println(objeto)` ya llama a `toString()`: no hace falta `.toString()` explícito.

Mejor que un `mostrarInfo()` inventado: `toString()` es el nombre estándar que todo
Java usa (println, concatenación, depuradores, colecciones).

Otros de `Object` (para más adelante): `equals(Object)` (igualdad por contenido, no por
referencia como `==`), `hashCode()` (para colecciones, Tema 8).

---

## Ejercicios resueltos del tema

- **6.1 — Jerarquía de empleados.** `Empleado` abstracta con `calcularSalarioMensual()`
  abstracto y `toString()` que lo usa (polimorfismo dentro del padre). `Programador`
  (plus por certificación) y `Comercial` (comisión por tramos) sobrescriben el cálculo.
  `main` con `Empleado[]` polimórfico. Lecciones: decidir si un dato es de instancia o
  constante según la regla de negocio (defendido: plus variable por seniority); no
  guardar lo derivado (porcentaje calculado, no almacenado); coherencia de tipos
  (`100.0` para evitar división entera).
- **6.2 — Biblioteca multimedia.** `Material` abstracta con `minutosConsumo()` abstracto
  y `esConsumoLargo()` concreto que se apoya en él. `Libro` (páginas × min/página),
  `Pelicula` (dato directo), `Disco` (**composición**: tiene `Cancion[]`, suma
  duraciones reales). `Biblioteca` con `ArrayList<Material>` y cálculos agregados
  polimórficos (tiempo total, contar largos, mayor). Lecciones: **composición**
  descubierta y aplicada (Disco tiene canciones, no es una canción); coherencia de tipos
  en toda la cadena (velocidad de lectura `double`, no solo el retorno); caso límite de
  colección vacía en "buscar el mayor"; una clase pública por archivo.

---

## Principios transversales del tema

1. **"Es un" → herencia; "tiene un" → composición.** No confundir.
2. **`super(...)` primero** en el constructor; el padre inicializa su parte.
3. **`@Override` siempre** que sobrescribas (red de seguridad del compilador).
4. **Polimorfismo**: tipo de variable decide QUÉ; objeto real decide CUÁL versión.
5. **Clase abstracta**: comparte lo común, obliga lo específico; método concreto que
   se apoya en abstracto = comportamiento único que varía por subclase.
6. **`toString()` estándar** que devuelve texto, en vez de `mostrarInfo()` que imprime.
7. **Coherencia de tipos en toda la cadena** de cálculo, no solo en el retorno.
