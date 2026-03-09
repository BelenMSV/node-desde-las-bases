# Tema 09: Creando nuestro primer CLI (Command Line Interface)

Todos los días utilizas programas de línea de comandos en tu terminal. Cuando escribes `ls`, `cd`, `git` o `npm`, estás ejecutando pequeños programas (CLIs). 

Lo genial de Node.js es que nos permite crear nuestros propios programas de línea de comandos de forma muy sencilla. En esta lección, vamos a crear nuestro propio `ls` (un listador de archivos) paso a paso.

---

## 📥 1. Leyendo argumentos de la terminal (`process.argv`)

Para que un programa de terminal sea útil, necesita recibir instrucciones o parámetros. Por ejemplo, si ejecutamos `node cli.js ./carpeta --flag`, necesitamos recuperar esa información en nuestro código.

Node.js nos proporciona el array `process.argv` (Argument Values) para acceder a esto.

Crea un archivo llamado `cli.js` y escribe lo siguiente:

```javascript
// cli.js
console.log(process.argv);
```

Si ejecutas en tu terminal `node cli.js ./dir --flag`, verás que te devuelve un array con varios elementos:

1. **Posición 0:** La ruta absoluta donde está instalado el ejecutable de Node.js en tu sistema.
2. **Posición 1:** La ruta absoluta del archivo que estás ejecutando (`cli.js`).
3. **Posición 2 en adelante:** ¡Los argumentos que tú le has pasado! (`./dir`, `--flag`, etc.).

Como las posiciones 0 y 1 siempre son información del sistema que rara vez necesitamos, la práctica habitual es "recortar" (*slice*) el array para quedarnos solo con lo que nos interesa:

```javascript
// cli.js
const args = process.argv.slice(2);
console.log('Mis argumentos son:', args); 
// Resultado: Mis argumentos son: [ './dir', '--flag' ]
```

---

## 🛠️ 2. Construyendo nuestro propio `ls`

Vamos a construir un programa que liste los archivos de una carpeta, nos diga si son directorios o archivos de texto, y nos muestre cuánto pesan.

Para ello, borra lo anterior en `cli.js` y vamos a ir construyendo el código paso a paso usando módulos nativos de Node.

### Paso 1: Importaciones y Argumentos
Vamos a importar `readdir` (para leer los nombres de la carpeta), `stat` (para sacar la información de tamaño) y `join` (para construir rutas de forma segura).

```javascript
// cli.js
import { readdir, stat } from 'node:fs/promises';
import { join } from 'node:path';

// 1. Recuperar la carpeta a listar
// Si el usuario no pasa ningún argumento (argv[2]), usamos '.' (carpeta actual)
const dir = process.argv[2] ?? '.';
```

### Paso 2: Utilidad de formateo
Vamos a crear una pequeña función para que los bytes se vean legibles (KB o Bytes).

```javascript
// 2. Formateo simple de tamaños
const formatBytes = (size) => {
  if (size < 1024) return `${size} B`;
  return `${(size / 1024).toFixed(2)} KB`;
};
```

### Paso 3: Leer el directorio y procesar la información
Usaremos `readdir` para obtener los nombres de los archivos. Luego, para cada archivo, usaremos `stat` para saber si es una carpeta y cuánto pesa. 

Como `stat` es asíncrono, usaremos `Promise.all` para procesar todos los archivos a la vez en paralelo (¡muy rápido!).

```javascript
async function executeLS() {
  try {
    // 3. Leer los nombres de los archivos de la carpeta
    const files = await readdir(dir);

    // 4. Recuperar la info detallada de cada archivo
    const entries = await Promise.all(
      files.map(async (name) => {
        const fullPath = join(dir, name);
        const info = await stat(fullPath);

        return {
          name,
          isDir: info.isDirectory(),
          size: formatBytes(info.size)
        };
      })
    );

    // 5. Renderizar la información por consola
    for (const entry of entries) {
      const icon = entry.isDir ? '📂' : '📄';
      const size = entry.isDir ? '-' : entry.size;
      
      // Usamos padEnd(25) para que los nombres queden alineados en columnas
      console.log(`${icon}  ${entry.name.padEnd(25)} ${size}`);
    }
  } catch (error) {
    console.error('❌ Error al leer el directorio:', error.message);
  }
}

executeLS();
```

## 🚀 3. ¡Probando nuestro CLI!

Guarda tu archivo `cli.js` y pruébalo en la terminal:

1. **Listar el directorio actual:**
   ```bash
   node cli.js
   ```

2. **Listar una carpeta específica:**
   ```bash
   node cli.js ./output/files/documents
   ```

3. **Listar la carpeta padre:**
   ```bash
   node cli.js ../
   ```

¡Verás una lista preciosa con iconos, nombres alineados y tamaños en KB!

---

## 🏋️‍♂️ Ejercicios propuestos

Si quieres llevar este CLI al siguiente nivel, aquí tienes unas cuantas ideas para practicar:

1. **Ordenación:** Usa `.sort()` en el array `entries` para que los directorios aparezcan siempre los primeros, y el resto de archivos en orden alfabético.
2. **Filtrado por Flags:** Lee los argumentos de `process.argv` y permite que si el usuario escribe el flag `--files-only`, el programa solo liste archivos (ocultando directorios), y si escribe `--dirs-only`, muestre solo directorios.