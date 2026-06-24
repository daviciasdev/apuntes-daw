# Linux: Comandos útiles, búsqueda y redirección

> **Bloque 0 — Entorno** · Linux básico (parte 2)

La caja de herramientas del día a día en la terminal: navegar, ver, buscar y conectar comandos.

---

## 1. Navegación rápida

| Comando | Qué hace |
|---------|----------|
| `pwd` | Muestra en qué carpeta estás (*print working directory*) |
| `cd carpeta` | Entra en una carpeta (*change directory*) |
| `cd ..` | Sube un nivel (a la carpeta padre) |
| `cd` (solo) | Va a tu carpeta personal (`~`) |
| `cd -` | Vuelve a la carpeta anterior en la que estabas |
| `mkdir carpeta` | **Crea** una carpeta nueva (*make directory*) |
| `ls` | Lista el contenido de la carpeta |
| `ls -a` | Lista **incluyendo los archivos ocultos** |

> ⚠️ `cd` solo **se mueve**; no crea nada. Para crear una carpeta es `mkdir`. Son acciones distintas.

**Rutas absolutas vs relativas:**
- **Absoluta**: desde la raíz o `~` (`~/proyectos/apuntes-daw`). Funciona desde cualquier sitio.
- **Relativa**: parte de donde estás ahora (`../proyectos`, `bloque-0-entorno`). Más corta.

**Atajos que ahorran tecleo:**
- **`Tab`** → autocompleta nombres de archivo/carpeta. Evita erratas. Úsalo siempre.
- **Flecha arriba** → recupera comandos anteriores.

**Archivos ocultos:** los que empiezan por `.` (punto) no salen con `ls`, solo con `ls -a`. Es una convención para archivos de configuración o internos. Ocultar **≠** proteger: siguen siendo accesibles, solo no estorban a la vista.

---

## 2. Ver contenido: `cat`

Vuelca el contenido de un archivo en pantalla (*concatenate*):

```bash
cat archivo.txt
cat archivo1.txt archivo2.txt    # los muestra encadenados, uno tras otro
```

> `cat` **solo lee y muestra**; no modifica ni borra los archivos. La "fusión" de varios ocurre solo en la salida (pantalla), no en el disco.

---

## 3. Buscar **dentro** de archivos: `grep`

Devuelve **solo las líneas que contienen** un patrón:

```bash
grep "patrón" archivo.txt
```

Ejemplo: `grep "chmod" linux_permisos_chmod.md` → solo las líneas con "chmod".

---

## 4. Buscar **archivos**: `find`

Busca archivos por nombre (no mira dentro), recorriendo subcarpetas:

```bash
find DÓNDE -name "patrón"
find . -name "*.md"      # todos los .md desde la carpeta actual hacia abajo
```

- **`.`** = la carpeta actual (punto de partida de la búsqueda).
- **`*`** = comodín, "cualquier cosa". `*.md` = cualquier nombre que acabe en `.md`.

> Diferencia clave: **`grep` busca texto dentro** de archivos · **`find` busca archivos** (por nombre, fecha, tamaño...).

---

## 5. Redirección: `>` y `>>`

Por defecto, los comandos escriben en pantalla (*salida estándar*). La redirección **desvía esa salida a un archivo**:

| Operador | Qué hace |
|----------|----------|
| `>` | **Sobrescribe**: vacía el archivo y escribe de cero |
| `>>` | **Añade** al final: conserva lo que había |

```bash
grep "octal" notas.md > resultado.txt    # crea/sobrescribe
echo "nueva línea" >> registro.txt        # añade sin borrar
```

> ⚠️ `>` **machaca** el contenido anterior sin avisar (como el `chmod` absoluto con los permisos). Si no existe el archivo, tanto `>` como `>>` lo **crean**.

---

## 6. Tuberías: `|`

Conecta comandos: **la salida de uno se convierte en la entrada del siguiente**.

```bash
find . -name "*.md" | grep "permisos"   # de los .md, solo los que tienen "permisos"
find . -name "*.md" | wc -l             # cuenta cuántos .md hay (wc -l cuenta líneas)
cat archivo.md | grep "texto"           # = grep "texto" archivo.md
```

> La tubería es el mecanismo que convierte comandos sueltos en herramientas combinadas. Es de lo más potente de Linux.

---

## Resumen de comandos

| Comando | Para qué |
|---------|----------|
| `cat` | Mostrar contenido de archivos |
| `grep "x" archivo` | Filtrar líneas que contienen "x" |
| `find DÓNDE -name "x"` | Buscar archivos por nombre |
| `>` / `>>` | Redirigir salida (sobrescribir / añadir) |
| `\|` | Tubería: conectar salida → entrada |
| `wc -l` | Contar líneas |
| `mkdir` / `pwd` / `ls -a` | Crear carpeta / ver dónde estás / ver ocultos |
