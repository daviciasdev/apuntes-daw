# Ciclo de Git — Bloque 0

> Chuleta del flujo de trabajo básico de Git: de tu carpeta local a GitHub.
> Da por hecho que Git ya está instalado y configurado (ver chuleta de Git/GitHub/SSH).

---

## El modelo de las tres zonas

En Git tus archivos pasan por tres zonas. Entenderlo es la clave de todo:

1. **Directorio de trabajo** → tus archivos tal cual, donde editas.
2. **Área de preparación** (*staging*) → la "sala de espera" con lo que entrará en la próxima foto. Se llena con `git add`.
3. **Repositorio** → donde quedan guardadas las "fotos" (*commits*). Se graba con `git commit`.

**Flujo:** editar → `git add` (preparar) → `git commit` (hacer la foto) → `git push` (subir a la nube).

---

## 1. Iniciar un repositorio

```bash
git init        # convierte la carpeta actual en repositorio (crea .git)
git status      # ¿en qué estado está cada archivo?
```

`git status` es tu brújula: úsalo a menudo para ver qué archivos están sin seguimiento, preparados o pendientes.

---

## 2. El archivo `.gitignore`

Un archivo de texto que lista lo que Git debe **ignorar** (no versionar): código compilado (`out/`, `target/`), configuración del IDE, dependencias, secretos... Se versiona el **código fuente**, no lo que se genera automáticamente.

---

## 3. Preparar y confirmar

```bash
git add archivo.txt    # prepara un archivo concreto
git add .              # prepara TODO lo de la carpeta (respeta .gitignore)
git status             # comprobar qué hay preparado
git commit -m "Mensaje descriptivo del cambio"
```

- **Mensaje de commit**: corto y claro, que diga qué incluye el cambio.

---

## 4. Corregir el último commit

```bash
git commit --amend -m "Mensaje corregido"
```

- **`--amend`** reescribe el último commit (cambia el mensaje o añade archivos olvidados). Genera un commit nuevo, con identificador nuevo.
- ⚠️ Solo en commits que **aún NO has subido** (`push`). Reescribir commits ya compartidos causa problemas.

---

## 5. Ver el historial

```bash
git log              # historial completo
git log --oneline    # versión compacta (identificador + mensaje)
```

- Si `git log` abre un visor a pantalla completa, sal pulsando **`q`**.

---

## 6. Conectar con GitHub y subir (primera vez)

**Antes:** crea el repositorio en GitHub **VACÍO** (sin README, sin .gitignore, sin licencia). Si no, los historiales chocan y el `push` se rechaza.

```bash
git remote add origin git@github.com:USUARIO/REPO.git   # enlaza con GitHub (URL SSH)
git branch -M main                                       # asegura que la rama es 'main'
git push -u origin main                                  # sube 'main' y enlaza con la remota
```

- **`origin`** → nombre convencional del remoto principal.
- **`-u`** → enlaza tu rama local con la remota (para los próximos `push`).
- Usa la URL **SSH** (`git@github.com:...`), no la HTTPS.

---

## 7. El día a día (después del primer push)

Una vez enlazado con `-u`, cada cambio es:

```bash
# 1. editas tus archivos
git add .
git commit -m "Qué has cambiado"
git push           # basta con esto; ya sabe a dónde va
```

---

## Resumen visual

```
  editar          git add           git commit          git push
[ trabajo ]  -->  [ staging ]  -->  [ repo local ]  -->  [ GitHub ]
```

---

## Comandos de un vistazo

| Comando | Qué hace |
|---|---|
| `git init` | Crea un repositorio en la carpeta actual. |
| `git status` | Muestra el estado de los archivos. |
| `git add .` | Prepara los cambios (staging). |
| `git commit -m "..."` | Graba una foto con mensaje. |
| `git commit --amend -m "..."` | Corrige el último commit (sin subir). |
| `git log --oneline` | Historial compacto. |
| `git remote add origin <url>` | Enlaza con un repo remoto. |
| `git push -u origin main` | Sube por primera vez y enlaza la rama. |
| `git push` | Sube cambios (tras el primer push con `-u`). |
