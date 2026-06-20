# Comandos de terminal — Bloque 0 (Día 3)

> Chuleta de los comandos básicos de terminal en Ubuntu (bash).
> Recuerda: en Linux **las mayúsculas importan** (`Descargas` ≠ `descargas`).

---

## Orientarte y moverte

| Comando | Qué hace |
|---|---|
| `pwd` | *Print working directory*. Muestra en qué carpeta estás (ruta completa). |
| `ls` | *List*. Lista el contenido de la carpeta actual. |
| `ls -l` | Lista en formato **detallado** (permisos, propietario, tamaño, fecha). |
| `ls -a` | Lista **incluyendo los archivos ocultos** (los que empiezan por `.`). |
| `cd carpeta` | *Change directory*. Entra en esa carpeta. |
| `cd ..` | Sube un nivel (a la carpeta "padre"). |
| `cd` (o `cd ~`) | Vuelve a tu carpeta personal (`/home/tuusuario`) desde cualquier sitio. |
| `cd -` | Vuelve a la carpeta anterior en la que estabas. |

**Atajos de ruta importantes:**
- `~` → tu carpeta personal (`/home/tuusuario`).
- `.` → "esta misma carpeta".
- `..` → "la carpeta de arriba" (la padre).

---

## Crear

| Comando | Qué hace |
|---|---|
| `mkdir nombre` | *Make directory*. Crea una carpeta nueva. |
| `touch archivo.txt` | Crea un archivo **vacío** (o actualiza su fecha si ya existe). |

---

## Copiar, mover, renombrar y borrar

| Comando | Qué hace |
|---|---|
| `cp origen destino` | *Copy*. Copia un archivo. |
| `mv viejo nuevo` | *Move*. **Renombra** (si el destino es un nombre nuevo) **o mueve** (si el destino es una carpeta que existe). |
| `rm archivo` | *Remove*. Borra un archivo. **Sin papelera.** |
| `rmdir carpeta` | Borra una carpeta, **solo si está vacía**. |
| `rm -r carpeta` | Borra una carpeta **y todo su contenido** (recursivo). |
| `rm -ri carpeta` | Igual que `rm -r` pero **pregunta antes de borrar cada cosa** (más seguro). |

> ⚠️ **Cuidado con `rm` y sobre todo con `rm -r`:** lo que borras se va **para siempre**, no hay deshacer ni papelera. Lee dos veces qué estás borrando antes de pulsar Enter.

**Truco sobre `mv`:** un mismo comando renombra o mueve según el destino.
- `mv notas.txt apuntes.txt` → renombra.
- `mv notas.txt ~/Descargas/` → mueve a la carpeta Descargas.

---

## Encadenar comandos

| Separador | Qué hace |
|---|---|
| `comando1 && comando2` | Ejecuta el segundo **solo si el primero salió bien** (condicional). |
| `comando1 ; comando2` | Ejecuta el segundo **pase lo que pase** con el primero. |

> Por costumbre, mejor `&&`: si el primero falla, no se ejecuta el segundo por error.

---

## Comentarios

Todo lo que va después de `#` se ignora:

```bash
# Esto es un comentario, la terminal no hace nada con ello
ls -l    # también vale comentar al final de un comando
```

(El `#` debe ir al principio o tras un espacio; dentro de comillas es literal.)

---

## Leer la salida de `ls -l`

Ejemplo:
```
-rw-rw-r-- 1 davicias davicias 0 Jun 17 13:18 notas.txt
```

| Campo | Significado |
|---|---|
| `-rw-rw-r--` | Tipo + permisos (ver abajo). |
| `1` | Número de enlaces (normalmente 1 en archivos). |
| `davicias` (1º) | Propietario (usuario dueño). |
| `davicias` (2º) | Grupo al que pertenece. |
| `0` | Tamaño en bytes. |
| `Jun 17 13:18` | Fecha y hora de última modificación. |
| `notas.txt` | Nombre. |

**Cómo leer `-rw-rw-r--`:**

```
 -   rw-   rw-   r--
 │    │     │     └── otros: solo leer
 │    │     └──────── grupo: leer y escribir
 │    └────────────── propietario: leer y escribir
 └─────────────────── tipo: archivo normal
```

- Primer carácter: `-` archivo, `d` directorio, `l` enlace.
- Luego 3 grupos de 3 → propietario / grupo / otros.
- `r` = leer, `w` = escribir, `x` = ejecutar, `-` = sin ese permiso.

---

## Trucos útiles

- **`Tab`** → autocompleta nombres de archivo y comandos. Escribe las primeras letras y pulsa Tab.
- **`Flecha arriba / abajo`** → recorre comandos que ya has escrito (historial).
- **`Ctrl + C`** → cancela el comando actual (o la línea que estás escribiendo).
- **`Ctrl + L`** (o `clear`) → limpia la pantalla.
- **Los colores de `ls`** te dicen el tipo de un vistazo: **azul = carpeta**, color normal = archivo (más adelante: verde = ejecutable, cian = enlace).
