---
title: Configuración de los requisitos previos para AKS-Engine en Azure Stack | Microsoft Docs
description: Establezca los requisitos para ejecutar el motor de ASK en Azure Stack.
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
ms.openlocfilehash: b56c5a2be45e9f92630283a2b702f37471e80290
ms.sourcegitcommit: 534117888d9b7d6d363ebe906a10dcf0acf8b685
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2019
ms.locfileid: "72173094"
---
# <a name="set-up-the-prerequisites-for-the-aks-engine-on-azure-stack"></a>Configuración de los requisitos previos para AKS-Engine en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede instalar AKS-Engine en una máquina virtual de su entorno o en cualquier máquina cliente con acceso al punto de conexión del Administrador de recursos de Azure Stack. Necesitará lo siguiente antes de ejecutar el motor: un servidor Ubuntu de AKS Base y una extensión de script personalizado de Linux disponibles en la suscripción, una identidad de entidad de servicio que se ha asignado a un rol de colaborador y un par de claves privada y pública para el acceso SSH al servidor Ubuntu. Además, si usa el Kit de desarrollo de Azure Stack, necesitará que la máquina confíe en los certificados adecuados.

Si tiene los requisitos previos, puede empezar a [definir el clúster.](azure-stack-kubernetes-aks-engine-deploy-cluster.md)

Si es el operador en la nube de Azure Stack y quiere ofrecer el motor de AKS, siga las instrucciones que se indican en [Agregar el motor de AKS a Marketplace de Azure Stack](../operator/azure-stack-aks-engine.md).

## <a name="prerequisites-for-the-aks-engine"></a>Requisitos previos para AKS-Engine

Para usar AKS-Engine, debe tener disponibles los siguientes recursos. Tenga en cuenta que AKS-Engine está pensado para que lo usen los inquilinos de Azure Stack para implementar clústeres de Kubernetes en su suscripción de inquilino. La única parte donde se puede requerir la implicación del operador de Azure Stack es para descargar los elementos de Marketplace y crear una identidad de entidad de servicio. Puede encontrar los detalles en la tabla siguiente.

| Requisito previo | DESCRIPCIÓN | Obligatorio | Instrucciones |
| --- | --- | --- | --- |
| Extensión de script personalizado de Linux | Extensión de script personalizado de Linux 2.0<br>Oferta: Script personalizado para Linux 2.0<br>Versión: 2.0.6 (o la versión más reciente)<br>Publicador: Microsoft Corp | Obligatorio | Si no tiene este elemento en la suscripción, póngase en contacto con el operador de la nube. |
| Imagen base de Ubuntu de AKS | Imagen base de AKS<br>Oferta: AKS<br>Versión: 2019.09.19 (o una versión más reciente)<br>Publicador: Microsoft-AKS<br>SKU: aks-ubuntu-1604-201909 | Obligatorio | Si no tiene este elemento en la suscripción, póngase en contacto con el operador de la nube. Para obtener más información sobre la dependencia de versión, consulte [Correspondencia de la versión del motor con la imagen base](#matching-engine-to-base-image-version).<br> Si es el operador en la nube de Azure Stack y quiere ofrecer el motor de AKS, siga las instrucciones que se indican en [Agregar el motor de AKS a Marketplace de Azure Stack](../operator/azure-stack-aks-engine.md). |
| Suscripción a Azure Stack | Puede acceder a las ofertas de Azure Stack mediante las suscripciones. La oferta contiene los servicios que tiene a su disposición. | Obligatorio | Para poder implementar cargas de trabajo de inquilinos en Azure Stack, primero debe obtener una [suscripción a Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-subscribe-services). |
| Identidad de la entidad de servicio (SPN) |  Una aplicación que necesita implementar o configurar recursos a través de Azure Resource Manager, debe estar representada por una entidad de servicio. | Obligatorio | Es posible que deba ponerse en contacto con el operador de Azure Stack para este elemento.  Para obtener instrucciones, consulte [Uso de una identidad de aplicación para acceder a recursos](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals). |
| Rol de **colaborador** asignado (SPN) | Para permitir que una aplicación acceda a los recursos de su suscripción con su entidad de servicio, debe asignar la entidad de servicio un rol para un determinado recurso. | Obligatorio | Para obtener instrucciones, consulte la sección [Asignar un rol](https://docs.microsoft.com/azure-stack/operator/azure-stack-create-service-principals#assign-a-role). |
| Resource group | Un grupo de recursos es un contenedor que almacena los recursos relacionados con una solución de Azure. Si no especifica un grupo de recursos existente, la herramienta creará uno automáticamente. | Opcional | [Administración de grupos de recursos de Azure Resource Manager mediante Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/manage-resource-groups-portal) |
| Clave pública privada | Para usar una conexión SSH abierta desde la máquina de desarrollo a la máquina virtual del servidor en la instancia de Azure Stack que hospeda la aplicación web, tiene que crear un par de claves, pública y privada, de Secure Shell (SSH). | Obligatorio | Para obtener instrucciones sobre cómo generar una clave, consulte [SSH Key Generation](https://docs.microsoft.com/azure-stack/user/azure-stack-dev-start-howto-ssh-public-key) (Generación de claves SSH).|

> [!Note]  
> También puede crear los requisitos previos para AKS-Engine con la [CLI de Azure para Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-version-profiles-azurecli2) o [PowerShell de Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install).

## <a name="matching-engine-to-base-image-version"></a>Correspondencia de la versión del motor con la imagen base

AKS-Engine usa una imagen compilada, la **imagen base de AKS**. Cualquier versión de AKS-Engine depende de una versión de imagen específica que esté disponible en Azure Stack por el operador de Azure Stack. Puede encontrar una tabla que muestre las versiones de AKS-Engine y las versiones de Kubernetes compatibles correspondientes en [Versiones admitidas de Kubernetes](https://github.com/Azure/aks-engine/blob/master/docs/topics/azure-stack.md#supported-kubernetes-versions). Por ejemplo, la versión de AKS-Engine `v0.41.2` depende de la versión `2019.09.19` de la imagen base de AKS. Pida al operador de Azure Stack que descargue la versión de imagen específica desde Azure Marketplace en Marketplace de Azure Stack.

Se desencadenará un error si la imagen no está disponible en Marketplace de Azure Stack. Por ejemplo, si actualmente utiliza AKS Engine versión v0.41.2 y la versión `2019.09.19` de la imagen base de AKS no está disponible, verá el siguiente error al ejecutar AKS Engine: 

```Text  
The platform image 'microsoft-aks:aks:aks-ubuntu-1604-201908:2019.08.09' is not available. 
Verify that all fields in the storage profile are correct.
```

Para comprobar la versión actual de AKS-Engine, ejecute el comando siguiente:

```bash  
$ aks-engine version
Version: v0.39.1
GitCommit: 6fff62731
GitTreeState: clean
```

## <a name="next-steps"></a>Pasos siguientes

> [!div class="nextstepaction"]
> [Implementación de AKS-Engine en Windows para Azure Stack](azure-stack-kubernetes-aks-engine-deploy-windows.md)  
> [Implementación de AKS-Engine en Linux en Azure Stack](azure-stack-kubernetes-aks-engine-deploy-linux.md)