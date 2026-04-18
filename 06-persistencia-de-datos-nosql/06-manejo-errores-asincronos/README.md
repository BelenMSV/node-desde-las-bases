# Tema 06: Manejo de Errores Asíncronos

Si miras tu archivo `userController.js` del tema anterior, notarás un patrón muy repetitivo: absolutamente todas nuestras funciones están envueltas en un bloque `try { ... } catch (error) { ... }`. 

Aunque esto funciona perfectamente, a medida que tu aplicación crezca y tengas 50 controladores, escribir 50 `try/catch` se volverá agotador y ensuciará tu código. Hoy vamos a aprender cómo centralizar los errores para tener un código mucho más limpio.

---

## 💥 1. El problema de Express con lo Asíncrono

Express es un framework maravilloso, pero (hasta su versión 5) tiene un pequeño "defecto" de diseño con las Promesas: **No sabe capturar errores asíncronos automáticamente.**

Si haces una consulta a la base de datos con `await User.find()` y esa consulta falla (por ejemplo, porque no hay internet), JavaScript lanzará un error. Si no tienes un `catch` para atraparlo, el error quedará "suelto", la petición se quedará colgada cargando infinitamente en el navegador del cliente y, en el peor de los casos, tu servidor de Node.js se apagará por completo.

---

## 🗑️ 2. El Middleware de Errores Global

Hasta ahora hemos usado middlewares normales (que tienen `req`, `res`, `next`). Pero Express tiene un middleware especial diseñado exclusivamente para atrapar errores.

Se distingue porque **tiene 4 parámetros** en lugar de 3: `(err, req, res, next)`.

Vamos a crear este "basurero global" de errores. Lo ideal es colocarlo en el archivo `index.js`, **justo al final de todo**, después de todas tus rutas.

```javascript
// index.js (Al final del archivo, antes del app.listen)

// ... tus rutas (app.use('/users', usersRouter)) ...

// 🚨 MIDDLEWARE GLOBAL DE ERRORES
app.use((err, req, res, next) => {
  console.error('🔥 Error capturado:', err.message);

  // Si el error tiene un código de estado (ej. 404), lo usamos. Si no, usamos 500 (Error del servidor)
  const statusCode = err.statusCode || 500;

  res.status(statusCode).json({
    error: true,
    message: err.message || 'Error interno del servidor'
  });
});
```

---

## 🔀 3. Conectando el Controlador con el Error Global

Ahora que tenemos nuestro middleware global, tenemos que decirle a nuestros controladores que le envíen los errores allí. 

En Express, para "saltar" todo el código y enviar un error directamente al middleware global, utilizamos la función `next(error)`.

Vamos a refactorizar uno de nuestros controladores:

```javascript
// controllers/userController.js ANTES
export const getUsers = async (req, res) => {
  try {
    const users = await User.find();
    res.json(users);
  } catch (error) {
    res.status(500).json({ error: 'Error al obtener usuarios' });
  }
};

// controllers/userController.js AHORA (Usando next)
export const getUsers = async (req, res, next) => { // 👈 Añadimos 'next'
  try {
    const users = await User.find();
    res.json(users);
  } catch (error) {
    // 👈 Le pasamos el error a Express para que vaya al middleware global
    next(error); 
  }
};
```

Esto ya es una mejora, pero... **¡seguimos escribiendo el maldito `try/catch`!**

---

## 🎩 4. El truco Pro: El "Wrapper" (Envoltorio) Asíncrono

Aquí viene el truco que diferencia a un Junior de un Senior. En lugar de escribir el `try/catch` en cada controlador, vamos a crear una función "mágica" (un *Higher-Order Function*) que envuelva a nuestro controlador, haga el `try/catch` por detrás y llame a `next(error)` automáticamente si algo falla.

Puedes crear este archivo en una nueva carpeta llamada `utils`:

```javascript
// utils/catchAsync.js

// Esta función recibe tu controlador como parámetro (fn)
export const catchAsync = (fn) => {
  // Y devuelve una nueva función con el (req, res, next) que Express necesita
  return (req, res, next) => {
    // Ejecuta tu función, y si falla (.catch), se lo pasa directamente a next()
    fn(req, res, next).catch(next);
  };
};
```

*(Nota: Si no quieres crear esta función a mano, existe un paquete muy popular en NPM llamado `express-async-handler` que hace exactamente lo mismo).*

---

## ✨ 5. La limpieza definitiva (El resultado final)

Ahora vamos a usar nuestro nuevo "envoltorio" en el archivo de controladores. ¡Mira cómo desaparecen todos los `try/catch` y el código se vuelve pura lógica de negocio!

```javascript
// controllers/userController.js
import User from '../models/User.js';
import { catchAsync } from '../utils/catchAsync.js';

// ¡Sin try, sin catch, sin next manual!
export const getUsers = catchAsync(async (req, res) => {
  const users = await User.find();
  res.json(users);
});

export const createUser = catchAsync(async (req, res) => {
  const newUser = await User.create(req.body);
  res.status(201).json(newUser);
});

export const getUserById = catchAsync(async (req, res) => {
  const user = await User.findById(req.params.id);
  
  if (!user) {
    // Para lanzar un error personalizado, podemos crear el error y tirarlo (throw)
    // Nuestro catchAsync lo atrapará y lo mandará al middleware global
    const error = new Error('Usuario no encontrado');
    error.statusCode = 404;
    throw error;
  }

  res.json(user);
});
```

¡Míralo! Tu código es ahora maravillosamente limpio, fácil de leer y está 100% protegido contra caídas inesperadas del servidor.

---

## 🎯 ¡Fin del Módulo 6!

¡Enhorabuena! Has completado el módulo de Bases de Datos. Miremos atrás un momento y apreciemos todo lo que has logrado:
1. Has desplegado un servidor de base de datos en la nube (MongoDB Atlas).
2. Has conectado tu API de Express de forma segura usando Variables de Entorno (`.env`).
3. Has diseñado reglas estrictas de validación usando Schemas de Mongoose.
4. Has implementado un CRUD completo con lógica asíncrona.
5. Has centralizado el manejo de errores para que tu servidor sea indestructible.

Estás creando software a un nivel profesional.