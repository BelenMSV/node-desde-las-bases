# Módulo 06: Bases de Datos y Persistencia (MongoDB + Mongoose)

¡Bienvenido al sexto módulo! Hasta ahora, nuestra API sufría de "amnesia": cada vez que reiniciábamos el servidor, todos los datos que habíamos creado desaparecían porque solo vivían en la memoria RAM de nuestro ordenador. 

En este módulo daremos el salto definitivo al desarrollo profesional conectando nuestra aplicación a una base de datos real en la nube. Aprenderemos el paradigma NoSQL utilizando **MongoDB**, y utilizaremos la librería **Mongoose** (un ODM) para estructurar, validar y guardar nuestros datos de forma segura, completando así nuestra arquitectura MVC.

**⏱️ Tiempo estimado de estudio y práctica:** 4 - 5 horas.

## 🎯 Objetivos de Aprendizaje

- Entender las diferencias arquitectónicas entre bases de datos Relacionales (SQL) y No Relacionales (NoSQL).
- Desplegar un clúster de base de datos gratuito en la nube utilizando MongoDB Atlas.
- Proteger credenciales y cadenas de conexión utilizando Variables de Entorno (`.env`).
- Conectar una aplicación de Node.js/Express a MongoDB utilizando Mongoose.
- Diseñar y aplicar esquemas de validación estrictos (Schemas) para modelos de datos NoSQL.
- Ejecutar operaciones CRUD completas (Crear, Leer, Actualizar y Borrar) interactuando con la base de datos real.
- Implementar un manejo de errores asíncronos profesional mediante bloques `try/catch`.

## 📚 Temario Detallado

1. **[Bases de Datos (SQL vs NoSQL) y la Nube](./01-sql-vs-nosql-y-nube/README.md)**
   > El debate fundamental. Descubre cuándo usar tablas y cuándo usar documentos JSON. Además, crearemos nuestro propio servidor de base de datos en la nube totalmente gratis.

2. **[Conectando Node a MongoDB (Mongoose)](./02-conectando-node-mongoose/README.md)**
   > Olvídate de guardar contraseñas en tu código. Aprenderemos a inyectar de forma segura nuestra cadena de conexión y conectaremos nuestro servidor de Express a la nube mediante Mongoose.

3. **[El Modelo de Datos (Schemas)](./03-modelo-de-datos-schemas/README.md)**
   > MongoDB es muy flexible, a veces demasiado. Usaremos Mongoose para crear "reglas de negocio" (Schemas) que obliguen a los datos a tener un formato correcto antes de guardarse en la carpeta `models`.

4. **[Operaciones CRUD I (Crear y Leer)](./04-crud-1-crear-leer/README.md)**
   > Conectaremos nuestros Controladores con el Modelo. Aprenderemos a hacer peticiones asíncronas a la base de datos para guardar nuevos usuarios y recuperar la lista completa.

5. **[Operaciones CRUD II (Actualizar y Borrar)](./05-crud-2-actualizar-borrar/README.md)**
   > Completamos la API introduciendo los métodos HTTP que nos faltaban: `PATCH` para editar campos específicos de un usuario y `DELETE` para eliminar registros permanentemente.

6. **[Manejo de errores asíncronos](./06-manejo-errores-asincronos/README.md)**
   > ¿Qué pasa si la base de datos se cae, tarda mucho en responder o el usuario envía un ID inválido? Aprenderemos a envolver nuestra lógica para que el servidor nunca explote y devuelva errores elegantes.

---

[🔙 Volver al Índice Global](../README.md)