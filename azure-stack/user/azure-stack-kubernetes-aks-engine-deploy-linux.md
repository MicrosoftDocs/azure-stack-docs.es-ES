---
title: Instalación de AKS-Engine en Linux para Azure Stack | Microsoft Docs
description: Aprenda a usar una máquina Linux en Azure Stack para hospedar AKS-Engine con el fin de implementar y administrar un clúster de Kubernetes.
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
ms.date: 09/14/2019
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 09/14/2019
ms.openlocfilehash: 7e2ac217ead86502513b7914a102a029f2472a40
ms.sourcegitcommit: 09d14eb77a43fd585e7e6be93c32fa427770adb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019477"
---
# <a name="install-the-aks-engine-on-linux-in-azure-stack"></a>Instalación de AKS-Engine en Linux para Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar una máquina Linux en Azure Stack para hospedar AKS-Engine con el fin de implementar y administrar un clúster de Kubernetes. En este artículo, se examina la preparación de la máquina virtual cliente para administrar su clúster para instancias de Azure Stack conectadas y desconectadas, se comprueba la instalación y se configura la máquina virtual cliente en el ASDK.

## <a name="prepare-the-client-vm"></a>Preparación de la máquina virtual cliente

AKS-Engine es una herramienta de línea de comandos que se usa para implementar y administrar el clúster de Kubernetes. El motor se puede ejecutar en una máquina de Azure Stack. Desde esta máquina, ejecutará AKS-Engine para implementar los recursos y el software de IaaS necesarios para ejecutar el clúster. Después, puede usar la máquina que ejecuta el motor para realizar tareas de administración en el clúster.

Al elegir la máquina cliente, tenga en cuenta lo siguiente:

1. Si la máquina cliente debe recuperarse en caso de desastre.
2. Cómo se va conectar a la máquina cliente y cómo va a interactuar la máquina con el clúster.

## <a name="install-in-a-connected-environment"></a>Instalación en un entorno conectado

Puede instalar la máquina virtual cliente para administrar el clúster de Kubernetes en una instancia de Azure Stack conectada a Internet.

1. Cree una máquina virtual Linux en Azure Stack. Para obtener instrucciones, consulte [Inicio rápido: Creación de una máquina virtual de servidor Linux mediante el portal de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).
2. Conéctese a la máquina virtual.
3. Busque la versión de AKS-Engine en la tabla [Versiones admitidas de Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). La imagen base de AKS debe estar disponible en Marketplace de Azure Stack. Al ejecutar el comando, especificará la versión, por ejemplo, `--version v0.39.0`. Si no lo hace, el comando instalará la versión más reciente, que puede no estar disponible en Marketplace.
4. Ejecute el siguiente comando:

    ```bash  
        curl -o get-akse.sh https://raw.githubusercontent.com/Azure/aks-engine/master/scripts/get-akse.sh
        chmod 700 get-akse.sh
        ./get-akse.sh --version v0.XX.X
    ```

    > [!Note]  
    > Si se produce un error en este método en la instalación, puede probar los pasos del [entorno desconectado](#install-in-a-disconnected-environment) o [probar GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), que es otro un administrador de paquetes.

## <a name="install-in-a-disconnected-environment"></a>Instalación en un entorno desconectado

Puede instalar la máquina virtual cliente para administrar el clúster de Kubernetes en una instancia de Azure Stack desconectada de Internet.

1.  Desde una máquina con acceso a Internet, vaya a GitHub [Azure/aks-engine](https://github.com/Azure/aks-engine/releases/latest). Descargue un archivo (*.tar.gz) para una máquina Linux, por ejemplo, `aks-engine-v0.xx.x-linux-amd64.tar.gz`.

2.  Cree una cuenta de almacenamiento en la instancia de Azure Stack para cargar el archivo de almacenamiento (*.tar.gz) con el archivo binario de AKS-Engine. Para obtener instrucciones sobre el uso del Explorador de Azure Storage, consulte [Explorador de Azure Storage con Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Cree una máquina virtual Linux en Azure Stack. Para obtener instrucciones, consulte [Inicio rápido: Creación de una máquina virtual de servidor Linux mediante el portal de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).

3.  Descargue el archivo en la máquina virtual de administración de la dirección URL del blob de la cuenta de almacenamiento de Azure Stack donde cargó el archivo de almacenamiento (*.tar.gz). Extraiga el archivo en el directorio `/usr/local/bin`.

4. Conéctese a la máquina virtual.

5.  Ejecute el siguiente comando:

    ```bash  
    curl -o aks-engine-v0.xx.x-linux-amd64.tar.gz <httpurl/aks-engine-v0.xx.x-linux-amd64.tar.gz>
    tar xvzf aks-engine-v0.xx.x-linux-amd64.tar.gz -C /usr/local/bin
    ```

## <a name="verify-the-installation"></a>Comprobación de la instalación

Una vez configurada la máquina virtual de cliente, compruebe que ha instalado AKS-Engine.

1. Conéctese a su máquina virtual cliente.
2. Ejecute el siguiente comando:

    ```bash  
    aks-engine version
    ```

Si no puede comprobar que ha instalado AKS-Engine en la máquina virtual cliente, consulte [Solución de problemas de instalación de AKS-Engine](azure-stack-kubernetes-aks-engine-troubleshoot.md).


## <a name="asdk-installation"></a>Instalación de ASDK

Tendrá que agregar un certificado al ejecutar la máquina virtual del cliente para AKS-Engine en ASDK.

Si usa un ASDK, el punto de conexión de Azure Resource Manager utiliza un certificado autofirmado y debe agregar explícitamente este certificado al almacén de certificados de confianza de la máquina. Puede encontrar el certificado raíz del ASDK en cualquier máquina virtual que implemente en el ASDK. Por ejemplo, en una máquina virtual Ubuntu, la encontrará en este directorio `/var/lib/waagent/Certificates.pem`. 

Copie el archivo de certificado con el siguiente comando:

```bash
sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt

sudo update-ca-certificates
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación de un clúster de Kubernetes con AKS-Engine en Azure Stack](azure-stack-kubernetes-aks-engine-deploy-cluster.md)