# Tema 13: Clientes API (Bruno, Postman) y el problema de los Métodos HTTP

Hasta ahora hemos estado probando nuestra API simplemente escribiendo la URL en la barra de direcciones de nuestro navegador web (ej. `http://localhost:3000/users`). 

Sin embargo, **los navegadores web tienen una limitación:** cuando escribes una dirección y pulsas Enter, *siempre* hacen una petición de tipo **GET** (recuperar información). Para desarrollar APIs reales, necesitamos enviar datos, actualizar registros y borrar cosas. Para eso, el navegador ya no nos sirve; necesitamos un **Cliente de APIs**.

---

## 🛠️ 1. ¿Qué es un Cliente de APIs?

Un cliente de APIs es un programa diseñado específicamente para que los desarrolladores puedan construir, enviar y guardar peticiones HTTP personalizadas a sus servidores. 

Existen muchísimos en el mercado. Los más famosos son **Postman** e **Insomnia**, pero recientemente ha ganado mucha popularidad **Bruno** (que es open-source, muy ligero y es el que usaremos de ejemplo).



### Creando nuestra primera petición en Bruno (o tu cliente favorito):
Aunque no se vea todo el proceso hablado en el vídeo, estos son los pasos visuales que debes seguir en tu cliente API:

1. **Crear una Colección:** Abre tu cliente y crea una nueva colección llamada `localhost-node-api`. (Una colección es como una carpeta para guardar todas las peticiones de un mismo proyecto).
2. **Crear una Petición (Request):** Dentro de esa colección, crea una nueva petición y llámala `Health Check`.
3. **Configurar la Petición:**
   * **Método:** Selecciona `GET` en el menú desplegable.
   * **URL:** Escribe la dirección exacta con tu puerto (ej. `http://localhost:1234/health`). *(Asegúrate de poner el puerto correcto en el que está corriendo tu Node.js, si pones el 3000 y está en el 1234, te dará error de conexión).*
4. **Ejecutar:** Haz clic en el botón de **"Send"** (Enviar).

En el panel derecho o inferior de tu programa verás la respuesta del servidor perfectamente formateada:
```json
{
  "status": "ok",
  "uptime": 142
}
```

## 🚦 2. Los Métodos HTTP (Verbos)

Como pudiste ver al crear la petición, al lado de la URL hay un menú desplegable con varios "verbos" o métodos. En el estándar web, la misma URL puede hacer cosas totalmente distintas dependiendo del método que uses. 

Los más importantes para construir una API REST son:
* **GET:** Para **recuperar/leer** información (ej. obtener la lista de usuarios).
* **POST:** Para **crear** nueva información (ej. registrar un usuario nuevo).
* **PUT:** Para **actualizar** o reemplazar un recurso existente por completo.
* **PATCH:** Para **parchear** o actualizar solo una parte de un recurso (ej. cambiar solo la contraseña).
* **DELETE:** Para **borrar** un recurso.

Hablaremos de esto en profundidad en la próxima clase sobre APIs REST, pero es vital entender este concepto para ver el fallo grave que tiene nuestro código actual.

---

## 🐛 3. El gran problema de nuestro Servidor actual

Vamos a hacer un experimento en nuestro cliente API (Bruno/Postman):

1. Ve a la petición que creaste (`http://localhost:1234/health`).
2. Cambia el método desplegable de `GET` a `DELETE`.
3. Pulsa **"Send"**.

**¿Qué ha pasado?**
¡El servidor nos ha respondido con un `200 OK` y nos ha devuelto el JSON con el status y el uptime exactamente igual que antes! 

Esto es un **error conceptual gravísimo**. Le acabamos de decir al servidor *"¡Oye, borra el health!"* y el servidor ha respondido dándonos la información como si no pasara nada. No debería responder a cualquier método.

**¿Por qué ocurre esto?**
Si revisas el código de nuestro `server.js` del tema anterior, verás que nuestros `if` solo preguntan por la ruta (`req.url`):

```javascript
// Nuestro código actual es "ciego" a la intención del usuario
if (req.url === '/health') {
  return sendJson(res, 200, { status: 'ok', uptime: Math.round(process.uptime()) });
}
```
Al servidor le da absolutamente igual si la petición es un `GET`, un `POST` o un `DELETE`. Solo mira que ponga `/health` y ejecuta el bloque de código.

En el siguiente tema, vamos a solucionar esto aprendiendo a inspeccionar el método de la petición (`req.method`) para que nuestra API empiece a comportarse de forma inteligente y segura.

## 🛡️ 4. Restringiendo métodos (Error 405 Method Not Allowed)

Para solucionar el problema de que nuestra API responda a métodos incorrectos (como intentar hacer un `DELETE` a `/health`), tenemos que leer la propiedad `method` que viene dentro del objeto de la petición (`req`).

Para limpiar un poco nuestro código y no tener que escribir `req.method` y `req.url` todo el rato, vamos a usar la **desestructuración de objetos** (destructuring) de JavaScript para extraer ambos valores al principio de nuestro servidor.



Vamos a actualizar nuestro `server.js` para añadir un "guardia de seguridad" global: si el método no es `GET`, cortamos la ejecución y devolvemos un error `405 Method Not Allowed` (Método no permitido).

```javascript
// server.js (Refactorizado con control de métodos)
import { createServer } from 'node:http';

process.loadEnvFile();
const port = process.env.PORT ?? 3000;

function sendJson(res, statusCode, data) {
  res.statusCode = statusCode;
  res.setHeader('Content-Type', 'application/json; charset=utf-8');
  res.end(JSON.stringify(data));
}

const server = createServer((req, res) => {
  // 1. Extraemos el método y la URL de la petición (Destructuring)
  const { method, url } = req;

  // 2. Guardia de seguridad global: Solo aceptamos peticiones GET por ahora
  if (method !== 'GET') {
    return sendJson(res, 405, { error: 'Method Not Allowed' });
  }

  // 3. Enrutamiento (Fíjate que ahora usamos la variable 'url' limpia, sin el 'req.')
  if (url === '/') {
    res.setHeader('Content-Type', 'text/plain; charset=utf-8');
    return res.end('Hola desde Node 👍!');
  }

  if (url === '/users') {
    return sendJson(res, 200, [
      { id: 1, name: 'Alice' },
      { id: 2, name: 'Bob' }
    ]);
  }

  if (url === '/health') {
    return sendJson(res, 200, {
      status: 'ok',
      uptime: Math.round(process.uptime()) 
    });
  }

  // 4. Si la ruta no coincide con ninguna de las anteriores, devolvemos 404
  return sendJson(res, 404, { error: 'Not Found' });
});

server.listen(port, () => {
  const address = server.address();
  console.log(`🚀 Servidor escuchando en http://localhost:${address.port}`);
});
```

### Comprobando la solución en Bruno
Si guardas este código y vuelves a tu cliente API (Bruno o Postman):
1. Intenta hacer de nuevo la petición `DELETE` a `http://localhost:1234/health`.
2. Verás que ahora el servidor es inteligente y te rechaza con un código **405** y el mensaje de error: `{"error": "Method Not Allowed"}`.
3. Si lo vuelves a cambiar a `GET`, te devolverá los datos correctamente.

¡Ahora sí tenemos un servidor mucho más robusto y preparado para el mundo real!