# Tema 8 — Colecciones

> Bloque 1 · IES Fernando Wirtz · DAW a distancia
> Apunte completo del tema.

Los **arrays** (Tema 4) tienen dos límites: tamaño fijo y operaciones manuales. Las
**colecciones** (Java Collections Framework) son estructuras que crecen/menguan solas y
traen métodos hechos (añadir, buscar, ordenar...). Son las que se usan en el día a día.

---

## 1. Las tres familias

| Familia | Guarda | Duplicados | Orden | Buscar por | Implementación |
|---|---|---|---|---|---|
| **List** | secuencia | sí | de inserción | índice `get(i)` | `ArrayList` |
| **Map** | pares clave→valor | claves no | no garantizado | clave `get(clave)` | `HashMap` |
| **Set** | elementos únicos | no | no garantizado | solo `contains` | `HashSet` |

Criterio de elección:
- ¿Orden y permites repetidos? → **List**.
- ¿Asociar cada elemento a una clave para buscarlo rápido? → **Map**.
- ¿Sin duplicados? → **Set**.

---

## 2. `ArrayList` (List)

Lista dinámica. Import: `java.util.ArrayList`.

```java
ArrayList<String> lista = new ArrayList<>();
lista.add("Ana");            // añadir al final
lista.get(0);                // leer por índice
lista.set(1, "Lucía");       // reemplazar
lista.remove(2);             // eliminar por índice
lista.remove("Ana");         // o por valor
lista.size();                // número de elementos
lista.contains("Ana");       // ¿está?
lista.isEmpty();             // ¿vacía?
lista.indexOf("Ana");        // posición, o -1 si no está
```

| Operación | Array | ArrayList |
|---|---|---|
| Tamaño | `a.length` (atributo) | `lista.size()` (método) |
| Leer | `a[0]` | `lista.get(0)` |
| Escribir | `a[0] = x` | `lista.set(0, x)` |
| Añadir/eliminar | no (tamaño fijo) | `add()` / `remove()` |

Recorrido: `for-each` (leer) o `for` clásico con `size()`/`get()` (si necesitas índice).

---

## 3. Genéricos `<Tipo>`

El `<...>` indica **qué tipo** guarda la colección.

```java
ArrayList<String> lista = new ArrayList<>();
lista.add("Ana");    // OK
lista.add(42);       // ERROR de compilación: no es String
```

Ventajas: **seguridad de tipos en compilación** (errores al compilar, no en
ejecución) y **no hace falta castear** al sacar elementos.

El **diamante** `<>` del `new` va vacío: Java deduce el tipo de la izquierda.

> **Los genéricos NO admiten primitivos.** `ArrayList<int>` no existe. Se usan las
> clases envoltorio (wrapper): `Integer`, `Double`, `Boolean`, `Character`. Java
> convierte automáticamente entre primitivo y wrapper (*autoboxing*):
> `lista.add(5)` mete un `Integer`. Aquí se cierra el círculo del `int` vs `Integer`
> del primer día.

---

## 4. `HashMap` (Map)

Pares **clave → valor**. Búsqueda rápida por clave (como un diccionario).
Import: `java.util.HashMap`. Dos tipos genéricos: `<clave, valor>`.

```java
HashMap<String, Comic> catalogo = new HashMap<>();
catalogo.put("978-84-01", comic1);    // asociar clave → valor
catalogo.get("978-84-01");            // recuperar por clave (rápido, sin recorrer)
catalogo.containsKey("978-84-01");    // ¿existe la clave?
catalogo.containsValue(comic1);       // ¿existe el valor?
catalogo.remove("978-84-01");         // eliminar por clave (devuelve el valor o null)
catalogo.size();
```

> **Claves únicas — pero NO impide duplicados, SOBRESCRIBE.** Un `put` con una clave
> existente reemplaza el valor anterior en silencio (no da error, no avisa). Si
> quieres rechazar duplicados, comprueba antes con `containsKey()`:
> ```java
> if (!mapa.containsKey(clave)) { mapa.put(clave, valor); }
> ```

> **`get` de una clave inexistente devuelve `null`.** Usar el resultado sin comprobar
> puede dar `NullPointerException`. Comprobar con `containsKey()` o verificar `!= null`.

> **Sin orden garantizado.** Los elementos no salen en orden de inserción. Si hace
> falta orden: `LinkedHashMap` (inserción) o `TreeMap` (por clave).

