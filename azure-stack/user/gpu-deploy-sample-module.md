---
title: Implementación de un módulo de IoT habilitado para una unidad de procesamiento gráfico (GPU) en Azure Stack Hub
description: Cómo implementar un módulo de IoT habilitado para una unidad de procesamiento gráfico (GPU) en Azure Stack Hub
author: mattbriggs
ms.author: mabrigg
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/13/2020
ms.reviewer: gara
ms.lastreviewed: 11/13/2020
ms.openlocfilehash: b8aef224c5694cd6b8408538473dce3b86996c4d
ms.sourcegitcommit: 8187658b1d45dceed727aca3ae1b9b57aca04392
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/14/2020
ms.locfileid: "94632840"
---
# <a name="deploy-a-gpu-enabled-iot-module-on-azure-stack-hub"></a>Implementación de un módulo de IoT habilitado para GPU en Azure Stack Hub

Con una instancia de Azure Stack Hub habilitada para GPU, puede implementar módulos de uso intensivo del procesador en dispositivos Linux que se ejecutan en IoT Edge. Los tamaños de VM optimizados para GPU son VM especializadas con GPU sencilla o múltiple de NVIDIA. En este artículo, aprenderá a usar VM optimizadas para GPU para ejecutar cargas de trabajo de visualización con un uso intensivo de procesos y gráficos.

Antes de empezar, necesitará una suscripción de Azure Active Directory (Azure AD) con acceso a Azure global y Azure Stack Hub, una instancia de Azure Container Registry (ACR) y una de IoT Hub.

En este artículo:
  - Instale una VM Linux habilitada para GPU y los controladores correctos.
  - Instale Docker y habilite la GPU en el entorno en tiempo de ejecución.
  - Conecte el dispositivo IoT a IoT Hub e instale desde el marketplace de IoT el modelo `Getting started with GPUs`.
  - Instale y supervise el dispositivo desde una máquina local con Azure IoT Explorer.
  - Opcionalmente, puede instalar y supervisar el dispositivo con la extensión de Azure IoT en Visual Studio Code.

## <a name="prerequisites"></a>Prerrequisitos

Necesitará disponer de los siguientes recursos en la instancia de Azure Stack Hub, en el entorno global de Azure y en la máquina de desarrollo local.

