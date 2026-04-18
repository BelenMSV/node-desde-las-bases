# Tema 04 (Bonus): Gestionando la Base de Datos en Producción

Tu API ya está subida en Render y conectada a tu base de datos remota. Pero hay un pequeño detalle: **tu base de datos en la nube está completamente en blanco**. No tiene la tabla `users` ni la tabla `posts` que creaste en tu ordenador local.

En las empresas grandes, de esto se encarga un administrador de bases de datos (DBA) o se automatiza mediante código ("Migraciones"). Pero como desarrollador Full-Stack o de Startups, tú debes saber cómo administrar tu base de datos remota directamente desde tu propio ordenador.

Vamos a conectar tu **MySQL Workbench** a tu servidor de producción para crear las tablas.

---

## 🕵️ 1. Diseccionando tu Cadena de Conexión

Cuando creaste tu base de datos en la nube (en Aiven, Railway, etc.), te dieron una URL de conexión que guardaste en tu `.env`. Tiene esta forma:

`mysql://usuario:contraseña@servidor.com:3306/nombre_bd`

Workbench no entiende esta URL entera. Te pide que le des cada pieza por separado. Así es como debes cortarla:
* **Usuario:** Lo que está entre `mysql://` y los dos puntos (`:`).
* **Contraseña:** Lo que está entre los dos puntos (`:`) y el arroba (`@`).
* **Servidor (Hostname):** Lo que está entre el arroba (`@`) y el puerto (`:`).
* **Puerto:** El número que sigue a los dos puntos (casi siempre `3306`).
* **Esquema (Default Schema):** El texto final después de la barra inclinada (`/`).

---

## 🔌 2. Configurando la conexión en MySQL Workbench

Abre tu programa de MySQL Workbench en tu ordenador y sigue estos pasos:

1. En la pantalla de inicio, pulsa el botón **+** junto a "MySQL Connections" para crear una nueva conexión.
2. Rellena el formulario con las piezas que hemos extraído de tu URL:
   * **Connection Name:** Ponle un nombre descriptivo (ej. `DevCampus - PRODUCCION`).
   * **Hostname:** Pega aquí la dirección de tu servidor.
   * **Port:** `3306`.
   * **Username:** Pega tu usuario.
   * **Password:** Haz clic en *"Store in Vault..."* y pega tu contraseña.
   * **Default Schema:** Pega el nombre de tu base de datos.
3. Pulsa el botón **"Test Connection"**. Si todo está bien configurado, verás un mensaje de éxito. ¡Tu ordenador acaba de atravesar internet y se ha conectado al servidor en la nube!
4. Haz clic en **OK** para guardar.

> **⚠️ REGLA DE ORO PROFESIONAL:** Para no confundirte y borrar datos reales por accidente, los desarrolladores suelen pintar las conexiones de producción de **color ROJO**. En Workbench, puedes hacer clic derecho sobre tu nueva conexión, elegir "Edit Connection", ir a la pestaña "Advanced" y marcar la opción para darle un color de advertencia.

---

## 🏗️ 3. Ejecutando el Script de Tablas (DDL)

Ahora que estás dentro de tu base de datos de producción:

1. Abre una nueva pestaña de SQL (Query).
2. Pega el script exacto que usamos en el Tema 04 y 05 del Módulo 7 para crear las tablas. Debe ser algo así:

```sql
CREATE TABLE users (
  id INT AUTO_INCREMENT PRIMARY KEY,
  name VARCHAR(100) NOT NULL,
  email VARCHAR(100) UNIQUE NOT NULL,
  age INT
);

CREATE TABLE posts (
  id INT AUTO_INCREMENT PRIMARY KEY,
  title VARCHAR(255) NOT NULL,
  content TEXT,
  user_id INT,
  FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE
);
```

3. Pulsa el **rayo amarillo** para ejecutar el código.
4. En el panel inferior (Output), verás los ticks verdes de éxito.

¡Ya está! Ahora tienes tus tablas creadas en internet. Si vuelves a Render y ejecutas tu script de `npm run seed_prod` (como vimos en el tema anterior), los datos se insertarán correctamente en estas nuevas tablas y tu API funcionará a la perfección.

---

## 🏆 Eres oficialmente Backend Developer

Saber programar está muy bien, pero saber **desplegar y gestionar tu infraestructura** es lo que te convierte en un profesional completo. 

¡Guarda este proyecto, añádelo a tu currículum y siéntete orgulloso de todo lo que has construido desde cero!