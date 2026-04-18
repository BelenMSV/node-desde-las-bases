# Módulo 03: El Ecosistema, NPM y Herramientas CLI

Bienvenido al tercer módulo del curso. Hasta ahora hemos trabajado exclusivamente con las herramientas que Node.js trae de fábrica. Pero el verdadero poder de Node.js no reside solo en su código base, sino en su comunidad. 

En este módulo daremos el salto al ecosistema abierto: aprenderemos a descargar y gestionar paquetes de terceros utilizando NPM, protegeremos nuestros repositorios de la temida carpeta `node_modules`, y pasaremos de escribir simples scripts a desarrollar verdaderas herramientas de línea de comandos (CLIs) que podrás instalar globalmente en tu sistema.

**⏱️ Tiempo estimado de estudio y práctica:** 3 - 4 horas.

## 🎯 Objetivos de Aprendizaje

- Instalar y gestionar dependencias externas utilizando NPM.
- Entender el Versionado Semántico (SemVer), el peligro del "Caret" (`^`) y el propósito del `package-lock.json`.
- Configurar un entorno de proyecto profesional creando un `.gitignore` y atajos con NPM Scripts.
- Diferenciar entre dependencias de producción, dependencias de desarrollo y el uso del comando `npx`.
- Leer y procesar parámetros de la terminal (`process.argv`) para crear programas interactivos.
- Convertir un script de Node.js en un comando global del sistema utilizando el *Shebang* y `npm link`.

## 📚 Temario Detallado

1. **[NPM y Dependencias Externas](./01-npm-y-dependencias-externas/README.md)**
   > Abriremos la puerta al código de terceros. Instalaremos nuestra primera librería (`ms`), analizaremos qué ocurre bajo el capó (el `package.json` y `node_modules`) y aprenderemos a leer las versiones de los paquetes.

2. **[Protegiendo el repo: .gitignore y NPM Scripts](./02-gitignore-y-npm-scripts/README.md)**
   > Subir la carpeta `node_modules` a GitHub es un error fatal. Aprenderemos a ignorarla correctamente y configuraremos "Scripts" en nuestro `package.json` para ejecutar tareas largas con atajos rápidos.

3. **[Dependencias de Desarrollo y el comando npx](./03-dependencias-desarrollo-y-npx/README.md)**
   > Descubre la diferencia entre instalar una librería para tu app (`dependencies`) y una herramienta para ti como programador (`devDependencies`). Además, aprenderemos a ejecutar paquetes sin instalarlos usando `npx`.

4. **[Creando nuestro primer CLI (El clon de ls)](./04-primer-cli-clon-ls/README.md)**
   > Nos pondremos manos a la obra. Crearemos un programa de terminal interactivo capaz de leer argumentos para listar el contenido de una carpeta, calcular el tamaño de los archivos y diferenciar entre iconos y directorios.

5. **[De script a comando global (Shebang y npm link)](./05-de-script-a-comando-global/README.md)**
   > El broche de oro. Haremos que nuestro programa deje de ejecutarse con `node cli.js` para convertirse en un comando real (como `mi-ls`) que podamos usar desde cualquier carpeta de nuestro ordenador.

---

[🔙 Volver al Índice Global](../README.md)