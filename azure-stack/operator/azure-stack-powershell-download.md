---
title: Descarga de herramientas de Azure Stack desde GitHub | Microsoft Docs
description: Obtenga información sobre cómo descargar las herramientas necesarias para trabajar con Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 09/18/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 09/18/2019
ms.openlocfilehash: 89a31f6d7894ed9af710263298fef03604d1351b
ms.sourcegitcommit: acebda8a42ac8ecdeba490fc1738e9041479dab0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/23/2019
ms.locfileid: "72813975"
---
# <a name="download-azure-stack-tools-from-github"></a>Descarga de herramientas de Azure Stack desde GitHub

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

**AzureStack-Tools** es un [repositorio de GitHub](https://github.com/Azure/AzureStack-Tools) que hospeda módulos de PowerShell para administrar e implementar recursos en Azure Stack. Si planea establecer conectividad VPN, puede descargar estos módulos de PowerShell en el Kit de desarrollo de Azure Stack (ASDK) o en un cliente externo basado en Windows. Para obtener estas herramientas, clone el repositorio de GitHub o descargue la carpeta **AzureStack-Tools** mediante la ejecución del script siguiente:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```

## <a name="functionality-provided-by-the-modules"></a>Funcionalidad proporcionada por los módulos

El repositorio **AzureStack-Tools** contiene los módulos de PowerShell que admiten las funcionalidades siguientes para Azure Stack:  

| Funcionalidad | DESCRIPCIÓN | ¿Quién puede usar este módulo? |
| --- | --- | --- |
| [Funcionalidades en la nube](../user/azure-stack-validate-templates.md) | Utilice este módulo para obtener las funcionalidades en la nube de una nube. Por ejemplo, puede obtener funcionalidades en la nube como la versión de API y los recursos de Azure Resource Manager. También puede obtener las extensiones de VM para Azure Stack y Azure Cloud. | Operadores de nube y usuarios |
| [Directiva de Resource Manager para Azure Stack](../user/azure-stack-policy-module.md) | Utilice este módulo para configurar una suscripción de Azure o un grupo de recursos de Azure con la misma disponibilidad de servicios y control de versiones que Azure Stack. | Operadores de nube y usuarios |
| [Registro con Azure](azure-stack-registration.md ) | Utilice este módulo para registrar la instancia del ASDK en Azure. Después del registro, puede descargar los elementos de Azure Marketplace y usarlos en Azure Stack. | Operadores de nube |
| [Implementación de Azure Stack](../asdk/asdk-install.md) | Use este módulo para preparar el equipo host de Azure Stack para implementar y volver a implementar con la imagen de disco duro virtual (VHD) de Azure Stack. | Operadores de nube|
| [Conexión a Azure Stack](azure-stack-powershell-install.md) | Utilice este módulo para configurar la conectividad VPN a Azure Stack. | Operadores de nube y usuarios |
| [Validador de plantilla](../user/azure-stack-validate-templates.md) | Utilice este módulo para comprobar si una plantilla nueva o existente puede implementarse en Azure Stack. | Operadores de nube y usuarios|

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a PowerShell en Azure Stack](../user/azure-stack-powershell-overview.md).
- [Configure el entorno de PowerShell del usuario de Azure Stack](../user/azure-stack-powershell-configure-user.md).
- [Conexión al Kit de desarrollo de Azure Stack a través de una VPN](../asdk/asdk-connect.md).
