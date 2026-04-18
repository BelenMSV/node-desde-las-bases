# Tema 04: Primer proyecto y el Sistema de Módulos

Ya tenemos Node.js instalado correctamente en nuestra máquina. Ahora vamos a abrir nuestro editor de código (como VS Code) en una carpeta completamente vacía y a empezar a escribir nuestro primer código nativo.

---

## 👋 1. Nuestro primer "Hello World"

Vamos a crear un archivo llamado `index.js`. Para asegurarnos de que todo funciona, escribiremos el código más básico posible:

```javascript
// index.js
console.log('¡Hello world! :D');
```

Para ejecutar este archivo, abrimos la terminal integrada de nuestro editor y le decimos a Node.js qué archivo queremos que procese:

```bash
node index.js
```
Si ves el mensaje `¡Hello world! :D` en tu terminal, ¡felicidades! Tu entorno está perfectamente configurado y funcionando.

---

## 🧩 2. Creando nuestros propios módulos

En cualquier proyecto real, no vas a tener todo el código en un solo archivo. Necesitas dividirlo. Vamos a crear un segundo archivo llamado `math.js` con una función sencilla:

```javascript
// math.js
export const sum = (a, b) => a + b;
```
Ahora, intentemos importar esta función en nuestro `index.js` principal:

```javascript
// index.js
import { sum } from "./math"; // 👈 Aquí lo importamos a la manera del Frontend, sin la extensión

const result = sum(2, 3);
console.log(`The sum of 2 and 3 is ${result}`);
```
Si intentas ejecutar `node index.js` ahora mismo pero **sin poner el .js al final de la ruta**, te dará un error diciendo que no encuentra el módulo. ¿Por qué?

---

## ⚠️ 3. El error de la extensión: Node.js vs Frontend

Este es uno de los errores más comunes cuando vienes del Frontend.

En el Frontend (usando herramientas como Vite, Webpack o Next.js), poner la extensión `.js` al importar es opcional. ¿Por qué? Porque el código de Frontend tiene un paso de **construcción (Build time)**: el empaquetador tiene tiempo de buscar en las carpetas y adivinar si el archivo termina en `.js`, `.ts` o `.jsx`.

Node.js, por el contrario, ejecuta el código **en tiempo de ejecución (Runtime)**. No va a perder milisegundos intentando adivinar extensiones. Busca exactamente lo que le pides. 

La forma correcta de importarlo en Node es incluyendo siempre la extensión:

```javascript
// index.js
import { sum } from "./math.js"; // 👈 Fíjate en el .js

const result = sum(2, 3);
console.log(`The sum of 2 and 3 is ${result}`);
```
Si lo ejecutas ahora, funcionará, pero es probable que veas un **Warning (Advertencia)** en la consola sobre el tipo de módulo. Vamos a arreglarlo entendiendo un poco de historia.

---

## 🕰️ 4. CommonJS vs. ES Modules

Cuando Node.js se creó hace años, JavaScript no tenía un sistema nativo para importar y exportar archivos. Por lo tanto, los creadores de Node tuvieron que inventarse uno propio. A ese sistema se le llamó **CommonJS**.

Con el paso de los años, JavaScript evolucionó y estandarizó una forma oficial de hacerlo (los **ES Modules**, que usan `import` y `export`). Node.js hoy en día soporta ambos, pero necesita que le aclaremos cuál queremos usar para no darnos advertencias.

Para que lo reconozcas si ves código antiguo, así se veía **CommonJS**:

```javascript
// math.js (Formato antiguo CommonJS)
const sum = (a, b) => a + b;
module.exports = { sum };

// index.js (Formato antiguo CommonJS)
const { sum } = require('./math.js');
```
**Nosotros usaremos el estándar moderno (ES Modules con `import`/`export`).**

---

## ⚙️ 5. El archivo package.json

Para decirle a Node.js que queremos usar el sistema moderno y quitar esa advertencia de la consola, necesitamos crear el archivo de configuración central de cualquier proyecto de Node: el `package.json`.

Crea un archivo llamado `package.json` en la raíz de tu carpeta con este contenido:

```json
{
  "name": "ejemplos-de-node",
  "version": "0.0.1",
  "main": "index.js",
  "type": "module",
  "author": "Belen"
}
```

La línea clave aquí es **`"type": "module"`**. 
Con esto le estamos diciendo estrictamente a Node.js: *"Usa el sistema de módulos moderno (ES Modules) por defecto"*.

Si ahora vuelves a ejecutar `node index.js`, verás que el resultado aparece perfecto, rapidísimo y sin ninguna advertencia en la consola.