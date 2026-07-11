# Tema 7 — Interfaces

> Bloque 1 · IES Fernando Wirtz · DAW a distancia
> Apunte completo del tema.

Construye sobre la POO (Temas 5-6). Donde la herencia modela **qué ES** algo ("es un"),
la interfaz modela **qué SABE HACER** ("es capaz de"), y permite que clases sin
parentesco compartan capacidades.

---

## 1. Qué es una interfaz y por qué

Una interfaz es un **contrato**: define qué métodos debe tener una clase, sin decir
cómo los implementa. Una promesa de comportamiento.

Resuelve dos límites de la herencia:
- Una clase solo hereda de **una** superclase (herencia simple).
- La herencia exige relación "es un" real (clases emparentadas).

Pero hay comportamientos que **cruzan** jerarquías: un `Pato`, un `Avion` y un
`Superheroe` pueden **volar** sin tener ancestro común. La interfaz captura esa
capacidad transversal.

> **Mentalidad:**
> - Herencia ("es un"): define **qué ES** un objeto. Perro es un Animal.
> - Interfaz ("es capaz de"): define **qué SABE HACER**. Pato es capaz de volar.
>
> Por eso suelen nombrarse como capacidades, a menudo "-able" en inglés:
> `Comparable`, `Runnable`, `Serializable`.

---

## 2. `interface` e `implements`

```java
public interface Volador {
    void volar();              // sin cuerpo; abstract y public IMPLÍCITOS
    int getAltitudMaxima();
}

public class Pato implements Volador {
    @Override
    public void volar() { System.out.println("El pato vuela."); }
    @Override
    public int getAltitudMaxima() { return 100; }
}
```

- Métodos **sin cuerpo**, solo firma. No hace falta `abstract` ni `public` (implícitos).
- `implements` (no `extends`). La clase queda **obligada a implementar todos** los
  métodos, o no compila.
- `@Override` también se usa al implementar métodos de interfaz.
- Una interfaz **es un tipo**: puedes tener variables/parámetros/arrays de ese tipo.

| | Herencia | Interfaz |
|---|---|---|
| Palabra | `extends` | `implements` |
| Relación | "es un" | "es capaz de" |
| Sin cuerpo | `abstract void x();` | `void x();` (implícito) |
| Obliga a implementar | sí (abstractos) | sí (todos) |

> **Sin constructor y sin estado.** Una interfaz clásica no tiene atributos de
> instancia ni constructor (no hay estado que inicializar, no se instancia). Sí puede
> tener constantes (`public static final`).
>
> (Java 8+: las interfaces admiten métodos `default`/`static` con cuerpo. Por ahora,
> mentalidad de interfaz clásica = contrato puro.)

---

## 3. Interfaz vs. clase abstracta (comparación clave)

| | Clase abstracta | Interfaz (clásica) |
|---|---|---|
| Relación | "es un" | "es capaz de" |
| **Estado (atributos)** | **Sí** | No (solo constantes) |
| **Constructor** | **Sí** | **No** |
| Métodos con cuerpo | Sí (concretos) | Solo `default`/`static` |
| **¿Cuántas por clase?** | **Una** (herencia simple) | **Varias** |

Las tres diferencias que importan:
1. **Estado/constructor**: la abstracta comparte código y datos; la interfaz es
   contrato puro.
2. **Una vs. varias**: una clase `extends` UNA superclase pero `implements` VARIAS
   interfaces. Es la "herencia múltiple de comportamiento" de Java.
3. **Mentalidad**: abstracta = jerarquía natural con código común; interfaz =
   capacidad transversal entre clases no emparentadas.

**Criterio:**
- Clase abstracta: relación "es un" clara + compartir código/atributos (ej. `Material`
  → `Libro`, `Pelicula`, `Disco`).
- Interfaz: capacidad que cruza familias, o varios roles a la vez (`Comparable`,
  `Volador`, `Descargable`).

No son excluyentes: una clase puede `extends` una abstracta E `implements` varias
interfaces a la vez.

---

## 4. Polimorfismo con interfaces

El polimorfismo del Tema 6, pero **sin necesidad de herencia común**:

