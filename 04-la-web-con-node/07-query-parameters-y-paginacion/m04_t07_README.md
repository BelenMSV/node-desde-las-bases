# Tema 16: Query Parameters, Pathname y Paginación

Nuestra API ya permite listar y crear usuarios, pero tenemos un "bug" (fallo) oculto. 

Imagina que en un futuro tenemos 10.000 usuarios en nuestra base de datos. Si un cliente hace un `GET` a `/users`, le devolveríamos 10.000 registros de golpe, colapsando su navegador y nuestro servidor. Para evitar esto, necesitamos permitir **filtros y paginación** a través de la URL.

Pero si intentamos ir a `http://localhost:3000/users?limit=2`, nuestra API nos devuelve un **404 Not Found**. ¿Por qué ocurre esto si la ruta `/users` sí existe?

---

## 🔍 1. El problema del `req.url` (Pathname vs Query String)

Hasta ahora, en nuestros `if` estábamos comparando directamente el `req.url`. El problema es que el `req.url` incluye **todo** lo que va después del dominio.

* Si visitas `localhost:3000/users`, el `req.url` es `'/users'`. ¡Funciona!
* Si visitas `localhost:3000/users?limit=2`, el `req.url` es `'/users?limit=2'`. Como eso no es exactamente igual a `'/users'`, nuestro servidor se confunde y lanza el Error 404.



Para solucionarlo, tenemos que separar la ruta real (**pathname**) de los parámetros de búsqueda (**query string**). Lo haremos dividiendo el texto usando el símbolo de interrogación `?`.

```javascript
// Extraemos la URL completa de la petición
const { url } = req;

// Separamos la ruta (pathname) de las variables (querystring)
const [pathname, querystring] = url.split('?'); 
// Ejemplo: Si la URL es '/users?limit=2'
// pathname = '/users'
// querystring = 'limit=2'
```
> ⚠️ **IMPORTANTE:** A partir de ahora, todos nuestros `if` de enrutamiento deben evaluar el `pathname` en lugar del `url`. (Ej: `if (pathname === '/users')`).

---

## 🧮 2. ¿Qué son Limit y Offset?

Para hacer una paginación profesional, en el Backend siempre utilizamos dos conceptos fundamentales:

* **Limit (Límite):** Cuántos resultados queremos que el servidor nos devuelva como máximo en esta petición (ej. "Dame solo 2 usuarios").
* **Offset (Desplazamiento):** Cuántos resultados queremos "saltarnos" o ignorar antes de empezar a contar (ej. "Sáltate los primeros 5 usuarios y dame los siguientes").

[Image of pagination limit and offset concept in arrays or databases]

Combinando ambos, podemos crear páginas. Por ejemplo, si mostramos 10 usuarios por página:
* Página 1: `limit=10`, `offset=0` (Los 10 primeros).
* Página 2: `limit=10`, `offset=10` (Nos saltamos 10 y damos los 10 siguientes).

---

## 🛠️ 3. Leyendo y validando la Query String

Node.js tiene una clase nativa maravillosa llamada `URLSearchParams` que coge ese texto raro del `querystring` (`limit=2&offset=1`) y lo convierte en un objeto fácil de leer.

```javascript
const searchParams = new URLSearchParams(querystring);
const limite = searchParams.get('limit'); // Nos devuelve "2"
```

### 🚨 La trampa de los Tipos de Datos
Las URLs **siempre** envían texto (Strings). Aunque el usuario escriba `limit=2`, a nuestro servidor le llega el texto `"2"`. Si intentamos hacer operaciones matemáticas con textos, JavaScript nos dará problemas. 

Debemos forzar la conversión a número usando `Number()`. Además, ¿qué pasa si el usuario es malicioso y escribe `?limit=patata`? Debemos validar que la conversión no resulte en un `NaN` (Not a Number).

---

## 🚀 4. Integrando la paginación en nuestro Servidor

Vamos a aplicar todos estos conceptos a nuestro `server.js`. Actualiza tu código prestando especial atención a cómo extraemos el `pathname` al principio y cómo modificamos el `GET` de `/users`.

