---
title: Implementación de AKS-Engine en Windows para Azure Stack | Microsoft Docs
description: Aprenda a usar una máquina Windows en Azure Stack para hospedar AKS-Engine con el fin de implementar y administrar un clúster de Kubernetes.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na (Kubernetes)
ms.devlang: nav
ms.topic: article
ms.date: 10/09/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 10/09/2019
ms.openlocfilehash: 9c19047629d95ba1ffe7c0fe6ab2da5c745576a0
ms.sourcegitcommit: 12034a1190d52ca2c7d3f05c8c096416120d8392
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/08/2019
ms.locfileid: "72037943"
---
# <a name="install-the-aks-engine-on-windows-in-azure-stack"></a>Instalación de AKS-Engine en Windows para Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar una máquina Windows en Azure Stack para hospedar AKS-Engine con el fin de implementar y administrar un clúster de Kubernetes. En este artículo, se examina la preparación de la máquina virtual cliente para administrar su clúster para instancias de Azure Stack conectadas y desconectadas, se comprueba la instalación y se configura la máquina virtual cliente en el ASDK.

## <a name="prepare-the-client-vm"></a>Preparación de la máquina virtual cliente

AKS-Engine es una herramienta de línea de comandos que se usa para implementar y administrar el clúster de Kubernetes. El motor se puede ejecutar en una máquina de Azure Stack. Desde esta máquina, ejecutará AKS-Engine para implementar los recursos y el software de IaaS necesarios para ejecutar el clúster. Después, puede usar la máquina que ejecuta el motor para realizar tareas de administración en el clúster.

Al elegir la máquina cliente, tenga en cuenta lo siguiente:

1. Si la máquina cliente debe recuperarse en caso de desastre.
3. Cómo se va conectar a la máquina cliente y cómo va a interactuar la máquina con el clúster.

## <a name="install-in-a-connected-environment"></a>Instalación en un entorno conectado

Puede instalar la máquina virtual cliente para administrar el clúster de Kubernetes en una instancia de Azure Stack conectada a Internet.

1. Cree una máquina virtual Windows en Azure Stack. Para obtener instrucciones, consulte [Inicio rápido: Creación de una máquina virtual Windows Server con el portal de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal).
2. Conéctese a la máquina virtual.
3. [Instale Chocolatey con las instrucciones de PowerShell](https://chocolatey.org/install#install-with-powershellexe). 

    Según el sitio web de Chocolatey: Chocolatey es un administrador de paquetes para Windows, como apt-get o yum pero para Windows. Se ha diseñado para ser una plataforma descentralizada para instalar rápidamente las aplicaciones y las herramientas necesarias. Se basa en la infraestructura de NuGet que usa actualmente PowerShell y su objetivo es entregar los paquetes de las distribuciones a su equipo.
4. Busque la versión de AKS-Engine en la tabla [Versiones admitidas de Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). La imagen base de AKS debe estar disponible en Marketplace de Azure Stack. Al ejecutar el comando, debe especificar la versión `--version v0.41.2`. Si no lo hace, el comando instalará la versión más reciente, que podría necesitar una imagen de VHD que no está disponible en Marketplace.
5. Ejecute el siguiente comando desde un símbolo del sistema con privilegios elevados e incluya el número de versión:

    ```PowerShell  
        choco install aks-engine --version v0.41.2 -y
    ```

> [!Note]  
> Si se produce un error en este método para la instalación, puede probar los pasos del [entorno desconectado](#install-in-a-disconnected-environment) o [probar GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), que es otro administrador de paquetes.

## <a name="install-in-a-disconnected-environment"></a>Instalación en un entorno desconectado

Puede instalar la máquina virtual cliente para administrar el clúster de Kubernetes en una instancia de Azure Stack desconectada de Internet.

1.  Desde una máquina con acceso a Internet, vaya a GitHub [Azure/aks-engine](https://github.com/Azure/aks-engine/releases/latest). Descargue un archivo (*.tar.gz) para una máquina Windows, por ejemplo, `aks-engine-v0.38.8-windows-amd64.tar.gz`.

2.  Cree una cuenta de almacenamiento en la instancia de Azure Stack para cargar el archivo de almacenamiento (*.tar.gz) con el archivo binario de AKS-Engine. Para obtener instrucciones sobre el uso del Explorador de Azure Storage, consulte [Explorador de Azure Storage con Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Cree una máquina virtual Windows en Azure Stack. Para obtener instrucciones, consulte [Inicio rápido: Creación de una máquina virtual Windows Server con el portal de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-windows-portal)

4.  Descargue el archivo en la máquina virtual de administración de la dirección URL del blob de la cuenta de almacenamiento de Azure Stack donde cargó el archivo de almacenamiento (*.tar.gz). Extraiga el archivo a un directorio al que tenga acceso desde el símbolo del sistema.

5. Conéctese a la máquina virtual.

6. [Instale Chocolatey con las instrucciones de PowerShell](https://chocolatey.org/install#install-with-powershellexe). 

7.  Ejecute el siguiente comando en un símbolo del sistema con privilegios elevados. Incluya el número de versión correcto:

    ```PowerShell  
        choco install aks-engine --version v0.41.2 -y
    ```

## <a name="verify-the-installation"></a>Comprobación de la instalación

Una vez configurada la máquina virtual de cliente, compruebe que ha instalado AKS-Engine.

1. Conéctese a su máquina virtual cliente.
2. Ejecute el siguiente comando:

    ```PowerShell  
    aks-engine version
    ```

Si no puede comprobar que ha instalado AKS-Engine en la máquina virtual cliente, consulte [Solución de problemas de instalación de AKS-Engine](azure-stack-kubernetes-aks-engine-troubleshoot.md).


## <a name="asdk-installation"></a>Instalación de ASDK

Tendrá que agregar un certificado al ejecutar la máquina virtual cliente para AKS-Engine en el ASDK en una máquina que está fuera del ASDK. Si utiliza una máquina virtual Windows en el propio entorno del ASDK, la máquina ya confía en el certificado del ASDK. Si la máquina cliente está fuera del ASDK, debe extraer el certificado del ASDK y agregarlo a la máquina Windows.

Si usa un ASDK, el punto de conexión de Azure Resource Manager utiliza un certificado autofirmado y debe agregar explícitamente este certificado al almacén de certificados de confianza de la máquina. Puede encontrar el certificado raíz del ASDK en cualquier máquina virtual que implemente en el ASDK.

1. Exporte el certificado raíz de la entidad de certificación. Para ver las instrucciones necesarias, consulte [Exportación del certificado raíz de la entidad de certificación de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#export-the-azure-stack-ca-root-certificate)
2. Confíe en el certificado de raíz de la entidad de certificación de Azure Stack. Para ver las instrucciones necesarias, consulte [Confiar en el certificado de raíz de la entidad de certificación de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2#trust-the-azure-stack-ca-root-certificate).

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación de un clúster de Kubernetes con AKS-Engine en Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)