```java
public class Pato implements Volador { ... }
public class Avion implements Volador { ... }   // sin parentesco con Pato

Volador[] voladores = { new Pato(), new Avion() };
for (Volador v : voladores) {
    v.volar();   // cada uno a su manera (enlace dinámico)
}
```

Agrupas objetos por **lo que saben hacer**, no por lo que son. Un método puede pedir un
`Volador` y aceptar cualquier cosa que vuele:

```java
public void despegar(Volador v) { v.volar(); }   // pato, avión, lo que sea
```

Principio: **"programar contra interfaces, no contra implementaciones."**

---

## 5. Implementar varias interfaces

```java
public class Pato extends Animal implements Volador, Nadador {
    @Override public void volar() { ... }
    @Override public void nadar() { ... }
}
```

Una **identidad** (extends, una superclase) + **muchas capacidades** (implements,
varias interfaces). Obligada a implementar todos los métodos de todas.

El mismo objeto se trata según el rol que interese:
```java
Pato p = new Pato();
Volador vv = p;   // como Volador
Nadador nn = p;   // como Nadador
Animal  aa = p;   // como Animal
```

---

## 6. `instanceof` y casting de objetos

Al recorrer una colección del tipo de una interfaz (o superclase), cada elemento se
"ve" con ese tipo. Para saber si además cumple **otro** tipo/interfaz en tiempo de
ejecución, y poder usar sus métodos, se combinan dos piezas:

**`instanceof`** — comprueba si un objeto es de un tipo (clase o interfaz), devuelve
`boolean`:

```java
if (elemento instanceof Descargable) { ... }   // ¿este objeto implementa Descargable?
```

**Casting de objetos** — convierte la referencia a ese tipo para acceder a sus métodos
(recuerda: el tipo de la variable decide qué métodos puedes llamar):

```java
for (Reproducible r : lista) {
    r.reproducir();
    if (r instanceof Descargable) {              // 1) comprobar
        Descargable d = (Descargable) r;         // 2) castear (seguro tras el if)
        total += d.getTamanioMB();               // 3) usar métodos del nuevo tipo
    }
}
```

> **Orden inseparable: primero `instanceof`, luego el casting.** Castear sin comprobar
> puede lanzar `ClassCastException` (ej. castear una RadioEnVivo a Descargable). El
> `instanceof` es la red de seguridad que garantiza que el casting es válido.
>
> **Java moderno (14+, disponible en JDK 21): pattern matching** junta ambos pasos:
> `if (r instanceof Descargable d) { ... }` declara `d` ya convertida. Aprender primero
> los dos pasos separados; el atajo, después.

---

## Ejercicio resuelto del tema

- **7.1 — Sistema de reproducción multimedia.** Dos interfaces independientes:
  `Reproducible` (reproducir + duración) y `Descargable` (descargar + tamaño).
  `Cancion` y `Podcast` implementan **ambas**; `RadioEnVivo` implementa **solo**
  `Reproducible` (en directo, no se descarga). `main` con un `Reproducible[]` (lo
  común a los tres) que se recorre reproduciendo todos, y con `instanceof Descargable`
  + casting para sumar el tamaño solo de los descargables. Lecciones:
  - Dos interfaces separadas (no una) para que cada clase firme solo las capacidades
    que tiene: la radio no arrastra un método de descarga sin sentido.
  - El tipo de la colección es la **capacidad común** (`Reproducible`), lo que permite
    meter clases no emparentadas.
  - Duplicación detectada entre `Cancion` y `Podcast` → posible base abstracta común
    (herencia + interfaces combinadas), con `RadioEnVivo` fuera por no ser descargable.
  - Conflicto de nombres entre temas (Cancion T6 vs T7) → parche con renombrado; la
    solución real son los **paquetes** (`package`).

---

## Principios transversales del tema

1. **Herencia = identidad ("es un"); interfaz = capacidad ("es capaz de").**
2. **Una superclase, muchas interfaces.**
3. Interfaz clásica: contrato puro, **sin estado ni constructor**.
4. **Programar contra interfaces**: agrupar y aceptar objetos por lo que saben hacer.
5. **`instanceof` antes del casting**, siempre (evita `ClassCastException`).
