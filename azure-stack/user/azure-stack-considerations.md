---
title: Diferencias entre Azure Stack Hub y Azure al usar servicios y compilar aplicaciones
description: Descripción de las diferencias entre Azure y Azure Stack Hub al usar servicios y compilar aplicaciones.
author: sethmanheim
ms.topic: overview
ms.date: 11/20/2020
ms.author: sethm
ms.lastreviewed: 11/20/2020
ms.openlocfilehash: 8d8cd26bc53deef5b2e23955b349cb68a0eb51f0
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2020
ms.locfileid: "95516994"
---
# <a name="differences-between-azure-stack-hub-and-azure-when-using-services-and-building-apps"></a>Diferencias entre Azure Stack Hub y Azure al usar servicios y compilar aplicaciones

Antes de usar servicios o compilar aplicaciones para Azure Stack Hub, es importante que sepa que existen diferencias entre Azure Stack Hub y Azure. En este artículo se identifican las diferentes características y aspectos clave que se deben tener en cuenta al usar Azure Stack Hub como entorno de desarrollo en la nube híbrida.

## <a name="overview"></a>Información general

Azure Stack Hub es una plataforma en la nube híbrida que permite usar servicios de Azure desde el centro de datos de la empresa o de un proveedor de servicios. Puede compilar una aplicación en Azure Stack Hub y luego implementarla en Azure Stack Hub, en Azure o en la nube híbrida de Azure.

El operador de Azure Stack Hub le indica de qué servicios dispone y cómo obtener soporte técnico. Le proporcionará estos servicios a través de sus ofertas y planes personalizados.

El [contenido de la documentación técnica de Azure](/azure) da por supuesto que las aplicaciones se están desarrollando para un servicio de Azure y no para Azure Stack Hub. Al compilar e implementar aplicaciones en Azure Stack Hub, debe comprender algunas diferencias clave, como:

* Azure Stack Hub ofrece un subconjunto de los servicios y características que están disponibles en Azure.
* El proveedor del servicio o la empresa pueden elegir qué servicios desean ofrecer. Las opciones disponibles pueden incluir servicios o aplicaciones personalizados. Podrían ofrecer su propia documentación personalizada.
* Use los puntos de conexión específicos de Azure Stack Hub correctos (por ejemplo, las URL de la dirección del portal y el punto de conexión de Azure Resource Manager).
* Debe usar las versiones de PowerShell y de la API que son compatibles con Azure Stack Hub. El uso de versiones admitidas garantiza que las aplicaciones funcionarán en Azure Stack Hub y en Azure.

## <a name="cheat-sheet-high-level-differences"></a>Hoja de referencia rápida: diferencias de alto nivel

En la tabla siguiente se describen las diferencias de alto nivel entre Azure Stack Hub y Azure. Téngalas en cuenta al desarrollar para Azure Stack Hub o usar servicios de Azure Stack Hub:

