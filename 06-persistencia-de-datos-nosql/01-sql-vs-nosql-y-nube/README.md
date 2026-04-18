# Tema 01: Bases de Datos (SQL vs NoSQL) y la Nube

Hasta ahora hemos construido una API maravillosa con Express. Podemos crear usuarios, buscarlos y paginarlos. Pero tenemos un problema trágico: nuestra base de datos es un simple *Array* en la memoria RAM. 

Si guardas un archivo y el servidor se reinicia con el `--watch`, o si apagamos el ordenador... **todos los datos desaparecen para siempre**. Ha llegado el momento de la Persistencia de Datos. Necesitamos una Base de Datos (DB).

---

## 🥊 1. El gran debate: SQL vs NoSQL

Cuando decides añadir una base de datos a tu proyecto, la primera gran decisión arquitectónica que debes tomar es el paradigma que vas a utilizar. Existen principalmente dos mundos:

### Bases de Datos Relacionales (SQL)
Son las clásicas, las que llevan dominando la industria décadas (MySQL, PostgreSQL, Oracle). 
* **Estructura:** Guardan los datos en **Tablas** con filas y columnas (muy parecido a un Excel).
* **Esquemas:** Son extremadamente estrictas. Si dices que la tabla "Usuarios" tiene Nombre y Edad, no puedes meter de repente a un usuario con un campo "Mascota". Dará error.
* **Relaciones:** Son brillantes para conectar datos complejos (ej. El Usuario 1 compró el Producto 4 que pertenece a la Categoría 2).

### Bases de Datos No Relacionales (NoSQL)
Nacieron para solucionar problemas de velocidad y escalabilidad en la era moderna de internet (MongoDB, CouchDB).
* **Estructura:** Guardan los datos en **Documentos**. En lugar de filas de una tabla, cada registro es un objeto JSON (exactamente igual a los que enviamos desde nuestra API).
* **Esquemas:** Son flexibles (Schemaless). Un usuario puede tener 2 campos y el siguiente usuario puede tener 10. No pasa nada.
* **Velocidad:** Son increíblemente rápidas para leer y escribir datos masivos.

> **¿Cuál elegiremos para este módulo?** > Vamos a utilizar **MongoDB (NoSQL)**. ¿Por qué? Porque es el mejor amigo de Node.js. Como MongoDB guarda los datos en formato JSON, no tenemos que hacer traducciones raras. Habla exactamente el mismo idioma que nuestro código JavaScript.

---

## ☁️ 2. El problema local vs. La Nube (DBaaS)

Históricamente, para usar una base de datos tenías que descargarla e instalarla en tu ordenador. Esto es un dolor de cabeza inmenso: tienes que configurar puertos, instalar servicios en segundo plano que consumen mucha RAM, y tu código solo funciona en tu máquina.

Hoy en día, los profesionales usamos **DBaaS** (Database as a Service). Es decir, alquilamos una base de datos que ya está instalada y funcionando en los servidores de Amazon, Google o Microsoft, y simplemente nos conectamos a ella a través de internet.

Para MongoDB, la plataforma oficial en la nube se llama **MongoDB Atlas**.

---

## 🛠️ 3. Creando nuestro clúster gratuito en MongoDB Atlas

Vamos a crear nuestra base de datos real en la nube sin gastar un solo céntimo. Sigue estos pasos al pie de la letra:

### Paso A: Registro y Creación
1. Entra en [mongodb.com/atlas](https://www.mongodb.com/atlas) y regístrate de forma gratuita (puedes usar tu cuenta de Google o GitHub).
2. Te pedirá crear una organización o proyecto (pon el nombre que quieras, ej. `Curso-Node`).
3. Te preguntará qué tipo de base de datos quieres crear. **Elige siempre la opción "M0 FREE"** (Es para siempre y nos da 512MB, más que de sobra para aprender).
4. Elige el proveedor que más te guste (AWS, Google Cloud o Azure) y la región más cercana a tu país para que vaya rápido. Dale a **Create**.

### Paso B: Seguridad (Usuarios y Red)
Atlas no deja entrar a cualquiera. Tenemos que crear credenciales.
1. **Security Quickstart:** Te pedirá crear un usuario para la base de datos. Pon un nombre de usuario (ej. `admin`) y genera una contraseña. **¡Copia esa contraseña en un bloc de notas temporal, la vas a necesitar!**
2. **Network Access (IP Access List):** Atlas te pregunta desde qué ordenadores vas a conectarte. Por seguridad de producción, solo deberías poner la IP de tu servidor real. Pero como estamos en desarrollo y nuestra IP cambia todos los días, selecciona **"Allow access from anywhere"** (Permitir acceso desde cualquier lugar) que pondrá la IP `0.0.0.0/0`.
3. Termina la configuración y entra al panel principal de tu clúster.

### Paso C: La Cadena de Conexión (Connection String)
Ya tenemos la base de datos viva. Ahora necesitamos su "número de teléfono" para que nuestro Node.js pueda llamarla.

1. En el panel principal, haz clic en el botón gris que dice **"Connect"** (Conectar).
2. Selecciona la opción **"Drivers"** (Vamos a conectarnos desde una aplicación de Node.js).
3. Aparecerá un texto largo que empieza por `mongodb+srv://...`. Esta es tu cadena de conexión.
4. Cópiala y pégala en tu bloc de notas temporal.

> ⚠️ **¡Cuidado con la contraseña!**
> Si te fijas, dentro de esa cadena de conexión larga pone `<password>`. Debes borrar esa palabra (incluyendo los símbolos `<` y `>`) y poner ahí la contraseña que generaste en el Paso B.

---

## 🎯 Resumen

¡Felicidades! Tienes una base de datos profesional NoSQL funcionando en un servidor en la nube, protegida con usuario y contraseña.

A partir de este momento, todo tu equipo podría conectarse a la misma base de datos sin importar si están en Windows o Mac. En la siguiente clase, descubriremos cómo llevar esa Cadena de Conexión a nuestro proyecto de Express utilizando las Variables de Entorno (`.env`) e instalaremos Mongoose para hacer la conexión.