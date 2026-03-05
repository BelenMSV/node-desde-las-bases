# Tema 06: El módulo OS e Información del Sistema

Vamos con otro módulo nativo muy interesante que tiene Node.js, que es el de exponer información de la máquina. Esto puede ser muy útil para tus scripts. Seguro que hay un montón de programas que has visto alguna vez que ejecutas en la terminal y te dicen cuánta memoria tienes libre, cuánto estás utilizando de CPU, cuántas CPUs tienes y todo esto. 

Con JavaScript y Node.js, podemos hacer exactamente lo mismo.

## 💻 1. Nuestro primer script de información del sistema

Vamos a crear un nuevo fichero llamado `system-info.js`. En este archivo, lo que vamos a hacer es importar el paquete `os` (Operating System - sistema operativo):

```javascript
// system-info.js
import os from 'node:os';

```

## 🛡️ 2. El prefijo `node:` (Importante)

Una cosa muy importante que tienes que tener en cuenta es que cuando importas un módulo nativo en Node.js, el prefijo `node:` (como en `node:os`) es opcional pero **muy recomendado**.

¿Qué quiere decir esto? Que podríamos quitarlo y funcionaría exactamente igual:

```javascript
import os from 'os'; // Esto también funciona

```

Pero tú dirás, *"¿Y por qué lo haces entonces si escribes más?"*.

Esto es una práctica moderna que se hace para **asegurarte de que estás importando el módulo nativo**. Imagínate que más adelante instalas una dependencia (paquete externo) que por casualidad se llame `os`, `path` o `config`. Node.js podría tener un conflicto y no sabría si importar su propio módulo nativo o la dependencia de terceros. Usando `node:os`, evitamos este tipo de problemas y nos aseguramos siempre de importar la herramienta oficial que queremos.

## 📊 3. Obteniendo los datos de la máquina

Con este módulo podemos sacar un montón de información: el tipo de sistema operativo, la plataforma, la arquitectura, la memoria total y libre, el directorio *home* del usuario, el tiempo de actividad del sistema (cuánto tiempo lleva encendido) y mucha más información sobre la CPU y las interfaces de red.

Vamos a completar nuestro archivo `system-info.js` para mostrar todo esto por consola:

```javascript
// system-info.js
import os from 'node:os';

console.log('--- Información del sistema operativo ---');
console.log('Tipo de SO:', os.type());
console.log('Plataforma:', os.platform());
console.log('Arquitectura:', os.arch());
console.log('Memoria total (bytes):', os.totalmem());
console.log('Memoria libre (bytes):', os.freemem());
console.log('Directorio home del usuario:', os.homedir());
console.log('Tiempo de actividad del sistema (segundos):', os.uptime());
console.log('CPUS:', os.cpus());
console.log('Interfaces de red:', os.networkInterfaces());
console.log('-----------------------------------------');

```

Si ejecutas esto en tu terminal con `node system-info.js`, verás un montón de información de tu sistema operativo. Verás la memoria actual, la libre, el directorio *home* y, por ejemplo, los segundos que lleva encendido sin apagarse. También te mostrará una lista inmensa con todos los núcleos de tu CPU, su velocidad y su modelo.

## 🚧 4. El problema de la legibilidad

Si te fijas en la salida de nuestro script, hay algo que cuesta leer: el tiempo de actividad del sistema (`os.uptime()`) aparece en **segundos** (por ejemplo, `117000` segundos).

Esto no parece muy legible para un usuario humano. Para mejorar la legibilidad de esto, en el próximo tema vamos a aprender cómo instalar una pequeña dependencia externa (paquetes de terceros) que nos ayude a transformar estos datos en algo mucho más fácil de entender.
