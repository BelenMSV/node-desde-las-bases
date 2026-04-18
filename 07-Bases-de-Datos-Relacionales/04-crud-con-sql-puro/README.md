# Tema 04: Operaciones CRUD con SQL puro

En el tema anterior aprendimos la regla de oro: **usar siempre `?` para evitar la inyección SQL**. Hoy vamos a aplicar esa regla para construir nuestro Controlador de Usuarios (`userController.js`), pero esta vez, interactuando con MySQL.

Antes de empezar, necesitamos tener una tabla donde guardar los datos. Si estás usando una base de datos en la nube (como Aiven o Railway), puedes ejecutar este código SQL en su consola o a través de un programa como DBeaver/TablePlus para crear la tabla:

```sql
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  age INT
);
```

¡Perfecto! Ya tenemos la estructura. Vamos a programar el CRUD en nuestro `controllers/userController.js`.

---

## 📥 1. CREATE (Insertar datos)

En SQL, para guardar un nuevo registro usamos la sentencia `INSERT INTO`. Fíjate en cómo la librería `mysql2` nos devuelve un objeto gigante con mucha información, pero a nosotros nos interesa especialmente el `insertId` (el ID que MySQL le ha asignado automáticamente).

```javascript
// controllers/userController.js
import { pool } from '../config/db.js';

export const createUser = async (req, res) => {
  try {
    const { name, email, age } = req.body;

    // 1. Preparamos la consulta SQL
    const consulta = 'INSERT INTO users (name, email, age) VALUES (?, ?, ?)';
    
    // 2. Ejecutamos pasándole los valores en el array exacto
    // Destructuramos el primer elemento [result] porque el segundo son metadatos que no usamos
    const [result] = await pool.query(consulta, [name, email, age]);

    // 3. Respondemos con el ID que MySQL generó (insertId)
    res.status(201).json({
      message: 'Usuario creado con éxito',
      userId: result.insertId
    });
  } catch (error) {
    // Si el email ya existe, MySQL lanzará un error (código ER_DUP_ENTRY)
    res.status(400).json({ error: error.message });
  }
};
```

---

## 📖 2. READ (Leer datos)

Para leer datos usamos la famosa sentencia `SELECT`. Vamos a hacer dos funciones: una para traer todos los usuarios y otra para buscar por ID.

### Obtener todos los usuarios
```javascript
export const getUsers = async (req, res) => {
  try {
    // Seleccionamos todas las columnas (*) de la tabla users
    const consulta = 'SELECT * FROM users';
    
    // Aquí 'rows' contendrá un array con todos los usuarios
    const [rows] = await pool.query(consulta);

    res.json(rows);
  } catch (error) {
    res.status(500).json({ error: 'Error al obtener usuarios' });
  }
};
```

### Buscar un usuario por ID
```javascript
export const getUserById = async (req, res) => {
  try {
    const { id } = req.params;

    const consulta = 'SELECT * FROM users WHERE id = ?';
    const [rows] = await pool.query(consulta, [id]);

    // Si el array de resultados está vacío, el usuario no existe
    if (rows.length === 0) {
      return res.status(404).json({ error: 'Usuario no encontrado' });
    }

    // Como buscamos por ID, sabemos que solo habrá un resultado (en la posición 0)
    res.json(rows[0]);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};
```

---

## ✏️ 3. UPDATE (Actualizar datos)

Para modificar registros usamos la sentencia `UPDATE`. A diferencia del `INSERT`, cuando hacemos un `UPDATE`, MySQL no nos devuelve los datos del usuario modificado, solo nos dice cuántas filas han sido afectadas (`affectedRows`).

Vamos a hacer una actualización total (PUT) para simplificar la consulta SQL:

```javascript
export const updateUser = async (req, res) => {
  try {
    const { id } = req.params;
    const { name, email, age } = req.body;

    const consulta = 'UPDATE users SET name = ?, email = ?, age = ? WHERE id = ?';
    // ¡El orden del array debe coincidir EXACTAMENTE con el orden de las '?' en la consulta!
    const [result] = await pool.query(consulta, [name, email, age, id]);

    // Si affectedRows es 0, significa que no encontró ningún usuario con ese ID
    if (result.affectedRows === 0) {
      return res.status(404).json({ error: 'Usuario no encontrado' });
    }

    res.json({ message: 'Usuario actualizado correctamente' });
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
};
```

---

## 🗑️ 4. DELETE (Borrar datos)

Finalmente, para eliminar un usuario usamos la sentencia `DELETE FROM`. Al igual que con el `UPDATE`, comprobaremos la propiedad `affectedRows` para saber si realmente se borró algo.

```javascript
export const deleteUser = async (req, res) => {
  try {
    const { id } = req.params;

    const consulta = 'DELETE FROM users WHERE id = ?';
    const [result] = await pool.query(consulta, [id]);

    if (result.affectedRows === 0) {
      return res.status(404).json({ error: 'Usuario no encontrado' });
    }

    res.json({ message: 'Usuario eliminado definitivamente' });
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};
```

> ⚠️ **¡PELIGRO! El UPDATE/DELETE sin WHERE:**
> En SQL hay una broma muy recurrente que causa pesadillas a los programadores Junior. Si olvidas poner el `WHERE id = ?` en una sentencia `UPDATE` o `DELETE`... **¡Actualizarás o borrarás TODOS los registros de la tabla de golpe!** Asegúrate siempre de incluir tu condición.

---

## 🗺️ 5. Conectando nuestro Controlador a las Rutas

Como la arquitectura de nuestra API es profesional, el archivo de rutas se ve exactamente igual que en el módulo anterior. El archivo `index.js` ni siquiera sabe si estamos usando MongoDB o MySQL, ¡esa es la magia de separar responsabilidades!

```javascript
// routes/users.js
import { Router } from 'express';
import { 
  createUser, 
  getUsers, 
  getUserById, 
  updateUser, 
  deleteUser 
} from '../controllers/userController.js';

const router = Router();

router.get('/', getUsers);
router.get('/:id', getUserById);
router.post('/', createUser);
router.put('/:id', updateUser);
router.delete('/:id', deleteUser);

export default router;
```

---

## 🎯 Resumen y Siguientes Pasos

Acabas de construir una API completamente funcional usando SQL nativo. Has dominado:
1. `INSERT INTO` (Crear) y el `insertId`.
2. `SELECT *` (Leer).
3. `UPDATE` (Actualizar) y el `affectedRows`.
4. `DELETE FROM` (Borrar).

Pero una base de datos relacional no sirve de mucho si solo tienes una tabla. La verdadera potencia de MySQL reside en relacionar datos (por ejemplo, Usuarios y Posts). ¡En el último tema del curso aprenderemos la sentencia más poderosa del mundo Backend: El **JOIN**!