# Tema 20: Middlewares en Express (El corazón del framework)

En esta clase introducimos uno de los conceptos fundamentales de Express: los **middlewares**. Hasta ahora hemos creado un servidor con Express y algunas rutas básicas, pero todavía no hemos visto qué ocurre en ese espacio intermedio antes de que una petición llegue a su destino final. 

¿Qué es un middleware? Es, literalmente, una función que se ejecuta **en medio** del ciclo de petición-respuesta. 

Cada vez que entra una petición a nuestro servidor, Express la hace pasar por una "tubería" (pipeline) de funciones. Si una función no termina la petición (enviando una respuesta), debe pasarle el testigo a la siguiente.

### 🧱 Estructura de un middleware
Un middleware en Express es una función que recibe siempre tres parámetros:
* **`req`**: El objeto de la petición (request).
* **`res`**: El objeto de la respuesta (response).
* **`next`**: Una función que, al ser llamada, le dice a Express que continúe con el siguiente middleware o ruta.

---

## 🌍 1. Nuestro primer Middleware Global (Un Logger)

Usamos `app.use()` para registrar un middleware que se ejecute en todas las peticiones. Al no especificarle ninguna ruta concreta, Express entiende que **toda petición** tiene que pasar primero por aquí.

Vamos a crear un *logger* que nos imprima la hora, el método y la URL:

```javascript
import express from 'express';

const PORT = process.env.PORT ?? 1234;
const app = express();

// 🚧 NUESTRO PRIMER MIDDLEWARE GLOBAL
app.use((req, res, next) => {
  const timeString = new Date().toLocaleTimeString();
  console.log(`[${timeString}] --- Nueva petición recibida ---`);
  console.log(`Método: ${req.method} | URL: ${req.url}`);
  
  // ¡Muy importante llamar a next()!
  next(); 
});

app.get('/', (req, res) => {
  res.send('<h1>¡Hola Mundo!</h1>');
});

app.get('/health', (req, res) => {
  return res.json({ status: 'ok', uptime: process.uptime() });
});

app.listen(PORT, () => {
  console.log(`🚀 Servidor levantado en http://localhost:${PORT}`);
});
```

### 🛑 El problema de la "Página colgada" y la magia de `next()`

Este código se ejecutará **antes** de llegar a `/` o `/health`. Si ejecutas el middleware anterior **sin** llamar a la función `next()`, la petición se quedaría "colgada" y el navegador seguiría esperando eternamente.

Piensa en el servidor como una cadena de montaje:
* El middleware coge la caja (la petición).
* Le pega una pegatina (hace los `console.log`).
* Pero si no empujas la caja a la siguiente estación (llamando a `next()`), la cadena se detiene por completo.

---

## 🎯 2. Middlewares específicos para una ruta

No todos los middlewares tienen que ser globales. Podemos crear middlewares como funciones normales y pasarlos **solo a las rutas que nos interesen**.

```javascript
// 1. Definimos el middleware como una función normal
const previusHomeMiddleware = (req, res, next) => {
  console.log('👀 Ejecutando el middleware previo SOLO para la ruta /');
  next();
};

// 2. Lo inyectamos en medio de la definición de la ruta
app.get('/', previusHomeMiddleware, (req, res) => {
  res.send('<h1>¡Hola Mundo con Express!</h1>');
});
```
Si visitas la raíz (`/`), Express pasará por el middleware global, luego por `previusHomeMiddleware`, y finalmente entregará la respuesta. Si visitas `/health`, **solo** pasará por el middleware global.

---

## ⚠️ 3. El orden de ejecución importa (¡Crucial!)

Express ejecuta los middlewares y las rutas **en el orden exacto en el que se definen en el código**.
* Si pones un `app.use()` al principio del archivo, afectará a todas las rutas de abajo.
* Si pones un `app.use()` después de una ruta, solo se ejecutará si esa ruta no ha terminado la respuesta. (Por ejemplo, esto es ideal para poner un middleware de Error 404 al final de todo el archivo).

---

## 🚀 4. ¿Para qué sirven en el mundo real?

Aunque aquí solo hemos hecho un `console.log`, los middlewares son la columna vertebral de Express y se usan para:
* **Logs y Auditoría:** Registrar quién accede a qué.
* **Seguridad:** Comprobar si el usuario está autenticado antes de mostrarle datos.
* **Transformación de datos:** Parsear el cuerpo de la petición (JSON, formularios).
* **Gestión de errores:** Capturar fallos de forma centralizada.

---

## 📝 Resumen: Los puntos clave de la clase

* ✅ Un middleware es una función que se ejecuta antes de las rutas.
* ✅ Se registran globalmente usando `app.use()`.
* ✅ La función `next()` es imprescindible para no bloquear el flujo.
* ✅ El orden en el que defines los middlewares determina cómo se procesa la petición.

En la siguiente clase empezaremos a usar middlewares más potentes para transformar los datos que recibimos en nuestro servidor.