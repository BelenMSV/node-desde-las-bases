# Tema 12: Enrutamiento (Routing) y respuestas JSON

Hasta ahora, nuestro servidor tenía un problema grave: daba igual a qué URL entraras (`localhost:3000/`, `localhost:3000/users` o `localhost:3000/patata`), siempre respondía lo mismo. 

En este tema vamos a aprender a discriminar la ruta (lo que se conoce como **Routing**) y a devolver datos estructurados en formato **JSON** (el estándar de la web).

---

## 🛤️ 1. Discriminando rutas (`req.url`)

La información sobre qué ruta ha visitado el usuario viene dentro del objeto de petición (`req`), concretamente en la propiedad `req.url`.

Usando simples condicionales `if`, podemos hacer que el servidor actúe de forma diferente según la URL visitada.

Vamos a modificar nuestro `server.js` para manejar tres escenarios:
1. La ruta raíz (`/`)
2. La ruta de usuarios (`/users`)
3. Una ruta que no existe (Error 404)

```javascript
// server.js
import { createServer } from 'node:http';

process.loadEnvFile();
const port = process.env.PORT ?? 3000;

const server = createServer((req, res) => {
  // 1. Ruta Raíz
  if (req.url === '/') {
    res.setHeader('Content-Type', 'text/plain; charset=utf-8');
    // IMPORTANTE: Usamos 'return' para que la ejecución se detenga aquí
    return res.end('Hola desde Node 👍!');
  }

  // 2. Ruta Users
  if (req.url === '/users') {
    // Para enviar JSON, debemos cambiar la cabecera (Content-Type)
    res.setHeader('Content-Type', 'application/json; charset=utf-8');
    // Convertimos nuestro objeto/array JavaScript a un string JSON
    return res.end(JSON.stringify([{ id: 1, name: 'Belen' }]));
  }

  // 3. Ruta no encontrada (Error 404)
  // Si el código llega hasta aquí, significa que la ruta no era ni '/' ni '/users'
  res.statusCode = 404; // Indicamos explícitamente el código de error
  res.setHeader('Content-Type', 'text/plain; charset=utf-8');
  return res.end('Not Found');
});

server.listen(port, () => {
  const address = server.address();
  console.log(`🚀 Servidor escuchando en http://localhost:${address.port}`);
});
```

> **⚠️ Cuidado con el `statusCode`:** Un error común es intentar pasar el código de estado directamente dentro del `res.end(404)`. Eso fallará. Siempre debes asignarlo a la propiedad `res.statusCode = 404;` antes de enviar la respuesta.

> **⚠️ La importancia del `return`:** Fíjate que en cada bloque `if` hacemos `return res.end(...)`. Si olvidamos el `return`, Node.js intentará seguir ejecutando el código de abajo y enviará múltiples respuestas, lo que hará que el servidor explote con el error: *"Cannot set headers after they are sent to the client"*.

---

## 📦 2. Refactorizando: Creando un helper para JSON

Enviar JSON es algo que haremos constantemente al crear una API. Como has visto, escribir todo el rato `res.setHeader(...)` y `res.end(JSON.stringify(...))` es pesado y propenso a errores.

Vamos a crear una función de ayuda (helper) llamada `sendJson` para simplificar nuestra vida y limpiar el código.

```javascript
// Añadimos esta función fuera de nuestro createServer
function sendJson(res, statusCode, data) {
  res.statusCode = statusCode;
  res.setHeader('Content-Type', 'application/json; charset=utf-8');
  res.end(JSON.stringify(data));
}
```

Ahora, vamos a aplicar este helper a nuestro servidor. Aprovecharemos para que nuestro Error 404 también devuelva un JSON (lo cual es una buena práctica en las APIs modernas).

```javascript
// server.js (Refactorizado)
import { createServer } from 'node:http';

process.loadEnvFile();
const port = process.env.PORT ?? 3000;

function sendJson(res, statusCode, data) {
  res.statusCode = statusCode;
  res.setHeader('Content-Type', 'application/json; charset=utf-8');
  res.end(JSON.stringify(data));
}

const server = createServer((req, res) => {
  
  if (req.url === '/') {
    res.setHeader('Content-Type', 'text/plain; charset=utf-8');
    return res.end('Hola desde Node 👍!');
  }

  if (req.url === '/users') {
    // ¡Mira qué limpio queda ahora!
    return sendJson(res, 200, [
      { id: 1, name: 'Alice' },
      { id: 2, name: 'Bob' }
    ]);
  }

  // Ahora nuestro 404 devuelve un JSON estructurado
  return sendJson(res, 404, { error: 'Not Found' });
});

server.listen(port, () => {
  const address = server.address();
  console.log(`🚀 Servidor escuchando en http://localhost:${address.port}`);
});
```
Si visitas `localhost:3000/users` en tu navegador y usas las herramientas de desarrollador (Network/Red), verás que ahora sí lo reconoce como `application/json` y lo formatea bonito.

---

## 🩺 3. Añadiendo una ruta Health Check

En la industria, es un estándar que todas las APIs tengan una ruta específica (suele ser `/health` o `/ping`) que sirva exclusivamente para comprobar si el servidor está "vivo" y funcionando correctamente.

Vamos a añadir esta ruta a nuestro servidor y vamos a usar `process.uptime()` para devolver cuántos segundos lleva levantado el servidor sin caerse.

```javascript
// Añade este bloque antes del 404 en tu server.js

  // Ruta Health Check
  if (req.url === '/health') {
    return sendJson(res, 200, {
      status: 'ok',
      // Math.round para quitar los decimales y dejarlo en segundos exactos
      uptime: Math.round(process.uptime()) 
    });
  }
```
Si visitas `/health`, verás que el número `uptime` va subiendo cada vez que recargas. Si haces un cambio en el código y lo guardas (lo que reinicia el servidor gracias al modo `--watch`), el `uptime` volverá a cero. ¡Un indicador perfecto de la estabilidad de nuestro servicio!