```javascript
// server.js
import { createServer } from 'node:http';
import { json } from 'node:stream/consumers';
import { randomUUID } from 'node:crypto';

process.loadEnvFile();
const port = process.env.PORT ?? 3000;

function sendJson(res, statusCode, data) {
  res.statusCode = statusCode;
  res.setHeader('Content-Type', 'application/json; charset=utf-8');
  res.end(JSON.stringify(data));
}

const users = [
  { id: 1, name: 'Alice' },
  { id: 2, name: 'Bob' },
  { id: 3, name: 'Charlie' },
  { id: 4, name: 'Diana' } // He añadido un par más para probar bien la paginación
];

const server = createServer(async (req, res) => {
  const { method, url } = req;

  // 1. SEPARAMOS PATHNAME DE QUERYSTRING
  const [pathname, querystring] = url.split('?');
  // 2. CREAMOS EL GESTOR DE PARÁMETROS
  const searchParams = new URLSearchParams(querystring);

  // --- RUTAS GET (Leer) ---
  if (method === 'GET') {
    if (pathname === '/') {
      res.setHeader('Content-Type', 'text/plain; charset=utf-8');
      return res.end('Hola desde Node 👍!');
    }

    if (pathname === '/users') {
      // 3. RECUPERAMOS LOS VALORES DE LA URL
      const limitParam = searchParams.get('limit');
      const offsetParam = searchParams.get('offset');

      // 4. VALIDACIÓN: Si existen, ¿son números válidos?
      if (
        (limitParam && Number.isNaN(Number(limitParam))) || 
        (offsetParam && Number.isNaN(Number(offsetParam)))
      ) {
        return sendJson(res, 400, { error: 'Limit and offset must be numbers' });
      }

      // 5. ASIGNAMOS VALORES POR DEFECTO (Si no nos pasan limit, devolvemos todos. Si no hay offset, es 0)
      const limit = limitParam ? Number(limitParam) : users.length;
      const offset = offsetParam ? Number(offsetParam) : 0;

      // 6. PAGINAMOS EL ARRAY (slice recorta el array desde el offset hasta el offset + límite)
      const paginatedUsers = users.slice(offset, offset + limit);

      return sendJson(res, 200, paginatedUsers);
    }

    if (pathname === '/health') {
      return sendJson(res, 200, {
        status: 'ok',
        uptime: Math.round(process.uptime()) 
      });
    }
  }

  // --- RUTAS POST (Crear) ---
  if (method === 'POST') {
    if (pathname === '/users') { // OJO: Aquí también hemos cambiado 'url' por 'pathname'
      const body = await json(req);

      if (!body || !body.name) {
        return sendJson(res, 400, { error: 'Name is required' });
      }

      const newUser = {
        id: randomUUID(),
        name: body.name
      };

      users.push(newUser);
      return sendJson(res, 201, { message: 'Usuario creado', user: newUser });
    }
  }

  // 404 Not Found
  return sendJson(res, 404, { error: 'Not Found' });
});

server.listen(port, () => {
  const address = server.address();
  console.log(`🚀 Servidor escuchando en http://localhost:${address.port}`);
});
```
### 🧪 5. Probando en tu navegador o cliente API

Ahora puedes probar diferentes combinaciones en tu navegador o en Bruno (asegúrate de hacer una petición `GET`):

* `http://localhost:3000/users` -> Te devuelve los 4 usuarios.
* `http://localhost:3000/users?limit=2` -> Te devuelve solo a Alice y Bob.
* `http://localhost:3000/users?limit=2&offset=1` -> Se salta a Alice (offset 1) y te devuelve 2 resultados: Bob y Charlie.
* `http://localhost:3000/users?limit=patata` -> El servidor te protege y devuelve un Error 400 diciendo que deben ser números.

¡Felicidades! Acabas de implementar una lógica de paginación robusta y segura como la que usan las grandes aplicaciones en producción.