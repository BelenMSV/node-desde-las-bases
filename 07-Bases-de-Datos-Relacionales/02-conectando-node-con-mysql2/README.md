# Tema 02: Conectando Node con mysql2 (Promesas y Pools)

En el tema anterior conseguimos nuestra cadena de conexión a la base de datos MySQL en la nube y la guardamos a salvo en nuestro archivo `.env` bajo el nombre `DATABASE_URL`. 

Hoy vamos a escribir el código para que nuestra API de Express se comunique con esa base de datos. Para ello, necesitamos instalar un "traductor" (un driver) en Node.js.

---

## 📦 1. El driver: `mysql` vs `mysql2`

Si buscas en internet cómo conectar Node.js a MySQL, es posible que encuentres tutoriales antiguos que usan un paquete llamado simplemente `mysql`. **No lo uses.**

La comunidad moderna utiliza **`mysql2`**. ¿Por qué?
1. Es muchísimo más rápido.
2. Soporta *Prepared Statements* de forma nativa (vital para la seguridad, lo veremos en el próximo tema).
3. **Soporta Promesas (`async/await`)**. El paquete antiguo usaba *callbacks*, lo que generaba un código horrible y difícil de leer (el famoso *Callback Hell*).

Abre tu terminal y ejecuta:

```bash
npm install mysql2
```

---

## 🛒 2. La arquitectura: Conexión Simple vs Pool

Antes de escribir código, tenemos que tomar una decisión de arquitectura súper importante. Hay dos formas de conectar un servidor a una base de datos:

### A) Conexión Simple (Single Connection)
Es como abrir una sola caja de cobro en un supermercado. Si entran 100 usuarios a tu página web a la vez, el primero hace su consulta y los otros 99 **tienen que hacer cola** esperando a que la caja quede libre. Si la cola es muy larga, la conexión se satura y tu servidor se cuelga (Crash).

### B) El Pool de Conexiones (Connection Pool)
Un "Pool" es como abrir 10 cajas de cobro a la vez. Cuando llega una petición, el Pool le asigna una caja libre. Cuando la petición termina, esa caja no se destruye, sino que se limpia y queda libre para el siguiente usuario. **Esta es la única forma profesional de conectar una API web a SQL.**

---

## 🛠️ 3. Escribiendo la conexión (db.js)

Vamos a crear nuestra configuración separada del `index.js`. Crea una carpeta `config` y dentro un archivo llamado `db.js`.

Fíjate en un detalle crítico: **importamos `mysql2/promise`**, no solo `mysql2`. Esto es lo que nos permite usar `async/await`.

```javascript
// config/db.js
import mysql from 'mysql2/promise';

// 1. Creamos el Pool de conexiones usando nuestra variable de entorno
// El pool gestionará automáticamente múltiples conexiones por detrás
export const pool = mysql.createPool(process.env.DATABASE_URL);

// 2. Función auxiliar para comprobar que la conexión funciona al arrancar
export const testConnection = async () => {
  try {
    // Pedimos una "caja" prestada al pool solo para ver si responde
    const connection = await pool.getConnection();
    console.log('✅ Conexión exitosa a la base de datos MySQL');
    
    // Es vital devolver la conexión al pool cuando terminamos
    connection.release(); 
  } catch (error) {
    console.error('❌ Error conectando a la base de datos:', error.message);
    process.exit(1); // Apagamos el servidor si no hay base de datos
  }
};
```

---

## 🚀 4. Integración en el Servidor (index.js)

Ahora volvemos a nuestro `index.js` para arrancar la prueba de conexión antes de que Express empiece a escuchar peticiones.

```javascript
// index.js
import express from 'express';
import cors from 'cors';
import { testConnection } from './config/db.js';

process.loadEnvFile();

const app = express();
const PORT = process.env.PORT ?? 1234;

// Middlewares
app.use(cors());
app.use(express.json());

// Probamos la base de datos ANTES de levantar el servidor
testConnection();

app.get('/', (req, res) => {
  res.send('API con MySQL lista 🚀');
});

app.listen(PORT, () => {
  console.log(`🚀 Servidor en http://localhost:${PORT}`);
});
```

Si guardas el archivo y tienes configurado tu `node --watch index.js`, deberías ver en la terminal el mensaje: **✅ Conexión exitosa a la base de datos MySQL**.

---

## 🎯 Resumen

1. Hemos instalado `mysql2` porque soporta Promesas de forma nativa.
2. Hemos descubierto que crear una sola conexión es peligroso para una API web.
3. Hemos creado un **Pool de Conexiones** que gestionará eficientemente el tráfico de nuestra base de datos.
4. Hemos exportado ese `pool` para poder usarlo en nuestros controladores más adelante.

¡Nuestra arquitectura está lista! Pero antes de empezar a guardar datos como locos, tenemos que hablar de seguridad. En la próxima clase descubriremos cómo un usuario malintencionado podría borrar toda tu base de datos desde un simple formulario si no programas tus consultas SQL correctamente.