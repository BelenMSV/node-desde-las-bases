# Tema 03: Instalación de Node.js (La forma correcta)

Si vas a la [página oficial de Node.js](https://nodejs.org/es/), lo primero que verás son unos botones gigantes para descargar un instalador para Windows, macOS o Linux. **Esa es la forma tradicional, pero no es la recomendada para desarrollo.**

---

## 🚫 ¿Por qué NO usar el instalador oficial?

Instalar Node.js directamente desde el instalador (`.exe`, `.pkg`, etc.) te obliga a tener **una única versión** instalada en tu sistema. 

En el mundo real, trabajarás en diferentes proyectos, y cada uno puede requerir una versión distinta de Node.js. Si usas el instalador oficial, cambiar de versión es un dolor de cabeza, te obliga a desinstalar e instalar constantemente, y dificulta mucho el trabajo en equipo.

---

## 🆚 Entendiendo las versiones: LTS vs. Current

En la web de Node.js verás siempre dos versiones principales. Es crucial entender la diferencia antes de instalar nada:

| Tipo de Versión | ¿Qué significa? | ¿Para qué se usa? |
| :--- | :--- | :--- |
| **LTS (Long Term Support)** | Versión estable con soporte extendido. | **Recomendada para la mayoría de proyectos y producción.** Ofrece máxima compatibilidad, estabilidad y cero sorpresas. |
| **Current** | La versión más reciente con las últimas *features*. | Genial para probar novedades o APIs experimentales, pero su soporte es corto y el ecosistema de paquetes puede tardar en ser compatible. |

**Conclusión:** Para tu día a día y para este curso, utilizaremos siempre la versión **LTS**.

---

## 🛠️ La solución: Gestores de Versiones (Version Managers)

Para solucionar el problema de tener que manejar múltiples versiones, los desarrolladores utilizamos **Gestores de Versiones**. 

Herramientas como `nvm` (Node Version Manager) son muy famosas, pero en este curso **vamos a utilizar `fnm` (Fast Node Manager)**.

**¿Por qué elegir `fnm`?**
1. **Velocidad:** Está construido en Rust, lo que lo hace increíblemente rápido.
2. **Multiplataforma real:** A diferencia de `nvm` (que da problemas en Windows y requiere versiones no oficiales de terceros), `fnm` funciona exactamente igual en macOS, Linux y Windows.

> **💡 Nota sobre otras alternativas:** Existen herramientas todo-en-uno como `mise` o `asdf`. Son válidas, pero a menudo hacen "demasiadas cosas" gestionando múltiples lenguajes que no necesitamos ahora mismo. Fieles a la filosofía de usar la herramienta más sencilla y específica para el trabajo, `fnm` es perfecto y directo al grano.

---

## 🚀 Guía de instalación y uso de `fnm`

### 1. Instalar `fnm`
Dependiendo de tu sistema operativo, puedes seguir las instrucciones de la [documentación oficial de fnm](https://github.com/Schniz/fnm). 

En macOS y Linux, habitualmente basta con ejecutar este script en tu terminal:

```bash
curl -fsSL [https://fnm.vercel.app/install](https://fnm.vercel.app/install) | bash
```
*(Si usas Windows, revisa la documentación oficial para instalarlo vía `winget` o `choco`).*

> **⚠️ Importante:** Si tras instalarlo el comando `fnm` no es reconocido, simplemente **cierra tu terminal y vuelve a abrirla** para que se recarguen las variables de entorno.

Verifica que se ha instalado correctamente ejecutando:
```bash
fnm --version
```

### 2. Instalar Node.js (Versión LTS)
No hace falta que busques en internet cuál es la versión LTS actual, `fnm` lo hace por ti. Ejecuta:

```bash
fnm install --lts
```
Esto descargará e instalará la última versión estable (por ejemplo, la 24.12.0).

### 3. Comandos básicos para tu día a día

Una vez tienes `fnm`, gestionar tus versiones es cuestión de segundos:

* **Listar versiones instaladas:** Mira qué versiones de Node tienes disponibles en tu máquina.
  ```bash
  fnm ls
  ```

* **Instalar una versión específica:** ¿Tienes un proyecto antiguo que usa la versión 18?
  ```bash
  fnm install 18
  ```

* **Cambiar de versión temporalmente:** Cambia la versión activa solo para la pestaña actual de tu terminal.
  ```bash
  fnm use 18
  ```
(Puedes verificar el cambio ejecutando node --version)

* **Establecer una versión por defecto:** Haz que cada vez que abras una nueva terminal, arranque con tu versión preferida (idealmente la LTS que instalaste).
  ```bash
  fnm default 24.12.0
  ```

* **Desinstalar una versión:** Limpia tu sistema de versiones que ya no usas.
  ```bash
  fnm uninstall 18
  ```

¡Y ya está! Con esto tienes un entorno de desarrollo profesional, flexible y a prueba de balas para empezar a trabajar con Node.js en cualquier proyecto.