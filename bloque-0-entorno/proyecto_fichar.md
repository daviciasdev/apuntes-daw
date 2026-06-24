# Proyecto: `fichar` — script de fichaje de estudio

> **Bloque 0 — Entorno** · Linux básico (parte 2) · proyecto aplicado

Un comando propio para registrar el tiempo de estudio: `fichar in` al empezar, `fichar out` al terminar. Cada sesión queda guardada en un CSV.

---

## 1. Diseño: dos archivos con papeles distintos

Todo vive en `~/registro-estudio/`:

| Archivo | Papel | Operador |
|---------|-------|----------|
| `.fichaje_actual` | "Post-it" **oculto**: guarda **una** hora de entrada (la sesión abierta) | `>` (sobrescribe) |
| `fichajes.csv` | **Histórico visible**: todas las sesiones cerradas | `>>` (añade) |

> El post-it usa `>` porque solo necesita recordar la entrada vigente. El histórico usa `>>` para no borrar las sesiones anteriores. La hora de entrada no se pierde al machacar el post-it porque, al cerrar, ya se ha copiado al CSV.
>
> **Norma de uso:** hacer `out` antes del siguiente `in` (cerrar una sesión antes de abrir otra).

---

## 2. Conceptos de bash usados

**Variables** (sin espacios alrededor del `=`):
```bash
nombre=valor      # crear
echo $nombre      # usar (con $ delante)
```

**Sustitución de comandos** `$( )` — ejecuta un comando y captura su salida:
```bash
entrada=$(date +%s)        # guarda el resultado de date
entrada=$(cat archivo)     # guarda el contenido de un archivo
```
> El valor se **congela** en el momento de la asignación (es una *foto*, no un reloj en vivo). Por eso sirve para medir intervalos: capturas entrada y salida en instantes distintos y las restas.

**Aritmética** `$(( ))`:
```bash
duracion=$(( salida - entrada ))   # resta
minutos=$(( duracion / 60 ))       # división ENTERA (descarta el resto)
```

---

## 3. El tiempo como segundos (*epoch*)

`date +%s` devuelve los **segundos transcurridos desde el 1/1/1970 00:00 UTC** (el *Unix epoch*):

```bash
date +%s            # p. ej. 1750869644
```

> ¿Por qué guardar segundos y no "18:20"? Porque restar dos números planos es trivial (`b - a`), mientras que restar horas:minutos obliga a lidiar con bases de 60 y cambios de día. Un epoch nunca se rompe, ni cruzando la medianoche.

**Formatear un epoch concreto** con `-d @`:
```bash
date -d @1750869644 +%F        # fecha de ESE instante (2026-06-23)
date -d @1750869644 +%H:%M     # hora de ESE instante (19:09)
```
- El `@` le dice a `date`: "este número son segundos epoch".
- `+%F` → fecha `AAAA-MM-DD` · `+%H:%M` → hora `HH:MM`.

> Esto resuelve un bug real: la fecha y la hora de entrada deben salir del **instante del `in`** (`$entrada`), no de "ahora" (el `out`). Si no, una sesión que cruza medianoche se registraría con el día equivocado.

---

## 4. De comandos a script

**Shebang** (primera línea, siempre): indica con qué intérprete ejecutar el archivo.
```bash
#!/bin/bash
```

**Argumentos:** lo que escribes tras el nombre del script se recoge en `$1`, `$2`...
```bash
# fichar in   ->   $1 vale "in"
```

**Condicional `case`** (elegir según un valor):
```bash
case "$1" in
    in)   comandos ;;     # ;; cierra cada caso
    out)  comandos ;;
    *)    echo "..." ;;   # * = cualquier otro valor (caso por defecto)
esac                      # "case" al revés, cierra el bloque
```

**Rutas absolutas** para que funcione desde cualquier carpeta: se define la carpeta una vez (con `$HOME`) y se usa en cada archivo.
```bash
DIR="$HOME/registro-estudio"
... > "$DIR/.fichaje_actual"
```
> Sin esto, los archivos se crearían "donde estés al ejecutar", y los datos se desperdigarían.

**Permiso de ejecución** (reutilizando `chmod` de la parte 1):
```bash
chmod +x fichar
```

**Alias** para llamarlo como `fichar` desde cualquier sitio (sin `./`):
```bash
echo "alias fichar='$HOME/registro-estudio/fichar'" >> ~/.bashrc
source ~/.bashrc    # recarga la config sin reabrir la terminal
```

---

## 5. El script completo

```bash
#!/bin/bash
DIR="$HOME/registro-estudio"

case "$1" in
    in)
        date +%s > "$DIR/.fichaje_actual"
        ;;
    out)
        entrada=$(cat "$DIR/.fichaje_actual")
        salida=$(date +%s)
        duracion_seg=$(( salida - entrada ))
        duracion_min=$(( duracion_seg / 60 ))
        echo "$(date -d @$entrada +%F),$(date -d @$entrada +%H:%M),$(date -d @$salida +%H:%M),$duracion_min" >> "$DIR/fichajes.csv"
        ;;
    *)
        echo "Uso: fichar in|out"
        ;;
esac
```

---

## 6. Uso

```bash
fichar in                          # al empezar a estudiar
fichar out                         # al terminar
cat ~/registro-estudio/fichajes.csv   # ver el histórico
```

Cada línea del CSV: `fecha,hora_entrada,hora_salida,minutos`
Ejemplo: `2026-06-23,19:00,20:30,90`

---

### Ideas para mejorarlo más adelante
- `fichar resumen` → sumar los minutos de la semana.
- Avisar si haces `out` sin un `in` previo.
