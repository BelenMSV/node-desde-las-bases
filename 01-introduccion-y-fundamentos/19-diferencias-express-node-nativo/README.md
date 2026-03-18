# Tema 19: Diferencias entre usar Express y no usarlo

Ya tenemos nuestra primera ruta funcionando en Express y hemos visto lo rápido que es levantar el servidor. En esta clase vamos a comparar directamente **un servidor creado con Node.js nativo frente al mismo servidor usando Express**, para entender de forma clara qué cambia y por qué este framework facilita tanto el trabajo.

La idea no es demonizar el servidor nativo (de hecho, es excelente para entender los fundamentos de cómo funciona la web por debajo). El objetivo es entender **qué problemas de escalabilidad y mantenimiento aparecen** cuando el código empieza a crecer.



---

## 🏗️ 1. Servidor sin Express (Node.js nativo)

Cuando usamos el módulo `node:http`, tenemos un enfoque **imperativo**: tenemos que encargarnos manualmente de decirle a Node *cómo* hacer cada paso del ciclo de vida de la petición.

Si revisamos nuestro antiguo `server.js`, teníamos que:
1. Comprobar el método HTTP (`GET`, `POST`, etc.).
2. Parsear la URL para obtener el `pathname` (`url.split('?')`).
3. Gestionar cabeceras (`res.setHeader('Content-Type', ...)`).
4. Enviar la respuesta y cerrarla manualmente con `res.end()`.

**Ejemplo de cómo lo hacíamos (El problema):**
```javascript
import http from 'node:http';

const server = http.createServer((req, res) => {
  const { url, method } = req;
  const [pathname] = url.split('?');

  // Lógica de red mezclada con lógica de negocio
  if (method === 'GET') {
    if (pathname === '/') {
      res.writeHead(200, { 'Content-Type': 'text/html; charset=utf-8' });
      res.end('<h1>¡Hola Mundo desde lo nativo!</h1>');
      return;
    }
    
    if (pathname === '/health') {
      res.writeHead(200, { 'Content-Type': 'application/json; charset=utf-8' });
      res.end(JSON.stringify({ status: 'ok', uptime: process.uptime() }));
      return;
    }
  }

  // Manejo del 404
  res.writeHead(404);
  res.end('No encontrado');
});

server.listen(1234);
```

Este código funciona, pero tiene inconvenientes graves:
* **Se vuelve repetitivo:** Cada ruta nueva requiere validaciones y cabeceras similares.
* **Es más difícil de leer:** La lógica de nuestra aplicación se mezcla con la lógica de red.
* **Escala mal:** A medida que añades 10, 20 o 50 rutas, el "infierno de los condicionales" se vuelve inmanejable.

---

## ✨ 2. Servidor usando Express (La solución)

Con Express, el mismo comportamiento se escribe de forma **declarativa** y limpia. Le decimos al framework *qué* queremos hacer, y él se encarga del *cómo*.

Fíjate en lo limpio que queda nuestro código ahora:

```javascript
import express from 'express';

const PORT = process.env.PORT ?? 1234;
const app = express();

// 1. Enrutamiento semántico
app.get('/', (req, res) => {
  res.send('<h1>¡Hola Mundo con Express!</h1>');
});

app.get('/health', (req, res) => {
  // res.json() convierte el objeto, pone el status 200 y añade las cabeceras automáticamente
  return res.json({ status: 'ok', uptime: process.uptime() });
});

// 2. Manejo del 404 (Middleware global)
app.use((req, res) => {
  res.status(404).send('No encontrado');
});

app.listen(PORT, () => {
  console.log(`🚀 Servidor levantado en http://localhost:${PORT}`);
});
```

### ¿Qué ha cambiado aquí?
* **Enrutamiento semántico:** Usamos `app.get()`, lo que hace que el código sea autodocumentado.
* **Abstracción de cabeceras:** `res.send()` y `res.json()` detectan automáticamente el contenido y añaden los *headers* necesarios por ti.
* **Sin parsing manual:** No hemos tenido que parsear la URL para saber la ruta.
* **Ciclo de respuesta automático:** Express cierra la conexión por nosotros, no hace falta llamar a `res.end()`.

---

## ⚖️ Las 3 diferencias clave

1. **Legibilidad y Mantenibilidad:** Con Express, al leer el código es evidente qué rutas existen y qué hace cada una. En el nativo, esa información está dispersa dentro de un gran bloque de condicionales.
2. **Eliminación de código repetitivo (Boilerplate):** Express elimina la necesidad de escribir una y otra vez el código para configurar códigos de estado o transformar objetos a JSON.
3. **Facilidad para escalar:** Añadir un nuevo *endpoint* en Express es tan sencillo como añadir tres líneas de código. En un servidor nativo, implica refactorizar el flujo de control de la aplicación.

---

## 🎯 Para terminar…

Express no hace "magia", simplemente usa el módulo `http` nativo por debajo de una forma optimizada para nosotros, ahorrándonos tiempo y reduciendo la complejidad accidental.

* ✅ Comparamos el enfoque **imperativo** (nativo) vs el **declarativo** (Express).
* ✅ Entendimos por qué `res.send()` y `res.json()` son más potentes que `res.end()`.
* ✅ Vimos cómo Express simplifica la estructura de nuestro proyecto.
* ✅ Aprendimos que Express nos permite centrarnos en la **lógica de nuestra aplicación** en lugar de en los detalles del protocolo HTTP.

En las próximas clases seguiremos profundizando en cómo Express maneja datos más complejos y cómo estructurar nuestra API profesionalmente.