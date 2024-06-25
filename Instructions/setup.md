---
lab:
  title: Configuración del entorno de laboratorio
  module: Setup
---

# Configuración del entorno de laboratorio

Los ejercicios están diseñados para completarse en un entorno de laboratorio hospedado. Si desea completarlos en su propio equipo, puede hacerlo instalando el software siguiente. Puede experimentar diálogos y comportamientos inesperados al usar su propio entorno. Debido a la amplia variedad de configuraciones locales posibles, el equipo del curso no puede cubrir problemas que puedan surgir en su propio entorno.

> **Nota**: Las instrucciones siguientes son para un equipo con Windows 11. También puede usar Linux o MacOS. Es posible que tenga que adaptar las instrucciones del laboratorio para el sistema operativo que elija.

### Sistema operativo base (Windows 11)

#### Windows 11

Instale Windows 11 y aplique todas las actualizaciones.

#### Edge

Instale [Edge (Chromium)](https://microsoft.com/edge)

### SDK de .NET Core

1. Realice la descarga e instalación desde https://dotnet.microsoft.com/download (descargue el SDK de .NET Core, no solo el entorno de ejecución). Si está ejecutando los laboratorios de este curso en su propia máquina, debe tener .NET 7.0. Los laboratorios se han probado en .NET 7.0, pero la versión 7.0 no recibe actualmente soporte técnico. Puede usar la versión 8.0, pero puede haber algunos problemas menores. Se recomienda encarecidamente usar el entorno hospedado.

### Paquete redistribuible de C++

1. Descargue e instale Visual C++ Redistributable (x64) en https://aka.ms/vs/16/release/vc_redist.x64.exe.

### Node.JS

1. Descargue la versión LTS más reciente en https://nodejs.org/en/download/ 
2. Instale con las opciones predeterminadas

### Python (y paquetes necesarios)

1. Descargue la versión 3.11 en https://docs.conda.io/en/latest/miniconda.html 
2. Ejecute el programa de instalación para instalar. **Importante**: seleccione las opciones para agregar Miniconda a la variable PATH y para registrar Miniconda como el entorno de Python predeterminado.
3. Después de la instalación, abra el símbolo del sistema de Anaconda y escriba los siguientes comandos para instalar paquetes: 

```
pip install flask requests python-dotenv pylint matplotlib pillow
pip install --upgrade numpy
```

### Azure CLI

1. Descargue en https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest 
2. Instale con las opciones predeterminadas

### Git

1. Descargue e instale en https://git-scm.com/download.html, con las opciones predeterminadas


### Visual Studio Code (y extensiones)

1. Descargue en https://code.visualstudio.com/Download 
2. Instale con las opciones predeterminadas 
3. Después de la instalación, inicie Visual Studio Code y, en la pestaña **Extensiones** (Ctrl + Mayús + X), busque e instale las siguientes extensiones de Microsoft:
    - Python
    - C#
    - Azure Functions
    - PowerShell
