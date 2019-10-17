---
title: Implementación de una solución de análisis de datos almacenados provisionalmente en Azure Stack | Microsoft Docs
description: Aprenda a implementar una solución de análisis de datos almacenados provisionalmente en Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.topic: conceptual
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: 187fe88ad304804462cc4461b24eb10a7af7a299
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277739"
---
# <a name="deploy-a-staged-data-analytics-solution-to-azure-stack"></a>Implementación de una solución de análisis de datos almacenados provisionalmente en Azure Stack

En este artículo se muestra cómo implementar una solución de recopilación de datos que requiera análisis en el mismo punto de recopilación para poder tomar decisiones rápidas. Esta recopilación de datos se produce con frecuencia sin acceso a Internet. Una vez restablecida la conectividad, puede que necesite un análisis intensivo del recurso de los datos para obtener información adicional.

En esta solución, creará un entorno de ejemplo para:

> [!div class="checklist"]
> - Crear el blob de almacenamiento de datos sin procesar.
> - Crear una función de Azure Stack para mover los datos limpios de Azure Stack a Azure.
> - Crear una función desencadenada por Blob Storage.
> - Crear una cuenta de almacenamiento de Azure Stack que contenga un blob y una cola.
> - Crear una función desencadenada por la cola.
> - Probar la función desencadenada por la cola.

> [!Tip]  
> ![hybrid-pillars.png](./media/azure-stack-solution-cloud-burst/hybrid-pillars.png)  
> Microsoft Azure Stack es una extensión de Azure. Azure Stack aporta la agilidad y la innovación de la informática en la nube a su entorno local y hace posible la única nube híbrida que le permite crear e implementar aplicaciones híbridas en cualquier parte.  
> 
> En el artículo [Consideraciones de diseño para aplicaciones híbridas](azure-stack-edge-pattern-overview.md) se examinan los pilares de la calidad de software (selección de ubicación, escalabilidad, disponibilidad, resistencia, manejabilidad y seguridad) para diseñar, implementar y usar aplicaciones híbridas. Las consideraciones de diseño ayudan a optimizar el diseño de aplicaciones híbridas y reducen los desafíos en los entornos de producción.

## <a name="architecture-for-staged-data-analytics"></a>Arquitectura de análisis de datos almacenados provisionalmente

![análisis de datos almacenados provisionalmente](media/azure-stack-solution-staged-data/image1.png)

## <a name="prerequisites-for-staged-data-analytics"></a>Requisitos previos para el análisis de datos almacenados provisionalmente

  - Una suscripción de Azure.
  - Una entidad de servicio de Azure Active Directory (Azure AD) que tenga permisos para la suscripción del inquilino en Azure y Azure Stack. Es posible que deba crear dos entidades de servicio si Azure Stack usa un inquilino de AAD diferente que su suscripción de Azure. Para obtener información sobre cómo crear una entidad de servicio para Azure Stack, visite [Creación de entidades de servicio para otorgar a las aplicaciones acceso a los recursos de Azure Stack](https://docs.microsoft.com/azure-stack/user/azure-stack-create-service-principals).
      - **Tome nota del id. de aplicación, el secreto de cliente, el id. de inquilino de Azure AD y nombre de inquilino (xxxxx.onmicrosoft.com) de cada entidad de servicio.**
  - Deberá proporcionar una colección de datos para el análisis de datos. Se proporcionan datos de ejemplo.
  - [Docker para Windows](https://docs.docker.com/docker-for-windows/) instalado en la máquina local.

## <a name="get-the-docker-image"></a>Obtener la imagen de Docker

Las imágenes de Docker para cada implementación eliminan los problemas de dependencia entre las distintas versiones de Azure PowerShell.
1.  Asegúrese de que Docker para Windows utiliza contenedores de Windows.
2.  Ejecute lo siguiente en un símbolo del sistema con privilegios elevados para obtener el contenedor de Docker con los scripts de implementación.

```
 docker pull intelligentedge/stageddatasolution:1.0.0
```

## <a name="deploy-the-solution"></a>Implementación de la solución

1.  Una vez extraída correctamente la imagen de contenedor, inicie la imagen.

      ```powershell  
      docker run -it intelligentedge/stageddatasolution:1.0.0 powershell
      ```

2.  Una vez iniciado el contenedor, se le ofrecerá un terminal de PowerShell con privilegios elevados en el contenedor. Cambie los directorios para acceder al script de implementación.

      ```powershell  
      cd .\SDDemo\
      ```

3.  Ejecute la implementación. Proporcione las credenciales y los nombres de los recursos cuando sea necesario. “Alta disponibilidad” se refiere a la instancia de Azure Stack donde se implementará el clúster de alta disponibilidad, y “DR” a la instancia de Azure Stack donde se implementará el clúster de DR.

      ```powershell
      .\DeploySolution-Azure-AzureStack.ps1 `
      -AzureApplicationId "applicationIDforAzureServicePrincipal" `
      -AzureApplicationSercet "clientSecretforServicePrincipal" `
      -AzureTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -AzureStackAADTenantName "azurestacktenant.onmicrosoft.com" `
      -AzureStackTenantARMEndpoint "https://management.haazurestack.com" `
      -AzureStackApplicationId "applicationIDforStackServicePrincipal" `
      -AzureStackApplicationSercet "ClientSecretforStackServicePrincipal" `
      -AzureStackTenantId "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" `
      -ResourcePrefix "aPrefixForResources"
      ```

1.  Si se le solicita, especifique una región para la implementación de Azure y Application Insights.

2.  Escriba "Y" para permitir que el proveedor de NuGet se instale, lo que iniciará los módulos "2018-03-01-hybrid" del perfil de la API que se van a instalar para permitir su implementación en Azure y Azure Stack.

3.  Una vez implementados los recursos, pruebe que los datos se generen para Azure Stack y Azure.

    ```powershell  
      .\TDAGenerator.exe
    ```

4.  Para ver los datos que se están procesando, vaya a las aplicaciones web implementadas en Azure o Azure Stack.

### <a name="azure-web-app"></a>Aplicación web de Azure
 
![solución de análisis de datos almacenados provisionalmente](media/azure-stack-solution-staged-data/image2.png)
 
### <a name="azure-stack-web-app"></a>Aplicación web de Azure Stack
 
![solución de análisis de datos almacenados provisionalmente para Azure Stack](media/azure-stack-solution-staged-data/image3.png)

## <a name="next-steps"></a>Pasos siguientes

  - Para obtener más información sobre las aplicaciones en la nube híbrida, consulte [Soluciones en la nube híbrida.](https://aka.ms/azsdevtutorials)

  - Use sus propios datos o modifique el código según este ejemplo en [GitHub](https://github.com/Azure-Samples/azure-intelligent-edge-patterns).