Recorrer: `keySet()` (claves), `values()` (valores), `entrySet()` (pares, con
`getKey()`/`getValue()`).

---

## 5. `HashSet` (Set)

Elementos **únicos, sin duplicados**. Import: `java.util.HashSet`.

```java
HashSet<String> generos = new HashSet<>();
generos.add("Acción");
generos.add("Acción");   // NO se añade: ya estaba. Sin error, sin duplicar.
generos.contains("Acción");
generos.size();          // cuenta elementos únicos
```

- vs `ArrayList`: sin duplicados y **sin posición** (no hay `get(indice)`).
- Gestiona la unicidad **automáticamente**: metes a lo bruto y descarta repetidos, sin
  que tú compruebes nada (a diferencia del `HashMap`, donde el control de duplicados
  lo haces tú con `containsKey`).
- Sin orden garantizado (`LinkedHashSet` / `TreeSet` si hace falta).

> "Hash" en HashMap/HashSet = búsqueda casi instantánea a cambio de no mantener orden
> (usan una tabla hash internamente).

---

## 6. Ordenar: `Comparable` y `Comparator`

Java no sabe ordenar objetos tuyos por sí solo: tú defines el criterio. Dos interfaces:

**`Comparable` — el orden natural (uno, dentro de la clase):**

```java
public class Comic implements Comparable<Comic> {
    @Override
    public int compareTo(Comic otro) {
        return this.titulo.compareTo(otro.titulo);   // delega en el compareTo de String
    }
}
Collections.sort(lista);   // usa el compareTo (orden natural)
```

`compareTo` devuelve `int`: **negativo** si `this` va antes, **cero** si igual,
**positivo** si va después. Se suele delegar en el `compareTo` de un tipo que Java ya
sabe comparar (String, Integer...).

**`Comparator` — órdenes alternativos (varios, fuera de la clase):**

```java
lista.sort(Comparator.comparing(Comic::getTitulo));           // por título
lista.sort(Comparator.comparingInt(Comic::getAnio));          // por año (int)
lista.sort(Comparator.comparingDouble(Comic::getNota).reversed());  // por nota, mayor a menor
```

- `comparing` (genérico) / `comparingInt` / `comparingDouble` / `comparingLong`
  (variantes para primitivos).
- `.reversed()` invierte el orden (por defecto es ascendente, menor a mayor).

| | `Comparable` | `Comparator` |
|---|---|---|
| Cuántos órdenes | uno (natural) | varios |
| Dónde | dentro de la clase | fuera |
| Método | `compareTo(otro)` | `comparing(...)` |

**Patrón para ordenar valores de un HashMap** (que no tiene orden): sacar los valores a
una lista y ordenarla.
```java
ArrayList<Comic> lista = new ArrayList<>(mapa.values());
Collections.sort(lista);   // o lista.sort(comparator)
```

---

## Ejercicios resueltos del tema

- **8.1 — Gestor de videojuegos (asistido).** `Videojuego implements Comparable`
  (orden natural por título). `GestorVideojuegos` con `HashMap<String, Videojuego>`
  (título como clave), control de duplicados con `containsKey`, `listarPorTitulo`
  (Comparable + Collections.sort), `listarPorNota` (Comparator.comparingDouble +
  reversed), `obtenerGeneros` (HashSet), `notaMedia` con valor centinela -1 si vacío.
- **8.2 — Gestor de contactos (autónomo).** Equivalente con `Contacto` (orden natural
  por nombre) y `Agenda` (teléfono como clave). Puntos propios resueltos: orden por
  edad de menor a mayor **sin** `reversed()`; edad `int` → `comparingInt`; media con
  acumulador `int` y `(double)` casting antes de dividir (coherencia de tipos); y en el
  main, **comprobar el centinela -1** antes de mostrar (cerrar el círculo del centinela).

---

## Principios transversales del tema

1. **Elegir la colección según la necesidad**: orden+repetidos (List), búsqueda por
   clave (Map), unicidad (Set).
2. **HashMap sobrescribe claves duplicadas**: controlar con `containsKey` si importa.
3. **HashSet gestiona la unicidad solo**; el HashMap no (lo haces tú).
4. **Comparable** = orden natural único; **Comparator** = alternativos.
5. **Valor centinela** (`-1`): señal de "sin resultado" en primitivos (no admiten
   `null`); pero depende de que quien llama lo compruebe.
6. **Coherencia de tipos**: casting `(double)` antes de dividir enteros para la media.
