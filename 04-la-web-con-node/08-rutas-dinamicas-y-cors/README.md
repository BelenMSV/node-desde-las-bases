# Tema 08: Rutas dinámicas y el problema de CORS

En el tema anterior solucionamos el problema de buscar y paginar usuarios utilizando *Query Strings* (`/users?limit=2`). Pero en el mundo real, cuando entramos al perfil de un usuario, la URL no suele ser así. Suele ser algo como `localhost:3000/users/1` o `localhost:3000/users/1b9d6bcd`.

A esto se le conoce como **Rutas Dinámicas** (o *Path Params*). Además, si intentamos consumir esta API desde una página web real (Frontend), nos chocaremos con el mayor enemigo de los programadores Junior: **El error de CORS**. 

Hoy vamos a sufrir estos dos problemas para entenderlos y solucionarlos de forma nativa.

---

## 🛤️ 1. La pesadilla de las Rutas Dinámicas a mano

Imagina que queremos devolver los datos de un único usuario cuando nos hacen un `GET` a `/users/1`.

Si intentamos hacer un `if (pathname === '/users/1')`, funcionará para el usuario 1, pero tendríamos que escribir un `if` infinito para cada ID que exista en la base de datos. ¡Imposible!

En Node.js puro, para capturar ese `1` (o cualquier ID) de forma dinámica, tenemos que "trocear" la URL manualmente:

```javascript
// Si el pathname es '/users/1'
const parts = pathname.split('/'); 
// parts será un array: ['', 'users', '1']

if (method === 'GET' && parts[1] === 'users' && parts.length === 3) {
  const userId = Number(parts[2]); // Extraemos el ID dinámico
  
  // Buscamos al usuario en nuestra base de datos
  const user = users.find(u => u.id === userId);
  
  if (!user) {
    return sendJson(res, 404, { error: 'Usuario no encontrado' });
  }
  
  return sendJson(res, 200, user);
}
```

¿Ves lo complejo y feo que se vuelve el código? Imagina tener que parsear rutas como `/users/1/posts/45/comments`. ¡Te volverías loco haciendo `split('/')`! Este es uno de los grandes motivos por los que, en el próximo módulo, pasaremos a usar frameworks que nos resuelven esto mágicamente.

---

## 🛑 2. Conectando el Frontend y el Error de CORS

Imagina que un compañero de Frontend (o tú mismo) crea un archivo `index.html` básico e intenta hacer un `fetch` a tu API para mostrar la lista de usuarios. 

Abres el archivo HTML en tu navegador y... ¡Boom! Nada funciona. Abres la consola del navegador y ves un texto rojo aterrador:

> ❌ *Access to fetch at 'http://localhost:3000/users' from origin 'http://127.0.0.1:5500' has been blocked by CORS policy: No 'Access-Control-Allow-Origin' header is present on the requested resource.*

### ¿Qué es CORS?
CORS significa **Cross-Origin Resource Sharing** (Intercambio de Recursos de Origen Cruzado). 

Es un **mecanismo de seguridad del navegador web**, no de Node.js. El navegador dice: *"Oye, esta página web viene del dominio X (ej. localhost:5500), pero está intentando pedirle datos a un servidor en el dominio Y (localhost:3000). Eso me parece súper sospechoso. Voy a bloquear la respuesta por si acaso son hackers robando datos"*.

Para que el navegador permita leer la respuesta, el servidor (nosotros) debe decirle explícitamente: *"Tranquilo navegador, yo permito que este dominio lea mis datos"*.

---

## 👻 3. La petición fantasma (`OPTIONS`)

Para complicar más las cosas, cuando el navegador intenta hacer una petición compleja (como un `POST`, un `DELETE` o enviar un JSON), antes de enviar la petición real, envía una **petición "fantasma" de exploración**.

Esta petición utiliza un método HTTP especial llamado **`OPTIONS`** (Preflight). Básicamente, el navegador le pregunta al servidor: *"Hola, ¿aceptas peticiones POST? ¿De quién las aceptas?"*.

Si nuestro servidor no responde a ese `OPTIONS`, el navegador bloquea el `POST` original.

---

## 🔓 4. Solucionando CORS de forma nativa

Para solucionar esto, tenemos que añadir dos cosas a nuestro servidor:
1. Responder automáticamente con `200 OK` a cualquier método `OPTIONS`.
2. Añadir cabeceras especiales (Headers) en **todas** las respuestas de nuestra API que autoricen al navegador.

Vamos a actualizar nuestro archivo `server.js` con la solución completa:

```javascript
import { createServer } from 'node:http';

process.loadEnvFile();
const port = process.env.PORT ?? 3000;

// 1. Configuramos las cabeceras de CORS globales
const CORS_HEADERS = {
  // ¿Quién puede acceder? '*' significa TODO EL MUNDO. 
  // En producción deberías poner tu dominio real (ej. '[https://mi-web.com](https://mi-web.com)')
  'Access-Control-Allow-Origin': '*', 
  // ¿Qué métodos permitimos?
  'Access-Control-Allow-Methods': 'GET, POST, PUT, PATCH, DELETE, OPTIONS',
  // ¿Qué cabeceras extra permitimos que nos envíen?
  'Access-Control-Allow-Headers': 'Content-Type'
};

function sendJson(res, statusCode, data) {
  res.statusCode = statusCode;
  
  // 2. Inyectamos las cabeceras CORS en todas nuestras respuestas JSON
  Object.entries(CORS_HEADERS).forEach(([key, value]) => {
    res.setHeader(key, value);
  });
  
  res.setHeader('Content-Type', 'application/json; charset=utf-8');
  res.end(JSON.stringify(data));
}

const server = createServer(async (req, res) => {
  const { method, url } = req;

  // 3. CAPTURAMOS LA PETICIÓN FANTASMA (Preflight)
  if (method === 'OPTIONS') {
    Object.entries(CORS_HEADERS).forEach(([key, value]) => {
      res.setHeader(key, value);
    });
    res.statusCode = 200;
    return res.end(); // Terminamos la respuesta vacía
  }

  // --- RESTO DE TU CÓDIGO (GET, POST, Paginación, etc.) ---
  // ...
});

server.listen(port, () => {
  console.log(`🚀 Servidor escuchando en http://localhost:${port}`);
});
```

### ¿Qué hemos hecho?
* Hemos creado un objeto `CORS_HEADERS` con las "reglas de permiso".
* Con `'Access-Control-Allow-Origin': '*'` le hemos dicho al navegador: *"Deja pasar a cualquiera, confía en mí"*.
* Hemos interceptado el método `OPTIONS` para que el navegador se quede tranquilo antes de enviar un `POST`.

¡Si tu compañero de Frontend vuelve a hacer el `fetch` ahora, funcionará mágicamente y no habrá rastro del texto rojo en su consola!

---

## 🎯 El fin de una era

¡Enhorabuena! Has construido una API REST desde cero usando Node.js puro. Sabes gestionar métodos, extraer variables de la URL, paginar resultados, parsear un *Body* manualmente y sobrevivir al temido error de CORS.

Has tocado el techo de lo que deberías hacer "a mano". Escribir APIs así en aplicaciones enormes es inviable, cuesta mucho tiempo y es fácil cometer errores.

Ha llegado el momento. En el próximo módulo, borraremos casi todo este código manual y descubriremos cómo un Framework llamado **Express** hace todo esto por nosotros en apenas tres líneas de código. ¡Vamos allá!