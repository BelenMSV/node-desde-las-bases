# Módulo 07: Bases de Datos Relacionales (MySQL + mysql2)

¡Bienvenido al séptimo módulo! En el módulo anterior descubrimos la flexibilidad del mundo NoSQL con MongoDB y Mongoose. Sin embargo, en la industria real (bancos, tiendas online, sistemas de reservas), los datos suelen tener una estructura estricta y estar fuertemente conectados entre sí. 

En este módulo daremos el salto al ecosistema de las **Bases de Datos Relacionales (SQL)**. Y para mantener la filosofía de este curso ("aprender las bases antes de usar magia"), no utilizaremos ningún ORM moderno como Prisma o Sequelize. Nos ensuciaremos las manos escribiendo sentencias SQL puras y conectándolas a Node.js utilizando la librería `mysql2`.

**⏱️ Tiempo estimado de estudio y práctica:** 4 - 5 horas.

## 🎯 Objetivos de Aprendizaje

- Entender el paradigma relacional: Tablas, Filas, Columnas y Claves Primarias/Foráneas.
- Desplegar una base de datos MySQL gratuita en la nube utilizando plataformas modernas (ej. Aiven o Railway).
- Conectar Node.js/Express a MySQL utilizando el paquete `mysql2` con soporte para Promesas.
- Configurar y comprender la importancia de un **Pool de Conexiones** para evitar colapsos en el servidor.
- Comprender la vulnerabilidad número uno de la web (**Inyección SQL**) y cómo prevenirla usando *Prepared Statements*.
- Ejecutar operaciones CRUD completas utilizando sintaxis SQL pura (`SELECT`, `INSERT`, `UPDATE`, `DELETE`).
- Relacionar tablas y extraer información compleja utilizando la cláusula `JOIN`.

## 📚 Temario Detallado

1. **[El mundo Relacional y la Nube](./01-el-mundo-relacional-y-la-nube/README.md)**
   > El contraste definitivo. Descubre por qué un Excel es el mejor ejemplo para entender SQL. Además, desplegaremos nuestro servidor MySQL en la nube y obtendremos nuestra cadena de conexión.

2. **[Conectando Node con mysql2 (Promesas y Pools)](./02-conectando-node-con-mysql2/README.md)**
   > Instala el driver oficial `mysql2`. Aprende por qué usar la versión de promesas (`mysql2/promise`) es vital, y por qué crear un *Pool* es obligatorio para que tu API no se cuelgue al recibir muchas peticiones simultáneas.

3. **[Seguridad e Inyección SQL (El ataque de Bobby Tables)](./03-inyeccion-sql-y-seguridad/README.md)**
   > Concatenar strings en una base de datos SQL es un error fatal. Descubre cómo los hackers pueden borrar toda tu base de datos desde un simple formulario y aprende a blindar tu código usando consultas parametrizadas (`?`).

4. **[Operaciones CRUD con SQL puro](./04-crud-con-sql-puro/README.md)**
   > Olvídate de los métodos mágicos de Mongoose. Conectaremos nuestros controladores para insertar nuevos usuarios y listar los existentes escribiendo el código SQL nosotros mismos.

5. **[El poder de las relaciones (Claves Foráneas y JOINs)](./05-el-poder-de-las-relaciones/README.md)**
   > La verdadera magia de SQL. Aprenderemos a crear una tabla de "Posts" vinculada a los "Users". En lugar de hacer múltiples peticiones, usaremos un `JOIN` para que la base de datos nos devuelva toda la información cruzada en un milisegundo.

---

[🔙 Volver al Índice Global](../README.md)