# Tema 07: NPM y Dependencias Externas

En el tema anterior vimos que el tiempo de actividad de nuestro sistema (`os.uptime()`) se mostraba en segundos, lo cual era bastante difícil de leer de un vistazo. 

Como Node.js no tiene una herramienta nativa para formatear esto fácilmente, vamos a solucionarlo instalando nuestro primer **paquete externo** (código creado por otros desarrolladores).

## 🧰 1. La librería `ms` (Vercel)

Hay un paquete muy pequeñito y popular creado por Vercel llamado `ms`. Esta librería hace exactamente lo que necesitamos: transforma textos legibles en milisegundos y viceversa.

Fíjate en lo sencillo que es su funcionamiento:

```javascript
// Transformar de texto a milisegundos:
ms('2 days')  // Devuelve: 172800000
ms('10h')     // Devuelve: 36000000
ms('1m')      // Devuelve: 60000

// Transformar de milisegundos a texto corto:
ms(60000)         // Devuelve: "1m"
ms(ms('10 hours'))// Devuelve: "10h"

// Transformar de milisegundos a texto largo (legible):
ms(60000, { long: true }) // Devuelve: "1 minute"
ms(7200000, { long: true }) // Devuelve: "2 hours"
```

Esto es exactamente lo que necesitamos para simplificar la salida de nuestro script y hacerla legible para cualquier humano.

---

## 📦 2. Gestores de paquetes y cómo instalar `ms`

Para poder usar esta librería en nuestro proyecto, necesitamos descargarla. Normalmente, si vas al repositorio oficial de cualquier librería, te indicarán el comando exacto para instalarla usando un **gestor de paquetes**.

Existen muchos gestores de paquetes en el ecosistema de JavaScript:
* `npm` (El oficial y el que viene instalado por defecto con Node.js).
* `pnpm` (Una alternativa más rápida y moderna, muy usada por profesionales).
* `yarn` (Creado por Facebook, muy popular hace unos años).
* `bun` y `deno` (Nuevos entornos que traen sus propios gestores).

No hay uno estrictamente mejor que otro, puedes usar el que más rápido te vaya o con el que te sientas más cómodo. En nuestro caso, para empezar, vamos a utilizar **NPM**, que es el estándar universal.

Abre tu terminal, asegúrate de estar en la carpeta de tu proyecto (donde tienes el archivo `package.json`) y ejecuta el siguiente comando:

```bash
npm install ms
```

## 🔍 3. ¿Qué ocurre al instalar una dependencia?

Al ejecutar el comando `npm install ms` dentro de la carpeta donde tienes tu `package.json`, NPM hace su magia. Si te fijas en tu proyecto, han ocurrido tres cosas muy importantes:

### 1. Actualización del `package.json`
Tu archivo `package.json` ahora tiene una nueva sección llamada `dependencies`. Esto registra que tu proyecto necesita obligatoriamente esta librería para funcionar.

```json
{
  "name": "ejemplos-de-node",
  "version": "0.0.1",
  "main": "index.js",
  "type": "module",
  "author": "Belen",
  "dependencies": {
    "ms": "^2.1.3"
  }
}
```

*(Nota: Hablaremos de ese misterioso simbolito `^` más adelante).*

### 2. Creación de la carpeta `node_modules`
Como su nombre indica, aquí se guardan los módulos de Node instalados. Si entras, verás una carpeta `ms` que contiene todo el código fuente de la librería (su propio `index.js`, su licencia, su `README`, etc.). **Importante:** Nunca debes modificar los archivos dentro de `node_modules` a mano.



### 3. Creación del archivo `package-lock.json`
Este archivo es vital. Actúa como una **fotografía (snapshot)** exacta de las versiones que se han instalado. 
* **Reproducibilidad:** Si otra persona se descarga tu proyecto, este archivo le obliga a instalar *exactamente* la misma versión que tú (por ejemplo, la `2.1.3`), evitando que el código se rompa por actualizaciones imprevistas.
* **Velocidad:** Como ya sabe exactamente qué versión descargar, NPM no tiene que perder tiempo preguntándole al servidor qué versiones hay disponibles; va directo a descargarla, haciendo las instalaciones mucho más rápidas.

