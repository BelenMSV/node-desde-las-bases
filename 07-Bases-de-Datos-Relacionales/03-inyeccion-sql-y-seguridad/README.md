# Tema 03: Seguridad e Inyección SQL (El ataque de Bobby Tables)

Ya tenemos nuestra API conectada a MySQL mediante un Pool de conexiones. El siguiente paso lógico sería empezar a guardar y buscar usuarios. Sin embargo, antes de escribir nuestra primera consulta, tenemos que hablar de la vulnerabilidad número uno en la historia de las bases de datos relacionales: **La Inyección SQL**.

Si no entiendes este concepto, podrías escribir código que permita a cualquier persona borrar tu base de datos entera desde un simple formulario de inicio de sesión.

---

## ☠️ 1. La vulnerabilidad: Concatenar Strings

Imagina que estamos programando el inicio de sesión (`login`) de nuestra aplicación. El usuario nos envía su email a través del `req.body.email`. 

El instinto natural de un programador Junior (y el mayor error que puede cometer) es construir la consulta SQL "pegando" o concatenando variables directamente en el texto:

```javascript
// ❌ EL ERROR FATAL: Concatenar variables directamente en el SQL
const emailUsuario = req.body.email; // Ej: "alice@mail.com"

// El programador Junior hace esto:
const consulta = "SELECT * FROM users WHERE email = '" + emailUsuario + "'";

// La base de datos recibe y ejecuta esto:
// SELECT * FROM users WHERE email = 'alice@mail.com'
```

Si el usuario es honesto y escribe `alice@mail.com`, todo funciona perfectamente. La base de datos busca a Alice y le da acceso.

---

## 💥 2. El Ataque (Inyección SQL)

¿Qué pasa si el usuario es un atacante (hacker) y sabe que estás concatenando strings? En lugar de escribir un email normal en el formulario, escribe este texto exacto:

`hacker@mail.com' OR '1'='1`

Mira lo que ocurre cuando tu código JavaScript "pega" ese texto dentro de la consulta SQL:

```sql
-- La consulta que se ejecuta en tu base de datos:
SELECT * FROM users WHERE email = 'hacker@mail.com' OR '1'='1'
```

**¡Boom! Tu seguridad acaba de saltar por los aires.**
En SQL, la cláusula `WHERE` evalúa condiciones. La base de datos lee: *"Devuélveme al usuario cuyo email sea hacker@mail.com... O devolvérmelo si 1 es igual a 1"*. 

Como `1` siempre es igual a `1` (es una verdad absoluta), la base de datos ignora el email y **devuelve TODOS los usuarios de la tabla**, dándole al hacker acceso a la primera cuenta que encuentre (que suele ser la del Administrador).

### El chiste de "Little Bobby Tables"
Esta vulnerabilidad es tan famosa que originó uno de los cómics más conocidos del mundo de la programación (XKCD #327). En el cómic, una madre llama a su hijo `Robert'); DROP TABLE Students; --`. 
Cuando el colegio inserta ese nombre en su base de datos concatenando strings, el código ejecuta el cierre del nombre y luego un comando `DROP TABLE`, borrando la tabla de estudiantes entera.

---

## 🛡️ 3. La Solución: Prepared Statements (Consultas Parametrizadas)

Para solucionar este problema de raíz, **nunca debemos concatenar variables en una consulta SQL**. 

En su lugar, debemos usar **Consultas Parametrizadas** (Prepared Statements). Esto consiste en poner símbolos de interrogación (`?`) en los lugares donde deberían ir los datos, y pasarle las variables a la librería `mysql2` en un array separado.

```javascript
// ✅ LA FORMA CORRECTA: Usar el símbolo '?'

import { pool } from '../config/db.js';

export const loginUser = async (req, res) => {
  try {
    const emailUsuario = req.body.email; // El hacker envía: hacker@mail.com' OR '1'='1

    // 1. Escribimos la consulta con un '?'
    const consulta = 'SELECT * FROM users WHERE email = ?';

    // 2. Le pasamos la consulta a mysql2, y los datos en un array como segundo parámetro
    const [rows] = await pool.query(consulta, [emailUsuario]);

    // ... evaluar resultado ...
    res.json(rows);
  } catch (error) {
    res.status(500).json({ error: error.message });
  }
};
```

### ¿Por qué esto es seguro?
Cuando usamos `?`, la librería `mysql2` coge el array de variables y dice: *"Ojo, este texto es estrictamente un valor, NO es código ejecutable"*. 

La librería se encarga de "escapar" el texto de forma segura. Si el hacker envía `' OR '1'='1`, la base de datos lo interpretará literalmente como un nombre de correo muy raro. Buscará a alguien cuyo email exacto sea la frase completa `hacker@mail.com' OR '1'='1`. Al no encontrar a nadie con ese correo absurdo, denegará el acceso. **¡Ataque frustrado!**

---

## 🎯 Resumen de la Regla de Oro

* **Nunca uses `+` o \`${variable}\`** para meter datos del usuario (req.body, req.params, req.query) dentro de un *string* de SQL.
* **Usa siempre `?`** en tus consultas de `mysql2` y pasa las variables en un array.

Ahora que sabemos cómo escribir consultas SQL sin poner en riesgo nuestra base de datos, ¡estamos listos para empezar a guardar y leer usuarios! En la próxima lección implementaremos el CRUD completo.