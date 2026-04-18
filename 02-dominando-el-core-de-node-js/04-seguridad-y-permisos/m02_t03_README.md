# Tema 08: Seguridad y Sistema de Permisos

Hasta ahora hemos estado ejecutando scripts que leen y escriben archivos en nuestro ordenador. Quizás te haya surgido una duda razonable: *"Si este script puede leer mis archivos de texto... ¿podría leer las contraseñas guardadas en mi navegador o instalar un virus en el sistema?"*

La respuesta corta es: **Sí.**

Por defecto, cuando ejecutas Node.js, este tiene **acceso total (libertinaje)** a tu sistema con los mismos permisos que tenga tu usuario. Esto es muy peligroso si descargas un script de internet y lo ejecutas sin mirar el código.

Afortunadamente, Node.js introdujo un **Modelo de Permisos** para solucionar esto.

---

## 🛑 1. Activando el Modelo de Permisos

Para evitar que un script haga lo que quiera, debemos decirle a Node.js que se ejecute en "modo seguro" activando los permisos.

Vamos a volver a usar un script que lee un archivo y escribe en otro (como el que hicimos en el Tema 05). 

Si intentamos ejecutarlo activando los permisos con la flag `--permission`, mira lo que ocurre:

```bash
node --permission manage-files.js
```
**Resultado esperado:** El programa fallará (*Crash*) y te lanzará un error indicando que *"el acceso a esta API ha sido restringido"*. No te dejará ni siquiera leer el primer archivo.

---

## 🔓 2. Otorgando permisos específicos por consola

Para que nuestro script funcione en modo seguro, tenemos que decirle explícitamente a Node.js qué le dejamos hacer usando *flags* (argumentos) en la terminal.

### Opción A: Permiso global (Poco recomendado)
Podemos decirle que lea y escriba en *cualquier* parte del sistema usando un asterisco `*`:
```bash
node --permission --allow-fs-read="*" --allow-fs-write="*" manage-files.js
```

### Opción B: Permiso granular (Lo correcto)
Lo ideal es darle permiso *solo* para los archivos y carpetas que necesita. Si nuestro script lee `archivo.txt` y guarda cosas en la carpeta `output`, el comando sería:

```bash
node --permission --allow-fs-read="./archivo.txt" --allow-fs-write="./output/*" manage-files.js
```

De esta forma, si el script intenta leer tus contraseñas o escribir fuera de la carpeta `output`, Node.js lo bloqueará instantáneamente.

---

## 🛡️ 3. Comprobando permisos desde el código

Si ejecutamos el script sin permisos, Node.js lanza un error horrible y el programa se cierra de golpe. Sería mucho mejor si nuestro código pudiera "preguntar" primero si tiene permisos y, si no los tiene, mostrar un mensaje amigable.

Podemos hacer esto usando `process.permission.has()`.

Vamos a refactorizar nuestro script para hacerlo profesional y seguro:

```javascript
// manage-files.js
import { mkdir, readFile, writeFile } from 'node:fs/promises';
import { join } from 'node:path';

// RUTAS
const fileToRead = './archivo.txt';
const outputDir = join('output', 'files', 'documents');
const fileToWrite = join(outputDir, 'archivo-uppercase.txt');

async function processFiles() {
  let content = '';

  // 1. Comprobamos si tenemos permiso para LEER
  if (process.permission.has('fs.read', fileToRead)) {
    content = await readFile(fileToRead, 'utf-8');
    console.log('✅ Archivo leído correctamente.');
  } else {
    console.error('❌ ERROR: No tienes permiso para leer el archivo.');
    return; // Detenemos la ejecución
  }

  // 2. Comprobamos si tenemos permiso para ESCRIBIR
  if (process.permission.has('fs.write', outputDir)) {
    // Creamos la carpeta
    await mkdir(outputDir, { recursive: true });
    
    // Escribimos el archivo
    const uppercaseContent = content.toUpperCase();
    await writeFile(fileToWrite, uppercaseContent);
    
    console.log('✅ Proceso de escritura completado con éxito.');
  } else {
    console.error('❌ ERROR: No tienes permiso para escribir en el directorio destino.');
  }
}

processFiles();
```

Ahora tienes un control absoluto. Si el usuario ejecuta simplemente `node --permission manage-files.js`, en lugar de un error fatal, verá tus mensajes amigables por consola explicándole qué permisos le faltan.