# Tema 04: Operaciones CRUD I (Crear y Leer)

En el tema anterior creamos nuestro "plano" de usuario (`User.js`). Ahora tenemos nuestra conexión a MongoDB lista y las reglas de validación preparadas. 

Ha llegado el momento de hacer **CRUD** (Create, Read, Update, Delete). En esta clase nos centraremos en las dos primeras: **Crear** (POST) y **Leer** (GET). 

Además, vamos a dar el paso definitivo en nuestra arquitectura aplicando el patrón **Controlador**.

---

## 👔 1. El Controlador: Separando la lógica

Hasta ahora, todo nuestro código (validaciones, respuestas, búsquedas) vivía dentro del archivo de rutas (`routes/users.js`). En una API real, el archivo de rutas solo debe actuar como un "mapa" que conecte URLs con funciones.

Vamos a crear una nueva carpeta llamada `controllers` en la raíz de nuestro proyecto, y dentro, un archivo llamado `userController.js`. Aquí es donde ocurrirá la magia de Mongoose.

---

## 📥 2. CREATE (Crear un usuario)

Vamos a escribir nuestra primera función en el controlador para guardar un usuario. Fíjate en que ahora las funciones **deben ser asíncronas (`async`)**. Hablar con un servidor en la nube lleva tiempo, así que debemos usar `await` para esperar a que la base de datos termine su trabajo.

```javascript
// controllers/userController.js
import User from '../models/User.js'; // Importamos nuestro Modelo

export const createUser = async (req, res) => {
  try {
    // 1. Extraemos los datos del body (Express.json() ya hizo su magia)
    const { name, email, age } = req.body;

    // 2. Mongoose crea el documento, lo valida contra el Schema y lo guarda en la DB
    const newUser = await User.create({ name, email, age });

    // 3. Devolvemos el código 201 (Created) y el nuevo usuario con su ID de MongoDB
    res.status(201).json(newUser);

  } catch (error) {
    // Si Mongoose detecta un error (ej. falta el email o ya existe), caerá aquí
    res.status(400).json({ error: error.message });
  }
};
```

---

## 📖 3. READ (Listar usuarios y Paginación Mágica)

¿Recuerdas el dolor de cabeza que fue hacer la paginación a mano con `.slice(offset, offset + limit)`? 

Con Mongoose, buscar en la base de datos es tan fácil como llamar al método `.find()`. Además, MongoDB tiene métodos nativos para saltar (`skip`) y limitar (`limit`) resultados.

```javascript
// controllers/userController.js

export const getUsers = async (req, res) => {
  try {
    // 1. Extraemos los parámetros de búsqueda de la URL (si existen)
    const limit = Number(req.query.limit) || 10; // Por defecto 10
    const skip = Number(req.query.skip) || 0;    // Por defecto 0 (saltar ninguno)

    // 2. Pedimos los usuarios a la base de datos
    const users = await User.find()
      .skip(skip)    // Ignora los primeros X documentos
      .limit(limit); // Devuelve solo Y documentos

    // 3. (Opcional) Podemos contar cuántos usuarios hay en total en la DB
    const total = await User.countDocuments();

    // 4. Devolvemos un objeto estructurado profesional
    res.json({
      total,
      limit,
      skip,
      data: users
    });
  } catch (error) {
    res.status(500).json({ error: 'Error al obtener usuarios' });
  }
};
```

---

## 🔍 4. READ (Buscar por ID)

Cuando visitábamos el perfil de un usuario (`/users/:id`), antes buscábamos en nuestro array con un `.find()`. 

En Mongoose, cada documento tiene un campo especial `_id`. Existe un método ultra-rápido diseñado específicamente para buscar por ese identificador: `.findById()`.

```javascript
// controllers/userController.js

export const getUserById = async (req, res) => {
  try {
    const { id } = req.params;

    // Buscamos al usuario en MongoDB por su ID único
    const user = await User.findById(id);

    // Si Mongoose no encuentra nada, devuelve null
    if (!user) {
      return res.status(404).json({ error: 'Usuario no encontrado' });
    }

    res.json(user);
  } catch (error) {
    // Si el ID tiene un formato inválido (ej. "patata"), Mongoose lanza un error
    res.status(400).json({ error: 'Formato de ID inválido' });
  }
};
```

---

## 🗺️ 5. Conectando las piezas: El archivo de Rutas

Ahora que nuestro `userController.js` tiene toda la inteligencia, vamos a volver a nuestro archivo `routes/users.js` y a limpiarlo por completo. 

Fíjate en cómo nuestro archivo de rutas ahora es simplemente un "mapa" maravillosamente legible.

```javascript
// routes/users.js
import { Router } from 'express';
import { createUser, getUsers, getUserById } from '../controllers/userController.js';

const router = Router();

// Asignamos cada ruta a su controlador correspondiente
router.get('/', getUsers);
router.post('/', createUser);
router.get('/:id', getUserById);

export default router;
```

---

## 🧪 6. ¡A probar en Bruno/Postman!

¡Ha llegado la hora de la verdad! 

1. **Asegúrate de que tu servidor esté corriendo** (`node --watch index.js`) y que veas el mensaje de "MongoDB Conectado".
2. **Abre tu Cliente API** y haz un `POST` a `http://localhost:1234/users` con un JSON válido. Deberías recibir un 201.
3. **Cierra tu servidor** con `Ctrl + C` y vuelve a iniciarlo. 
4. **Haz un `GET`** a `http://localhost:1234/users`. ¡El usuario sigue ahí! ¡Tu API ahora es inmortal!

En la próxima clase completaremos nuestro CRUD con las operaciones para editar perfiles (`PUT/PATCH`) y borrarlos (`DELETE`).