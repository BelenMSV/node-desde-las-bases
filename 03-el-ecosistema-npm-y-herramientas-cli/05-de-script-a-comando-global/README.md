# Tema 05: De script a comando global (Shebang y npm link)

En el tema anterior construimos un clon del comando `ls` espectacular. Pero hay un pequeño detalle que lo hace sentir "poco profesional": para usarlo, tenemos que estar en la carpeta del proyecto y escribir `node cli.js ./carpeta`. 

Piensa en las herramientas que usas a diario. Cuando quieres usar Git, no escribes `c/archivos-de-programa/git/ejecutable ./carpeta`. Escribes directamente `git`. 

En este tema vamos a convertir nuestro humilde script de Node en un verdadero comando global para tu sistema operativo. Le llamaremos `mi-ls`.

---

## 🪄 1. El Shebang (La línea mágica)

El primer problema que tenemos es que, si intentamos ejecutar el archivo directamente sin escribir la palabra `node` delante, el sistema operativo (Windows, macOS o Linux) no sabrá qué hacer con él. ¿Es un archivo de texto? ¿Es un script de bash? ¿Es Python?

Para solucionar esto, existe una convención en el mundo de la programación llamada **Shebang**. Es una línea especial de código que se pone **estrictamente en la primera línea** de un archivo para decirle al sistema operativo qué programa debe usar para leerlo.

Abre tu archivo `cli.js` y añade esta línea en la parte más alta (antes de cualquier `import`):

```javascript
#!/usr/bin/env node

// cli.js
import { readdir, stat } from 'node:fs/promises';
import { join } from 'node:path';

// ... resto de tu código ...
```

Con `#!/usr/bin/env node` le estamos diciendo a tu ordenador: *"Oye, cuando intentes ejecutar este archivo, busca el entorno de Node.js que tengas instalado y úsalo para leer el código de abajo"*.

---

## ⚙️ 2. El campo "bin" en el package.json

Ahora que nuestro archivo sabe cómo ejecutarse a sí mismo, tenemos que decirle a NPM qué palabra queremos usar en nuestra terminal para llamar a este archivo.

Abre tu `package.json` y añade un nuevo campo llamado `"bin"` (de *binary* o ejecutable). 

```json
{
  "name": "ejemplos-de-node",
  "version": "0.0.1",
  "main": "index.js",
  "type": "module",
  "bin": {
    "mi-ls": "./cli.js"
  },
  "scripts": {
    "start": "node system-info.js"
  },
  "dependencies": {
    "ms": "^2.1.3"
  }
}
```

¿Qué le estamos diciendo aquí a NPM? *"Quiero que crees un comando en la terminal que se llame `mi-ls`, y cada vez que alguien lo escriba, quiero que ejecutes el archivo `./cli.js`"*.

---

## 🔗 3. El puente final: `npm link`

Ya tenemos el archivo preparado y el nombre del comando configurado. Ahora solo falta "instalarlo" en nuestro ordenador.

Como nuestro proyecto es local y no lo hemos subido a internet, no podemos usar `npm install -g`. Pero NPM tiene un comando especial para desarrolladores que sirve exactamente para probar CLIs locales: **`npm link`**.

Abre tu terminal, asegúrate de estar en la carpeta raíz de tu proyecto (donde está el `package.json`) y ejecuta:

```bash
npm link
```

**¿Qué acaba de pasar?**
NPM ha creado un "acceso directo" (enlace simbólico) global en tu ordenador. Ha cogido el nombre que pusiste en el campo `"bin"` (`mi-ls`) y lo ha registrado en el sistema operativo.

---

## 🚀 4. ¡Magia en la terminal!

Ha llegado el momento de la verdad. Cierra tu terminal y abre una completamente nueva. Vete a cualquier otra carpeta de tu ordenador (por ejemplo, a tu Escritorio o a la carpeta de Descargas).

Escribe tu nuevo comando:

```bash
mi-ls
```

O pásale un argumento:

```bash
mi-ls ./Documentos
```

¡Boom! 💥 Tu script se ejecuta perfectamente sin necesidad de escribir `node` y desde cualquier parte del ordenador. Acabas de crear tu primer software de línea de comandos. 

> **🌍 El paso a producción:** Si alguna vez decides que tu herramienta es tan buena que quieres compartirla con el mundo, solo tendrías que crearte una cuenta en la web de NPM y ejecutar el comando `npm publish` en tu terminal. Tu código se subiría al registro público y cualquier persona en el mundo podría escribir en su ordenador `npm install -g tu-paquete` para disfrutar de tu CLI.

---

## 🎯 Resumen del Módulo 3

¡Felicidades! Has completado uno de los módulos más importantes del curso. 
1. Empezaste instalando y utilizando librerías de otros (dependencias de producción).
2. Aprendiste a proteger tu código de la pesada carpeta `node_modules`.
3. Descubriste cómo usar `npx` y configurar herramientas para ti (dependencias de desarrollo).
4. Terminaste creando tu propia herramienta CLI, configurándola con un Shebang e instalándola globalmente en tu máquina.

Estás más que preparado para dar el salto al siguiente nivel. En el próximo módulo dejaremos la terminal a un lado y empezaremos a construir lo que Node.js sabe hacer mejor: **Servidores Web**.