| Área | Azure (global) | Azure Stack Hub |
| -------- | ------------- | ----------|
| ¿Quién lo administra? | Microsoft | Su organización o el proveedor de servicios.|
| ¿Quién es su contacto de soporte técnico? | Microsoft | En el caso de un sistema integrado, póngase en contacto con su operador de Azure Stack Hub (en su organización o proveedor de servicios) para obtener soporte técnico.<br><br>Para obtener soporte técnico para el Kit de desarrollo de Azure Stack (ASDK), visite los [foros de Microsoft](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack). Dado que el kit de desarrollo es un entorno de evaluación, Soporte técnico de Microsoft no ofrece soporte técnico oficial.
| Servicios disponibles | Consulte la lista de [productos de Azure](https://azure.microsoft.com/services/?b=17.04b). Los servicios disponibles varían según la región de Azure. | Azure Stack Hub admite un subconjunto de servicios de Azure. Los servicios reales variarán en función de lo que el proveedor de servicios o la organización decidan ofrecer.
| Punto de conexión de Azure Resource Manager* | `https://management.azure.com` | Para un sistema integrado de Azure Stack Hub, use el punto de conexión que proporciona su operador de Azure Stack Hub.<br><br>Para el kit de desarrollo, use: `https://management.local.azurestack.external`.
| URL del portal* | [https://portal.azure.com](https://portal.azure.com) | Para un sistema integrado de Azure Stack Hub, use la dirección URL que proporciona su operador de Azure Stack Hub.<br><br>Para el kit de desarrollo, use: `https://portal.local.azurestack.external`.
| Region | Puede seleccionar en qué región desea implementar. | En sistemas integrados de Azure Stack Hub, use la región que está disponible en el sistema.<br><br>Para el kit de desarrollo de Azure Stack, la región siempre será **local**.
| Grupos de recursos | Un grupo de recursos puede abarcar varias regiones. | Para los sistemas integrados y el kit de desarrollo, hay una sola región.
|Espacios de nombres, tipos de recursos y versiones de API compatibles | La versión más reciente (o versiones anteriores que no están en desuso). | Azure Stack Hub es compatible con versiones específicas. Consulte la sección [Requisitos de versión](#version-requirements) de este mismo artículo.
| | |

*Si es un operador de Azure Stack Hub, consulte [Uso del portal de administrador](../operator/azure-stack-manage-portals.md) y [Conceptos básicos de administración](../operator/azure-stack-manage-basics.md) para más información.

## <a name="helpful-tools-and-best-practices"></a>Herramientas útiles y prácticas recomendadas

Microsoft proporciona las herramientas e instrucciones necesarias que le ayudan a desarrollar para Azure Stack Hub.

| Recomendación | Referencias |
| -------- | ------------- |
| Instalar las herramientas adecuadas en la estación de trabajo de desarrollador. | - [Instalación de PowerShell](../operator/powershell-install-az-module.md)<br>- [Descarga de herramientas](../operator/azure-stack-powershell-download.md)<br>- [Configuración de PowerShell](azure-stack-powershell-configure-user.md)<br>- [Instalación de Visual Studio](azure-stack-install-visual-studio.md)
| Revise la información acerca de los siguientes aspectos:<br>- Consideraciones sobre la plantilla de Azure Resource Manager.<br>- Búsqueda de plantillas de inicio rápido.<br>- Uso de un módulo de directivas que le ayude a usar Azure para desarrollar para Azure Stack. | [Desarrollo para Azure Stack Hub](azure-stack-developer.md) |
| Revise y siga las prácticas recomendadas para plantillas. | [Plantillas de inicio rápido de Resource Manager](https://aka.ms/aa6yz42)
| | |

## <a name="version-requirements"></a>Requisitos de versión

Azure Stack Hub admite versiones específicas de Azure PowerShell y las API del servicio Azure. Use versiones compatibles para asegurarse de que la aplicación se puede implementar tanto en Azure Stack Hub como en Azure.

Para asegurarse de que está usando una versión correcta de Azure PowerShell, use [perfiles de la versión de API](azure-stack-version-profiles.md). Para determinar el perfil de la versión de API más reciente que puede usar, averigüe qué compilación de Azure Stack Hub está usando. Puede consultar esta información en el administrador de Azure Stack Hub.

> [!NOTE]
> Si usa el Kit de desarrollo de Azure Stack y tiene acceso administrativo, consulte la sección [Determinación de la versión actual](../operator/azure-stack-updates.md) para determinar la compilación de Azure Stack Hub.

Para otras API, ejecute el siguiente comando de PowerShell para generar los espacios de nombres, los tipos de recursos y las versiones de API compatibles con la suscripción de Azure Stack Hub (aún podría haber diferencias en un nivel de propiedad). Para que este comando funcione, debe haber [instalado](../operator/powershell-install-az-module.md) y [configurado](azure-stack-powershell-configure-user.md) PowerShell para un entorno de Azure Stack Hub. También hay que tener una suscripción a una oferta de Azure Stack Hub.

### <a name="az-modules"></a>[Modules de Az](#tab/az)

```powershell
Get-AzResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```
### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm)

```powershell
Get-AzureRMResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

---




Resultado de ejemplo (truncado): ![Resultado de ejemplo del comando Get-AzResourceProvider](media/azure-stack-considerations/image1.png)

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información detallada acerca de las diferencias en un nivel de servicio, consulte:

* [Consideraciones sobre máquinas virtuales en Azure Stack Hub](azure-stack-vm-considerations.md)
* [Consideraciones sobre el almacenamiento en Azure Stack Hub](azure-stack-acs-differences.md)
* [Consideraciones sobre las redes de Azure Stack Hub](azure-stack-network-differences.md)
* [Consideraciones sobre el proveedor de recursos SQL de Azure Stack Hub](../operator/azure-stack-sql-resource-provider.md)
