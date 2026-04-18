# Tema 01: Preparando la API para Producción

Antes de subir nuestro código a un servidor en internet, tenemos que hacer tres ajustes "quirúrgicos" para que el servidor remoto sepa cómo arrancar nuestra aplicación.

---

## 🔌 1. El Puerto Dinámico (La regla de oro)

En tu ordenador siempre usamos el puerto `1234`. Pero en un servidor real (como los de Render o Amazon), tú no eliges el puerto. El servidor te asigna uno libre automáticamente a través de una variable de entorno llamada `PORT`.

**Debes asegurarte de que tu `index.js` use ese puerto:**

```javascript
// index.js
// ❌ MAL: const PORT = 1234;
// ✅ BIEN:
const PORT = process.env.PORT ?? 1234; 
```

---

## 📜 2. El Script de Inicio (`npm start`)

Hasta ahora hemos usado `node --watch index.js` para desarrollar. Pero en producción **no se usa el modo watch**. Queremos que el servidor arranque de la forma más estable posible.

Debes configurar tu `package.json` para que tenga un script llamado `start`:

```json
{
  "name": "mi-api",
  "version": "1.0.0",
  "type": "module",
  "scripts": {
    "start": "node index.js",
    "dev": "node --watch index.js"
  }
}
```
El servidor de despliegue buscará automáticamente el comando `npm start` para encender tu API.

---

## 🚫 3. El archivo `.gitignore` (Repaso vital)

Recuerda que **NUNCA** debemos subir a internet:
1. La carpeta `node_modules` (es pesadísima y el servidor la instalará solo).
2. El archivo `.env` (contiene tus contraseñas).

Asegúrate de tener un archivo `.gitignore` en la raíz con esto:
```text
node_modules
.env
```

---

## 🎯 Resumen

Si tu puerto es dinámico, tienes el script `start` y tu `.gitignore` está listo, tu código es **"Production Ready"**. En la siguiente lección, subiremos este código a GitHub para que el servidor de despliegue pueda leerlo.