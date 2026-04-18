# Tema 03: Dependencias de Desarrollo y el comando npx

En el Tema 01 instalamos la librería `ms`. Si te fijas en tu `package.json`, esa librería se guardó dentro del bloque `"dependencies"`. 

Esto tiene todo el sentido del mundo: tu código usa `ms` para transformar los segundos en texto. Si el día de mañana subimos este proyecto a un servidor en internet, el servidor **necesita** descargar `ms` o la aplicación se romperá (*crash*). 

Pero, ¿qué pasa con las herramientas que usamos **solo nosotros como programadores** para hacernos la vida más fácil?

---

## 🛠️ 1. Dependencies vs. devDependencies

Imagina que estás construyendo una casa.
* **Dependencies (Dependencias de producción):** Son los ladrillos, el cemento, las tuberías y los cristales. La casa (tu aplicación) los necesita para existir y funcionar. Ejemplos: `ms`, `express`, `react`.
* **devDependencies (Dependencias de desarrollo):** Son los andamios, la hormigonera, tu casco y tus guantes. Tú (el desarrollador) las necesitas para construir la casa más rápido y mejor, pero una vez la casa está terminada y la gente entra a vivir, no dejas la hormigonera en medio del salón. Ejemplos: formateadores de código (`prettier`), herramientas de testing (`jest`) o recargadores automáticos (`nodemon`).

### ¿Cómo instalamos una dependencia de desarrollo?

Vamos a instalar una herramienta muy famosa llamada `standard`, que sirve para avisarnos si escribimos código feo o con malas prácticas (un *linter*).

Para decirle a NPM que esta herramienta es solo para nosotros, añadimos la bandera `-D` (o `--save-dev`) al final del comando:

```bash
npm install standard -D
```

Si ahora abres tu `package.json`, verás que NPM ha creado una sección completamente nueva:

```json
{
  "name": "ejemplos-de-node",
  "version": "0.0.1",
  "main": "index.js",
  "type": "module",
  "scripts": {
    "start": "node system-info.js"
  },
  "dependencies": {
    "ms": "^2.1.3"
  },
  "devDependencies": {
    "standard": "^17.1.0"
  }
}
```

¿Por qué importa tanto esto? Porque cuando subas tu código a un servidor real (como AWS o Vercel), el servidor ejecutará un comando especial (`npm install --production`) que **ignorará todas las devDependencies**, ahorrando muchísimo tiempo de instalación, ancho de banda y espacio en disco.

---

## 🌍 2. El problema de las instalaciones globales

Históricamente, cuando los desarrolladores querían usar herramientas de terminal (como `standard` o `nodemon`), las instalaban de forma global en su ordenador usando `npm install -g nodemon`.

**Esto hoy en día se considera una mala práctica.** Si instalas las cosas de forma global, estás atando tu ordenador a una versión específica. ¿Qué pasa si tienes un proyecto antiguo que necesita la versión 1 de una herramienta, y un proyecto nuevo que necesita la versión 3? ¡Conflicto total!

La regla actual es: **Instala siempre las herramientas localmente en cada proyecto (como devDependencies)**. 

Pero aquí surge un problema... Si instalo algo localmente, mi terminal no lo reconoce. Si escribo `standard` en la consola, me dirá `command not found` (comando no encontrado).

---

## 🦸‍♂️ 3. La llegada del héroe: `npx` (Node Package Execute)

Para solucionar este problema, NPM introdujo un comando mágico llamado **`npx`** (que ya viene instalado en tu ordenador junto con Node).

`npx` hace dos cosas maravillosas:

### Superpoder 1: Ejecutar paquetes locales
Si tienes una herramienta instalada en tus `devDependencies` (como acabamos de hacer con `standard`), `npx` es capaz de buscarla dentro de tu carpeta `node_modules` y ejecutarla.

Pruébalo en tu terminal:
```bash
npx standard
```
*(Si tienes algún error de formato en tu código, esta herramienta te lo escupirá por pantalla. ¡Pruébalo!)*

### Superpoder 2: Ejecutar paquetes ¡SIN INSTALARLOS!
Este es el uso más espectacular de `npx`. Si le pides a `npx` que ejecute un paquete que **no** tienes instalado, buscará ese paquete en internet, lo descargará en una carpeta temporal, lo ejecutará, y cuando termine... **lo borrará automáticamente sin dejar rastro en tu proyecto**.

Vamos a probarlo. Ejecuta este comando (es un clásico de la comunidad que dibuja una vaca hablando):

```bash
npx cowsay "¡He dominado npx!"
```

Verás a la vaca en tu terminal, pero si miras tu `package.json`, ¡no se ha instalado nada!

> **💡 El mundo real:** ¿Te suena el comando `npx create-react-app` o `npx create-next-app`? Ahora ya sabes lo que hacen. No instalan un generador de proyectos de React en tu ordenador para siempre; simplemente lo descargan de forma temporal, generan tu proyecto y se esfuman.

---

## 🎯 Resumen de la clase
1. Usa `npm install <paquete>` para cosas que tu código necesita para funcionar en el servidor (ej: bases de datos).
2. Usa `npm install <paquete> -D` para herramientas que te ayudan a programar (ej: testing, linters).
3. Usa `npx <comando>` para ejecutar esas herramientas locales o para probar paquetes de internet sin ensuciar tu disco duro.

¡Con este conocimiento, estás listo para dejar de ser un consumidor de paquetes y empezar a crearlos! En el próximo tema crearemos nuestro propio CLI interactivo.