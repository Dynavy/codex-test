# Configuración de OpenAI Codex en Windows usando WSL y VSCode

## Nota sobre el repositorio

Aunque una cuenta de GitHub esté enlazada a Codex, cualquier persona que quiera trabajar con el repositorio **codex-test** en su máquina local necesita tener **el repo disponible localmente**.

- Si el repo es **privado**, se debe tener una **cuenta de GitHub con acceso al repositorio** para poder clonarlo (**en este caso el repo es público**).
- Si el repo es **público**, no hace falta cuenta de GitHub para clonarlo; basta con ejecutar:

  ```bash
  git clone https://github.com/dylan/codex-test.git
  ```

> ⚠️ Importante: Clonar el repo permite usar Codex para generar y editar código localmente, pero **hacer commits o push hacia GitHub requiere una cuenta propia con permisos**. Codex no puede usar otra cuenta automáticamente.

## 1. Enlazar GitHub con Codex:

- Abrir Codex desde ChatGPT y enlazar la cuenta de GitHub.
- Se debe dar acceso al repositorio **codex-test** para poder hacer pruebas sin afectar otros proyectos.

## 2. Uso de VSCode como IDE principal:

- Actualmente, VSCode es la IDE con soporte oficial de integración de Codex.
- Para otras IDEs como IntelliJ, se requieren plugins y configuraciones adicionales.
- Se recomienda instalar la extensión: **Codex - OpenAI’s coding agent** para tener mas features disponibles en la IDE.

## 3. Configuración en `settings.json` de VSCode:

- Hay 3 `settings.json` distintos; el correcto es **USER SETTINGS**.
- Para acceder rápidamente: `Ctrl + Alt + P` → `Preferences: Open Settings (JSON)`.
- Al final del archivo, agregar:

  ```json
  "chatgpt.runCodexInWindowsSubsystemForLinux": true
  ```

  Esto permite que Codex funcione correctamente en entornos de WSL desde VSCode.

## 4. Instalación de WSL en Windows:

- Abrir PowerShell como administrador y ejecutar:

  ```powershell
  wsl --install
  ```

- Esto instalará WSL con la distribución predeterminada (recomendado Ubuntu).

## 5. Instalación de Node Version Manager (NVM) dentro de WSL:

- Todo lo que se instale aquí estará disponible únicamente dentro del entorno WSL.
- Para acceder al entorno WSL desde cualquier terminal:

  ```powershell
  wsl
  ```

- Dentro de WSL, instalar NVM:

  ```bash
  curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/master/install.sh | bash
  ```

## 6. Instalación de Node.js:

- Una vez NVM esté instalado, instalar Node.js versión 22:

  ```bash
  nvm install 22
  ```

- Reiniciar PowerShell para que los cambios surtan efecto.

## 7. Uso de Codex desde VSCode:

- Abrir la terminal integrada de VSCode dentro del repositorio de pruebas `codex-test`.
- Ejecutar los comandos de Codex directamente desde esa terminal, funcionando sobre Node.js dentro de WSL.
- **Clonar el repo** (si es privado, se necesita cuenta de GitHub; si es público, basta con `git clone`).
- **Hacer commits o push** requiere tener una **cuenta de GitHub propia** y configuración de Git local; Codex no puede usar otra cuenta automáticamente.

## Posibles problemas

- Si al ejecutar `wsl` se abre un entorno incorrecto, esto puede ocurrir si hay una instalación de Docker en local, ya que Docker crea su propia distribución WSL (`docker-desktop`).
- Para comprobar cuál es la distribución que se abre por defecto:

  ```powershell
  wsl --list --verbose
  ```

- Si la distribución de Docker se está abriendo por encima de Ubuntu, se puede cambiar la predeterminada con:

  ```powershell
  wsl --set-default Ubuntu
  ```

  Esto asegurará que al ejecutar `wsl` se abra la distribución correcta para usar Codex y Node.js.
