# Tema 08: Arquitectura y express.Router()

Hasta ahora hemos construido una API maravillosa que busca, pagina, lee JSON y gestiona CORS. Pero si miras tu archivo `index.js`, probablemente ya tenga más de 100 líneas de código.

Imagina que tu aplicación crece y decides añadir rutas para `/posts`, `/comments`, `/likes` y `/products`. Tu archivo `index.js` pasaría a tener miles de líneas. En la industria, a esto se le llama **Código Espagueti (Spaghetti Code)**, y es una pesadilla de mantener.

Hoy vamos a aprender a organizar nuestra casa dividiendo las responsabilidades.

---

## 🏢 1. La analogía del edificio de oficinas

Piensa en tu archivo `index.js` como si fuera la persona de recepción en la planta baja de un gran edificio de oficinas. 

El trabajo de la recepcionista no es hacerte una entrevista de trabajo ni venderte un producto. Su único trabajo es recibirte (middlewares globales) y decirte: *"¿Vienes a hablar de Usuarios? Sube a la primera planta. ¿Vienes a hablar de Posts? Ve a la segunda planta"*.

En Express, esas "plantas" del edificio se crean utilizando una herramienta llamada **`express.Router()`**.

---

## ✂️ 2. Refactorizando: La carpeta `routes`

Vamos a dividir nuestro código. Lo primero que debes hacer en tu proyecto es crear una nueva carpeta llamada `routes` (rutas). Dentro de ella, crearemos un archivo dedicado exclusivamente a los usuarios: `users.js`.

### Paso A: Crear el Router (`routes/users.js`)

En este archivo vamos a mover TODAS las rutas que tengan que ver con usuarios. 

> ⚠️ **El truco de la ruta base:** Como este archivo *solo* tratará sobre usuarios, ya no necesitamos escribir `/users` en cada ruta. El prefijo lo configuraremos después. Aquí la ruta raíz `/` significará `/users`.

```javascript
// routes/users.js
import { Router } from 'express';
import { randomUUID } from 'node:crypto';

// 1. Creamos nuestro "mini-app" o enrutador
const router = Router();

// Nuestra base de datos simulada
const users = [
  { id: '1', name: 'Alice' },
  { id: '2', name: 'Bob' }
];

// 2. Definimos las rutas (Fíjate que ahora usamos 'router.get' en vez de 'app.get')
// Y la ruta es '/' en vez de '/users'
router.get('/', (req, res) => {
  res.json(users);
});

router.get('/:id', (req, res) => {
  const { id } = req.params;
  const user = users.find(u => u.id === id);
  if (!user) return res.status(404).json({ error: 'Usuario no encontrado' });
  res.json(user);
});

router.post('/', (req, res) => {
  const { name } = req.body;
  if (!name) return res.status(400).json({ error: 'Name es obligatorio' });

  const newUser = { id: randomUUID(), name };
  users.push(newUser);
  res.status(201).json(newUser);
});

// 3. Exportamos el router para poder usarlo en el index.js
export default router;
```

---

## 🔌 3. Conectando el Router al servidor principal

Ahora volvemos a nuestro archivo principal, nuestro querido `index.js` (la recepcionista). 

Su trabajo ahora es mucho más fácil. Solo tiene que cargar las configuraciones generales (CORS, lectura de JSON) y delegar el tráfico al archivo de rutas que acabamos de crear.

```javascript
// index.js
import express from 'express';
import cors from 'cors';
import usersRouter from './routes/users.js'; // Importamos nuestro router

const app = express();
const PORT = process.env.PORT ?? 1234;

// --- 1. MIDDLEWARES GLOBALES ---
app.use(cors());
app.use(express.json());

// --- 2. DELEGACIÓN DE RUTAS ---
// Le decimos a Express: "Cualquier petición que empiece por /users, 
// mándasela al usersRouter para que él se encargue".
app.use('/users', usersRouter);

// --- 3. MANEJO DE ERRORES (404) ---
app.use((req, res) => {
  res.status(404).json({ error: 'Ruta no encontrada' });
});

// --- 4. LEVANTAR SERVIDOR ---
app.listen(PORT, () => {
  console.log(`🚀 Servidor levantado en http://localhost:${PORT}`);
});
```

¡Míralo! Tu `index.js` ha pasado de ser un monstruo incontrolable a ser un archivo elegante, limpio y súper profesional. 

Si mañana necesitas añadir rutas para productos, simplemente crearás `routes/products.js`, lo importarás, y añadirás una sola línea en tu `index.js`: `app.use('/products', productsRouter)`.

---

## 🔭 4. El siguiente nivel: El patrón MVC (Adelanto)

Al separar las rutas, hemos dado el primer gran paso hacia una arquitectura profesional. Sin embargo, en empresas reales se utiliza un patrón de diseño llamado **MVC (Modelo - Vista - Controlador)**.

Aunque no lo aplicaremos a fondo hoy, la idea es sencilla: el archivo de rutas solo debería decir "A qué URL" se va, pero la lógica de qué ocurre (los condicionales, las búsquedas en base de datos) se saca a otra carpeta llamada `controllers`.

Tu arquitectura final se vería así:

Aunque no lo aplicaremos a fondo hoy, la idea es sencilla: el archivo de rutas solo debería decir "A qué URL" se va, pero la lógica de qué ocurre (los condicionales, las búsquedas en base de datos) se saca a otra carpeta llamada `controllers`.

Tu arquitectura final se vería así:

```text
mi-api-express/
├── package.json        (Dependencias y scripts)
├── .env                (Variables de entorno, puertos, secretos)
├── index.js            (Punto de entrada: Middlewares globales y conexión de rutas)
├── routes/             (Definición de URLs y verbos HTTP)
│   ├── users.js
│   └── posts.js
└── controllers/        (La lógica real: Búsquedas, validaciones, base de datos)
    ├── userController.js
    └── postController.js
```

## 🎯 ¡Fin del Módulo 5!

¡Felicidades! Has completado el módulo más transformador del curso. 
* Has aprendido a usar el estándar de la industria (Express).
* Has automatizado el molesto parseo de JSON y CORS con middlewares.
* Has dominado las rutas dinámicas y los parámetros de búsqueda.
* Y has estructurado tu proyecto como lo hacen los ingenieros de software reales.

¡Estás preparado para conectar tu API con una Base de Datos real en el próximo módulo!