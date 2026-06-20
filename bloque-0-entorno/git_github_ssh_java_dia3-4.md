# Git, GitHub, SSH y Java — Días 3-4

> Resumen de la configuración del entorno: control de versiones, cuenta en la nube,
> autenticación por SSH e instalación de Java. Sirve también como guía para repetirlo
> en otro equipo.

---

## 1. Conceptos clave

- **Git** → sistema de **control de versiones**. Vive en tu portátil. Lleva el historial de cambios de tus archivos: guarda "fotos" (*commits*), te deja volver atrás y ver qué cambió.
- **GitHub** → **servicio en la nube** donde alojas tus repositorios. Copia de seguridad, sitio para compartir y portfolio.
- En resumen: **Git es la herramienta; GitHub es el sitio donde guardas lo que la herramienta gestiona.**

---

## 2. Instalar Git

```bash
git --version                 # comprobar si ya está instalado
sudo apt update && sudo apt install git   # instalar si no lo está
```

---

## 3. Configurar Git

Cada *commit* se sella con tu nombre y email, así que hay que decírselo a Git:

```bash
git config --global user.name "Tu Nombre"
git config --global user.email "tuemail@ejemplo.com"
git config --global init.defaultBranch main
git config --list             # ver la configuración actual
```

- `--global` → vale para todos tus proyectos (se guarda en `~/.gitconfig`).
- ⚠️ El **email debe coincidir** con un correo verificado en tu cuenta de GitHub, para que reconozca tus commits como tuyos.
- Solo el **email** tiene que coincidir entre Git y GitHub; el `user.name` y el username de GitHub no necesitan ser iguales entre sí.
- Se puede cambiar las veces que quieras (sobrescribe el valor anterior).

---

## 4. Cuenta de GitHub

- Registro en <https://github.com> (Sign up).
- Usa el **mismo email** que en `git config`.
- El **username es público y semipermanente** (aparece en las URLs y en tu portfolio): elígelo profesional y consistente.
- El plan **gratuito (Free)** incluye repositorios públicos y privados ilimitados.

---

## 5. Autenticación por SSH

**Idea:** un par de claves enlazadas. La **privada** se queda en tu portátil (secreta, nunca se comparte); la **pública** se la das a GitHub. Permite conectarte sin teclear la contraseña cada vez.

### Pasos

```bash
# 1. Comprobar si ya tienes claves
ls -al ~/.ssh

# 2. Generar el par de claves (cambia el email)
ssh-keygen -t ed25519 -C "tuemail@ejemplo.com"
#    - "Enter file..."  -> Enter (ubicación por defecto)
#    - "Enter passphrase..." -> frase de contraseña (recomendado) o vacío

# 3. Mostrar la clave PÚBLICA para copiarla
cat ~/.ssh/id_ed25519.pub

# 4. Probar la conexión (después de añadirla en GitHub)
ssh -T git@github.com
```

**Añadir la clave pública en GitHub:**
Foto de perfil → **Settings** → **SSH and GPG keys** → **New SSH key** → pegar la línea de `id_ed25519.pub` → darle un título (p. ej. el nombre del equipo) → **Add SSH key**.

**Notas:**
- ⚠️ Comparte solo el archivo terminado en **`.pub`**. La clave privada (`id_ed25519` sin `.pub`) **nunca** se comparte.
- En `ssh -T git@github.com`, la primera vez pide confirmar la huella del servidor → escribir `yes`.
- Respuesta correcta: `Hi <usuario>! You've successfully authenticated...`. La parte *"does not provide shell access"* es **normal**, no es un error.

---

## 6. Instalar Java (JDK)

- Instala **OpenJDK** (libre y gratuito), no el de Oracle.
- **JDK** (incluye compilador, para desarrollar) ≠ **JRE** (solo para ejecutar). Necesitas el JDK.
- Versión recomendada: **Java 21 LTS** (máxima compatibilidad con material y con Spring). Java 25 LTS es la más nueva, como alternativa.

```bash
sudo apt update && sudo apt install openjdk-21-jdk
java -version      # comprueba el motor de ejecución
javac -version     # comprueba el compilador (confirma que es el JDK completo)
```

---

## Flujo recomendado para los apuntes

1. Copia de trabajo en una **carpeta local** del portátil.
2. **Versionar en GitHub** (repo del diario de aprendizaje): `editar → commit → push`.
3. Opcional: subir las chuletas más útiles al proyecto para tenerlas a mano.
