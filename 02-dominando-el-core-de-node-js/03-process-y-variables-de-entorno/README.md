# Tema 03: El objeto process y Variables de Entorno

En los temas anteriores hemos visto cómo interactuar con archivos y con el hardware. Pero, ¿cómo sabe Node.js desde dónde se está ejecutando? ¿Cómo podemos pasarle información "secreta" (como una clave de API o una contraseña) sin escribirla directamente en el código?

Todo esto sucede gracias a un objeto global llamado `process`.

---

## 🌎 1. ¿Qué es el objeto `process`?

El objeto `process` es una variable global que proporciona información y control sobre el proceso actual de Node.js que se está ejecutando. Al ser global, no necesitas importarlo, aunque por buenas prácticas modernas puedes encontrarlo como `import process from 'node:process'`.

### Propiedades útiles de `process`:
* **`process.pid`**: El identificador único del proceso en tu sistema operativo.
* **`process.platform`**: Identifica la plataforma (ej. 'win32', 'linux', 'darwin').
* **`process.version`**: La versión de Node.js que está corriendo.
* **`process.cwd()`**: (Es un método) Devuelve la ruta del directorio donde has ejecutado el comando (Current Working Directory).

---

## ⌨️ 2. Argumentos de entrada (`process.argv`)

¿Alguna vez te has preguntado cómo herramientas como `git` o `npm` reciben comandos? (Ejemplo: `npm install express`). 

Node.js guarda todo lo que escribes en la terminal en un array llamado `process.argv`.

```javascript
// script.js
console.log(process.argv);
```

Si ejecutas `node script.js hola mundo`, verás:
1. La ruta de ejecución de Node.
2. La ruta de tu archivo `script.js`.
3. **"hola"**
4. **"mundo"**

> **💡 Truco Pro:** Normalmente siempre ignoramos los dos primeros elementos usando `.slice(2)`.

---

## 🔐 3. Variables de Entorno (`process.env`)

Las **Variables de Entorno** son valores que viven fuera del código fuente, en el sistema operativo o en el entorno donde corre el servidor. 

Son vitales por dos razones:
1. **Seguridad:** No queremos subir nuestras contraseñas de base de datos a GitHub.
2. **Configuración:** El puerto de tu ordenador local suele ser el `3000`, pero en producción (internet) puede ser el `80`.

Podemos acceder a ellas mediante `process.env.NOMBRE_VARIABLE`.

---

## 📄 4. Archivos `.env` y `process.loadEnvFile()`

Históricamente, para usar archivos `.env` (donde guardamos nuestras variables) necesitábamos instalar un paquete externo llamado `dotenv`. 

**¡Ya no es necesario!** Desde Node.js 20.6+, tenemos una forma nativa de cargar estos archivos.

### Paso 1: Crea un archivo llamado `.env`
```text
PORT=3000
API_KEY=mi_clave_super_secreta_123
```

### Paso 2: Carga las variables en tu código
```javascript
import process from 'node:process';

// Cargamos el archivo .env de forma nativa
process.loadEnvFile(); 

const port = process.env.PORT;
const key = process.env.API_KEY;

console.log(`Servidor configurado en el puerto: ${port}`);
console.log(`Usando la clave: ${key}`);
```

---

## 🎯 Ejemplo Práctico: El script "Inteligente"

Vamos a crear un script que salude al usuario. El nombre puede venir por un argumento en la terminal o por una variable de entorno. Si no hay ninguno, usará "Invitado".

```javascript
import process from 'node:process';

// 1. Intentamos cargar variables de entorno
try {
  process.loadEnvFile();
} catch (e) {
  // Si no existe el archivo .env no pasa nada, seguimos
}

// 2. Prioridad: Argumento de terminal > Variable de Entorno > "Invitado"
const args = process.argv.slice(2);
const name = args[0] || process.env.USER_NAME || 'Invitado';

console.log(`¡Hola, ${name}!`);
console.log(`Estás ejecutando Node en: ${process.platform}`);
```

---

## ⚠️ Recordatorio de Seguridad
El archivo `.env` contiene secretos. **NUNCA** lo subas a tu repositorio de Git. Asegúrate de añadirlo siempre a tu archivo `.gitignore`.