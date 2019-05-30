---
title: Diferencias entre Azure Stack y Azure al usar servicios y compilar aplicaciones | Microsoft Docs
description: Diferencias entre Azure y Azure Stack al usar servicios y compilar aplicaciones
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 04/08/2019
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 9fcf27c8ebbde86e775b54eda593b25fcd03979c
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/24/2019
ms.locfileid: "66197287"
---
# <a name="differences-between-azure-stack-and-azure-when-using-services-and-building-apps"></a>Diferencias entre Azure Stack y Azure al usar servicios y compilar aplicaciones

Antes de usar servicios o compilar aplicaciones para Azure Stack, es importante que sepa que existen diferencias entre Azure Stack y Azure. En este artículo se identifican las diferentes características y aspectos clave que se deben tener en cuenta al usar Azure Stack como entorno de desarrollo en la nube híbrida.

## <a name="overview"></a>Información general

Azure Stack es una plataforma en la nube híbrida que permite usar servicios de Azure desde el centro de datos de la empresa o de un proveedor de servicios. Puede compilar una aplicación en Azure Stack y luego implementarla en Azure Stack, en Azure o en la nube híbrida de Azure.

El operador de Azure Stack le permitirá saber de qué servicios dispone y cómo obtener soporte técnico. Le proporcionará estos servicios a través de sus ofertas y planes personalizados.

El contenido técnico de Azure da por supuesto que las aplicaciones se están desarrollando para un servicio de Azure y no para Azure Stack. Al compilar e implementar aplicaciones en Azure Stack, debe comprender algunas diferencias clave, como:

* Azure Stack ofrece un subconjunto de los servicios y características que están disponibles en Azure.
* El proveedor del servicio o la empresa pueden elegir qué servicios desean ofrecer. Las opciones disponibles pueden incluir servicios o aplicaciones personalizados. Podrían ofrecer su propia documentación personalizada.
* Debe usar los puntos de conexión específicos de Azure Stack correctos (por ejemplo, las URL de la dirección del portal y el punto de conexión de Azure Resource Manager).
* Debe usar las versiones de PowerShell y de la API que son compatibles con Azure Stack. El uso de versiones admitidas garantiza que las aplicaciones funcionarán en Azure Stack y en Azure.

## <a name="cheat-sheet-high-level-differences"></a>Hoja de referencia rápida: diferencias de alto nivel

En la tabla siguiente se describen las diferencias de alto nivel entre Azure Stack y Azure. Téngalas en cuenta al desarrollar para Azure Stack o usar servicios de Azure Stack.

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

