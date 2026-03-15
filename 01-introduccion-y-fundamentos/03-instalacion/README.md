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

### 1. Instalar `fnm` según tu Sistema Operativo

#### 🍎 Para macOS y 🐧 Linux
Abre tu terminal y ejecuta este script oficial:
```bash
curl -fsSL [https://fnm.vercel.app/install](https://fnm.vercel.app/install) | bash
```

*Cierra tu terminal y vuelve a abrirla para que se apliquen los cambios.*

#### 👨‍💻 Para Windows (Guía a prueba de fallos)
En Windows, la configuración requiere un par de pasos extra para asegurar que funcione perfectamente. **⚠️ IMPORTANTE: Usa siempre `PowerShell` y NO el Símbolo del Sistema (CMD) clásico.**

**Paso A: Instalar el programa**
Abre una terminal de **PowerShell** y ejecuta:
```powershell
winget install Schniz.fnm
```
**Paso B: Dar permisos a PowerShell (Seguridad)**
Por defecto, Windows bloquea la ejecución de scripts. Para programar, necesitas habilitar los scripts locales. En la misma terminal, ejecuta:
```powershell
Set-ExecutionPolicy -ExecutionPolicy RemoteSigned -Scope CurrentUser
```
**Paso C: Configurar el perfil automáticamente**
Para que `fnm` funcione cada vez que abres PowerShell, necesitamos guardar su configuración. Ejecuta este comando (cópialo todo entero, soluciona problemas comunes si usas OneDrive):
```powershell
if (!(Test-Path (Split-Path $PROFILE))) { New-Item -Type Directory -Path (Split-Path $PROFILE) -Force }; Set-Content -Path $PROFILE -Value 'fnm env --use-on-cd | Out-String | Invoke-Expression'
```
**Paso D: Reiniciar**
Cierra completamente tu ventana de PowerShell y abre una nueva. Verifica que todo funciona escribiendo:
```powershell
fnm --version
```

### 2. Instalar Node.js (Versión LTS)
Una vez instalado `fnm`, no hace falta que busques en internet cuál es la versión LTS actual, el gestor lo hace por ti. Ejecuta:

```bash
fnm install --lts
```
Y para decirle a tu sistema que use esa versión por defecto cada vez que abras la terminal:
```bash
fnm default lts-latest
```
### 3. Comandos básicos para tu día a día

Una vez tienes `fnm`, gestionar tus versiones es cuestión de segundos:

* **Listar versiones instaladas:** Mira qué versiones de Node tienes en tu máquina.
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
(Puedes verificar el cambio ejecutando `node -v`)

* **Desinstalar una versión:** Limpia tu sistema de versiones que ya no usas.
  ```bash
  fnm uninstall 18
  ```