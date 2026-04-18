# Tema 05: Operaciones CRUD II (Actualizar y Borrar)

En el tema anterior logramos que nuestra API creara y leyera usuarios desde la base de datos real en MongoDB. Hoy vamos a cerrar el círculo del **CRUD** implementando las dos operaciones que nos faltan: **Update** (Actualizar) y **Delete** (Borrar).

---

## 🥊 1. El gran debate: PUT vs PATCH

Antes de escribir código para actualizar, necesitamos decidir qué verbo HTTP usar. Aunque a veces se usan indistintamente de forma incorrecta, en el estándar de la web significan cosas muy diferentes:

* **PUT (Reemplazo total):** Sustituye el documento por completo. Si un usuario tiene `name`, `email` y `age`, y tú haces un `PUT` enviando solo la `age`, el servidor debería borrar el `name` y el `email`, dejando solo la edad.
* **PATCH (Modificación parcial):** Aplica solo los cambios especificados. Si envías solo la `age`, el servidor mantendrá el `name` y el `email` intactos, y solo cambiará la edad.

Como en las aplicaciones modernas lo normal es que el usuario edite solo una parte de su perfil (como cambiar su contraseña o su foto de perfil), **vamos a utilizar `PATCH`**.

---

## ✏️ 2. UPDATE (Actualizar un usuario)

Mongoose nos ofrece una función increíblemente útil para esto: `findByIdAndUpdate()`. 

Esta función hace tres cosas de un solo golpe: busca al usuario por su ID, aplica los cambios que le pasemos y guarda el documento en la base de datos.

Vamos a añadir esta función a nuestro controlador:

```javascript
// controllers/userController.js

export const updateUser = async (req, res) => {
  try {
    const { id } = req.params; // El ID que viene en la URL
    const updateData = req.body; // Los datos que queremos cambiar

    // Actualizamos el usuario
    const updatedUser = await User.findByIdAndUpdate(
      id, 
      updateData, 
      { 
        new: true, // 👈 ¡CRUCIAL! Hace que Mongoose devuelva el documento YA actualizado
        runValidators: true // 👈 Obliga a Mongoose a aplicar las reglas del Schema (ej. age > 18)
      }
    );

    // Si el usuario no existe, devolvemos 404
    if (!updatedUser) {
      return res.status(404).json({ error: 'Usuario no encontrado' });
    }

    res.json(updatedUser);
  } catch (error) {
    res.status(400).json({ error: error.message });
  }
};
```

> **⚠️ La trampa de `new: true`:** Si olvidas poner esta opción, Mongoose actualizará el usuario en la base de datos, ¡pero tu API le devolverá al cliente el documento **antes** de ser modificado! Poner `new: true` te garantiza que `updatedUser` tiene los datos frescos.

---

## 🗑️ 3. DELETE (Borrar un usuario)

La operación de borrado es probablemente la más sencilla de todas. Usaremos el método `findByIdAndDelete()`.

Añade esto al final de tu controlador:

```javascript
// controllers/userController.js

export const deleteUser = async (req, res) => {
  try {
    const { id } = req.params;

    // Buscamos y borramos de un solo golpe
    const deletedUser = await User.findByIdAndDelete(id);

    // Si el ID no existe en la base de datos
    if (!deletedUser) {
      return res.status(404).json({ error: 'Usuario no encontrado' });
    }

    // Devolvemos un mensaje de éxito
    res.json({ message: 'Usuario eliminado correctamente', user: deletedUser });
  } catch (error) {
    res.status(400).json({ error: 'Formato de ID inválido' });
  }
};
```

---

## 🗺️ 4. Conectando las rutas

Ahora que nuestro controlador tiene todas las piezas del puzzle, volvemos a nuestro archivo de rutas para exponer estos dos nuevos *endpoints*.

```javascript
// routes/users.js
import { Router } from 'express';
import { 
  createUser, 
  getUsers, 
  getUserById, 
  updateUser, // 👈 Importamos
  deleteUser  // 👈 Importamos
} from '../controllers/userController.js';

const router = Router();

router.get('/', getUsers);
router.post('/', createUser);
router.get('/:id', getUserById);
router.patch('/:id', updateUser); // 👈 Ruta para actualizar
router.delete('/:id', deleteUser); // 👈 Ruta para borrar

export default router;
```

---

## 🧪 5. Probando el CRUD Completo

¡Tu API está terminada! Tienes un sistema completo de gestión de usuarios. Vamos a probarlo en tu cliente API (Bruno / Postman):

1. **Haz un `GET`** para ver la lista de usuarios y copia el `_id` de uno de ellos.
2. **Haz un `PATCH`** a `http://localhost:1234/users/EL_ID_COPIADO`.
   * Ve a la pestaña Body, selecciona JSON y escribe `{"age": 99}`.
   * Envíalo y comprueba que el servidor te devuelve al usuario con su nueva edad, pero conservando su nombre y su email.
3. **Haz un `DELETE`** a esa misma URL.
   * Recibirás el mensaje *"Usuario eliminado correctamente"*.
4. **Haz otro `GET`** a la lista general. ¡Verás que ese usuario ha desaparecido para siempre!