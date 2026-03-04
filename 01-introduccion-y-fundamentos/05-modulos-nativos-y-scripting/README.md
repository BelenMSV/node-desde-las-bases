# Tema 05: Módulos nativos y nuestro primer Script

Ya sabemos cómo importar y exportar nuestros propios archivos. Pero la verdadera magia de Node.js reside en sus **módulos nativos**: piezas de código que ya vienen instaladas con Node y que nos permiten interactuar directamente con el sistema operativo (leer archivos, ver la memoria, usar la red, etc.).

Vamos a crear nuestro primer script real de automatización. Crea un archivo llamado `manage-files.js` y un archivo de texto llamado `archivo.txt` en la misma carpeta.

```text
// archivo.txt
Hola, este es el contenido del fichero archivo.txt
```

## 📖 1. Leyendo archivos (`node:fs/promises`)

Para interactuar con el sistema de archivos (File System), Node nos da el módulo `fs`. Como queremos usar asincronía moderna, importaremos su versión de promesas.

*Nota: Fíjate que usamos el prefijo `node:` al importar. Es una buena práctica moderna para indicar que es un módulo nativo y no un paquete descargado de NPM.*

```javascript
// manage-files.js
import { readFile } from 'node:fs/promises';

const content = await readFile('./archivo.txt');
console.log(content);
```
Si ejecutas esto con `node manage-files.js`, verás algo raro en la consola: `<Buffer 48 6f 6c 61 2c... >`. 
Node está leyendo el archivo en bytes puros (un Buffer). Para que nosotros podamos leerlo como texto humano, necesitamos pasarle un segundo parámetro indicando la codificación (**UTF-8**):

```javascript
import { readFile } from 'node:fs/promises';

// Ahora sí leemos el texto correctamente
const content = await readFile('./archivo.txt', 'utf-8');
console.log(content);
```

> **⚠️ Cuidado:** Acabas de ver el poder (y el peligro) de Node.js. Al igual que leímos `./archivo.txt`, si le pasamos la ruta correcta, podríamos leer contraseñas o archivos sensibles del sistema operativo. ¡Node tiene acceso total!

---

## ✍️ 2. Escribiendo archivos y creando carpetas

Vamos a hacer el script más interesante: leeremos el archivo, pasaremos su texto a mayúsculas y lo guardaremos en una carpeta nueva que crearemos desde cero.

Para ello, importaremos también `writeFile` y `mkdir` (Make Directory):

```javascript
import { readFile, writeFile, mkdir } from 'node:fs/promises';

const content = await readFile('./archivo.txt', 'utf-8');

// 1. Transformamos el texto
const uppercaseContent = content.toUpperCase();

// 2. Creamos una carpeta de salida (de forma recursiva)
const outputDir = 'output/files/documents';
await mkdir(outputDir, { recursive: true });

// 3. Guardamos el nuevo archivo
await writeFile(`./${outputDir}/archivo-uppercase.txt`, uppercaseContent);

console.log('¡Archivo creado con contenido en mayúsculas!');
```
**¿Qué hace `recursive: true`?** Si la carpeta `output` no existe, la crea. Luego crea `files` dentro, y luego `documents`. Todo de golpe. Sin el recursivo, Node nos daría un error si las carpetas padre no existen.

---

## 🚧 3. El problema de las rutas (Windows vs macOS/Linux)

El código anterior tiene un **gran problema**. Funciona perfecto en macOS y Linux porque usan la barra inclinada hacia adelante (`/`) para separar carpetas. 

Pero Windows utiliza la contrabarra (`\`). Si alguien ejecuta nuestro script tal cual en Windows, es una mala práctica que genera bugs impredecibles. 

Los creadores de Node.js sabían de este problema, por lo que el módulo nativo `fs` (File System) es bastante inteligente y es capaz de entender la barra inclinada (`/`) incluso en Windows. Es decir, el script anterior probablemente funcionaría sin problemas si solo hacemos un `writeFile`.

**¿Dónde está el problema real entonces?** Aunque los módulos internos de Node a veces "traducen" esta barra por ti en operaciones sencillas, si empiezas a mezclar barras, comparas rutas de archivos, o pasas esa ruta a comandos nativos de la terminal y librerías de terceros más estrictas, tu script **dará comportamientos inesperados y bugs difíciles de rastrear**.

Para que nuestro código sea **resiliente, profesional y verdaderamente multiplataforma**, NUNCA debemos escribir las barras de las rutas a mano. Para eso existe el módulo nativo `node:path`.

### La solución: `path.join`

El método `join` coge un montón de trozos de texto y los une usando la barra correcta dependiendo del sistema operativo donde se esté ejecutando.

```javascript
import { join } from 'node:path';

// Node pondrá / en Mac/Linux y \ en Windows automáticamente
const outputDir = join('output', 'files', 'documents'); 
const outputFile = join(outputDir, 'archivo-uppercase.txt');
```

---

## 🕵️‍♂️ 4. Extrayendo información de las rutas

El módulo `node:path` tiene más herramientas geniales. A veces tienes una ruta larguísima y solo quieres saber la extensión del archivo, o su nombre. 
No hagas inventos raros haciendo `.split('.')`, usa las herramientas nativas: `basename` y `extname`.

---

## 🎯 Código Final Completo y Profesional

Así quedaría nuestro script final, a prueba de balas y compatible con cualquier sistema operativo:

```javascript
// manage-files.js
import { mkdir, readFile, writeFile } from 'node:fs/promises';
import { join, basename, extname } from 'node:path';

// 1. Leemos el archivo original
const content = await readFile('./archivo.txt', 'utf-8');

// 2. Construimos la ruta de la carpeta de forma segura
const outputDir = join('output', 'files', 'documents');
await mkdir(outputDir, { recursive: true });

// 3. Transformamos el contenido
const uppercaseContent = content.toUpperCase();

// 4. Construimos la ruta final del archivo
const outputFile = join(outputDir, 'archivo-uppercase.txt');

// 5. Extraemos información útil de la ruta
console.log('La extensión es:', extname(outputFile)); // .txt
console.log('El nombre del archivo es:', basename(outputFile)); // archivo-uppercase.txt

// 6. Guardamos el archivo
await writeFile(outputFile, uppercaseContent);

console.log('✅ Proceso completado con éxito.');
```
¡Con esto ya has creado tu primera automatización (Scripting) con Node.js! Has leído, transformado y creado archivos interactuando directamente con tu sistema operativo de forma segura.