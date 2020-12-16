---
title: Implementación del motor de AKS en Windows para Azure Stack Hub
description: Aprenda a usar una máquina Windows en Azure Stack Hub para hospedar el motor de AKS con el fin de implementar y administrar un clúster de Kubernetes.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: mabrigg
ms.reviewer: alfredop
ms.lastreviewed: 12/20/2019
ms.openlocfilehash: 65c0588cb0820c541578418d8de42be85bb34242
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941330"
---
# <a name="install-the-aks-engine-on-windows-in-azure-stack-hub"></a>Instalación del motor de AKS en Windows para Azure Stack Hub

Puede usar una máquina Windows en Azure Stack Hub para hospedar el motor de AKS con el fin de implementar y administrar un clúster de Kubernetes. En este artículo, va a preparar la máquina virtual cliente para administrar el clúster de las instancias conectadas y sin conexión de Azure Stack Hub y comprobar la instalación.
<!-- TZLASDKFIX2 Line above was previously:
In this article, we look at preparing the client VM to manage your cluster for both connected and disconnected Azure Stack Hub instances, check the install, and setting up the client VM on the ASDK.
-->
## <a name="prepare-the-client-vm"></a>Preparación de la máquina virtual cliente

El motor de AKS es una herramienta de línea de comandos que se usa para implementar y administrar el clúster de Kubernetes. El motor se puede ejecutar en una máquina de Azure Stack Hub. Desde esta máquina, ejecutará el motor de AKS para implementar los recursos y el software de IaaS necesarios para ejecutar el clúster. Después, puede usar la máquina que ejecuta el motor para realizar tareas de administración en el clúster.

Al elegir la máquina cliente, tenga en cuenta lo siguiente:

1. Si la máquina cliente debe recuperarse en caso de desastre.
3. Cómo se va conectar a la máquina cliente y cómo va a interactuar la máquina con el clúster.

## <a name="install-in-a-connected-environment"></a>Instalación en un entorno conectado

Puede instalar la máquina virtual cliente para administrar el clúster de Kubernetes en una instancia de Azure Stack Hub conectada a Internet.

1. Cree una máquina virtual Windows en Azure Stack Hub. Para obtener instrucciones, consulte [Inicio rápido: Creación de una máquina virtual Windows Server con el portal de Azure Stack Hub](../../user/azure-stack-quick-windows-portal.md).
2. Conéctese a la máquina virtual.
3. [Instale Chocolatey con las instrucciones de PowerShell](https://chocolatey.org/install#install-with-powershellexe). 

    Según el sitio web de Chocolatey: Chocolatey es un administrador de paquetes para Windows, como apt-get o yum pero para Windows. Se ha diseñado para ser una plataforma descentralizada para instalar rápidamente las aplicaciones y las herramientas necesarias. Se basa en la infraestructura de NuGet que usa actualmente PowerShell y su objetivo es entregar los paquetes de las distribuciones a su equipo.
4. Busque la versión del motor de AKS en la tabla [Versiones admitidas de Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-aks-engine-versions). La imagen base de AKS debe estar disponible en Marketplace de Azure Stack Hub. Al ejecutar el comando, debe especificar la versión `--version v0.48.0`. Si no lo hace, el comando instalará la versión más reciente, que podría necesitar una imagen de VHD que no está disponible en Marketplace.
5. Ejecute el siguiente comando desde un símbolo del sistema con privilegios elevados e incluya el número de versión:

    ```PowerShell  
        choco install aks-engine --version 0.55.4 -y
    ```

> [!NOTE]  
> Si se produce un error en este método para la instalación, puede probar los pasos del [entorno desconectado](#install-in-a-disconnected-environment) o [probar GoFish](../../user/azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), que es otro administrador de paquetes.

## <a name="install-in-a-disconnected-environment"></a>Instalación en un entorno desconectado

Puede instalar la máquina virtual cliente para administrar el clúster de Kubernetes en una instancia de Azure Stack Hub desconectada de Internet.

1.  Desde una máquina con acceso a Internet, vaya a GitHub [Azure/aks-engine](https://github.com/Azure/aks-engine/releases/latest). Descargue un archivo (*.tar.gz) para una máquina Windows, por ejemplo, `aks-engine-v0.38.8-windows-amd64.tar.gz`.

2.  Cree una cuenta de almacenamiento en la instancia de Azure Stack Hub para cargar el archivo de almacenamiento (*.tar.gz) con el archivo binario del motor de AKS. Para obtener instrucciones sobre el uso del Explorador de Azure Storage, consulte [Explorador de Azure Storage con Azure Stack Hub](../../user/azure-stack-storage-connect-se.md).

3. Cree una máquina virtual Windows en Azure Stack Hub. Para obtener instrucciones, consulte [Inicio rápido: Creación de una máquina virtual Windows Server con el portal de Azure Stack Hub](../../user/azure-stack-quick-windows-portal.md)

4.  Descargue el archivo en la máquina virtual de administración de la dirección URL del blob de la cuenta de Azure Stack Hub Storage donde cargó el archivo de almacenamiento (*.tar.gz). Extraiga el archivo a un directorio al que tenga acceso desde el símbolo del sistema.

5. Conéctese a la máquina virtual.

6. [Instale Chocolatey con las instrucciones de PowerShell](https://chocolatey.org/install#install-with-powershellexe). 

7.  Ejecute el siguiente comando en un símbolo del sistema con privilegios elevados. Incluya el número de versión correcto:

    ```PowerShell  
        choco install aks-engine --version 0.55.4 -y
    ```

## <a name="verify-the-installation"></a>Comprobación de la instalación

Una vez configurada la máquina virtual de cliente, compruebe que ha instalado AKS-Engine.

1. Conéctese a su máquina virtual cliente.
2. Ejecute el siguiente comando:

    ```PowerShell  
    aks-engine version
    ```

Si no puede comprobar que ha instalado el motor de AKS en la máquina virtual cliente, consulte [Solución de problemas de instalación del motor de AKS](../../user/azure-stack-kubernetes-aks-engine-troubleshoot.md).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación de un clúster de Kubernetes con el motor de AKS en Azure Stack Hub](../../user/azure-stack-kubernetes-aks-engine-deploy-cluster.md)
