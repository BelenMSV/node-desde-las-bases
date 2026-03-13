# Tema 14: Códigos de Estado HTTP (Status Codes)

En el tema anterior, cuando bloqueamos las peticiones `DELETE`, vimos que nuestro servidor devolvía un error **405 (Method Not Allowed)**. Anteriormente también usamos el **200 (OK)** y el famoso **404 (Not Found)**.

Pero, ¿qué significan exactamente estos números y cómo sabemos cuál usar?



---

## 🔢 1. Las "familias" de Códigos de Estado

Cada vez que un servidor responde a una petición HTTP, incluye un código de estado de 3 dígitos. Este número es un resumen rápido para que el cliente (el navegador, Bruno, Postman, etc.) sepa cómo ha ido la cosa.

Es imposible saberlos todos de memoria, pero sí debes conocer las **5 familias principales** en las que se dividen según su primer dígito:

* **1xx (Informativos):** La petición se ha recibido y el proceso continúa (se usan poco en el día a día).
* **2xx (Éxito):** Todo ha ido a la perfección.
    * *Ejemplo:* `200 OK` (Petición correcta) o `201 Created` (Se ha creado un recurso nuevo, como al registrar un usuario).
* **3xx (Redirecciones):** El recurso que buscas ya no está aquí, tienes que ir a otro lado.
    * *Ejemplo:* `301 Moved Permanently` (La URL ha cambiado para siempre).
* **4xx (Errores del Cliente):** Tú (el usuario o el cliente) te has equivocado al pedir algo.
    * *Ejemplo:* `404 Not Found` (Me pides una URL que no existe) o `405 Method Not Allowed` (Me pides borrar algo por una ruta que solo acepta leer).
* **5xx (Errores del Servidor):** El servidor ha petado por culpa nuestra (un fallo en nuestro código).
    * *Ejemplo:* `500 Internal Server Error`.

---

## 🐱 2. Aprender con Gatitos (http.cat)

Como hemos dicho, hay decenas de códigos muy específicos (como el `401 Unauthorized`, `403 Forbidden`, `429 Too Many Requests`...). 

Una de las mejores y más divertidas formas de explorarlos y entender la especificación oficial de cada uno es utilizar recursos visuales creados por la comunidad, como la web [http.cat](https://http.cat/).



Esta página asocia cada código de estado HTTP con la foto de un gato que ilustra perfectamente la situación:
* Si buscas el **200**, verás un gatito feliz.
* Si buscas el **404**, verás un gatito escondido que no se deja encontrar.
* Si buscas el **405 (Method Not Allowed)** que usamos antes, verás una imagen bastante gráfica (y graciosa) dejando claro que lo que intentas hacer *no está permitido*.

> 💡 **La regla de oro del Backend:** No hace falta que te sepas todos los códigos de memoria, pero **SÍ** es tu responsabilidad profesional buscar y utilizar el código correcto cuando devuelvas una respuesta o un error desde tu API. Devolver siempre un `200 OK` aunque haya habido un error (como pasaba en nuestro servidor antes de arreglarlo) es una de las peores prácticas que puedes cometer.