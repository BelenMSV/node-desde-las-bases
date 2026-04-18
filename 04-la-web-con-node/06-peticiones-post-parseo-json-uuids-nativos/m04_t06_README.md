# Tema 15: Peticiones POST, parseo de JSON y UUIDs nativos

Ya sabemos devolver información usando el método `GET`. Ahora vamos a dar un paso más allá y permitir que los usuarios de nuestra API puedan enviar datos para **crear** nuevos registros usando el método `POST`.

Para ello, vamos a sacar nuestra lista de usuarios fuera de la función del servidor para simular una base de datos en memoria (ten en cuenta que, al estar en memoria, los datos nuevos se borrarán si reinicias el servidor).

---

## 📥 1. El reto de leer el Body (Cuerpo de la petición)

Cuando un cliente (como Bruno o un Frontend) nos envía un JSON a través de un `POST`, esos datos viajan en el "cuerpo" (Body) de la petición. 



El problema es que la información no llega de golpe como un objeto mágico, sino que viaja a través de la red como un **flujo de datos (stream)** de bytes. Antiguamente, en Node.js, tenías que capturar esos pedazos de datos uno a uno y unirlos manualmente. 

¡Pero Node.js moderno tiene un "truco" nativo increíble! Podemos usar el módulo `node:stream/consumers` para que haga todo el trabajo sucio por nosotros.

---

## 🆔 2. Generando IDs únicos (UUID)

Cuando creamos un usuario, necesitamos asignarle un ID. Una tentación común es hacer `users.length + 1`, pero en el mundo real esto causa muchísimos problemas si borras usuarios. 

La mejor práctica es generar un **UUID** (Identificador Único Universal). De nuevo, en lugar de instalar librerías externas, usaremos el módulo nativo `node:crypto` de Node.js.

---

## 🚀 3. Implementando el método POST

Vamos a actualizar nuestro servidor. Fíjate en tres detalles clave:
1. Importamos `json` y `randomUUID`.
2. Convertimos el callback de `createServer` en una función asíncrona (`async (req, res)`).
3. Usamos `await json(req)` para leer y transformar el stream de datos en un objeto JavaScript real.

```javascript
// server.js
import { createServer } from 'node:http';
// 1. Importamos la "magia" para leer el JSON nativamente
import { json } from 'node:stream/consumers';
// 2. Importamos el generador de IDs únicos nativo
import { randomUUID } from 'node:crypto';

process.loadEnvFile();
const port = process.env.PORT ?? 3000;

function sendJson(res, statusCode, data) {
  res.statusCode = statusCode;
  res.setHeader('Content-Type', 'application/json; charset=utf-8');
  res.end(JSON.stringify(data));
}

// Simulamos nuestra base de datos en memoria
const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' }
];

// IMPORTANTE: Añadimos 'async' porque leer el body es un proceso asíncrono
const server = createServer(async (req, res) => {
  const { method, url } = req;

  const[pathname, querystring] = url.split('?');

  // --- RUTAS GET (Leer) ---
  if (method === 'GET') {
    if (url === '/') {
      res.setHeader('Content-Type', 'text/plain; charset=utf-8');
      return res.end('Hola desde Node 👍!');
    }

    if (url === '/users') {
      return sendJson(res, 200, users); // Devolvemos el array global
    }

    if (url === '/health') {
      return sendJson(res, 200, {
        status: 'ok',
        uptime: Math.round(process.uptime()) 
      });
    }
  }

  // --- RUTAS POST (Crear) ---
  if (method === 'POST') {
    if (url === '/users') {
      // 1. Recuperamos el JSON que nos envía el cliente
      const body = await json(req);

      // 2. Validación básica: Comprobamos si nos han enviado un nombre
      if (!body || !body.name) {
        // 400 Bad Request: El cliente ha enviado datos mal formados
        return sendJson(res, 400, { error: 'Name is required' });
      }

      // 3. Creamos el nuevo usuario
      const newUser = {
        id: randomUUID(), // Genera un ID como: "1b9d6bcd-bbfd-4b2d-9b5d-ab8dfbbd4bed"
        name: body.name
      };

      // 4. Lo guardamos en nuestra "base de datos"
      users.push(newUser);

      // 5. Devolvemos 201 Created (El código estándar al crear recursos)
      return sendJson(res, 201, { message: 'Usuario creado', user: newUser });
    }
  }

  // Si la ruta o el método no coinciden, devolvemos 404
  return sendJson(res, 404, { error: 'Not Found' });
});

server.listen(port, () => {
  const address = server.address();
  console.log(`🚀 Servidor escuchando en http://localhost:${address.port}`);
});
```

## 🧪 4. Probando la creación de usuarios

Nuestra API está lista para ser consumida desde cualquier lugar: un frontend (con `fetch`), la terminal (con `cURL`), o un cliente de APIs. Vamos a probarlo con **Bruno**.

### Paso 1: Configurar la petición POST en Bruno
1. Crea una nueva petición (Request) en tu colección de Bruno.
2. Llámala `Crear Usuario`.
3. Selecciona el método **POST**.
4. Pon la URL: `http://localhost:3000/users` *(Asegúrate de usar tu puerto dinámico o el que tengas en el `.env`)*.

### Paso 2: Enviar el Body (JSON)
1. Justo debajo de la URL, verás una pestaña que dice **Body**. Haz clic en ella.
2. Selecciona la opción **JSON**.
3. En el editor de texto que aparece, escribe el objeto que quieres enviar:
```json
{
  "name": "Belen"
}
```
4. Haz clic en **Send**. Deberías recibir un código `201 Created` y el mensaje de éxito.

### Paso 3: Comprobar que se ha guardado
Vuelve a tu petición anterior de "Listar usuarios" (el método `GET` a `/users`) y ejecútala. ¡Magia! En la respuesta verás a Alice, a Bob, y ahora también a Belen con un ID largo e indescifrable. ¡Ya tienes una API totalmente funcional sin instalar ni una sola dependencia externa!