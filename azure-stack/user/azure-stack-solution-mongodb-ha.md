---
title: Implementar una solución MongoDB de alta disponibilidad en Azure y Azure Stack | Microsoft Docs
description: Aprenda a implementar una solución MongoDB de alta disponibilidad en Azure y Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: e31a71098cda4f769fbce4e55bec6ec72a5a651b
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277767"
---
# <a name="deploy-a-highly-available-mongodb-solution-to-azure-and-azure-stack"></a>Implementar una solución MongoDB de alta disponibilidad en Azure y Azure Stack

Este artículo le guiará a lo largo de una implementación automatizada de un clúster de MongoDB de alta disponibilidad (HA) con un sitio de recuperación ante desastres (DR) entre dos entornos de Azure Stack. Para obtener más información acerca de MongoDB y la alta disponibilidad, consulte [Replica Set Members](https://docs.mongodb.com/manual/core/replica-set-members/) (Miembros del conjunto de réplicas).

En esta solución, creará un entorno de ejemplo para:

> [!div class="checklist"]
> - Coordinar una implementación en dos instancias de Azure Stack
> - Usar Docker para minimizar los problemas de dependencia con los perfiles de la API de Azure
> - Implementar un clúster de MongoDB de alta disponibilidad básico con un sitio de recuperación ante desastres


> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack es una extensión de Azure. Azure Stack aporta la agilidad y la innovación de la informática en la nube a su entorno local y hace posible la única nube híbrida que le permite crear e implementar aplicaciones híbridas en cualquier parte.  
> 
> En el artículo [Consideraciones de diseño para aplicaciones híbridas](azure-stack-edge-pattern-overview.md) se examinan los pilares de la calidad de software (selección de ubicación, escalabilidad, disponibilidad, resistencia, manejabilidad y seguridad) para diseñar, implementar y usar aplicaciones híbridas. Las consideraciones de diseño ayudan a optimizar el diseño de aplicaciones híbridas y reducen los desafíos en los entornos de producción.



## <a name="architecture-for-mongodb-with-azure-stack"></a>Arquitectura de MongoDB con Azure Stack

![MongoDB de alta disponibilidad en Azure Stack](media/azure-stack-solution-mongdb-ha/image1.png)

## <a name="prerequisites-for-mongodb-with-azure-stack"></a>Requisitos previos para MongoDB con Azure Stack

  - Dos sistemas integrados de Azure Stack conectados (Azure Stack): esta implementación no funciona en los Kits de desarrollo de Azure Stack (ASDK). Para obtener más información sobre Azure Stack, consulte [¿Qué es Azure Stack?](https://azure.microsoft.com/overview/azure-stack/).
      - Una suscripción de inquilino en cada instancia de Azure Stack.    
      - **Tome nota de cada identificador de suscripción y del punto de conexión de Azure Resource Manager para cada instancia de Azure Stack.**
  - Una entidad de servicio de Azure Active Directory (Azure AD) que tenga permisos para la suscripción del inquilino en Azure Stack. Es posible que deba crear dos entidades de servicio si las instancias de Azure Stack se implementan en diferentes inquilinos de Azure AD. Para obtener información sobre cómo crear una entidad de servicio para Azure Stack, visite [Creación de entidades de servicio para otorgar a las aplicaciones acceso a los recursos de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).    
      - **Tome nota del id. de aplicación, el secreto de cliente y el nombre de inquilino (xxxxx.onmicrosoft.com) de cada entidad de servicio.**
  - Ubuntu 16.04 sindicado en el Marketplace de cada instancia de Azure Stack. Para obtener más información acerca de la redifusión de Marketplace, vea [Descarga de elementos de Marketplace desde Azure a Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
  - [Docker para Windows](https://docs.docker.com/docker-for-windows/) instalado en la máquina local.

## <a name="get-the-docker-image"></a>Obtener la imagen de Docker

Las imágenes de Docker para cada implementación eliminan los problemas de dependencia entre las distintas versiones de Azure PowerShell.
1.  Asegúrese de que Docker para Windows utiliza contenedores de Windows.
2.  Ejecute lo siguiente en un símbolo del sistema con privilegios elevados para obtener el contenedor de Docker con los scripts de implementación.
```powershell  
docker pull intelligentedge/mongodb-hadr:1.0.0
```

## <a name="deploy-the-clusters"></a>Implementar los clústeres

1.  Una vez extraída correctamente la imagen de contenedor, inicie la imagen.\

    ```powershell  
    docker run -it intelligentedge/mongodb-hadr:1.0.0 powershell
    ```

2.  Una vez iniciado el contenedor, se le ofrecerá un terminal de PowerShell con privilegios elevados en el contenedor. Cambie los directorios para acceder al script de implementación.

    ```powershell  
    cd .\MongoHADRDemo\
    ```

3.  Ejecute la implementación. Proporcione las credenciales y los nombres de los recursos cuando sea necesario. “Alta disponibilidad” se refiere a la instancia de Azure Stack donde se implementará el clúster de alta disponibilidad, y “DR” a la instancia de Azure Stack donde se implementará el clúster de DR.

    ```powershell
    .\Deploy-AzureResourceGroup.ps1 `
    -AzureStackApplicationId_HA "applicationIDforHAServicePrincipal" `
    -AzureStackApplicationSercet_HA "clientSecretforHAServicePrincipal" `
    -AADTenantName_HA "hatenantname.onmicrosoft.com" `
    -AzureStackResourceGroup_HA "haresourcegroupname" `
    -AzureStackArmEndpoint_HA "https://management.haazurestack.com" `
    -AzureStackSubscriptionId_HA "haSubscriptionId" `
    -AzureStackApplicationId_DR "applicationIDforDRServicePrincipal" `
    -AzureStackApplicationSercet_DR "ClientSecretforDRServicePrincipal" `
    -AADTenantName_DR "drtenantname.onmicrosoft.com" `
    -AzureStackResourceGroup_DR "drresourcegroupname" `
    -AzureStackArmEndpoint_DR "https://management.drazurestack.com" `
    -AzureStackSubscriptionId_DR "drSubscriptionId"
    ```

4.  Escriba `Y` para permitir que el proveedor de NuGet se instale, lo que iniciará los módulos "2018-03-01-hybrid" del perfil de la API que se van a instalar.

5.  Los recursos de alta disponibilidad se implementarán en primer lugar. Supervise la implementación y espere a que se complete. Una vez que tenga el mensaje que indica que la implementación de alta disponibilidad se ha completado, puede comprobar el portal de Azure Stack de alta disponibilidad para ver los recursos implementados. 

6.  Continúe con la implementación de recursos de DR y decida si quiere habilitar un jumpbox en Azure Stack de DR para interactuar con el clúster.

7.  Espere a que la implementación de recursos de DR se complete.

8.  Una vez completada la implementación de recursos de DR, salga del contenedor.

  ```powershell
  exit
  ```

## <a name="next-steps"></a>Pasos siguientes

  - Si habilitó la VM de jumpbox en Azure Stack de DR, puede conectarse mediante SSH e interactuar con el clúster de MongoDB mediante la instalación de la CLI de mongo. Para obtener más información sobre cómo interactuar con MongoDB, consulte [The mongo Shell](https://docs.mongodb.com/manual/mongo/) (El shell mongo).

  - Para obtener más información sobre las aplicaciones en la nube híbrida, consulte [Soluciones en la nube híbrida.](https://aka.ms/azsdevtutorials)

  - Modifique el código según este ejemplo de [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
