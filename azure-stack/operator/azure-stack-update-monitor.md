---
title: Supervisión de actualizaciones con PowerShell en Azure Stack Hub
description: Aprenda a supervisar actualizaciones con PowerShell en Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: bee3cbf3ab3faedbe54212c67dada1faa746071a
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545268"
---
# <a name="monitor-updates-with-powershell-in-azure-stack-hub"></a>Supervisión de actualizaciones con PowerShell en Azure Stack Hub

Puede usar los puntos de conexión administrativos de Azure Stack Hub para supervisar y administrar las actualizaciones. Son accesibles con PowerShell. Para obtener instrucciones sobre cómo configurar PowerShell en Azure Stack Hub, consulte [Instalación de PowerShell para Azure Stack Hub](powershell-install-az-module.md).

Puede usar los siguientes cmdlets de PowerShell para administrar las actualizaciones:

| Cmdlet | Descripción |
|------------------------------------------------------|-------------|
| [Get-AzsUpdate](/powershell/module/azs.update.admin/get-azsupdate?view=azurestackps-1.8.0) | Obtiene la lista de actualizaciones disponibles. |
| [Get-AzsUpdateLocation](/powershell/module/azs.update.admin/get-azsupdatelocation?view=azurestackps-1.8.0)| Obtiene la lista de ubicaciones de actualización. |
| [Get-AzsUpdateRun](/powershell/module/azs.update.admin/get-azsupdaterun?view=azurestackps-1.8.0) | Obtiene la lista de ejecuciones de actualización.  |
| [Install-AzsUpdate](/powershell/module/azs.update.admin/install-azsupdate?view=azurestackps-1.8.0) | Aplica una actualización específica en una ubicación de actualización. |
| [Resume-AzsUpdateRun](/powershell/module/azs.update.admin/resume-azsupdaterun?view=azurestackps-1.8.0) | Reanuda una ejecución de actualización iniciada anteriormente en la que se produjo un error. |

## <a name="get-a-list-of-update-runs"></a>Obtención de una lista de ejecuciones de actualización

Para obtener la lista de ejecuciones de actualización, ejecute el siguiente comando:

```powershell
Get-AzsUpdateRun -UpdateName Microsoft1.0.180302.1
```

## <a name="resume-a-failed-update-operation"></a>Reanudar una operación de actualización con errores

Si se produce un error en la actualización, puede reanudar la ejecución de la actualización donde la dejó mediante el siguiente comando:

```powershell
Get-AzsUpdateRun -Name 5173e9f4-3040-494f-b7a7-738a6331d55c -UpdateName Microsoft1.0.180305.1 | Resume-AzsUpdateRun
```

## <a name="troubleshoot"></a>Solución de problemas

Para más información sobre la solución de problemas de las actualizaciones, consulte [Solución de problemas de Azure Stack](azure-stack-troubleshooting.md).

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a la administración de actualizaciones en Azure Stack Hub](./azure-stack-updates.md)
