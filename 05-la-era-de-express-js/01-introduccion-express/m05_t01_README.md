# Tema 17: Introducción a Express (El fin del "Node Puro")

Hasta ahora hemos estado trabajando con **Node.js puro**, sin ningún tipo de dependencias externas. Hemos creado pequeños programas de línea de comandos, utilidades para manejar archivos, obtenido información del sistema y, finalmente, levantamos un **servidor HTTP completamente nativo** usando el módulo `node:http`.

Ese servidor funcionaba estupendamente: devolvía JSON, permitía hacer peticiones `GET` y `POST`, e incluso le añadimos lógica de paginación con `limit` y `offset`. Todo estaba hecho con JavaScript puro, demostrando que podemos construir una API sin instalar absolutamente nada. 

Y eso está muy bien... hasta que deja de estarlo.

---

## 😫 El problema del servidor nativo

Usar `http` nativo ha sido excelente para entender las bases reales de cómo funciona un servidor. Pero cuando la API empieza a crecer, esta aproximación empieza a ser *un poco rollo*. 

Aunque el servidor nativo funciona y es potente, a medida que añadimos rutas el código empieza a volverse:
* **Más difícil de entender:** Nos perdemos en un mar de llaves y validaciones.
* **Más repetitivo:** Escribimos la misma lógica una y otra vez para tareas básicas.
* **Más costoso de mantener:** Cualquier cambio implica tocar estructuras muy delicadas.

Si revisamos nuestro último servidor, tenemos el "infierno de los `if`" para comprobar el método HTTP y el `pathname`. Tenemos que gestionar manualmente las cabeceras, parsear la URL a mano para sacar los parámetros y manejar *streams* complejos simplemente para leer el *body* de una petición POST y transformar los datos. 
  
Todo esto hace que tengamos que escribir **mucho más código del necesario** para resolver problemas comunes.



---

## 🚀 La Solución: ¿Qué es Express?

Aquí es donde entra **Express**, el framework más utilizado en todo el ecosistema de Node.js. 

> **¿Framework o Librería?** > Sus propios creadores lo definen en su web como un *"framework web rápido, flexible y minimalista"*. Y sí, es un framework. No es una opinión. Si alguien no está de acuerdo, que vaya a quejarse con el creador (a mí soltadme del brazo).

Express existe para resolver justo los problemas de complejidad del código nativo, dándonos una capa de abstracción sobre el módulo `http` que nos ahorra muchísimo trabajo manual.

### Características principales:
* **Minimalista:** No te impone una estructura de carpetas ni te obliga a seguir una arquitectura concreta. Te da utilidades y tú decides cómo organizar tu proyecto.
* **Flexible (Sin opiniones):** Al no ser "opiniado" (no tiene reglas estrictas de diseño), te permite hacer las cosas prácticamente como a ti te dé la gana.

---

## ✨ ¿Qué nos permite hacer Express?

Al dar el salto a este framework, vamos a mejorar drásticamente la legibilidad de nuestro código. Express nos permite:

1. **Definir rutas de forma mucho más expresiva:** Rutas directas y semánticas sin anidar condicionales.
2. **Manejar peticiones y respuestas (req/res) con esteroides:** Más métodos y facilidades integradas que nos evitan parsear datos manualmente.
3. **Usar Middlewares:** El concepto estrella. Funciones que interceptan las peticiones para reutilizar lógica (como parsear un JSON o validar usuarios) antes de llegar a la ruta final.
4. **Gestionar errores de forma centralizada:** Una forma limpia de capturar fallos sin que el servidor colapse.
5. **Crear APIs y aplicaciones web** de una forma muchísimo más sencilla.



---

## 🎯 Qué haremos a partir de ahora

El servidor nativo que hicimos antes no es malo. Conocerlo marca la diferencia entre ser un desarrollador que entiende lo que hay por detrás, y ser como un mono que simplemente le da a las teclas a ver qué pasa. 

Pero a partir de este punto del curso, **empezaremos a usar Express para construir nuestras APIs**. Aprovecharemos sus utilidades, su sistema de rutas y su forma de trabajar para dejar atrás gran parte del código manual que teníamos.

Esto es solo la introducción. A partir de la próxima clase, prepararemos la API, la pasaremos a Express y ¡empezaremos a sacarle partido de verdad!