### <a name="azure-stack-hub-and-azure"></a>Azure Stack Hub y Azure

  - Una suscripción como usuario que usa Azure Active Directory (Azure AD) en un sistema integrado de Azure Stack Hub con una GPU de NVIDIA. Los siguientes chips funcionan con IoT Hub:
    - NCv3
    - NCas_v4

    Para obtener más información sobre las GPU en Azure Stack Hub, consulte [VM de la unidad de procesamiento de gráficos (GPU) en Azure Stack Hub](gpu-vms-about.md).
  - Una suscripción de Azure global. Si no tiene una suscripción global a Azure, cree una [cuenta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de empezar.
- Una instancia de [Azure Container Registry (ACR)](/). Anote el servidor de inicio de sesión en ACR, el nombre de usuario y la contraseña.
-   Un [centro de IoT](https://docs.microsoft.com/azure/iot-hub/iot-hub-create-through-portal) de nivel estándar o gratis en el entorno global de Azure.

### <a name="a-development-machine"></a>Una máquina de desarrollo

-   Puede usar su propio equipo o una máquina virtual, según sus preferencias de desarrollo. La máquina de desarrollo necesitará admitir la virtualización anidada. Esta funcionalidad es necesaria para ejecutar Docker, el motor de contenedor que se usa en este artículo.

  - El equipo de desarrollo necesitará los siguientes recursos:
      - [Python 3.x](https://www.python.org/downloads/)
      - [PIP](https://pypi.org/project/pip/) para la instalación de paquetes de Python. Esto se instala con la instalación de Python. Si tiene PIP instalado, es recomendable actualizarlo a la versión más reciente. Puede realizar la actualización desde el propio PIP. Escriba: `pip install --upgrade pip`.
      - [CLI de Azure 2.0](/cli/azure/install-azure-cli?view=azure-cli-latest)
      - [Git](https://git-scm.com/downloads)
      - [Docker](https://docs.docker.com/get-docker/)
      - [Visual Studio Code](https://code.visualstudio.com/)
      - [Azure IoT Tools](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) para Visual Studio Code
      - [Paquete de extensiones de Python](https://marketplace.visualstudio.com/items?itemName=donjayamanne.python-extension-pack) para Visual Studio Code

## <a name="register-an-iot-edge-device"></a>Registro de un dispositivo de IoT Edge

Use un dispositivo independiente para hospedar el dispositivo IoT Edge. El uso de un dispositivo independiente proporcionará una separación entre la máquina de desarrollo y el dispositivo IoT Edge que refleja de manera más precisa un escenario de implementación. 

Cree un dispositivo IoT Edge en Azure con una VM Linux:

1.  [Cree una VM de servidor Linux de la serie N](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal) en Azure Stack Hub. Al instalar los componentes para el servidor, interactuará con el servidor mediante SSH. Para obtener más información, consulte [Uso de una clave pública SSH](/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key).

2.  [Crear y registrar un dispositivo IoT Edge](https://docs.microsoft.com/azure/iot-edge/how-to-register-device)

## <a name="prepare-a-gpu-enabled-vm"></a>Preparar una VM habilitada para GPU

1. Instale los controladores de GPU de NVIDIA en el servidor de Linux de la serie N siguiendo los pasos descritos en el artículo [Instalación de controladores de GPU NVIDIA en VM de la serie N que ejecutan Linux](https://docs.microsoft.com/azure/virtual-machines/linux/n-series-driver-setup).

    > [!NOTE]  
    > Usará la línea de comandos de Bash para instalar el software. Tome nota de los comandos, ya que usará los mismos para instalar los controladores en el contenedor que se ejecuta en Docker en la VM habilitada para GPU.

2.  Instale el entorno en tiempo de ejecución de IoT Edge más reciente en el servidor Linux de la serie N en Azure Stack Hub. Para obtener instrucciones, consulte [Instalación del entorno de ejecución de Azure IoT Edge en sistemas Linux basados en Debian](/azure/iot-edge/how-to-install-iot-edge-linux#install-the-latest-runtime-version).

## <a name="install-docker"></a>Instalar Docker

Instale Docker en la VM habilitada para GPU. Va a ejecutar el módulo desde el marketplace de IoT Edge en un contenedor en la VM.

Debe instalar Docker 19.02 o superior. Ahora, el entorno en tiempo de ejecución de Docker es compatible con las GPU de NVIDIA. Para obtener más información acerca de las GPU de Docker, consulte el artículo de la documentación de Docker [Opciones en tiempo de ejecución de memoria, CPU y GPU](https://docs.docker.com/config/containers/resource_constraints/#gpu).

### <a name="install-docker"></a>Instalar Docker

Los contenedores de Docker se pueden ejecutar en cualquier lugar, a nivel local en el centro de datos de cliente, en un proveedor de servicios externo o en la nube, en Azure. Los contenedores de imagen de Docker se pueden ejecutar de forma nativa en Linux y Windows. Sin embargo, las imágenes de Windows solo pueden ejecutarse en hosts de Windows y las imágenes de Linux pueden ejecutarse en hosts de Linux y hosts de Windows (con una máquina virtual Linux de Hyper-V, hasta el momento), donde host significa un servidor o una máquina virtual. Para obtener más información, consulte [¿Qué es Docker?](https://docs.microsoft.com/dotnet/architecture/microservices/container-docker-introduction/docker-defined).

1. Conéctese al servidor Linux de la serie N mediante el cliente SSH.

1.  Actualice el índice y las listas de apt.

    ```bash  
    sudo apt-get update
    ```

1.  Capture las nuevas versiones de los paquetes existentes en la máquina.

    ```bash  
    sudo apt-get upgrade
    ```

2.  Instale las dependencias necesarias para agregar el repositorio apt de Docker.

    ```bash  
    sudo apt-get install apt-transport-https ca-certificates curl software-properties-common
    ```

3.  Agregue la clave GPG de Docker.

    ```bash  
    curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo apt-key add -
    ```

4.  Agregue el repositorio apt de Docker.

    ```bash  
    sudo add-apt-repository "deb [arch=amd64] https://download.docker.com/linux/ubuntu $(lsb_release -cs) stable"
    ```

5.  Actualice las listas y el índice de apt e instale Docker Community Edition.

    ```bash  
    sudo apt-get update 
    sudo apt-get install docker-ce
    ```

6.  Compruebe la versión de Docker para verificar la instalación.

    ```bash  
    docker -v
    ```

7. Exponga los recursos de GPU disponibles en Docker.

    ```bash
    docker run -it --rm --gpus all ubuntu nvidia-smi
    ```

## <a name="get-the-item-from-the-marketplace"></a>Obtención del artículo del marketplace

Vuelva a Azure Portal y agregue el modelo al dispositivo perimetral mediante el marketplace de IoT. Seleccione **Módulo de Marketplace** en el menú. Busque `Getting started with GPUs` y siga las instrucciones para agregar el módulo.

Para obtener instrucciones, consulte [Selección de dispositivo y adición de módulos](/azure/iot-edge/how-to-deploy-modules-portal#select-device-and-add-modules).

## <a name="enable-monitoring"></a>Habilitar supervisión

1. Descargue [Azure IoT Explorer](/azure/iot-pnp/howto-use-iot-explorer) y conecte la aplicación a IoT Hub.

2. Seleccione el dispositivo IoT y vaya a Telemetría desde el menú de navegación.

3. Seleccione **Iniciar** para iniciar la supervisión de la salida desde el dispositivo IoT Edge.

![una instalación válida](media/gpu-deploy-sample-module/user-azure-iot-explorer-gpu.png)

## <a name="monitor-the-module-optional"></a>Supervisión del módulo (opcional)  

1. En la paleta de comandos de VS Code, ejecute **Azure IoT Hub: Select IoT Hub**.

2. Elija la suscripción y la instancia de IoT Hub que contienen el dispositivo IoT Edge que desea configurar. En este caso, seleccione la suscripción usada para implementar el dispositivo de Azure Stack Edge y seleccione el dispositivo de IoT Edge creado para el dispositivo de Azure Stack Edge. Esto sucede cuando se configura el proceso mediante Azure Portal en los pasos anteriores.

3. En el explorador de Visual Studio Code, expanda la sección Azure IoT Hub. En **Dispositivos**, debería ver el dispositivo de IoT Edge correspondiente a su dispositivo de Azure Stack Edge. 

    1. Seleccione ese dispositivo, haga clic con el botón derecho en **Iniciar la supervisión del punto de conexión de eventos integrado**.

    2. Vaya a **Dispositivos > Módulos** y debería ver el **módulo de GPU** en ejecución.

    3. El terminal de Visual Studio Code también debería mostrar los eventos de IoT Hub como salida de supervisión para el dispositivo de Azure Stack Edge.

    ![una instalación válida es](media/gpu-deploy-sample-module/gpu-monitor-events-output.png)

    Puede ver que el tiempo necesario para ejecutar el mismo conjunto de operaciones (5000 iteraciones de transformación de forma) por GPU es mucho menor que el de la CPU.

## <a name="next-steps"></a>Pasos siguientes

  - Más información acerca de la [Máquina virtual (VM) de la unidad de procesamiento gráfico (GPU) en Azure Stack Hub](gpu-vms-about.md)

  - Obtenga más información acerca de Azure Stack Hub, Data Box Edge y la inteligencia perimetral: [El futuro de la informática: inteligencia en la nube e inteligencia perimetral](https://azure.microsoft.com/overview/future-of-cloud)

  - Para obtener más información sobre las aplicaciones en la nube híbrida, consulte [Soluciones en la nube híbrida](https://docs.microsoft.com/hybrid/app-solutions/).