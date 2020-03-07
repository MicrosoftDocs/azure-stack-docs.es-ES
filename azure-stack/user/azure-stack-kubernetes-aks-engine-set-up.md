---
title: Configuración de los requisitos previos para el motor de AKS en Azure Stack Hub
description: Establezca los requisitos para ejecutar el motor de ASK en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 2/27/2020
ms.author: mabrigg
ms.reviewer: waltero
ms.lastreviewed: 2/27/2020
ms.openlocfilehash: 1f307a554eca0b9b1bc2af9d8e64a8ec585da078
ms.sourcegitcommit: bbc4023c9a673c146de4a9e242311d429f7781eb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/28/2020
ms.locfileid: "77782807"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack-hub"></a>Configuración de los requisitos previos para el motor de AKS en Azure Stack Hub

El motor de AKS se puede instalar en una máquina virtual del entorno o en cualquier máquina cliente con acceso al punto de conexión de Resource Manager de Azure Stack Hub. Necesitará lo siguiente antes de ejecutar el motor: un servidor Ubuntu de AKS Base y una extensión de script personalizado de Linux disponibles en la suscripción, una identidad de entidad de servicio que se ha asignado a un rol de colaborador y un par de claves privada y pública para el acceso SSH al servidor Ubuntu. Además, si usa el Kit de desarrollo de Azure Stack, necesitará que la máquina confíe en los certificados adecuados.

Si tiene los requisitos previos, puede empezar a [definir el clúster.](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

Si es operador en la nube de Azure Stack Hub y desea ofrecer el motor de AKS, siga las instrucciones que encontrará en [Incorporación de requisitos previos del motor de Azure Kubernetes Service (AKS) al Marketplace de Azure Stack Hub](../operator/azure-stack-aks-engine.md).

## <a name="prerequisites-for-the-aks-engine"></a>Requisitos previos para el motor de AKS

Para usar el motor de AKS, es preciso tener disponibles los siguientes recursos. Tenga en cuenta que el motor de AKS está pensado para que lo usen los inquilinos de Azure Stack Hub para implementar clústeres de Kubernetes en su suscripción de inquilino. La única parte en la que se puede requerir la implicación del operador de Azure Stack Hub es en la descarga de los elementos de Marketplace y la creación de una identidad de entidad de servicio. Puede encontrar los detalles en la tabla siguiente.

El operador de la nube deberá tener en vigor los siguientes elementos.

| Requisito previo | Descripción | Obligatorio | Instructions |
| --- | --- | --- | --- | --- |
| Azure Stack Hub 1910 o posterior | El motor de AKS requiere Azure Stack Hub 1910 u otra versión posterior. | Obligatorio | Si no está seguro de la versión de Azure Stack Hub, póngase en contacto con el operador de la nube. |
| Extensión de script personalizado de Linux | Extensión de script personalizado de Linux 2.0<br>Oferta: Script personalizado para Linux 2.0<br>Versión: 2.0.6 (o la versión más reciente)<br>Publicador: Microsoft Corp | Obligatorio | Si no tiene este elemento en la suscripción, póngase en contacto con el operador de la nube. |
| Imagen base de Ubuntu de AKS | Imagen base de AKS<br>Oferta: AKS<br> 2019.10.24 (o una versión más reciente)<br>Publicador: Microsoft-AKS<br>SKU: aks-ubuntu-1604-201910 | Obligatorio | Si no tiene este elemento en la suscripción, póngase en contacto con el operador de la nube. Para obtener más información sobre la dependencia de versión, consulte [Correspondencia de la versión del motor con la imagen base](#matching-engine-to-base-image-version).<br> Si es operador en la nube de Azure Stack Hub y desea ofrecer el motor de AKS, siga las instrucciones que encontrará en [Incorporación de requisitos previos del motor de Azure Kubernetes Service (AKS) al Marketplace de Azure Stack Hub](../operator/azure-stack-aks-engine.md). |
| Identidad de la entidad de servicio (SPN) |  Una aplicación que necesita implementar o configurar recursos a través de Azure Resource Manager, debe estar representada por una entidad de servicio. | Obligatorio | Es posible que deba ponerse en contacto con el operador de Azure Stack Hub para este elemento.  Para obtener instrucciones, consulte [Uso de una identidad de aplicación para acceder a recursos](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals). |
| Rol de **colaborador** asignado (SPN) | Para permitir que una aplicación acceda a los recursos de su suscripción con su entidad de servicio, debe asignar la entidad de servicio un rol para un determinado recurso. | Obligatorio | Para obtener instrucciones, consulte la sección [Asignar un rol](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals#assign-a-role). |

Puede establecer los siguientes elementos.

| Requisito previo | Descripción | Obligatorio | Instructions |
| --- | --- | --- | --- |
| Suscripción a Azure Stack Hub | A las ofertas de Azure Stack Hub se acceder mediante suscripciones. La oferta contiene los servicios que tiene a su disposición. | Obligatorio | Para poder implementar cargas de trabajo de inquilinos en Azure Stack Hub, primero es preciso obtener una [suscripción a Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services). |
| Resource group | Un grupo de recursos es un contenedor que almacena los recursos relacionados con una solución de Azure. Si no especifica un grupo de recursos existente, la herramienta creará uno automáticamente. | Opcional | [Administración de grupos de recursos de Azure Resource Manager mediante Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal) |
| Clave pública privada | Para usar una conexión SSH abierta desde una máquina de desarrollo a la máquina virtual del servidor en una instancia de Azure Stack Hub que hospede una aplicación web, es preciso crear un par de claves, pública y privada, de Secure Shell (SSH). | Obligatorio | Para obtener instrucciones sobre cómo generar una clave, consulte [SSH Key Generation](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key) (Generación de claves SSH).|


> [!Note]  
> También puede crear los requisitos previos para el motor de AKS con la [CLI de Azure para Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2) o [PowerShell de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install).

## <a name="matching-engine-to-base-image-version"></a>Correspondencia de la versión del motor con la imagen base

El **motor de AKS** usa una imagen compilada, la imagen base de AKS. Cualquier versión del motor de AKS depende de una versión de imagen específica que el operador de Azure Stack Hub haya incorporado a Azure Stack Hub para que esté disponible. Puede encontrar una tabla que muestre las versiones del motor de AKS y las versiones de Kubernetes compatibles correspondientes en [Versiones admitidas de Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). Por ejemplo, la versión del motor de AKS `v0.43.0` depende de la versión `2019.10.24` de la imagen base de AKS. Pida al operador de Azure Stack Hub que descargue la versión de imagen específica de Azure Marketplace en Marketplace de Azure Stack Hub.

Se desencadenará un error si la imagen no está disponible en Marketplace de Azure Stack Hub. Por ejemplo, si actualmente utiliza la versión v0.43.0 del motor de AKS y la versión `2019.10.24` de la imagen base de AKS no está disponible, verá el siguiente error al ejecutar el motor de AKS: 

```Text  
The platform image 'microsoft-aks:aks:aks-ubuntu-1604-201908:2019.08.09' is not available. 
Verify that all fields in the storage profile are correct.
```

Para comprobar la versión actual del motor de AKS, ejecute el comando siguiente:

```bash  
$ aks-engine version
Version: v0.39.1
GitCommit: 6fff62731
GitTreeState: clean
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación del motor de AKS en Windows para Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [Implementación del motor de AKS en Linux en Azure Stack Hub](azure-stack-kubernetes-aks-engine-deploy-linux.md)