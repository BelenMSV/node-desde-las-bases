# Tema 03: Despliegue en Render (Tu API en el mundo real)

Render es lo que llamamos una plataforma **PaaS** (Platform as a Service). Su trabajo es alquilarte un ordenador en la nube (un servidor), conectarlo a tu GitHub, descargar tu código, instalar las dependencias y dejarlo encendido 24 horas al día. 

Y lo mejor de todo: **tienen un plan gratuito perfecto para estudiantes y proyectos personales**.

---

## 🛠️ Paso 1: Crear tu cuenta y vincular GitHub

1. Entra en [render.com](https://render.com/).
2. Haz clic en el botón **"Get Started"** (arriba a la derecha).
3. Selecciona la opción **"Sign up with GitHub"** o **"GitHub"**.
   * *¿Por qué?* Porque Render necesita permiso para leer el repositorio que acabamos de crear en el Tema 02. Al iniciar sesión con GitHub, el puente se construye automáticamente.
4. Sigue los pasos de autorización hasta llegar al panel de control principal (Dashboard).

---

## 🏗️ Paso 2: Crear el "Web Service"

Render ofrece muchos productos (bases de datos, tareas en segundo plano...), pero lo que nosotros hemos construido es un servidor web (una API que escucha peticiones HTTP).

1. En tu panel principal de Render, haz clic en el botón **"New"** (Nuevo) y selecciona **"Web Service"**.
2. Verás una lista con tus repositorios de GitHub. 
3. Busca el repositorio de tu API (ej. `devcampus-backend`) y haz clic en el botón **"Connect"** que aparece a su derecha.

---

## ⚙️ Paso 3: Configuración del Servidor

Ahora Render te preguntará cómo quieres configurar este nuevo ordenador. Rellena el formulario exactamente así:

* **Name:** Ponle el nombre que quieras (ej. `devcampus-api-produccion`). Esto formará parte de tu URL pública final.
* **Region:** Elige la más cercana a ti (ej. Frankfurt si estás en Europa, o Ohio si estás en América).
* **Branch:** `main` (La rama principal de tu código).
* **Runtime:** Selecciona **Node**.
* **Build Command:** `npm install` 
  * *(Aquí le decimos a Render que, antes de encender nada, debe descargar la carpeta node_modules).*
* **Start Command:** `npm start`
  * *(Aquí Render ejecutará tu script perfecto que configuramos en el `package.json`).*
* **Instance Type:** Asegúrate de seleccionar **Free** ($0/month).

> 🛑 **¡ESPERA! NO LE DES A CREATE AÚN.** Tu servidor necesita la contraseña de la base de datos para arrancar. Si le das a crear ahora, Node.js no encontrará la base de datos y el servidor se estrellará.

---

## 🔐 Paso 4: Las Variables de Entorno (El secreto de la nube)

Como no subimos el archivo `.env` a GitHub, tenemos que darle esas contraseñas a Render directamente.

1. Haz scroll hacia abajo en esa misma página hasta encontrar la sección **"Advanced"** y haz clic para expandirla.
2. Busca el botón **"Add Environment Variable"** (Añadir Variable de Entorno).
3. Crea las variables exactas que usabas en tu archivo local:
   * **Key:** `DATABASE_URL` | **Value:** *Pega aquí la URL de tu MySQL en la nube (Aiven, Railway, etc).*
   * **Key:** `JWT_SECRET` | **Value:** *Pega aquí tu clave secreta para los tokens (si usaste JWT).*
   
> **Nota sobre el PORT:** No necesitas añadir la variable `PORT`. Render automáticamente inyecta un puerto (suele ser el 10000) en el Sistema Operativo. Tu código (`process.env.PORT ?? 1234`) lo detectará mágicamente.

4. Ahora sí, baja del todo y haz clic en el botón gigante **"Create Web Service"**.

---

## 🍿 Paso 5: Mirando los Logs (La consola remota)

En cuanto pulses crear, la pantalla cambiará y verás una ventana negra llena de texto moviéndose rápido. **Esos son los Logs del servidor.** Es exactamente lo mismo que veías en tu terminal (VS Code), pero ocurriendo en un servidor real.

Verás fases claras:
1. `Cloning from GitHub...` (Descargando tu código).
2. `Running npm install...` (Instalando dependencias).
3. `Running npm start...` (Encendiendo el servidor).
4. Si todo va bien, verás tus propios mensajes (ej. `✅ Conexión exitosa a MySQL` y `🚀 Servidor en puerto 10000`).

Justo encima de esa consola negra, verás un texto que dice **"Live"** en verde, y debajo del nombre de tu proyecto, **un enlace público** (ej. `https://devcampus-api-produccion.onrender.com`).

**¡TU API YA ESTÁ EN INTERNET! 🎉**

---

## 🌱 Paso 6: Poblar la Base de Datos en Producción (El Seed)

Tu API está online, pero tu base de datos de producción seguramente esté vacía (no tiene temas ni cursos). ¿Recuerdas el script `seed_prod` que configuramos? Vamos a ejecutarlo.

1. En el panel lateral izquierdo de tu proyecto en Render, haz clic en **"Shell"** (Consola).
2. Se abrirá una terminal negra abajo. Esto es una conexión directa al cerebro de tu servidor en la nube.
3. Escribe el comando que preparamos:
   ```bash
   npm run seed_prod
   ```
4. Pulsa Enter. Verás cómo tu script se ejecuta, inserta los datos y termina. ¡Acabas de inyectar datos reales en tu base de datos de producción desde el panel de control!

---

## 🧪 Paso 7: ¡A probar en Bruno / Postman!

1. Copia tu nueva URL pública (ej. `https://devcampus-api-produccion.onrender.com`).
2. Abre Bruno o Postman.
3. Haz un **GET** a `https://devcampus-api-produccion.onrender.com/tu-ruta-de-temas`.

Deberías recibir tu JSON con todos los datos. Ahora puedes enviarle ese enlace a cualquier amigo, compañero o a tu Frontend (React/Angular), y tu API responderá 24 horas al día.

*(⚠️ Nota sobre el Plan Gratuito: Render "duerme" los servidores gratuitos si no reciben tráfico en 15 minutos. Cuando hagas la primera petición después de un rato, tardará unos 30-50 segundos en despertar. Las siguientes peticiones serán ultrarrápidas).*