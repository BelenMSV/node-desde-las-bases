# Tema 18: Creando nuestro primer servidor con Express

En la clase anterior vimos qué es Express y por qué tiene sentido introducirlo cuando nuestra API empieza a crecer. 

Hasta ahora teníamos un servidor creado con `node:http`, donde comprobábamos manualmente el método (`GET`, `POST`), comparábamos el `pathname`, gestionábamos cabeceras a mano y enviábamos la respuesta manualmente. Todo funcionaba, pero el código empezaba a ser largo y repetitivo.

En esta clase damos el siguiente paso: **crear nuestro primer servidor con Express** y dejar atrás el servidor HTTP hecho a mano. Vamos a empezar desde cero en una carpeta nueva (por ejemplo, `04-express`).

---

## 🛠️ 1. Inicializar el proyecto y configurar package.json

Lo primero que necesitamos es inicializar nuestro proyecto. Vamos a usar un atajo para que NPM no nos haga preguntas y genere automáticamente el archivo `package.json`:

```bash
npm init -y
```

Una vez creado, vamos a abrir ese `package.json` y hacer un cambio fundamental. Como queremos usar la sintaxis moderna de importaciones (`import/export`), debemos indicarle a Node que nuestro proyecto es de tipo módulo:

```json
{
  "name": "04-express",
  "version": "1.0.0",
  "description": "",
  "main": "index.js",
  "type": "module", // 👈 ¡Añadimos esto!
  "author": "Belen",
  "license": "ISC"
}
```

## 📦 2. Instalar Express

Ahora que el proyecto está inicializado, instalamos Express en el proyecto usando npm:

```bash
npm install express
```

A partir de aquí, ya tenemos nuestra carpeta `node_modules` y estamos listos para importar Express y crear nuestra aplicación.

---

## 🚀 3. Nuestro primer "Hola Mundo"

Creamos un archivo `index.js` en la raíz de nuestra carpeta. Fíjate en la poca cantidad de código que necesitamos ahora:

```javascript
import express from 'express';

// 1. Recuperamos el puerto
const PORT = process.env.PORT ?? 1234;

// 2. Creamos la aplicación de Express (Sustituye a createServer)
const app = express();

// 3. Definimos nuestra primera ruta
app.get('/', (req, res) => {
  res.send('<h1>Hola mundo desde Express</h1>');
});

// 4. Levantamos el servidor
app.listen(PORT, () => {
  console.log(`🚀 Servidor escuchando en http://localhost:${PORT}`);
});
```

### 🔍 Analizando el código: Comparación con el servidor nativo

Si comparamos este código con nuestro antiguo servidor nativo, aquí pasan varias cosas muy importantes:

1. **La instancia `app`:** Al hacer `const app = express()`, esta variable se convierte en el núcleo de nuestra aplicación. Ya no necesitamos `createServer` ni callbacks manuales complejos.
2. **Definición de rutas (`app.get`):** En lugar de usar condicionales `if` o `switch` para validar el método, Express nos permite definir rutas de forma directa. No hay *parsing* manual de la URL.
3. **Gestión de la respuesta (`res.send`):** No tenemos que configurar `res.writeHead` ni cerrar la respuesta a mano con `res.end()`. Express se encarga de todo el ciclo de la petición automáticamente.

El comportamiento final es el mismo que teníamos antes, pero el código es mucho más **declarativo**, legible y fácil de mantener. Hemos eliminado todo el código repetitivo (*boilerplate*).

---

## 🔄 4. Ejecución: ¿Por qué usamos `--watch` en vez de Nodemon?

Para arrancar nuestro servidor, vamos a la terminal y ejecutamos:

```bash
node --watch index.js
```

Si vas a tu navegador y entras en `http://localhost:1234`, verás tu mensaje renderizado. 

**¿Por qué usamos `--watch` y no instalamos `nodemon`?**
En proyectos más antiguos verás que instalan `nodemon` para reiniciar el servidor con cada cambio. Sin embargo, al usar una **versión reciente de Node.js (v24)**, Node ya incluye esta funcionalidad de forma nativa con el flag `--watch`. Así mantenemos nuestro proyecto más ligero y sin dependencias extra.

---

## 🤯 5. El secreto de los grandes frameworks

Devolver un simple texto (`Hola mundo desde Express`) puede parecer demasiado básico, pero **esto es exactamente lo que hacen por debajo los frameworks más grandes del mercado.**

Frameworks gigantes como **Next.js** (basado en React) o **NestJS**, en realidad utilizan Express (u otros similares) por debajo. Conceptualmente, hacen algo parecido a esto:

```javascript
// Simplificación de cómo funciona un framework como Next.js por debajo
app.get('/', (req, res) => {
  // 1. React renderiza tu aplicación y la convierte en un String de HTML
  const htmlRenderizado = React.renderToString(<App />);
  
  // 2. Express envía ese String al navegador
  res.send(htmlRenderizado);
});
```

Entender esta base te dará superpoderes para dominar cualquier herramienta más compleja en el futuro.

---

## 🎯 Resumen: Qué hemos conseguido hoy

En muy pocas líneas hemos logrado un avance gigante. Los puntos clave de esta clase son:

* ✅ Instalamos Express en el proyecto.
* ✅ Creamos una aplicación con `express()`.
* ✅ Levantamos el servidor con `app.listen`, simplificando el arranque.
* ✅ Definimos rutas claras con `app.get`.
* ✅ Reemplazamos el servidor nativo por Express, eliminando el código repetitivo.

En la siguiente clase empezaremos a añadir más rutas y comportamiento real a nuestro servidor usando las herramientas de Express. ¡Vamos a ello!