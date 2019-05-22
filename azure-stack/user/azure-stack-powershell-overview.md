---
title: PowerShell en Azure Stack | Microsoft Docs
description: PowerShell en Azure Stack incluye una serie de módulos y contextos.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: b28a90ceca59e5cd072018b217d81d101c1b0853
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490043"
---
# <a name="get-started-with-powershell-on-azure-stack"></a>Introducción a PowerShell en Azure Stack

PowerShell está diseñado para administrar y gestionar recursos de Azure desde la línea de comandos. Puede usar PowerShell cuando quiera crear herramientas automatizadas que usan el modelo de Azure Resource Manager. Un módulo de PowerShell se puede definir como un conjunto de funciones de PowerShell que se agrupan para administrar todos los aspectos de un área determinada. Para trabajar con Azure Stack, deberá jugar con diferentes conjuntos de cmdlets de PowerShell.

Este artículo le sirve como orientación sobre estos diferentes módulos de PowerShell usados en Azure Stack. Hay cuatro conjuntos de API con los que puede interactuar al usar PowerShell en Azure Stack.

| API | Referencia de PowerShell | Referencia de REST |
| --- | --- | --- |
| 1. Global Azure Resource Manager | [Módulos de Azure PowerShell](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [Explorador de API REST](https://docs.microsoft.com/rest/api/) |
| 2. Administrador de recursos de Azure Stack | [Administración de perfiles de la versión de API en Azure Stack](azure-stack-version-profiles.md) | [Administración de perfiles de la versión de API en Azure Stack](azure-stack-version-profiles.md) |
| 3. Puntos de conexión de administrador de Azure Stack | [Módulo de administración de Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [Explorador de API REST en Azure Stack](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| 4.  Punto de conexión con privilegios de Azure Stack | [Uso del punto de conexión con privilegios en Azure Stack](../operator/azure-stack-privileged-endpoint.md) | |

Cada interfaz establece contacto con los proveedores de recursos en Azure global o Azure Stack. Los proveedores de recursos permiten funcionalidades de Azure. Por ejemplo, el proveedor de recursos de proceso de Azure proporciona acceso mediante programación a la creación y administración de máquinas virtuales y sus recursos auxiliares.

Además de proporcionar funcionalidad, los proveedores de recursos también proporcionan controles para administrar y configurar el recurso. Puede acceder a los proveedores de recursos mediante programación con Azure Resource Manager y, a cambio, la interfaz proporciona una superficie para PowerShell, la CLI de Azure y sus propios clientes REST.

## <a name="where-to-find-azure-stack-powershell"></a>Dónde encontrar PowerShell de Azure Stack

En el siguiente diagrama de bloques se muestra la relación de cada conjunto de módulos de PowerShell. En la máquina, puede cargar los módulos de PowerShell y administrar Azure global y Azure Stack.

![PowerShell de Azure Stack](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Azure global

Azure PowerShell ofrece un conjunto de cmdlets que usan la versión actual del modelo de Azure Resource Manager para administrar los recursos de Azure. Azure PowerShell usa .NET Standard, y está disponible para Windows, macOS y Linux. Azure PowerShell también está disponible en Azure Cloud Shell. Para más información, consulte el artículo de [introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-resource-manager"></a>Administrador de recursos de Azure Stack

PowerShell de Azure Stack proporciona un conjunto de cmdlets que usan versiones anteriores de Azure Resource Manager compatibles con los proveedores de recursos de Azure Stack. Cada proveedor de recursos de Azure Stack usa una versión anterior del proveedor que se encuentra en Azure global. Para ayudar a coordinar la versión de cada proveedor compatible con Azure Stack, puede usar perfiles de API. PowerShell de Azure Stack usa PowerShell 5.1 y solo está disponible en Windows. Para más información, consulte [Administración de perfiles de versión de API en Azure Stack](azure-stack-version-profiles.md).

### <a name="azure-stack-administrator"></a>Administrador de Azure Stack

Azure Stack expone un conjunto de proveedores de recursos al operador de nube con el fin de instalar y mantener Azure Stack. En Azure global esta interacción se separa del usuario y se gestiona en segundo plano como parte de Azure. Azure Stack, sin embargo, ofrece a las empresas la posibilidad de admitir una nube privada. Con el fin de llevar a cabo estas tareas, el operador interactúa con las API de administración de Azure Stack. Para más información, consulte [Instalación de PowerShell de Azure Stack](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-privileged-endpoint"></a>Punto de conexión con privilegios de Azure Stack

En el caso de las actividades de operador en Azure Stack, como la prueba de la instalación y el acceso a los registros, los operadores pueden interactuar con el punto de conexión con privilegios (PEP). El PEP es una consola remota de PowerShell preconfigurada que proporciona a los operadores acceso suficiente para realizar tareas específicas. El punto de conexión usa PowerShell JEA (Just Enough Administration) para exponer un conjunto restringido de cmdlets. Para más información, consulte [Uso del punto de conexión con privilegios en Azure Stack](../operator/azure-stack-privileged-endpoint.md).

### <a name="azurestack-tools"></a>AzureStack-Tools

Además, Azure Stack proporciona scripts y cmdlets adicionales disponibles en un repositorio de GitHub, Azure Stack Tools. AzureStack-Tools hospeda módulos de PowerShell para administrar e implementar recursos en Azure Stack. Si planea establecer conectividad VPN, puede descargar estos módulos de PowerShell en el Kit de desarrollo de Azure Stack o en un cliente externo basado en Windows. Para más información, consulte [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools).

## <a name="working-with-powershell-on-azure-stack"></a>Uso de PowerShell en Azure Stack

PowerShell proporciona una manera de interactuar con Azure Resource Manager mediante programación. Puede trabajar con un símbolo del sistema interactivo o escribir scripts si va a automatizar tareas.

Si pasa mucho tiempo trabajando con PowerShell de Azure Stack, deberá instalar y reinstalar los módulos. Esta tarea puede ser difícil si trabaja con Azure global al mismo tiempo, dado que deberá desinstalar y reinstalar los módulos según su destino. Puede usar contenedores de Docker para aislar cada versión de PowerShell en la máquina local. En [Uso de Docker para ejecutar PowerShell](azure-stack-powershell-user-docker.md) se examina el uso de contenedores de Docker, para que puede cambiar entre los módulos de PowerShell establecidos.


## <a name="next-steps"></a>Pasos siguientes

- Lea sobre [Perfiles de API para PowerShell](azure-stack-version-profiles.md) en Azure Stack.
- [Instalación de PowerShell de Azure Stack](../operator/azure-stack-powershell-install.md)
- Lea sobre la creación de [plantillas de Azure Resource Manager](azure-stack-develop-templates.md) para mantener la coherencia en la nube.