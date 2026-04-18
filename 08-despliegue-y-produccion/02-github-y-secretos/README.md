# Tema 02: GitHub y Secretos (El Puente Seguro)

GitHub es el **Punto de Verdad** de tu aplicación. En el flujo de trabajo profesional (CI/CD), tú no envías archivos directamente al servidor. Tú envías el código a GitHub, y el servidor (Render) "escucha" ese cambio y se actualiza solo.

Sin embargo, este puente tiene un peligro: **el robo de credenciales**. 

---

## 🛡️ 1. El archivo .gitignore (Tu Seguro de Vida)

Antes de ejecutar tu primer comando de Git, debes asegurarte de que tu archivo `.gitignore` esté correctamente configurado. Este archivo le dice a Git: *"Ignora estos archivos, no los metas en el paquete que vas a enviar a internet"*.

En la raíz de tu proyecto, crea (o edita) el archivo `.gitignore` con este contenido mínimo:

```text
# Dependencias (Se instalan en el servidor, no se suben)
node_modules/

# Secretos y llaves (¡NUNCA SUBIR!)
.env

# Archivos temporales de sistema
.DS_Store
dist/
```

> **🔥 El peligro de los Bots:** Existen programas automáticos que escanean GitHub cada segundo buscando la palabra `DATABASE_URL` o `PASSWORD`. Si subes tu `.env` por error, en menos de 2 minutos alguien podría estar borrando tu base de datos real.

---

## 📤 2. El flujo de trabajo: De Local a la Nube

Para subir tu código de forma profesional, seguiremos estos pasos en la terminal:

1. **Inicializar el repositorio:**
   ```bash
   git init
   ```
2. **Añadir los archivos (Vigila que node_modules y .env no se marquen):**
   ```bash
   git add .
   ```
3. **Crear el primer "punto de control" o Commit:**
   ```bash
   git commit -m "feat: api lista para produccion"
   ```
4. **Vincular con tu repositorio de GitHub:**
   *(Crea primero el repo vacío en la web de GitHub y copia la URL)*
   ```bash
   git remote add origin [https://github.com/TU_USUARIO/TU_REPO.git](https://github.com/TU_USUARIO/TU_REPO.git)
   git branch -M main
   git push -u origin main
   ```

---

## 🔑 3. ¿Dónde viven los secretos ahora?

Si el archivo `.env` se queda en tu ordenador, ¿cómo sabe el servidor cuál es la clave de la base de datos?

La estrategia profesional se llama **Inyección de Variables**:
- **En Local:** Node lee el archivo físico `.env`.
- **En GitHub:** No hay nada (el código es "ciego" a las contraseñas).
- **En el Servidor Real:** Usaremos el panel de control de la plataforma (Render) para escribir las variables. El servidor las guardará de forma ultra-segura y se las pasará a Node.js directamente al "oído" (memoria RAM) cuando el proceso arranque.

---

## 🎯 Resumen de seguridad

1. **Verifica el .gitignore** antes de cada `git add`.
2. **Nunca hagas commit de secretos**. Si lo haces por error, cambia la contraseña de tu base de datos inmediatamente, porque aunque borres el archivo, GitHub guarda el historial de versiones.
3. **El código debe ser genérico**: Tu código debe pedir `process.env.DB_URL`, sin importar qué valor tenga.