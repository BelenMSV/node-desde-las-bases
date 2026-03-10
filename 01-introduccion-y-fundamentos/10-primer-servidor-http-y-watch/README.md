# Tema 10: Nuestro primer Servidor HTTP y el modo Watch

Hasta ahora hemos ejecutado scripts que hacen una tarea y terminan. Pero el verdadero poder de Node.js brilla cuando creamos **servidores web**: programas que se quedan "escuchando" indefinidamente, esperando a que alguien (un navegador, una app) les haga una petición para devolverles una respuesta.

En Node.js, crear un servidor web sin instalar ninguna librería externa es facilísimo usando el módulo nativo `node:http`.

---

## 🌐 1. Lo mínimo indispensable para un servidor

Vamos a crear un archivo llamado `server.js`. 

Un servidor necesita dos cosas fundamentales:
1.  Saber en qué **puerto** debe escuchar (ej. el puerto `3000`).
2.  Una función que maneje las **Peticiones (`req` / request)** que entran y envíe las **Respuestas (`res` / response)** que salen.

```javascript
// server.js
import { createServer } from 'node:http';

const port = 3000;

const server = createServer((req, res) => {
  console.log('Petición recibida en:', req.method, req.url);
});

server.listen(port, () => {
  console.log(`🚀 Servidor escuchando en http://localhost:${port}`);
});
```

Si ejecutas `node server.js` en tu terminal, verás el mensaje de que el servidor está escuchando. 

**🚨 El problema del bucle infinito:**
Si abres tu navegador y entras en `http://localhost:3000`, verás que la página se queda cargando eternamente. Si miras tu terminal, verás que la petición ha llegado (`GET /`), pero el navegador sigue esperando. ¿Por qué? **Porque nunca le hemos dicho al servidor que termine la respuesta.**

---

## ✉️ 2. Respondiendo al usuario y el problema de la codificación

Un servidor *siempre* debe finalizar la petición enviando algo de vuelta usando `res.end()`. Vamos a enviar un mensaje con un emoji.

```javascript
// server.js
import { createServer } from 'node:http';

const port = 3000;

const server = createServer((req, res) => {
  res.end('Hola desde Node 🔥!'); // Terminamos la respuesta enviando texto
});

server.listen(port, () => {
  console.log(`🚀 Servidor escuchando en http://localhost:${port}`);
});
```

Cierra tu servidor anterior pulsando `Ctrl + C` en la terminal y vuelve a ejecutar `node server.js`. 

Si recargas el navegador, ¡ya responde! Pero oh sorpresa... **el emoji del fuego (`🔥`) seguramente se vea roto o como símbolos extraños.** 

### Arreglando la codificación con Cabeceras (Headers)
El navegador no es adivino. Si le enviamos texto, tenemos que decirle qué tipo de texto es y qué codificación usa. Para eso existen las **cabeceras (Headers)**.

Vamos a decirle explícitamente que le estamos enviando texto plano codificado en `utf-8` (que soporta emojis):

```javascript
// server.js
import { createServer } from 'node:http';

const port = 3000;

const server = createServer((req, res) => {
  // 1. Configuramos la cabecera para que entienda los emojis
  res.setHeader('Content-Type', 'text/plain; charset=utf-8');
  
  // 2. Enviamos la respuesta
  res.end('Hola desde Node 🔥!');
});

server.listen(port, () => {
  console.log(`🚀 Servidor escuchando en http://localhost:${port}`);
});
```

*(Reinicia el servidor con `Ctrl + C` y vuelve a iniciarlo. Ahora el emoji se verá perfecto).*

---

## ⏱️ 3. El modo Watch (`--watch`)

Como habrás notado, tener que parar el servidor (`Ctrl + C`) y volver a escribir `node server.js` cada vez que cambiamos una sola letra de nuestro código es una pesadilla.

Antiguamente se instalaban librerías externas como `nodemon` para solucionar esto, pero las versiones modernas de Node.js incluyen un modo de observación nativo.



Si inicias tu servidor añadiendo el flag `--watch`:

```bash
node --watch server.js
```

A partir de este momento, Node.js vigilará tu archivo. Prueba a cambiar el emoji de fuego (`🔥`) por un dinosaurio (`🦖`) y guarda el archivo. Verás que la terminal dice "Restarting 'server.js'". ¡Si recargas el navegador, el cambio estará ahí sin que hayas tenido que tocar la consola!