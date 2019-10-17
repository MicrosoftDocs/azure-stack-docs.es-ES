---
title: Implementación de un grupo de disponibilidad de SQL Server 2016 en Azure y Azure Stack | Microsoft Docs
description: Aprenda a implementar un grupo de disponibilidad de SQL Server 2016 en Azure y Azure Stack.
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
ms.openlocfilehash: afb295d775eb9e8d35878c4d9c69106c558ffec0
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277760"
---
# <a name="deploy-a-sql-server-2016-availability-group-to-azure-and-azure-stack"></a>Implementar un grupo de disponibilidad de SQL Server 2016 en Azure y Azure Stack

Este artículo le guiará a lo largo de una implementación automatizada de un clúster de SQL Server 2016 Enterprise de alta disponibilidad (HA) con un sitio de recuperación ante desastres (DR) asincrónica entre dos entornos de Azure Stack. Para obtener más información acerca de SQL Server 2016 y la alta disponibilidad, vea [Grupos de disponibilidad Always On: una solución de alta disponibilidad y recuperación ante desastres](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2016).

En esta solución, creará un entorno de ejemplo para:

> [!div class="checklist"]
> - Coordinar una implementación en dos instancias de Azure Stack
> - Usar Docker para minimizar los problemas de dependencia con los perfiles de la API de Azure
> - Implementar un clúster de SQL Server 2016 Enterprise de alta disponibilidad básico con un sitio de recuperación ante desastres

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack es una extensión de Azure. Azure Stack aporta la agilidad y la innovación de la informática en la nube a su entorno local y hace posible la única nube híbrida que le permite crear e implementar aplicaciones híbridas en cualquier parte.  
> 
> En el artículo [Consideraciones de diseño para aplicaciones híbridas](azure-stack-edge-pattern-overview.md) se examinan los pilares de la calidad de software (selección de ubicación, escalabilidad, disponibilidad, resistencia, manejabilidad y seguridad) para diseñar, implementar y usar aplicaciones híbridas. Las consideraciones de diseño ayudan a optimizar el diseño de aplicaciones híbridas y reducen los desafíos en los entornos de producción.

## <a name="architecture-for-sql-server-2016"></a>Arquitectura para SQL Server 2016

![Azure Stack de SQL de alta disponibilidad de SQL Server 2016](media/azure-stack-solution-sql-ha/image1.png)

## <a name="prerequisites-for-sql-server-2016"></a>Requisitos previos para SQL Server 2016

  - Dos sistemas integrados de Azure Stack conectados (Azure Stack): esta implementación no funciona en los Kits de desarrollo de Azure Stack (ASDK). Para obtener más información sobre Azure Stack, consulte [¿Qué es Azure Stack?](https://azure.microsoft.com/overview/azure-stack/).
  - Una suscripción de inquilino en cada instancia de Azure Stack.    
      - **Tome nota de cada identificador de suscripción y del punto de conexión de Azure Resource Manager para cada instancia de Azure Stack.**
  - Una entidad de servicio de Azure Active Directory (Azure AD) que tenga permisos para la suscripción del inquilino en Azure Stack. Es posible que deba crear dos entidades de servicio si las instancias de Azure Stack se implementan en diferentes inquilinos de Azure AD. Para obtener información sobre cómo crear una entidad de servicio para Azure Stack, visite [Creación de entidades de servicio para otorgar a las aplicaciones acceso a los recursos de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Tome nota del id. de aplicación, el secreto de cliente y el nombre de inquilino (xxxxx.onmicrosoft.com) de cada entidad de servicio.**
  - SQL Server 2016 Enterprise sindicado en el Marketplace de cada instancia de Azure Stack. Para obtener más información acerca de la redifusión de Marketplace, vea [Descarga de elementos de Marketplace desde Azure a Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).
    **Asegúrese de que su organización disponga de las licencias de SQL correspondientes.**
  - [Docker para Windows](https://docs.docker.com/docker-for-windows/) instalado en la máquina local.

## <a name="get-the-docker-image"></a>Obtener la imagen de Docker

Las imágenes de Docker para cada implementación eliminan los problemas de dependencia entre las distintas versiones de Azure PowerShell.

1.  Asegúrese de que Docker para Windows utiliza contenedores de Windows.
2.  Ejecute lo siguiente en un símbolo del sistema con privilegios elevados para obtener el contenedor de Docker con los scripts de implementación.

```powershell  
 docker pull intelligentedge/sqlserver2016-hadr:1.0.0
```

## <a name="deploy-the-availability-group"></a>Implementar el grupo de disponibilidad

1.  Una vez extraída correctamente la imagen de contenedor, inicie la imagen.

      ```powershell  
      docker run -it intelligentedge/sqlserver2016-hadr:1.0.0 powershell
      ```

2.  Una vez iniciado el contenedor, se le ofrecerá un terminal de PowerShell con privilegios elevados en el contenedor. Cambie los directorios para acceder al script de implementación.

      ```powershell  
      cd .\SQLHADRDemo\
      ```

3.  Ejecute la implementación. Proporcione las credenciales y los nombres de los recursos cuando sea necesario. “Alta disponibilidad” se refiere a la instancia de Azure Stack donde se implementará el clúster de alta disponibilidad, y “DR” a la instancia de Azure Stack donde se implementará el clúster de DR.

      ```powershell
      > .\Deploy-AzureResourceGroup.ps1 `
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

5.  Espere a que la implementación de recursos se complete.

6.  Una vez completada la implementación de recursos de DR, salga del contenedor.

      ```powershell
      exit
      ```

7.  Para inspeccionar la implementación, observe los recursos del portal de cada instancia de Azure Stack. Conéctese a una de las instancias de SQL en el entorno de alta disponibilidad e inspeccione el grupo de disponibilidad mediante SQL Server Management Studio (SSMS).

![Alta disponibilidad de SQL de SQL Server 2016](media/azure-stack-solution-sql-ha/image2.png)

## <a name="next-steps"></a>Pasos siguientes

  - Use SQL Server Management Studio para conmutar por error el clúster manualmente. Consulte [Realización de una conmutación por error manual forzada de un grupo de disponibilidad Always On (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/perform-a-forced-manual-failover-of-an-availability-group-sql-server?view=sql-server-2017).
  - Para obtener más información sobre las aplicaciones en la nube híbrida, consulte [Soluciones en la nube híbrida.](https://aka.ms/azsdevtutorials)
  - Use sus propios datos o modifique el código según este ejemplo en [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
