# Tema 11: Puertos dinámicos y Variables de Entorno (.env)

En el tema anterior dejamos nuestro servidor atado fijamente al puerto `3000`. Pero, ¿qué pasa si ese puerto ya está siendo usado por otra aplicación en tu ordenador? El servidor fallará y no podrá levantarse.

Para solucionar esto, en aplicaciones reales nunca fijamos el puerto "a fuego" (hardcoded) en el código. Vamos a ver cómo solucionarlo de dos formas.

---

## 🎲 1. El truco del puerto 0 (Puerto dinámico)

Si le decimos a Node.js que escuche en el puerto `0`, lo que hará internamente es buscar **el primer puerto que esté libre** en tu sistema operativo y asignárselo a tu servidor.

Para saber qué puerto nos ha tocado, tenemos que usar `server.address().port`.

Actualiza tu `server.js` así:

```javascript
// server.js
import { createServer } from 'node:http';

const port = 0; // 0 = "Búscame el primer puerto libre"

const server = createServer((req, res) => {
  res.setHeader('Content-Type', 'text/plain; charset=utf-8');
  res.end('Hola desde Node 🦖!');
});

server.listen(port, () => {
  // Obtenemos la información de dónde se ha levantado el servidor
  const address = server.address();
  console.log(`🚀 Servidor escuchando en http://localhost:${address.port}`);
});
```

Si ejecutas el servidor ahora, verás que la terminal te indica un puerto aleatorio (por ejemplo, el `54321` o el `60123`). ¡Problema de colisión solucionado!

---

## 🔐 2. Variables de Entorno (`process.env`)

Aunque el puerto `0` está bien para salir del paso, en la vida real (cuando subes tu proyecto a producción) el servidor que aloja tu web suele inyectar el puerto exacto a través de una **Variable de Entorno**.

Las variables de entorno nos permiten cambiar la configuración de nuestro programa *desde fuera*, sin tener que modificar ni una sola línea de código.

Podemos leer estas variables usando `process.env`. Vamos a decirle a nuestro código: *"Usa el puerto que te pasen por variable de entorno, y si no te pasan ninguno, usa el 3000 por defecto"*.

```javascript
// server.js
// Usamos el operador nullish coalescing (??) para dar un valor por defecto
const port = process.env.PORT ?? 3000;
```

Ahora, al ejecutar el programa en la terminal, podemos inyectarle esa variable directamente:

```bash
PORT=1234 node server.js
```

El servidor se levantará en el `1234`. Si ejecutas `PORT=8888 node server.js`, se levantará en el `8888`. ¡Magia!

---

## 📄 3. El archivo `.env` y `loadEnvFile`

Pasar las variables por consola está bien para una sola, pero imagínate tener que pasar el puerto, la contraseña de la base de datos, el token secreto de una API... Sería interminable.

[Image of Environment Variables concept showing .env file being read by a Node.js process]

Para eso se crearon los archivos `.env`. Son archivos de texto plano donde guardamos toda nuestra configuración secreta.

> ⚠️ **IMPORTANTE:** El archivo `.env` **NUNCA** se sube a GitHub por motivos de seguridad. Siempre debes añadirlo a tu archivo `.gitignore`.

1. Crea un archivo llamado literalmente `.env` en la misma carpeta que tu `server.js` y añade esto:
```env
PORT=5000
```

2. Antiguamente, para leer este archivo necesitabas instalar dependencias externas como `dotenv`. **¡Ya no!** En las versiones modernas de Node.js, podemos leerlo de forma nativa con `process.loadEnvFile()`.

```javascript
// server.js
import { createServer } from 'node:http';

// 1. Lee automáticamente el archivo .env y mete sus valores en process.env
process.loadEnvFile(); 

// 2. Recuperamos el puerto (ahora valdrá 5000 porque lo lee del .env)
const port = process.env.PORT ?? 3000;

const server = createServer((req, res) => {
  res.setHeader('Content-Type', 'text/plain; charset=utf-8');
  res.end('Hola desde Node 👍!');
});

server.listen(port, () => {
  const address = server.address();
  console.log(`🚀 Servidor escuchando en http://localhost:${address.port}`);
});
```

Ahora, si simplemente ejecutas `node server.js` (o `node --watch server.js`), Node.js leerá tu `.env` automáticamente y verás que arranca en el puerto `5000`. ¡Ya estás trabajando como un verdadero profesional del Backend!