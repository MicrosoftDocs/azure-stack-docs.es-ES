---
title: Supervisar las actualizaciones en Azure Stack con Powershell | Microsoft Docs
description: Aprenda a supervisar actualizaciones en Azure Stack con PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/23/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: a49f2f5c6b06f3362e4cb85553b765ea9fea1a72
ms.sourcegitcommit: b8260ef3e43f3703dd0df16fb752610ec8a86942
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/23/2019
ms.locfileid: "70010332"
---
# <a name="monitor-updates-in-azure-stack-using-powershell"></a>Supervisar actualizaciones en Azure Stack con PowerShell

*Se aplica a: Sistemas integrados de Azure Stack*

Puede usar los puntos de conexión administrativos de Azure Stack para supervisar y administrar las actualizaciones. Son accesibles con PowerShell. Para obtener instrucciones sobre cómo configurar PowerShell en Azure Stack, consulte [Instalación de PowerShell para Azure Stack](azure-stack-powershell-install.md).

Puede usar el siguiente cmdlet de PowerShell para administrar las actualizaciones:

| Cmdlet | DESCRIPCIÓN |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdate?view=azurestackps-1.7.2) | Obtiene la lista de actualizaciones disponibles. |
| [Get-AzsUpdateLocation](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateLocation?view=azurestackps-1.7.2)| Obtiene la lista de ubicaciones de actualización. |
| [Get-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateRun?view=azurestackps-1.7.2) | Obtiene la lista de ejecuciones de actualización.  |
| [Install-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Install-AzsUpdate?view=azurestackps-1.7.2) | Aplica una actualización específica en una ubicación de actualización. |
| [Resume-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Resume-AzsUpdateRun?view=azurestackps-1.7.2) | Reanuda una ejecución de actualización iniciada anteriormente en la que se produjo un error. |

## <a name="get-a-list-of-update-runs"></a>Obtención de una lista de ejecuciones de actualización

Para obtener la lista de comandos de ejecución de actualización:

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>Reanudar una operación de actualización con errores

Si se produce un error en la actualización, puede reanudar la ejecución de la actualización donde la dejó mediante el siguiente comando:

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="next-steps"></a>Pasos siguientes

-   [Administración de las actualizaciones en Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)