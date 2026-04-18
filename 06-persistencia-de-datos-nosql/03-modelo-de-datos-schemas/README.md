# Tema 03: El Modelo de Datos (Schemas)

En el Módulo 4 simulábamos nuestra base de datos con un simple array de objetos: `const users = [{ id: 1, name: 'Alice' }]`. 

El problema de las bases de datos NoSQL como MongoDB es que tienen **libertad absoluta (Schemaless)**. Si tú le envías a MongoDB un objeto que sea `{"patata": true, "edad": 99}`, MongoDB lo guardará felizmente en la colección de usuarios sin hacer preguntas. 

Para una API profesional, esto es una pesadilla. Necesitamos reglas. Necesitamos que todos los usuarios tengan un nombre, un correo electrónico y que la base de datos rechace cualquier intento de guardar basura. Aquí es donde **Mongoose** brilla con sus **Schemas**.

---

## 🏗️ 1. Arquitectura: La carpeta `models`

Siguiendo las buenas prácticas que aprendimos al final del módulo anterior (Patrón MVC), vamos a crear una nueva carpeta en la raíz de nuestro proyecto llamada `models`. 

Dentro de esta carpeta guardaremos los "planos" (blueprints) de nuestros datos. Crea un archivo llamado `User.js` (en mayúscula, por convención para los modelos).

---

## 📝 2. ¿Qué es un Schema?

Un **Schema** (Esquema) es un diccionario de reglas. Le dice a Mongoose exactamente qué propiedades debe tener un documento, de qué tipo de dato son (texto, número, fecha) y si son obligatorias o no.

Vamos a escribir nuestro primer Schema en `models/User.js`:

```javascript
// models/User.js
import { Schema, model } from 'mongoose';

// 1. DEFINIMOS LAS REGLAS (El Schema)
const userSchema = new Schema({
  name: {
    type: String,
    required: [true, 'El nombre es obligatorio'],
    trim: true // Borra los espacios en blanco al principio y al final
  },
  email: {
    type: String,
    required: [true, 'El email es obligatorio'],
    unique: true, // 👈 ¡Súper útil! Evita que se registren dos personas con el mismo email
    trim: true,
    lowercase: true // Convierte siempre a minúsculas antes de guardar
  },
  age: {
    type: Number,
    min: [18, 'Debes ser mayor de 18 años para registrarte']
  }
}, {
  // 2. CONFIGURACIONES EXTRA (Opciones)
  timestamps: true 
});

// 3. CREAMOS Y EXPORTAMOS EL MODELO
export default model('User', userSchema);
```

---

## ✨ 3. La magia automática de Mongoose

Si te fijas en el código anterior, hay tres cosas maravillosas que Mongoose y MongoDB van a hacer por nosotros a partir de ahora, borrando muchísimo código manual que teníamos antes:

### A. ¡Adiós al `randomUUID()`!
En nuestra API antigua teníamos que importar `node:crypto` y generar IDs a mano. Con Mongoose ya no hace falta. Cada vez que guardemos un usuario nuevo, MongoDB le inyectará automáticamente una propiedad mágica llamada **`_id`** (ej: `65a1b2c3d4e5f6g7h8i9j0k1`).

### B. El truco Pro: `timestamps: true`
Añadir este pequeño objeto de configuración al final del Schema es un estándar de la industria. Mongoose creará automáticamente dos campos en tu base de datos:
* `createdAt`: Cuándo se registró el usuario.
* `updatedAt`: Cuándo fue la última vez que modificó su perfil.
¡Y se actualizan solos!

### C. Validaciones personalizadas
Si alguien intenta hacer un `POST` mandando un usuario con `age: 15`, Mongoose interceptará la petición **antes** de enviarla a la base de datos y lanzará un error que dirá exactamente: *"Debes ser mayor de 18 años para registrarte"*. Nosotros solo tendremos que capturar ese error y devolvérselo al Frontend.

---

## 🤖 4. ¿Qué es la función `model()`?

Al final de nuestro archivo hemos hecho `model('User', userSchema)`. 

El Schema solo son las reglas escritas en un papel. La función `model()` coge esas reglas y crea un objeto poderoso que nos permite interactuar con la base de datos. 

Este modelo (`User`) es el que importaremos en nuestros controladores para hacer operaciones mágicas como `User.find()` o `User.create()`. Por cierto, Mongoose es tan listo que, al llamarlo 'User', buscará automáticamente en MongoDB una colección llamada en plural y minúsculas: `users`.

---

## 🎯 Resumen

1. MongoDB guarda datos libremente, Mongoose pone las reglas.
2. Hemos separado nuestros modelos de datos en la carpeta `models`.
3. Hemos aprendido a aplicar validaciones nativas (`required`, `unique`, `min`).
4. Descubrimos utilidades automáticas como `timestamps` y el `_id`.

En la próxima clase, conectaremos este Modelo recién creado con nuestras rutas de Express para por fin guardar usuarios reales en nuestra base de datos en la nube. ¡Llega el **CRUD**!