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
ms.openlocfilehash: f14b4180e567df13e576b520cdac01a676c101ea
ms.sourcegitcommit: b96a0b151b9c0d3eea59e7c2d39119a913782624
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2020
ms.locfileid: "75717950"
---
# <a name="monitor-updates-in-azure-stack-hub-using-powershell"></a>Supervisar actualizaciones en Azure Stack Hub con PowerShell

*Se aplica a: Sistemas integrados de Azure Stack Hub*

Puede usar los puntos de conexión administrativos de Azure Stack Hub para supervisar y administrar las actualizaciones. Son accesibles con PowerShell. Para obtener instrucciones sobre cómo configurar PowerShell en Azure Stack Hub, consulte [Instalación de PowerShell para Azure Stack Hub](azure-stack-powershell-install.md).

Puede usar el siguiente cmdlet de PowerShell para administrar las actualizaciones:

| Cmdlet | Descripción |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdate?view=azurestackps-1.8.0) | Obtiene la lista de actualizaciones disponibles. |
| [Get-AzsUpdateLocation](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateLocation?view=azurestackps-1.8.0)| Obtiene la lista de ubicaciones de actualización. |
| [Get-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Get-AzsUpdateRun?view=azurestackps-1.8.0) | Obtiene la lista de ejecuciones de actualización.  |
| [Install-AzsUpdate](https://docs.microsoft.com/powershell/module/azs.update.admin/Install-AzsUpdate?view=azurestackps-1.8.0) | Aplica una actualización específica en una ubicación de actualización. |
| [Resume-AzsUpdateRun](https://docs.microsoft.com/powershell/module/azs.update.admin/Resume-AzsUpdateRun?view=azurestackps-1.8.0) | Reanuda una ejecución de actualización iniciada anteriormente en la que se produjo un error. |

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
