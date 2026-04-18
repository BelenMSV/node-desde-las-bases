# Tema 05 (Bonus): Seguridad y CORS en Producción

¡Felicidades! Tu API está viva en internet, conectada a una base de datos real y lista para recibir peticiones. Sin embargo, hay un último detalle que separa a las APIs de juguete de las APIs profesionales: **Controlar quién tiene permiso para hablar con tu servidor**.

Aquí es donde entra en juego nuestro viejo amigo **CORS** (Cross-Origin Resource Sharing).

---

## 🛑 1. El Problema: El "Barra Libre" de tu API

Hasta ahora, en tu archivo `index.js` (o `server.js`), has estado usando el middleware de CORS de esta manera:

```javascript
import cors from 'cors';

// ...
app.use(cors()); // 👈 La configuración por defecto
```

Cuando dejas `cors()` vacío, le estás diciendo a la API: *"Acepta peticiones de cualquier página web del mundo"*. 

En desarrollo (localhost) esto es útil porque no queremos bloqueos molestos. Pero en producción, es un riesgo enorme. Si dejas esto así, alguien podría crear una página web llamada `www.cursos-piratas.com`, conectarla a tu API (`https://tu-api.onrender.com`), y consumir tu base de datos y los recursos de tu servidor ¡sin que te des cuenta!

Los navegadores web (Chrome, Firefox, Safari) tienen un sistema de seguridad interno. Antes de pedir datos a tu API, el navegador lee la política CORS de tu servidor. Si tu servidor dice *"No conozco a cursos-piratas.com"*, el navegador **bloquea la petición** inmediatamente.

---

## 🛡️ 2. La Solución: La lista VIP (Orígenes Permitidos)

Para proteger nuestra API, tenemos que configurar CORS para que **solo** acepte peticiones de nuestra propia página web Frontend (por ejemplo, si subiste tu React o HTML a Vercel, Netlify o GitHub Pages).

Podríamos "quemar" (escribir directamente) la URL en el código, así:

```javascript
// ❌ Funciona, pero es una mala práctica
app.use(cors({
  origin: '[https://devcampus-frontend.vercel.app](https://devcampus-frontend.vercel.app)'
}));
```

¿Por qué es mala práctica? Porque si mañana cambias el dominio de tu Frontend, tendrás que modificar el código Backend y volver a desplegar todo. Además, si quieres probar el Frontend localmente (`http://localhost:5173`), ¡tu propio Backend te bloqueará!

---

## ☁️ 3. La Configuración Profesional (Usando .env)

La solución elegante y profesional es usar nuestras queridas **Variables de Entorno**. 

Vamos a crear una variable llamada `FRONTEND_URL`. De esta forma, en nuestro ordenador permitiremos `localhost`, y en la nube (Render) permitiremos nuestro dominio real.

### Paso A: Modifica tu código (`server.js`)
Sustituye tu viejo `app.use(cors())` por esta configuración avanzada:

```javascript
// Ojo: Asegúrate de cargar las variables de entorno primero
try { process.loadEnvFile(); } catch (e) {}

import express from 'express';
import cors from 'cors';

const app = express();

// 🛡️ CONFIGURACIÓN CORS PROFESIONAL
const dominiosPermitidos = [process.env.FRONTEND_URL];

const corsOptions = {
  origin: function (origin, callback) {
    // Si la petición viene de un dominio permitido, o si no tiene origen (ej. Postman/Bruno)
    if (!origin || dominiosPermitidos.includes(origin)) {
      callback(null, true); // Da luz verde
    } else {
      callback(new Error('Bloqueado por la política CORS')); // Da luz roja
    }
  }
};

app.use(cors(corsOptions));
```

### Paso B: Actualiza tus Variables de Entorno

1. **En tu ordenador (Local):**
   Abre tu archivo `.env` y añade la URL donde sueles correr tu Frontend (por ejemplo, el puerto de Vite, React o LiveServer).
   ```text
   DATABASE_URL=mysql://...
   FRONTEND_URL=http://localhost:5173
   ```

2. **En Producción (Render):**
   Ve al panel de control de Render > Environment Variables, y añade la misma variable pero con la URL pública definitiva de tu Frontend.
   * **Key:** `FRONTEND_URL`
   * **Value:** `https://tu-proyecto-frontend.vercel.app` *(¡Asegúrate de NO poner una barra diagonal `/` al final de la URL!)*

---

## 🎯 4. Conclusión Final

¡Y con esto, tu fortaleza está sellada! Tu servidor en Render ahora actuará como un estricto portero de discoteca. Si la petición viene de tu Frontend oficial, le abrirá la puerta de par en par. Si viene de una web desconocida, la bloqueará al instante.

Has construido, asegurado y desplegado una arquitectura Backend moderna de principio a fin. Estás completamente preparado para enfrentarte al mundo real del desarrollo web. 

**¡Mucho éxito en tus próximos proyectos! 🚀**