| Ámbito | Azure (global) | Azure Stack |
| -------- | ------------- | ----------|
| ¿Quién lo administra? | Microsoft | Su organización o el proveedor de servicios.|
| ¿Quién es su contacto de soporte técnico? | Microsoft | En el caso de un sistema integrado, póngase en contacto con su operador de Azure Stack (en su organización o proveedor de servicios) para obtener soporte técnico.<br><br>Para obtener soporte técnico para el Kit de desarrollo de Azure Stack, visite los [foros de Microsoft](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Dado que el kit de desarrollo es un entorno de evaluación, no se ofrece ningún soporte técnico oficial a través de los servicios de soporte técnico al cliente (CSS) de Microsoft.
| Servicios disponibles | Consulte la lista de [productos de Azure](https://azure.microsoft.com/services/?b=17.04b). Los servicios disponibles varían según la región de Azure. | Azure Stack admite un subconjunto de servicios de Azure. Los servicios reales variarán en función de lo que el proveedor de servicios o la organización decidan ofrecer.
| Punto de conexión de Azure Resource Manager* | https://management.azure.com | Para un sistema integrado de Azure Stack, use el punto de conexión que proporciona su operador de Azure Stack.<br><br>Para el kit de desarrollo, use: https://management.local.azurestack.external.
| URL del portal* | [https://portal.azure.com](https://portal.azure.com) | Para un sistema integrado de Azure Stack, use la dirección URL que proporciona su operador de Azure Stack.<br><br>Para el kit de desarrollo, use: https://portal.local.azurestack.external.
| Region | Puede seleccionar en qué región desea implementar. | En sistemas integrados de Azure Stack, use la región que está disponible en el sistema.<br><br>Para el kit de desarrollo, la región siempre será **local**.
| Grupos de recursos | Un grupo de recursos puede abarcar varias regiones. | Para los sistemas integrados y el kit de desarrollo, hay una sola región.
|Espacios de nombres, tipos de recursos y versiones de API compatibles | La versión más reciente (o versiones anteriores que no están en desuso). | Azure Stack es compatible con versiones específicas. Consulte la sección [Requisitos de versión](#version-requirements) de este mismo artículo.
| | |

* Si es un operador de Azure Stack, consulte [Using the administrator portal](../operator/azure-stack-manage-portals.md) (Usar el portal de administrador) y [Administration basics](../operator/azure-stack-manage-basics.md) (Conceptos básicos de administración) para más información.

## <a name="helpful-tools-and-best-practices"></a>Herramientas útiles y prácticas recomendadas

Microsoft proporciona las herramientas e instrucciones necesarias para desarrollar para Azure Stack.

| Recomendación | Referencias |
| -------- | ------------- |
| Instalar las herramientas adecuadas en la estación de trabajo de desarrollador. | - [Instalación de PowerShell](../operator/azure-stack-powershell-install.md)<br>- [Descarga de herramientas](../operator/azure-stack-powershell-download.md)<br>- [Configuración de PowerShell](azure-stack-powershell-configure-user.md)<br>- [Instalación de Visual Studio](azure-stack-install-visual-studio.md) 
| Revise la información acerca de los siguientes aspectos:<br>- Consideraciones de la plantilla de Azure Resource Manager<br>- Cómo buscar plantillas de inicio rápido<br>- Usar un módulo de directivas que le ayude a usar Azure para desarrollar para Azure Stack | [Desarrollo para Azure Stack](azure-stack-developer.md) | 
| Revise y siga las prácticas recomendadas para plantillas. | [Plantillas de inicio rápido de Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Requisitos de versión

Azure Stack es compatible con versiones específicas de Azure PowerShell y de API del servicio Azure. Use versiones compatibles para asegurarse de que la aplicación se puede implementar tanto en Azure Stack como en Azure.

Para asegurarse de que está usando una versión correcta de Azure PowerShell, use [perfiles de la versión de API](azure-stack-version-profiles.md). Para determinar el perfil de la versión de API más reciente que puede usar, averigüe qué compilación de Azure Stack está usando. Puede consultar esta información en el administrador de Azure Stack.

> [!NOTE]
> Si usa el Kit de desarrollo de Azure Stack y tiene acceso administrativo, consulte la sección [Determinación de la versión actual](../operator/azure-stack-updates.md#determine-the-current-version) para determinar la compilación de Azure Stack.

Para otras API, ejecute el siguiente comando de PowerShell para generar los espacios de nombres, los tipos de recursos y las versiones de API compatibles con la suscripción de Azure Stack (aún podría haber diferencias en un nivel de propiedad). Para que este comando funcione, es preciso debe haber [instalado](../operator/azure-stack-powershell-install.md) y [configurado](azure-stack-powershell-configure-user.md) PowerShell para un entorno de Azure Stack. También hay que tener una suscripción a una oferta de Azure Stack.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Resultado de ejemplo (truncado): ![resultado de ejemplo del comando Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información detallada acerca de las diferencias en un nivel de servicio, consulte:

* [Consideraciones sobre máquinas virtuales en Azure Stack](azure-stack-vm-considerations.md)
* [Considerations for Storage in Azure Stack](azure-stack-acs-differences.md) (Consideraciones para almacenamiento en Azure Stack)
* [Considerations for Azure Stack networking](azure-stack-network-differences.md) (Consideraciones para los servicios de red de Azure Stack)
