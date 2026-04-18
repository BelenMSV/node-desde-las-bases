# Tema 07: Solucionando CORS con paquetes de terceros

En el Módulo 4 sufrimos en nuestras propias carnes el bloqueo de seguridad más famoso de los navegadores: **CORS (Cross-Origin Resource Sharing)**. 

Recordemos que para solucionarlo en Node.js puro tuvimos que:
1. Crear un objeto gigante con las cabeceras (`Access-Control-Allow-Origin`, etc.).
2. Inyectar esas cabeceras a mano en cada respuesta usando un `forEach`.
3. Interceptar la petición fantasma (`OPTIONS`) para devolver un código 200 y calmar al navegador.

Todo eso sumaba unas 20 líneas de código bastante complejo. Hoy vamos a ver cómo el ecosistema de Express reduce todo ese sufrimiento a instalar un paquete y escribir **una sola línea de código**.

---

## 📦 1. Middlewares de terceros (`cors`)

En la clase anterior usamos `express.json()`. Ese es un middleware **integrado** (viene de fábrica con Express). Sin embargo, la filosofía minimalista de Express dicta que no debe incluir absolutamente todo, sino dejar que la comunidad cree herramientas específicas.

Para solucionar el CORS, la comunidad creó un middleware de terceros llamado simplemente `cors`. 

Vamos a instalarlo en nuestro proyecto. Abre la terminal y ejecuta:

```bash
npm install cors
```
*(Recuerda que esto lo guardará en la sección `dependencies` de tu `package.json`).*

---

## ✨ 2. La solución de una línea (Modo Desarrollo)

Una vez instalado, usarlo es tan fácil como importarlo y decirle a nuestra aplicación que lo use globalmente mediante `app.use()`.

```javascript
import express from 'express';
import cors from 'cors'; // 1. Importamos el paquete de terceros

const app = express();

// 2. 🚧 INYECTAMOS EL MIDDLEWARE CORS
app.use(cors()); 

// ... resto de middlewares y rutas ...
app.get('/users', (req, res) => {
  res.json({ message: '¡CORS superado!' });
});
```

**¿Qué acaba de pasar por debajo?**
Al hacer `app.use(cors())` sin pasarle ninguna configuración, este middleware intercepta automáticamente todas las peticiones y:
* Responde a las peticiones fantasma (`OPTIONS`) por ti.
* Añade la cabecera `Access-Control-Allow-Origin: '*'` a todas tus respuestas.

¡Tu API ya puede ser consumida por cualquier Frontend en el mundo sin que el navegador bloquee nada!

---

## 🛡️ 3. Configuración avanzada (Modo Producción)

Usar `cors()` vacío está genial mientras desarrollamos en nuestra máquina local. Pero en el mundo real (Producción), poner un asterisco (`*`) significa que *cualquier* página web de internet puede hacer peticiones a tu API por detrás y consumir tus recursos. ¡Eso es peligroso!

Lo correcto es crear una **Lista Blanca (Whitelist)** o indicar específicamente qué dominios de Frontend tienen permiso para hablar con tu Backend.

El middleware `cors` nos permite pasarle un objeto de configuración muy potente:

```javascript
import express from 'express';
import cors from 'cors';

const app = express();

// Configuramos CORS para que solo acepte peticiones de dominios específicos
app.use(cors({
  origin: (origin, callback) => {
    const ACCEPTED_ORIGINS = [
      'http://localhost:5500', // Tu frontend en desarrollo local
      'http://localhost:8080', // Otro puerto común de desarrollo
      '[https://mi-aplicacion-real.com](https://mi-aplicacion-real.com)' // Tu frontend en producción
    ];

    // Si el origen de la petición está en nuestra lista blanca, lo dejamos pasar
    // (También dejamos pasar las peticiones sin origen, como las que hacemos desde Bruno o Postman)
    if (ACCEPTED_ORIGINS.includes(origin) || !origin) {
      return callback(null, true);
    }

    // Si no está, le bloqueamos el paso
    return callback(new Error('No permitido por CORS'));
  }
}));

app.get('/users', (req, res) => {
  res.json({ message: 'Conectado de forma segura' });
});

app.listen(1234);
```

### ¿Cómo funciona esta protección?
La función `origin` recibe quién está intentando acceder. 
* Si la URL de la página web está en nuestro array `ACCEPTED_ORIGINS`, el callback devuelve `true` y la petición continúa hacia tus rutas.
* Si un "hacker" crea una web falsa en `http://web-maliciosa.com` e intenta leer los datos de tus usuarios mediante un `fetch`, no estará en la lista blanca, el callback devolverá un error, y el navegador del usuario bloqueará la petición al instante.

---

## 🎯 Resumen y Siguientes Pasos

En este tema hemos visto cómo el gigantesco ecosistema de paquetes de NPM y los Middlewares de Express trabajan juntos para solucionarnos la vida. 

Nuestra API ya es robusta, lee JSON y es segura frente a navegadores. Sin embargo, tenemos un último problema estructural: **¡Nuestro archivo `index.js` empieza a tener demasiadas líneas!**

Si tuviéramos rutas para `/users`, `/posts`, `/comments` y `/likes`, tendríamos un archivo con cientos de rutas mezcladas. En el próximo y último tema del módulo, aprenderemos a crear una arquitectura profesional dividiendo nuestra aplicación en múltiples archivos usando `express.Router()`.