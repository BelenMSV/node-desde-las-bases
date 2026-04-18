# Tema 05: Peticiones POST y el middleware express.json()

En el Módulo 4 aprendimos a crear peticiones `POST` para guardar nuevos usuarios. ¿Recuerdas cómo tuvimos que hacerlo? Tuvimos que importar `node:stream/consumers`, usar la función `json(req)`, hacer que nuestro servidor fuera asíncrono y lidiar con los flujos de datos.

Si no hacíamos eso, la información nos llegaba en un formato ilegible de *Buffers* y bytes. 

Hoy vamos a ver cómo Express soluciona este problema utilizando el concepto que aprendimos en la clase anterior: **Los Middlewares**.

---

## 😫 1. El problema: Express es "sordo" por defecto

Si intentas hacer una petición `POST` enviando un JSON a un servidor de Express recién creado, te llevarás una sorpresa. 

Vamos a intentar leer el cuerpo de la petición (`req.body`) sin configurar nada:

```javascript
import express from 'express';

const app = express();

app.post('/users', (req, res) => {
  // Intentamos leer lo que el usuario nos ha enviado
  console.log(req.body); 
  
  res.send('Usuario creado');
});

app.listen(1234);
```

Si vas a Bruno o Postman y envías un JSON (`{"name": "Belen"}`) a esta ruta, **verás que la consola imprime `undefined`**. 

¿Por qué? Porque Express es un framework minimalista. Por defecto, no asume qué tipo de datos le vas a enviar. Podría ser un JSON, un archivo de vídeo, un formulario HTML o texto plano. Como no lo sabe, **no parsea el cuerpo de la petición para ahorrar memoria y tiempo**.

---

## ✨ 2. La magia de `express.json()`

Para solucionar esto, necesitamos colocar a un "traductor" en nuestra cadena de montaje (pipeline) antes de que la petición llegue a nuestra ruta. Necesitamos un middleware.

Afortunadamente, no tenemos que programarlo nosotros. Express trae uno nativo diseñado específicamente para leer *streams* de datos y convertirlos a objetos JavaScript: **`express.json()`**.

Para usarlo, solo tenemos que inyectarlo de forma global usando `app.use()` en la parte superior de nuestra aplicación:

```javascript
import express from 'express';

const app = express();

// 🚧 INYECTAMOS EL MIDDLEWARE TRADUCTOR
// A partir de esta línea, todas las peticiones pasarán por aquí
app.use(express.json());

app.post('/users', (req, res) => {
  // ¡Ahora req.body ya contiene nuestro objeto perfectamente formateado!
  console.log(req.body); 
  
  res.status(201).json({ 
    message: 'Usuario recibido',
    data: req.body 
  });
});

app.listen(1234);
```

¡Eso es todo! Con una sola línea de código (`app.use(express.json())`), Express se encarga de escuchar los eventos del *stream*, juntar los pedazos, hacer un `JSON.parse()` y dejarte el resultado cómodamente guardado en la variable `req.body`.

---

## 🚀 3. Refactorizando nuestra API de Usuarios

Vamos a coger la lógica de nuestra base de datos en memoria (el array de usuarios) que hicimos en Node puro, y vamos a reescribirla usando Express. 

Fíjate en cómo el código se reduce, se vuelve más semántico y ya no necesitamos hacer que la ruta sea asíncrona (`async`), porque el middleware ya ha hecho el trabajo pesado por nosotros.

```javascript
import express from 'express';
import { randomUUID } from 'node:crypto'; // Seguimos usando el nativo para los IDs

const app = express();
const PORT = process.env.PORT ?? 1234;

// 1. Middleware para parsear el JSON del body
app.use(express.json());

// 2. Nuestra "Base de datos" simulada
const users = [
  { id: '1', name: 'Alice' },
  { id: '2', name: 'Bob' }
];

// --- RUTAS ---

// Listar usuarios (GET)
app.get('/users', (req, res) => {
  res.json(users);
});

// Crear usuario (POST)
app.post('/users', (req, res) => {
  const { name } = req.body; // Desestructuramos el nombre directamente del body

  // Validación rápida
  if (!name) {
    return res.status(400).json({ error: 'El campo "name" es obligatorio' });
  }

  // Creamos el usuario
  const newUser = {
    id: randomUUID(),
    name: name
  };

  // Lo guardamos
  users.push(newUser);

  // Devolvemos la respuesta (201 Created)
  res.status(201).json(newUser);
});

// Middleware para 404 (Siempre al final)
app.use((req, res) => {
  res.status(404).json({ error: 'Ruta no encontrada' });
});

// Levantamos el servidor
app.listen(PORT, () => {
  console.log(`🚀 Servidor levantado en http://localhost:${PORT}`);
});
```

---

## 🎯 Resumen y Siguientes Pasos

¿Te das cuenta de la diferencia? En el Módulo 4 teníamos que hacer muchísimas validaciones y condicionales feos para lograr esto. En Express, crear un endpoint `POST` profesional se resume en:

1. Añadir `app.use(express.json())`.
2. Crear un bloque `app.post()`.
3. Leer `req.body`.
4. Contestar con `res.status(201).json()`.

Nuestra API vuelve a estar a pleno rendimiento. Sin embargo, todavía tenemos que recuperar algunas de las funcionalidades "avanzadas" que hicimos en Node puro, como la paginación y buscar a un usuario concreto por su ID. 

¡En el próximo tema veremos cómo la "magia" de Express vuelve a salvarnos la vida con los `req.params` y los `req.query`!