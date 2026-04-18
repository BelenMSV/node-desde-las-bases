# Tema 05: El poder de las relaciones (Claves Foráneas y JOINs)

Hasta ahora hemos trabajado con una sola tabla (`users`). Pero en una aplicación real, los datos no viven aislados. Los usuarios escriben posts, compran productos y dejan comentarios. 

En este último tema del curso, vamos a aprender a vincular dos tablas y a extraer su información cruzada usando la herramienta más poderosa de SQL: el **JOIN**.

---

## 🔗 1. Preparando el terreno (La tabla Posts)

Para poder relacionar datos, necesitamos una segunda tabla. Vamos a crear una tabla de `posts` que estará vinculada a nuestros `users`.

Ejecuta este código en la consola de tu base de datos para crear la tabla:

```sql
CREATE TABLE posts (
  id INT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  content TEXT,
  user_id INT, -- Esta es nuestra Clave Foránea (Foreign Key)
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

### ¿Qué hace `ON DELETE CASCADE`?
Es magia pura de las bases de datos relacionales. Significa que si borras a la usuaria 'Alice' (que tiene el `id: 1`), MySQL automáticamente destruirá todos los posts que tengan el `user_id: 1`. No tienes que programar nada en Node.js, la base de datos mantiene la limpieza por ti.

---

## 💔 2. El problema de las múltiples peticiones

Imagina que tu Frontend te pide crear una vista que muestre "El nombre del usuario y el título de todos los posts que ha escrito".

Un programador inexperto haría esto en Node.js:
1. Hace un `SELECT * FROM users` para obtener todos los usuarios.
2. Hace un bucle `for` en JavaScript.
3. Por cada usuario, hace **otra consulta** `SELECT * FROM posts WHERE user_id = ?`.

Si tienes 1.000 usuarios, ¡acabas de hacer 1.001 consultas a la base de datos! Tu servidor colapsará, tu base de datos se saturará y la aplicación será lentísima. A esto se le conoce en la industria como el **Problema N+1**.

---

## ✨ 3. La solución: La magia del `JOIN`

Las bases de datos relacionales están ultra optimizadas para cruzar datos. En lugar de hacer mil consultas, le pedimos a MySQL que cruce las tablas por nosotros y nos devuelva el resultado ya masticado en una sola petición.

Para ello usamos la cláusula **`INNER JOIN`**:

```sql
SELECT users.name, posts.title 
FROM users 
INNER JOIN posts ON users.id = posts.user_id;
```

**¿Qué significa esto en lenguaje humano?**
*"Oye MySQL, coge la tabla `users` y pégala con la tabla `posts`. Pero únelas **SOLO** donde el `id` del usuario coincida con el `user_id` del post. Ah, y de todo ese cruce, devuélveme solo el nombre del usuario y el título del post"*.

---

## 🚀 4. Implementándolo en nuestra API

Vamos a crear un nuevo controlador para devolver esta información enriquecida. Puedes añadirlo en tu `userController.js` o crear un `postController.js`.

```javascript
// controllers/userController.js
import { pool } from '../config/db.js';

export const getUsersWithPosts = async (req, res) => {
  try {
    // 1. Escribimos nuestra consulta JOIN
    const consulta = `
      SELECT users.id, users.name, users.email, posts.title, posts.content 
      FROM users 
      LEFT JOIN posts ON users.id = posts.user_id
    `;

    // 2. Ejecutamos la consulta en el Pool
    const [rows] = await pool.query(consulta);

    // 3. Devolvemos el JSON cruzado
    res.json(rows);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};
```

### ¿Por qué `LEFT JOIN` en lugar de `INNER JOIN`?
* **`INNER JOIN`:** Solo devuelve a los usuarios que han escrito al menos un post. Si Bob acaba de registrarse y no ha escrito nada, desaparecerá de la lista.
* **`LEFT JOIN`:** Devuelve a **TODOS** los usuarios de la tabla de la izquierda (`users`). Si Bob no ha escrito nada, MySQL nos devolverá a Bob, pero los campos de su post (`title`, `content`) vendrán con el valor `NULL`. Esto suele ser lo que queremos el 90% de las veces.

---

## 🗺️ 5. Exponiendo la ruta

Finalmente, vamos a exponer esta maravilla en nuestras rutas.

```javascript
// routes/users.js
import { Router } from 'express';
import { getUsersWithPosts } from '../controllers/userController.js';
// ... tus otras importaciones CRUD

const router = Router();

// OJO: Pon esta ruta ANTES del /:id para que Express no confunda la palabra 'posts' con un ID.
router.get('/posts', getUsersWithPosts); 

// ... el resto de tus rutas (router.get('/', ...), router.get('/:id', ...))

export default router;
```

¡Pruébalo en Bruno! Haz un `GET` a `http://localhost:1234/users/posts`. Verás una respuesta instantánea con los datos de las dos tablas perfectamente combinados. Has resuelto el problema N+1 con una sola consulta brillante.