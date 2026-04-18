# Módulo 08: Despliegue y Producción (Llevando tu API al mundo real)

¡Has llegado al final del camino! Tu API ya es potente, segura y está conectada a bases de datos en la nube. Sin embargo, todavía vive en tu ordenador (`localhost`). Si apagas tu PC, tu API muere.

En este último módulo aprenderemos a "subir" nuestro código a un servidor profesional que esté encendido 24/7. Utilizaremos **Render**, una plataforma de despliegue moderna que se conecta directamente a nuestro **GitHub** para que cada vez que subas un cambio, tu API se actualice sola en internet (Despliegue Continuo).

**⏱️ Tiempo estimado de estudio y práctica:** 2 - 3 horas.

## 🎯 Objetivos de Aprendizaje

- Preparar el código para producción (Puertos dinámicos, Scripts y Engines).
- Entender el flujo de trabajo profesional: **Local -> GitHub -> Cloud**.
- Configurar Variables de Entorno en un entorno de producción real.
- Desplegar una aplicación Node.js en **Render.com**.
- Conectar un cliente SQL local a una base de datos remota.
- Proteger tu API restringiendo los dominios permitidos (CORS).

## 📚 Temario Detallado

1. **[Preparando la API para Producción](./01-preparando-produccion/README.md)**
   > No todo lo que funciona en tu PC funciona en internet. Aprenderemos a configurar el puerto dinámico y a separar los scripts de inicio.

2. **[GitHub: El puente hacia la nube](./02-github-y-secretos/README.md)**
   > Subiremos nuestro código a un repositorio. Aprenderemos qué archivos NO subir y cómo gestionar nuestras contraseñas de base de datos de forma profesional.

3. **[Despliegue en Render.com](./03-despliegue-en-render/README.md)**
   > El gran momento. Conectaremos Render con GitHub, configuraremos las variables de entorno en la nube y lanzaremos nuestra API a una URL pública.

4. **[Bonus: Gestionando la BD en Producción](./04-gestion-bd-produccion/README.md)**
   > Tu API está online, pero tu base de datos está vacía. Aprenderemos a conectar MySQL Workbench a la nube para ejecutar nuestros scripts de creación de tablas.

5. **[Bonus: Seguridad y CORS en Producción](./05-cors-en-produccion/README.md)**
   > El escudo final. Aprenderemos a bloquear nuestra API para que solo nuestro propio Frontend tenga permiso para consumir los datos, evitando ataques externos.

---
[🔙 Volver al Índice Global](../README.md)