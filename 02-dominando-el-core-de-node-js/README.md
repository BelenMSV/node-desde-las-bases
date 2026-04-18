# Módulo 02: Dominando el Core de Node.js (Interacción con el sistema)

¡Bienvenido al segundo módulo! Aquí es donde Node.js demuestra que no es solo "un navegador sin ventana". En esta sección, dejaremos atrás los conceptos básicos y aprenderemos a utilizar los **módulos nativos** que vienen instalados de fábrica en las entrañas de Node.js. 

Descubriremos cómo leer, transformar y escribir archivos en el disco duro, extraer información del hardware, manejar variables de entorno y proteger nuestra máquina de scripts maliciosos.

**⏱️ Tiempo estimado de estudio y práctica:** 3 - 4 horas.

## 🎯 Objetivos de Aprendizaje

- Dominar la lectura y escritura de archivos de forma asíncrona usando promesas (`node:fs/promises`).
- Manejar rutas de archivos de forma profesional para evitar bugs entre Windows, macOS y Linux (`node:path`).
- Extraer información en tiempo real del hardware y el sistema operativo (`node:os`).
- Comprender y utilizar el objeto global `process` para inyectar y leer Variables de Entorno de forma nativa.
- Implementar el Modelo de Permisos de Node.js para restringir el acceso a la red y al sistema de archivos (`--permission`).

## 📚 Temario Detallado

1. **[Módulos nativos y nuestro primer Script](./01-modulos-nativos-y-scripting/README.md)**
   > Aprende a interactuar con el disco duro de tu ordenador. Crearemos un script automatizado para leer, transformar y escribir texto de forma asíncrona, solucionando el problema de las rutas multiplataforma.

2. **[El módulo OS e Información del Sistema](./02-modulo-os-e-informacion/README.md)**
   > Convierte tu script en una herramienta de diagnóstico. Descubre cómo preguntarle a Node.js cuánta memoria libre tienes, los núcleos de tu CPU o el tiempo de actividad del sistema.

3. **[El objeto process y Variables de Entorno](./03-process-y-variables-de-entorno/README.md)**
   > *NUEVO:* Descubre qué es el objeto global `process`. Aprenderemos a ocultar datos sensibles (como contraseñas) usando archivos `.env` y a cargarlos utilizando la nueva herramienta nativa `process.loadEnvFile()`.

4. **[Seguridad y Sistema de Permisos](./04-seguridad-y-permisos/README.md)**
   > Node.js tiene poder absoluto por defecto. Aprende a ejecutar tu código en "modo seguro", otorgando permisos granulares desde la terminal y comprobándolos directamente en tu código para evitar accesos no autorizados.

---

[🔙 Volver al Índice Global](../README.md)