---

## 🚀 4. Usando la dependencia en nuestro código

Ahora que ya tenemos `ms` instalado, vamos a integrarlo en nuestro script `system-info.js`. 

Para importarlo, usaremos exactamente el mismo nombre que aparece registrado en nuestro `package.json`. Además, como `os.uptime()` nos da el tiempo en segundos, tendremos que multiplicarlo por `1000` para pasarlo a milisegundos, que es lo que espera la librería `ms`.

Actualiza tu archivo así:

```javascript
// system-info.js
import os from 'node:os';
import ms from 'ms'; // Importamos la dependencia recién instalada

console.log('--- Información del sistema operativo ---');
console.log('Tipo de SO:', os.type());
console.log('Plataforma:', os.platform());
console.log('Arquitectura:', os.arch());
console.log('Memoria total (bytes):', os.totalmem());
console.log('Memoria libre (bytes):', os.freemem());
console.log('Directorio home del usuario:', os.homedir());

// 1. Obtenemos los segundos y los pasamos a milisegundos
const uptimeInMs = os.uptime() * 1000;

// 2. Usamos ms() para formatearlo de forma legible
console.log('Tiempo de actividad:', ms(uptimeInMs, { long: true }));

console.log('CPUS:', os.cpus());
console.log('Interfaces de red:', os.networkInterfaces());
console.log('-----------------------------------------');
```

Si ejecutas de nuevo node `system-info.js`, verás que la consola ya no te muestra algo como `117000`, sino que te dice maravillas legibles como `1 day` o `32 hours`.

¡Felicidades! Has instalado tu primera dependencia externa y la has usado para mejorar la legibilidad de tu programa.

## 🧠 5. Semantic Versioning (SemVer) y el peligro del "Caret" (`^`)



Si vuelves a mirar tu `package.json`, verás que la versión de `ms` dice algo como `"^2.1.3"`. ¿Qué son esos tres números y qué hace ese acento circunflejo o "caret" (`^`) ahí metido? No, no está ahí porque quede bonito.

Las dependencias en Node.js usan **SemVer (Versionado Semántico)**. Consiste en tres números separados por puntos: `Major.Minor.Patch` (Mayor.Menor.Parche).

* **Mayor (Major - ej. el 2):** Se actualiza cuando hay *breaking changes* (cambios muy bestias). Si actualizas una dependencia y cambia este número (ej. pasa a la versión 3.0.0), es muy probable que hayan cambiado el nombre de funciones o la forma de usarlas, y **tu código se rompa**.
* **Menor (Minor - ej. el 1):** Se actualiza cuando se añaden nuevas características (*features*). Es totalmente retrocompatible. Tu código actual seguirá funcionando sin problemas.
* **Parche (Patch - ej. el 3):** Se actualiza cuando solo se arreglan *bugs* o vulnerabilidades de seguridad.

### ⚠️ El símbolo Caret (`^`)

Este símbolo significa: *"Instálame cualquier versión nueva siempre y cuando **no cambie la versión Mayor**"*. 

Es decir, si tienes `^2.1.3` y mañana el creador publica la versión `2.9.9` (nuevas *features* y *bugs* arreglados), NPM la instalará automáticamente la próxima vez. Pero si sale la `3.0.0`, la ignorará para proteger tu código de roturas.

> **💡 Consejo profesional:** Aunque la teoría del SemVer suena genial, en el mundo real cada desarrollador publica sus paquetes como quiere y a veces meten cambios que rompen el código en una versión "Menor". Además, últimamente han ocurrido ataques de seguridad aprovechando estas actualizaciones automáticas en rangos amplios.
> 
> Si quieres tener un **control absoluto** y asegurarte de que tu proyecto siempre instale la versión exacta con la que tú probaste que todo funcionaba, **borra el `^` manualmente** del `package.json` y déjalo de forma estricta: `"ms": "2.1.3"`.