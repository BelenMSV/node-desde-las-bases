# Tema 02: Protegiendo el repositorio: .gitignore y NPM Scripts

En la clase anterior hicimos nuestra primera instalación con NPM. Seguramente notaste que en tu proyecto apareció una carpeta llamada `node_modules`. Si entras en ella para cotillear, verás la carpeta de `ms`... ¡pero en proyectos reales, esta carpeta puede llegar a pesar Gigabytes y contener decenas de miles de archivos!

Aquí surge la primera regla de oro del desarrollo con Node.js.

---

## 🕳️ 1. El agujero negro llamado `node_modules`

Imagina que quieres compartir la receta de una tarta con un amigo. ¿Le envías un papel con las instrucciones y los ingredientes, o le envías un camión de mudanzas con un saco de harina, tres docenas de huevos, un horno y la vaca entera?

* Tu código y tu `package.json` son **la receta**.
* La carpeta `node_modules` es **el camión con la vaca**.

**Nunca, jamás, bajo ningún concepto, debes subir la carpeta `node_modules` a GitHub o compartirla con otros desarrolladores.** Si otro programador se descarga tu proyecto, solo necesita tu `package.json`. Al ejecutar `npm install` en su máquina, NPM leerá esa "receta" y descargará todo lo necesario directamente desde internet, creando su propio `node_modules` local en cuestión de segundos.

---

## 🛡️ 2. El escudo protector: `.gitignore`

Para evitar subir esta carpeta por accidente al hacer un commit en Git, utilizamos un archivo especial llamado `.gitignore`. 

Este archivo le dice a Git: *"Oye, ignora por completo todo lo que esté escrito en esta lista. Haz como si no existiera"*.

### Paso a paso:
1. Crea un archivo en la raíz de tu proyecto llamado exactamente **`.gitignore`** (con el punto delante).
2. Ábrelo y escribe el nombre de la carpeta que queremos ignorar:

```text
# Dependencias
node_modules/

# Archivos de entorno (que veremos más adelante)
.env
```

A partir de este momento, Git se volverá ciego ante la carpeta `node_modules`. Ya puedes hacer `git add .` y `git commit` con total tranquilidad.

> **💡 Pregunta de entrevista:** ¿Debemos ignorar también el archivo `package-lock.json`? 
> **Respuesta:** ¡NO! El `package-lock.json` **sí se sube al repositorio**. Recuerda que es la fotografía exacta de las versiones que instalaste. Garantiza que tu equipo o tu servidor de producción instalen exactamente las mismas versiones que te funcionaron a ti.

---

## ⚡ 3. NPM Scripts: Los atajos de tu terminal

Ahora que nuestro proyecto está seguro, vamos a aprender a utilizar otra de las herramientas más potentes del `package.json`: la sección de **Scripts**.

Hasta ahora, para ejecutar tus archivos tenías que escribir comandos largos como:
`node --permission --allow-fs-read="*" manage-files.js`

Esto es agotador y muy propenso a errores tipográficos. Los **NPM Scripts** nos permiten crear "alias" o atajos de teclado para no tener que memorizar comandos interminables.

Abre tu `package.json` y busca la sección `"scripts"`. Vamos a añadir nuestros propios atajos:

```json
{
  "name": "ejemplos-de-node",
  "version": "0.0.1",
  "main": "index.js",
  "type": "module",
  "scripts": {
    "start": "node system-info.js",
    "files": "node --permission --allow-fs-read=\"./archivo.txt\" --allow-fs-write=\"./output/*\" manage-files.js",
    "saludar": "node script.js Belen"
  },
  "dependencies": {
    "ms": "^2.1.3"
  }
}
```

---

## 🚀 4. Ejecutando nuestros Scripts

Para lanzar estos atajos desde la terminal, utilizamos el comando `npm run <nombre-del-script>`.

Prueba a ejecutar estos comandos en tu terminal:

* `npm run files` (Ejecutará el script