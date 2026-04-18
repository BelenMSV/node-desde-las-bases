# Módulo 04: La Web con Node.js (Servidores Nativos)

Bienvenido al cuarto módulo. Ha llegado el momento de dejar atrás los scripts de consola y adentrarnos en lo que Node.js sabe hacer mejor: **La Web**. 

En este módulo no utilizaremos ningún framework. Construiremos una API REST completamente funcional "a mano", peleando cuerpo a cuerpo con el protocolo HTTP. Entender cómo funcionan las peticiones, las cabeceras y los flujos de datos por debajo te dará una ventaja competitiva enorme frente a los desarrolladores que solo saben usar "herramientas mágicas".

**⏱️ Tiempo estimado de estudio y práctica:** 4 - 6 horas.

## 🎯 Objetivos de Aprendizaje

- Levantar y mantener un servidor web utilizando exclusivamente el módulo nativo `node:http`.
- Gestionar configuraciones seguras y puertos dinámicos con Variables de Entorno (`.env`).
- Implementar un sistema de Enrutamiento (Routing) manual para responder a diferentes URLs.
- Consumir la API como un profesional utilizando Clientes API (Bruno, Postman).
- Comprender y aplicar correctamente los Códigos de Estado HTTP (Status Codes).
- Parsear cuerpos de peticiones `POST` a partir de flujos de datos (Streams) de forma nativa.
- Implementar lógicas complejas de filtrado y paginación (Limit y Offset) separando el *pathname* del *querystring*.

## 📚 Temario Detallado

1. **[Nuestro primer Servidor HTTP y el modo Watch](./01-primer-servidor-http-y-watch/README.md)**
   > Crea un servidor que se quede "escuchando" indefinidamente, aprende a devolver respuestas y usa el flag `--watch` para recargar los cambios en vivo sin dependencias.

2. **[Puertos dinámicos y Variables de Entorno](./02-puertos-dinamicos-y-variables-entorno/README.md)**
   > Evita colisiones de puertos asignando el puerto `0`. Además, aprende a cargar archivos secretos `.env` usando la nueva API nativa de Node.js.

3. **[Enrutamiento (Routing) y respuestas JSON](./03-enrutamiento-y-respuestas-json/README.md)**
   > Haz que tu servidor sea inteligente. Lee la URL solicitada para devolver diferentes datos JSON, añade una ruta Health Check y gestiona los errores 404.

4. **[Clientes API y Métodos HTTP](./04-clientes-api-y-metodos-http/README.md)**
   > El navegador se queda corto. Instala Bruno (o Postman), descubre los "verbos" de la web (GET, POST, DELETE...) y protege tus rutas analizando `req.method`.

5. **[Códigos de Estado HTTP](./05-codigos-de-estado-http/README.md)**
   > Descubre qué significan los famosos números 200, 404 o 500 y aprende la regla de oro de un buen desarrollador Backend: responder siempre con el estado correcto.

6. **[Peticiones POST, parseo de JSON y UUIDs nativos](./06-peticiones-post-parseo-json-uuids-nativos/README.md)**
   > Da el salto y permite que los usuarios creen datos. Utiliza los módulos `stream/consumers` y `crypto` para leer el cuerpo de la petición y generar identificadores únicos.

7. **[Query Parameters y Paginación](./07-query-parameters-y-paginacion/README.md)**
   > Protege tu API de colapsos. Aprende a separar los parámetros de búsqueda de la URL principal usando `URLSearchParams` para crear una paginación profesional.

8. **[Rutas dinámicas y el problema de CORS](./08-rutas-dinamicas-y-cors/README.md)**
   > Intenta buscar un usuario por su ID (`/users/1`) y descubre la pesadilla de parsear rutas a mano. Además, aprende por qué los navegadores bloquean tu API y cómo devolver cabeceras de seguridad.

---

[🔙 Volver al Índice Global](../README.md)