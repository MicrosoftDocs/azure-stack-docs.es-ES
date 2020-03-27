---
title: Instalación del motor de AKS en Linux para Azure Stack Hub
description: Aprenda a usar una máquina Linux en Azure Stack Hub para hospedar el motor de AKS con el fin de implementar y administrar un clúster de Kubernetes.
author: mattbriggs
ms.topic: article
ms.date: 3/19/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 3/19/2020
ms.openlocfilehash: 23ffcd6e92ba442447dfa43459344904af514d14
ms.sourcegitcommit: 17be49181c8ec55e01d7a55c441afe169627d268
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/21/2020
ms.locfileid: "80069094"
---
# <a name="install-the-aks-engine-on-linux-in-azure-stack-hub"></a>Instalación del motor de AKS en Linux para Azure Stack Hub

Puede usar una máquina Linux en Azure Stack Hub para hospedar el motor de AKS con el fin de implementar y administrar un clúster de Kubernetes. En este artículo, se examina la preparación de la máquina virtual cliente para administrar su clúster para instancias de Azure Stack Hub conectadas y desconectadas, se comprueba la instalación y se configura la máquina virtual cliente en ASDK.

## <a name="prepare-the-client-vm"></a>Preparación de la máquina virtual cliente

El motor de AKS es una herramienta de línea de comandos que se usa para implementar y administrar el clúster de Kubernetes. El motor se puede ejecutar en una máquina de Azure Stack Hub. Desde esta máquina, ejecutará el motor de AKS para implementar los recursos y el software de IaaS necesarios para ejecutar el clúster. Después, puede usar la máquina que ejecuta el motor para realizar tareas de administración en el clúster.

Al elegir la máquina cliente, tenga en cuenta lo siguiente:

1. Si la máquina cliente debe recuperarse en caso de desastre.
2. Cómo se va conectar a la máquina cliente y cómo va a interactuar la máquina con el clúster.

## <a name="install-in-a-connected-environment"></a>Instalación en un entorno conectado

Puede instalar la máquina virtual cliente para administrar el clúster de Kubernetes en una instancia de Azure Stack Hub conectada a Internet.

1. Cree una máquina virtual Linux en Azure Stack Hub. Para obtener instrucciones, consulte [Inicio rápido: Creación de una máquina virtual de servidor Linux mediante el portal de Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).
2. Conéctese a la máquina virtual.
3. Busque la versión del motor de AKS en la tabla [Versiones admitidas de Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). La imagen base de AKS debe estar disponible en el Marketplace de Azure Stack Hub. Al ejecutar el comando, debe especificar la versión `--version v0.43.0`. Si no lo hace, el comando instalará la versión más reciente, que podría necesitar una imagen de VHD que no está disponible en Marketplace.
4. Ejecute el siguiente comando:

    ```bash  
        curl -o get-akse.sh https://raw.githubusercontent.com/Azure/aks-engine/master/scripts/get-akse.sh
        chmod 700 get-akse.sh
        ./get-akse.sh --version v0.43.0
    ```

    > [!Note]  
    > Si se produce un error en este método en la instalación, puede probar los pasos del [entorno desconectado](#install-in-a-disconnected-environment) o [probar GoFish](azure-stack-kubernetes-aks-engine-troubleshoot.md#try-gofish), que es otro un administrador de paquetes.

## <a name="install-in-a-disconnected-environment"></a>Instalación en un entorno desconectado

Puede instalar la máquina virtual cliente para administrar el clúster de Kubernetes en una instancia de Azure Stack Hub desconectada de Internet.

1.  Desde una máquina con acceso a Internet, vaya a GitHub [Azure/aks-engine](https://github.com/Azure/aks-engine/releases/latest). Descargue un archivo (*.tar.gz) para una máquina Linux, por ejemplo, `aks-engine-v0.xx.x-linux-amd64.tar.gz`.

2.  Cree una cuenta de almacenamiento en la instancia de Azure Stack Hub para cargar el archivo de almacenamiento (*.tar.gz) con el archivo binario del motor de AKS. Para obtener instrucciones sobre el uso del Explorador de Azure Storage, consulte [Explorador de Azure Storage con Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-storage-connect-se).

3. Cree una máquina virtual Linux en Azure Stack Hub. Para obtener instrucciones, consulte [Inicio rápido: Creación de una máquina virtual de servidor Linux mediante el portal de Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-quick-linux-portal).

3.  Descargue el archivo en la máquina virtual de administración de la dirección URL del blob de la cuenta de Azure Stack Hub Storage donde cargó el archivo de almacenamiento (*.tar.gz). Extraiga el archivo en el directorio `/usr/local/bin`.

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

3. Si el punto de conexión de Azure Resource Manager utiliza un certificado autofirmado, debe agregar explícitamente el certificado raíz al almacén de certificados de confianza de la máquina. Puede encontrar el certificado raíz de la máquina virtual en este directorio: /var/lib/waagent/Certificates.pem. Copie el archivo de certificado con el siguiente comando: 

   ```bash
   sudo cp /var/lib/waagent/Certificates.pem /usr/local/share/ca-certificates/azurestackca.crt 
   sudo update-ca-certificates
   ```

Si no puede comprobar que ha instalado el motor de AKS en la máquina virtual cliente, consulte [Solución de problemas de instalación del motor de AKS](azure-stack-kubernetes-aks-engine-troubleshoot.md).


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
> [Implementación de un clúster de Kubernetes con el motor de AKS en Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-cluster.md)