# Módulo 05: La era de Express.js (Frameworks y APIs modernas)

Bienvenido al quinto módulo. Después de haber construido servidores a mano y haber peleado con las entrañas de Node.js, te has ganado un respiro. 

En este módulo daremos el salto a **Express**, el framework minimalista más utilizado en el ecosistema de Node.js. Descubriremos cómo esta herramienta nos ahorra cientos de líneas de código repetitivo, nos permite crear rutas de forma declarativa y nos introduce al poderoso mundo de los Middlewares. 

**⏱️ Tiempo estimado de estudio y práctica:** 4 - 5 horas.

## 🎯 Objetivos de Aprendizaje

- Entender la diferencia entre una librería y un framework minimalista.
- Configurar y levantar un servidor web utilizando Express.
- Diferenciar el enfoque imperativo (Node nativo) del declarativo (Express).
- Dominar el ciclo de vida de la petición a través de los **Middlewares** y la función `next()`.
- Leer datos complejos en formato JSON utilizando el middleware integrado `express.json()`.
- Simplificar el uso de parámetros de ruta (`req.params`) y de búsqueda (`req.query`).
- Configurar cabeceras de seguridad y políticas CORS utilizando paquetes de terceros (`cors`).
- Escalar la aplicación dividiendo el código en múltiples archivos mediante `express.Router()`.

## 📚 Temario Detallado

1. **[Introducción a Express (El fin del "Node Puro")](./01-introduccion-express/README.md)**
   > Justificamos el cambio. Descubre por qué el servidor nativo escala mal y qué ventajas nos ofrece el framework estándar de la industria.

2. **[Creando nuestro primer servidor con Express](./02-primer-servidor-con-express/README.md)**
   > Inicializa un proyecto, instala Express y levanta tu primer servidor con apenas un par de líneas de código y el modo `--watch`.

3. **[Diferencias entre usar Express y no usarlo](./03-diferencias-express-node-nativo/README.md)**
   > Una comparativa lado a lado. Observa cómo desaparece el "infierno de los condicionales" y cómo la gestión de cabeceras se automatiza.

4. **[Middlewares en Express (El corazón del framework)](./04-Middlewares/README.md)**
   > Entiende el concepto más importante de Express. Crea funciones intermedias que interceptan las peticiones, añade logs globales y domina el uso de `next()`.

5. **[Peticiones POST y el middleware express.json()](./05-peticiones-post-y-body/README.md)**
   > Despídete de los *streams* manuales. Aprende a leer datos JSON enviados por el usuario utilizando el middleware nativo más famoso de Express.

6. **[Rutas dinámicas y Query Strings en Express](./06-rutas-dinamicas-y-query/README.md)**
   > Extraer datos de la URL ahora es magia. Aprende a usar `req.params` para URLs como `/users/1` y `req.query` para filtrado y paginación rápida.

7. **[Solucionando CORS con paquetes de terceros](./07-cors-en-express/README.md)**
   > Deja de enviar peticiones `OPTIONS` a mano. Descubre lo fácil que es dar acceso a tu Frontend instalando y aplicando el middleware global `cors`.

8. **[Arquitectura y express.Router()](./08-arquitectura-y-router/README.md)**
   > Tu archivo principal empieza a crecer. Aprende a separar tus rutas en diferentes archivos (ej. `routes/users.js`) para mantener tu proyecto organizado y escalable.

---

[🔙 Volver al Índice Global](../README.md)