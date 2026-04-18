# Tema 06: Rutas dinámicas y Query Strings en Express

En el Módulo 4 sufrimos muchísimo para lograr dos cosas que en el desarrollo web son el pan de cada día:
1. **Buscar a un usuario por su ID:** Tuvimos que hacer `url.split('/')`, contar las posiciones del array y rezar para no equivocarnos.
2. **Hacer paginación:** Tuvimos que separar el *pathname* del *querystring* a mano y usar `new URLSearchParams()` para leer variables como `?limit=2`.

Hoy vamos a ver cómo Express reduce todo ese código a dos simples propiedades que ya vienen masticadas dentro del objeto de petición (`req`): **`req.params`** y **`req.query`**.

---

## 🛣️ 1. Rutas Dinámicas (`req.params`)

Imagina que queremos crear un *endpoint* para ver el perfil de un usuario concreto: `GET /users/1` o `GET /users/99`. 

En Express, para decirle que una parte de la URL es una variable dinámica, simplemente le ponemos **dos puntos (`:`)** delante del nombre que queramos en la definición de la ruta.

```javascript
// El ':id' le dice a Express que esa parte de la URL es dinámica
app.get('/users/:id', (req, res) => {
  // Express guarda el valor automáticamente en req.params
  const userId = req.params.id; 
  
  res.json({ message: `Buscando al usuario con ID: ${userId}` });
});
```

Si el cliente visita `/users/patata`, `req.params.id` valdrá `"patata"`. ¡Adiós al `split('/')`!

> 💡 **Nota sobre los tipos de datos:** Todo lo que viaja en la URL siempre llega como un *String* (texto). Si tu ID es un número, recuerda convertirlo haciendo `Number(req.params.id)`.

---

## 🔍 2. Parámetros de Búsqueda (`req.query`)

Cuando el usuario quiere filtrar, ordenar o paginar resultados, suele enviar los datos al final de la URL usando el símbolo de interrogación `?`. Por ejemplo: `/users?limit=5&offset=10`.

A diferencia de las rutas dinámicas, **no necesitas definir nada especial en tu ruta `app.get('/users')`**. Express es tan listo que automáticamente detecta todo lo que va después de la `?`, lo convierte en un objeto, y te lo deja en la propiedad **`req.query`**.

```javascript
app.get('/users', (req, res) => {
  // Extraemos las variables directamente con desestructuración
  const { limit, offset } = req.query;
  
  console.log(`Límite: ${limit}, Salto: ${offset}`);
  
  res.json({ message: 'Lista de usuarios' });
});
```
¡Magia pura! Ya no necesitas `URLSearchParams`.

---

## 🚀 3. Refactorizando nuestra API

Vamos a juntar estos dos superpoderes y a integrarlos en nuestra base de datos simulada para recuperar las funciones avanzadas que teníamos en el módulo anterior.

Actualiza tu archivo principal añadiendo estas rutas:

```javascript
import express from 'express';

const app = express();
const PORT = process.env.PORT ?? 1234;

app.use(express.json()); // Nuestro middleware de la clase anterior

const users = [
  { id: '1', name: 'Alice' },
  { id: '2', name: 'Bob' },
  { id: '3', name: 'Charlie' },
  { id: '4', name: 'Diana' }
];

// --- 1. GET con QUERY STRINGS (Paginación) ---
app.get('/users', (req, res) => {
  const { limit, offset } = req.query;

  // Si no nos pasan valores, usamos valores por defecto
  const limitNum = limit ? Number(limit) : users.length;
  const offsetNum = offset ? Number(offset) : 0;

  // Paginamos el array
  const paginatedUsers = users.slice(offsetNum, offsetNum + limitNum);

  res.json(paginatedUsers);
});


// --- 2. GET con RUTAS DINÁMICAS (Buscar por ID) ---
app.get('/users/:id', (req, res) => {
  // Extraemos el ID dinámico de la URL
  const { id } = req.params;

  // Buscamos el usuario en nuestro array
  const user = users.find(u => u.id === id);

  // Si no existe, devolvemos un 404
  if (!user) {
    return res.status(404).json({ error: 'Usuario no encontrado' });
  }

  // Si existe, lo devolvemos
  res.json(user);
});


// --- Manejo del 404 global ---
app.use((req, res) => {
  res.status(404).json({ error: 'Ruta no encontrada' });
});

app.listen(PORT, () => {
  console.log(`🚀 Servidor levantado en http://localhost:${PORT}`);
});
```

---

## 🧪 4. Probando nuestra API

Abre Bruno o Postman y diviértete comprobando lo robusta que es tu API ahora:

1. **Paginación:** Haz un `GET` a `http://localhost:1234/users?limit=2`. Deberías ver solo a Alice y Bob.
2. **Usuario exitoso:** Haz un `GET` a `http://localhost:1234/users/3`. Deberías ver el JSON de Charlie.
3. **Usuario no encontrado:** Haz un `GET` a `http://localhost:1234/users/99`. Deberías recibir un código 404 y tu mensaje de error en JSON.

> ⚠️ **El orden importa en Express:** Si creas una ruta como `app.get('/users/admin')` y la pones *debajo* de `app.get('/users/:id')`, Express pensará que "admin" es un ID e intentará buscar el usuario con ID "admin". **Regla de oro:** Pon siempre las rutas estáticas arriba y las rutas dinámicas abajo.

---

## 🎯 Resumen de variables en Express

Para que nunca lo olvides, así es como recibes datos del exterior en Express:
* **`req.body`**: Para datos ocultos en peticiones POST/PUT (JSON, formularios).
* **`req.params`**: Para variables obligatorias en la estructura de la URL (`/users/:id`).
* **`req.query`**: Para variables opcionales al final de la URL (`?limit=10`).

En la próxima clase, por fin mataremos a nuestro mayor enemigo. Descubriremos cómo el gigantesco problema de CORS se soluciona en Express en tan solo tres segundos.