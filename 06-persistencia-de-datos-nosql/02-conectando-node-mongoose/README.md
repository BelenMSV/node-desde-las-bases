# Tema 02: Conectando Node a MongoDB (Mongoose)

En la clase anterior creamos nuestro clúster en la nube y obtuvimos nuestra **Connection String**. Ahora vamos a escribir el código para que nuestra API de Express hable con MongoDB.

Para ello, utilizaremos **Mongoose**. Mongoose es un **ODM** (Object Data Modeling). Piensa en él como un "traductor" de lujo: nos permite trabajar con los datos de la base de datos como si fueran objetos normales de JavaScript, añadiendo validaciones y una estructura mucho más cómoda.

---

## 📦 1. Instalación de Mongoose

Lo primero es instalar la librería en nuestro proyecto de Express. Abre tu terminal y ejecuta:

```bash
npm install mongoose
```

---

## 🔐 2. Protegiendo la conexión en el `.env`

La cadena de conexión (URL) de MongoDB contiene tu usuario y tu contraseña. **NUNCA** debes escribirla directamente en tu código, porque si subes el proyecto a GitHub, cualquiera podrá borrar tu base de datos.

1. Abre tu archivo `.env`.
2. Crea una nueva variable llamada `MONGO_URI` y pega tu cadena de conexión (asegúrate de haber sustituido `<password>` por tu contraseña real).

```env
PORT=1234
MONGO_URI=mongodb+srv://admin:tu_password_aqui@cluster0.abcde.mongodb.net/nombre_de_tu_db?retryWrites=true&w=majority
```

---

## 🛠️ 3. Escribiendo la lógica de conexión

Vamos a crear un archivo dedicado a la conexión para mantener nuestro `index.js` limpio. Aunque podrías hacerlo todo en el archivo principal, en arquitectura profesional solemos separar la configuración.

Crea una carpeta llamada `config` y dentro un archivo `db.js`:

```javascript
// config/db.js
import mongoose from 'mongoose';

export const connectDB = async () => {
  try {
    // Intentamos la conexión usando la variable de entorno
    const conn = await mongoose.connect(process.env.MONGO_URI);
    
    console.log(`✅ MongoDB Conectado: ${conn.connection.host}`);
  } catch (error) {
    console.error(`❌ Error de conexión: ${error.message}`);
    // Si la base de datos falla, lo mejor es cerrar la aplicación
    process.exit(1); 
  }
};
```

---

## 🚀 4. Integración en el Servidor (index.js)

Ahora, en nuestro `index.js`, debemos llamar a esa función **antes** de que el servidor empiece a escuchar peticiones. Es una buena práctica asegurar que la base de datos esté lista antes de aceptar clientes.

```javascript
// index.js
import express from 'express';
import { connectDB } from './config/db.js';

process.loadEnvFile(); // Cargamos el .env

const app = express();
const PORT = process.env.PORT ?? 1234;

// 1. CONECTAMOS A LA BASE DE DATOS
connectDB();

app.use(express.json());

app.get('/', (req, res) => {
  res.send('API conectada a MongoDB 🚀');
});

app.listen(PORT, () => {
  console.log(`🚀 Servidor en http://localhost:${PORT}`);
});
```

---

## 🚦 5. Los estados de la conexión

Mongoose es inteligente y gestiona por nosotros los eventos de la conexión. Es muy útil saber en qué estado se encuentra nuestra base de datos en todo momento.



Puedes escuchar estos eventos en tu código para depurar errores:
* **`connected`**: Se ha establecido la conexión con éxito.
* **`error`**: Algo ha ido mal (mala contraseña, fallo de red).
* **`disconnected`**: Se ha perdido la conexión.

---

## 🎯 Resumen de la clase

1. Instalamos `mongoose` como nuestro ODM profesional.
2. Guardamos la `MONGO_URI` en el archivo `.env` para no exponer secretos.
3. Creamos una función asíncrona para conectar y manejar errores.
4. Conectamos la base de datos antes de levantar el servidor Express.

¡Ya tienes el puente construido! Ahora mismo tu API y MongoDB están "tomados de la mano", pero todavía no sabemos cómo guardar datos reales. En la próxima clase aprenderemos a crear **Schemas** (Esquemas) para definir cómo deben ser nuestros usuarios.