---
title: Modelos de conexión de sistemas integrados en Azure Stack | Microsoft Docs
description: Determinar los modelos de conexión y otras decisiones de planeamiento de una implementación en sistemas integrados de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: wfayed
ms.lastreviewed: 02/21/2019
ms.openlocfilehash: 8646e9d1936c865482368d176194c095c9dd0483
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909410"
---
# <a name="azure-stack-integrated-systems-connection-models"></a>Modelos de conexión de sistemas integrados en Azure Stack
Si está interesado en obtener un sistema integrado de Azure Stack, es preciso que comprenda [varias consideraciones de integración del centro de datos](azure-stack-datacenter-integration.md) para la implementación de Azure Stack y determinar cómo va a ajustarse el sistema a su centro de datos. Además, tendrá que decidir de qué forma va a integrar Azure Stack en su entorno de nube híbrida. En este artículo se proporciona información general acerca de estas decisiones importantes, lo que incluye decisiones acerca de los modelos de conexión de Azure, opciones del almacén de identidades y opciones del modelo de facturación.

Si decide comprar un sistema integrado, el proveedor de hardware del fabricante de equipo original (OEM) le guiará mediante gran parte del proceso de planeamiento con más detalle. El fabricante del hardware OEM también realiza la implementación real.

## <a name="choose-an-azure-stack-deployment-connection-model"></a>Elija un modelo de conexión de la implementación de Azure Stack
Puede implementar Azure Stack, ya sea conectado a Internet (y a Azure) o desconectado. Realice la implementación conectada a Azure para obtener el máximo provecho de Azure Stack, incluidos los escenarios híbridos entre Azure Stack y Azure. Esto define las opciones que están disponibles tanto para el almacén de identidades (Azure Active Directory o Servicios de federación de Active Directory) como para el modelo de facturación (facturación basada en pago por uso o facturación por capacidad), como se resume en el diagrama y la tabla siguientes:

![Escenarios de facturación e implementación de Azure Stack](media/azure-stack-connection-models/azure-stack-scenarios.png)
  
> [!IMPORTANT]
> Este es un punto clave en las decisiones. La elección de Servicios de federación de Active Directory (AD FS) o de Azure Active Directory (Azure AD) es una decisión que se toma una sola vez y se realiza en el momento de la implementación. Para cambiarlo posteriormente es preciso volver a implementar todo el sistema.  


|Opciones|Con conexión a Azure|Sin conexión a Azure|
|-----|:-----:|:-----:|
|Azure AD|![Compatible](media/azure-stack-connection-models/check.png)| |
|AD FS|![Compatible](media/azure-stack-connection-models/check.png)|![Compatible](media/azure-stack-connection-models/check.png)|
|Facturación por consumo|![Compatible](media/azure-stack-connection-models/check.png)| |
|Facturación por capacidad|![Compatible](media/azure-stack-connection-models/check.png)|![Compatible](media/azure-stack-connection-models/check.png)|
|Licencias| Contrato Enterprise o Proveedor de soluciones en la nube | Contrato Enterprise |
|Revisiones y actualizaciones|El paquete de actualización se puede descargar directamente de Internet a Azure Stack. |  Obligatorio<br><br>También requiere medios extraíbles<br> y un dispositivo conectado independiente. |
| Registro | Automatizado | Obligatorio<br><br>También requiere medios extraíbles<br> y un dispositivo conectado independiente. |

Una vez que haya decidido el modelo de conexión de Azure que va a usar para la implementación de Azure Stack, deben tomarse más decisiones que dependen de la conexión para el método de facturación y el almacén de identidades.

## <a name="next-steps"></a>Pasos siguientes

[Decisiones de implementación de Azure Stack con conexión a Azure](azure-stack-connected-deployment.md)

[Decisiones de implementación de Azure Stack sin conexión a Azure](azure-stack-disconnected-deployment.md)
