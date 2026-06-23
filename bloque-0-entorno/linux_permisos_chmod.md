# Linux: Permisos de archivos (`chmod`)

> **Bloque 0 — Entorno** · Linux básico (parte 1)

---

## 1. Leer permisos con `ls -l`

Cada línea de `ls -l` empieza con 10 caracteres:

```
-rw-r--r--
^^^^^^^^^^
| └──────┴─ 9 letras de permisos (3 grupos de 3)
└────────── tipo de archivo
```

- **1.ª letra → tipo:** `-` archivo, `d` directorio.
- **9 letras → permisos**, repartidas en tres grupos de tres.

---

## 2. Los tres grupos

| Grupo | Quién es |
|-------|----------|
| **u** (*user*) | El **dueño** del archivo |
| **g** (*group*) | El **grupo** del archivo |
| **o** (*others*) | **Todos los demás** |

Cada grupo tiene tres permisos posibles:

- `r` → **lectura**
- `w` → **escritura**
- `x` → **ejecución** (en directorios: **entrar / atravesar**)

> ⚠️ Los permisos se aplican según **tu relación con el archivo**, no según quién esté logueado. Linux comprueba en orden: ¿eres el dueño? → ¿estás en su grupo? → si no, otros. Y aplica **solo la primera terna que encaje**.

Ejemplo:

```
-rw-r--r--  1 david david  220 jun 23 README.md
            ^^^^^^^^^^^^^
       dueño = david, grupo = david
```

- `rw-` dueño: lee y escribe
- `r--` grupo: solo lee
- `r--` otros: solo lee

---

## 3. Notación octal

Cada permiso vale un número; se **suman** dentro de cada grupo:

| Permiso | Valor |
|---------|-------|
| `r` | **4** |
| `w` | **2** |
| `x` | **1** |

> 🔑 **Ancla: `r w x` = `4 2 1`** (en el mismo orden en que se escriben).

Cada grupo → un dígito (0–7). El archivo entero → **tres dígitos**.

| Octal | rwx | | Octal | rwx |
|-------|-----|---|-------|-----|
| 7 | `rwx` | | 3 | `-wx` |
| 6 | `rw-` | | 2 | `-w-` |
| 5 | `r-x` | | 1 | `--x` |
| 4 | `r--` | | 0 | `---` |

Ejemplo: `rw-r--r--` → **644**

---

## 4. `chmod` — dos formas

### a) Octal (absoluta: fija los tres grupos de golpe)

```bash
chmod 644 notas.txt      # rw-r--r--
chmod 755 script.sh      # rwxr-xr-x
chmod 600 id_ed25519     # rw------- (privado)
```

### b) Simbólica (relativa: toca solo lo que nombras)

Estructura: **[quién][operación][permiso]**

- **Quién:** `u` `g` `o` `a` (a = todos)
- **Operación:** `+` añadir · `-` quitar · `=` dejar exactamente eso
- **Permiso:** `r` `w` `x`

```bash
chmod u+x script.sh      # añade ejecución SOLO al dueño
chmod go-w archivo.txt   # quita escritura a grupo y a otros
chmod a+r documento.pdf  # lectura para todos
```

---

## 5. Relativo vs absoluto (¡cuidado!)

- `+` / `-` → **respetan** los demás permisos. Retocan.
- `=` y el octal → **machacan** el estado anterior. Reemplazan.

Partiendo de `755` (`rwxr-xr-x`):

| Comando | Resultado | Por qué |
|---------|-----------|---------|
| `chmod g+w f` | `775` | Añade `w` al grupo, resto intacto |
| `chmod g=w f` | `725` | El grupo queda **solo** con `w` (pierde `r` y `x`) |

---

## 6. Casos típicos

- **`755`** → directorios y scripts ejecutables. En un directorio, la `x` significa poder **entrar** (`cd`) y acceder a su contenido. Sin `x`, no entras aunque tengas `r`.
- **`644`** → archivo de texto normal.
- **`600`** → archivos privados/sensibles. Las **claves SSH** lo exigen: SSH **se niega a funcionar** si la clave privada tiene permisos más abiertos.

---

### Comandos vistos
`ls -l` · `chmod`
