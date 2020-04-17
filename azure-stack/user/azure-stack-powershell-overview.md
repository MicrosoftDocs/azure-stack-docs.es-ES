---
title: PowerShell en Azure Stack Hub
description: PowerShell en Azure Stack Hub incluye una serie de módulos y contextos.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: cf9d17bda49c1fe5d401481d79b61b18442ee7ef
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "77703815"
---
# <a name="get-started-with-powershell-in-azure-stack-hub"></a>Introducción a PowerShell en Azure Stack Hub

PowerShell está diseñado para administrar y gestionar recursos de Azure desde la línea de comandos. Puede usar PowerShell cuando quiera crear herramientas automatizadas que usan el modelo de Azure Resource Manager. Un módulo de PowerShell se puede definir como un conjunto de funciones de PowerShell que se agrupan para administrar todos los aspectos de un área determinada. Para trabajar con Azure Stack Hub, es necesario jugar con distintos conjuntos de cmdlets de PowerShell.

Este artículo sirve como orientación sobre los diferentes módulos de PowerShell que se usan en Azure Stack Hub. Al usar PowerShell en Azure Stack Hub, puede interactuar con cualquiera de los cuatro conjuntos de API, como se muestra en la tabla siguiente:

| API | Referencia de PowerShell | Referencia de REST |
| --- | --- | --- |
| Global Azure Resource Manager | [Módulos de Azure PowerShell](https://github.com/Azure/azure-powershell/blob/master/documentation/azure-powershell-modules.md) | [Explorador de API REST](https://docs.microsoft.com/rest/api/) |
| Punto de conexión de Resource Manager de Azure Stack Hub | [Administración de perfiles de la versión de API en Azure Stack Hub](azure-stack-version-profiles.md) | [Administración de perfiles de la versión de API en Azure Stack Hub](azure-stack-version-profiles.md) |
| Puntos de conexión de administrador de Azure Stack Hub | [Módulo de administración de Azure Stack Hub](https://docs.microsoft.com/powershell/azure/azure-stack/overview) | [Explorador de API REST en Azure Stack Hub](https://docs.microsoft.com/rest/api/?term=Azure%20Azure%20Stack%20Admin) |
| Punto de conexión con privilegios de Azure Stack Hub | [Uso del punto de conexión con privilegios en Azure Stack Hub](../operator/azure-stack-privileged-endpoint.md) | |

Cada interfaz establece contacto con los proveedores de recursos en Azure global o Azure Stack Hub. Los proveedores de recursos permiten funcionalidades de Azure. Por ejemplo, el proveedor de recursos de proceso de Azure proporciona acceso mediante programación a la creación y administración de máquinas virtuales y sus recursos auxiliares.

Los proveedores de recursos proporcionan tanto la funcionalidad como los controles necesarios para administrar y configurar el recurso. Puede usar la programación para acceder a los proveedores de recursos mediante Azure Resource Manager. A su vez, la interfaz proporciona una superficie para PowerShell, la CLI de Azure y sus propios clientes de REST.

## <a name="where-to-find-azure-stack-hub-powershell"></a>Dónde encontrar PowerShell de Azure Stack Hub

En el siguiente diagrama de bloques se muestra la relación entre los conjuntos de módulos de PowerShell. En la máquina, puede cargar los módulos de PowerShell y administrar Azure global y Azure Stack Hub.

![PowerShell de Azure Stack Hub](media/azure-stack-powershell-overview/Azure-Stack-PowerShell.png)

### <a name="global-azure"></a>Azure global

Azure PowerShell contiene un conjunto de cmdlets que usan la versión actual de Azure Resource Manager para trabajar con los recursos de Azure. Azure PowerShell usa la versión de .NET Standard, lo que significa que puede usar las versiones de PowerShell con Windows, macOS y Linux. Azure PowerShell también está disponible en Azure Cloud Shell. Para más información, consulte el artículo de [introducción a Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

### <a name="azure-stack-hub-resource-manager"></a>Punto de conexión de Resource Manager de Azure Stack Hub

PowerShell de Azure Stack Hub proporciona un conjunto de cmdlets que usan versiones anteriores de Azure Resource Manager. Estos cmdlets son compatibles con los proveedores de recursos de Azure Stack Hub. Cada proveedor de recursos de Azure Stack Hub usa una versión anterior del proveedor que se encuentra en Azure global. Para ayudar a coordinar la versión de cada proveedor compatible con Azure Stack Hub, puede usar perfiles de API. PowerShell de Azure Stack Hub usa PowerShell 5.1 y solo está disponible en Windows. Para más información, consulte [Administración de perfiles de versión de API en Azure Stack Hub](azure-stack-version-profiles.md).

### <a name="azure-stack-hub-administrator"></a>Administrador de Azure Stack Hub

Azure Stack Hub expone un conjunto de proveedores de recursos al operador en la nube para que este pueda realizar la instalación y el mantenimiento de Azure Stack Hub. En Azure global, esta interacción se separa del usuario y se gestiona en segundo plano como parte de Azure. Sin embargo, con Azure Stack Hub, las empresas pueden admitir una nube privada. Para hacer estas tareas, el operador interactúa con las API de administración de Azure Stack Hub. Para más información, consulte [Instalación de PowerShell para Azure Stack Hub](../operator/azure-stack-powershell-install.md).

### <a name="azure-stack-hub-privileged-endpoint"></a>Punto de conexión con privilegios de Azure Stack Hub

En el caso de las actividades de operador en Azure Stack Hub, como la prueba de la instalación y el acceso a los registros, los operadores pueden interactuar con el punto de conexión con privilegios (PEP). El PEP es una consola remota de PowerShell preconfigurada que proporciona a los operadores el acceso suficiente para realizar tareas específicas. El punto de conexión usa PowerShell Just Enough Administration (JEA ) para exponer un conjunto restringido de cmdlets. Para más información, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](../operator/azure-stack-privileged-endpoint.md).

### <a name="azure-stack-hub-tools"></a>Herramientas de Azure Stack Hub

Azure Stack Hub pone scripts y cmdlets adicionales a disposición de los usuarios en un repositorio de GitHub: *AzureStack-Tools*. AzureStack-Tools hospeda módulos de PowerShell para administrar e implementar recursos en Azure Stack Hub. Si planea establecer conectividad VPN, puede descargar estos módulos de PowerShell en el Kit de desarrollo de Azure Stack o en un cliente externo basado en Windows. Para obtener más información, vaya a la página [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools).

## <a name="work-with-powershell-in-azure-stack-hub"></a>Trabajo con PowerShell en Azure Stack Hub

PowerShell proporciona una manera de interactuar con Azure Resource Manager mediante programación. Puede trabajar con un símbolo del sistema interactivo o, si va a automatizar tareas, escribir scripts.

Si pasa mucho tiempo trabajando con PowerShell de Azure Stack Hub, deberá instalar y reinstalar los módulos. Si trabaja con Azure global al mismo tiempo, esta rutina puede resultar difícil, ya que tendrá que desinstalar y reinstalar los módulos en función del destino. 

Puede usar contenedores de Docker para aislar cada versión de PowerShell en la máquina local. Para usar contenedores de Docker y poder cambiar entre conjuntos de módulos de PowerShell, consulte [Uso de Docker para ejecutar PowerShell](azure-stack-powershell-user-docker.md).


## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre los [Perfiles de API para PowerShell](azure-stack-version-profiles.md) en Azure Stack Hub.
- Instalación de [PowerShell de Azure Stack Hub](../operator/azure-stack-powershell-install.md).
- Lea sobre la creación de [plantillas de Azure Resource Manager](azure-stack-develop-templates.md) para mantener la coherencia en la nube.
