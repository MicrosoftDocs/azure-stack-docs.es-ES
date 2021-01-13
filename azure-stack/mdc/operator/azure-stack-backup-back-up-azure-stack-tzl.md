---
title: 'Copia de seguridad de Azure Stack: MDC | Microsoft Docs'
description: Aprenda a realizar una copia de seguridad a petición en Azure Stack para un Centro de datos modular (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: f64b656b6a0a0d5310b6d6e2fbb8ff26d5206ad1
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910898"
---
# <a name="back-up-azure-stack---modular-data-center-mdc"></a>Copia de seguridad de Azure Stack: Centro de datos modular (MDC)

*Se aplica a: Centro de datos modular, Azure Stack Hub resistente*

En este artículo se muestra cómo realizar una copia de seguridad a petición de Azure Stack.

## <a name="start-backup"></a>Inicio de la copia de seguridad

Las copias de seguridad se programan automáticamente. Iniciar una copia de seguridad a petición solo es necesario si recibe una alerta para desencadenar manualmente la copia de seguridad. Seleccione **Hacer copia de seguridad ahora** para realizar una copia de seguridad a petición. Una copia de seguridad a petición no modificará la hora de la siguiente copia de seguridad programada. Una vez completada la tarea, puede confirmar la configuración en la hoja **Información esencial**:

![Configuración de copia de seguridad](media/azure-stack-backup-back-up-azure-stack-tzl/on-demand-backup.png)

## <a name="start-azure-stack-backup"></a>Inicio de la copia de seguridad de Azure Stack

También puede ejecutar el cmdlet de PowerShell **Start-AzsBackup** en el equipo de administración de Azure Stack.

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Inicio de una nueva copia de seguridad sin seguimiento del progreso de trabajo

Use **Start-AzSBackup** para iniciar una nueva copia de seguridad inmediatamente sin seguimiento del progreso del trabajo.

```powershell
Start-AzsBackup -Force
```

## <a name="start-azure-stack-backup-with-job-progress-tracking"></a>Inicio de una copia de seguridad de Azure Stack con seguimiento del progreso de trabajo

Use **Start-AzSBackup** para iniciar una nueva copia de seguridad con el parámetro **-AsJob** y guárdelo como una variable para realizar el seguimiento del progreso del trabajo de copia de seguridad.

> [!NOTE]
> El trabajo de copia de seguridad aparece como completado correctamente en el portal unos 10 o 15 minutos antes de que finalice el trabajo.
>
> El estado real se observa mejor mediante el código siguiente.

El retraso inicial de 1 milisegundo se introduce porque el código es demasiado rápido para registrar el trabajo correctamente. El código genera un informe sin **PSBeginTime** y, a su vez, sin **Estado** del trabajo.

```powershell
$BackupJob = Start-AzsBackup -Force -AsJob
While (!$BackupJob.PSBeginTime) {
    Start-Sleep -Milliseconds 1
}
Write-Host "Start time: $($BackupJob.PSBeginTime)"
While ($BackupJob.State -eq "Running") {
    Write-Host "Job is currently: $($BackupJob.State) - Duration: $((New-TimeSpan -Start ($BackupJob.PSBeginTime) -End (Get-Date)).ToString().Split(".")[0])"
    Start-Sleep -Seconds 30
}

If ($BackupJob.State -eq "Completed") {
    Get-AzsBackup | Where-Object {$_.BackupId -eq $BackupJob.Output.BackupId}
    $Duration = $BackupJob.Output.TimeTakenToCreate
    $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Mohs>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
    If ($Duration -match $Pattern) {
        If (!$Matches.ContainsKey("Hours")) {
            $Hours = ""
        } 
        Else {
            $Hours = ($Matches.Hours).ToString + 'h '
        }
        $Minutes = ($Matches.Minutes)
        $Seconds = [math]::round(($Matches.Seconds))
        $Runtime = '{0}{1:00}m {2:00}s' -f $Hours, $Minutes, $Seconds
    }
    Write-Host "BackupJob: $($BackupJob.Output.BackupId) - Completed with Status: $($BackupJob.Output.Status) - It took: $($Runtime) to run" -ForegroundColor Green
}
ElseIf ($BackupJob.State -ne "Completed") {
    $BackupJob
    $BackupJob.Output
}
```

## <a name="confirm-backup-has-completed"></a>Confirmación de que la copia de seguridad se ha completado

### <a name="confirm-backup-has-completed-using-powershell"></a>Confirmación de que la copia de seguridad se ha completado con PowerShell

Use los siguientes comandos de PowerShell para asegurarse de que la copia de seguridad se ha completado correctamente:

```powershell
Get-AzsBackup
```

El resultado debe tener un aspecto similar a la siguiente salida:

```shell
BackupDataVersion : 1.0.1
BackupId          : <backup ID>
RoleStatus        : {NRP, SRP, CRP, KeyVaultInternalControlPlane...}
Status            : Succeeded
CreatedDateTime   : 7/6/2018 6:46:24 AM
TimeTakenToCreate : PT20M32.364138S
DeploymentID      : <deployment ID>
StampVersion      : 1.1807.0.41
OemVersion        : 
Id                : /subscriptions/<subscription ID>/resourceGroups/System.local/providers/Microsoft.Backup.Admin/backupLocations/local/backups/<backup ID>
Name              : local/<local name>
Type              : Microsoft.Backup.Admin/backupLocations/backups
Location          : local
Tags              : {}
```

### <a name="confirm-backup-has-completed-in-the-administrator-portal"></a>Confirmación de que la copia de seguridad se ha completado en el portal del administrador

Use el portal de administración de Azure Stack para comprobar que esa copia de seguridad se ha completado correctamente; para ello, siga estos pasos:

1. Abra el [portal de administración de Azure Stack](../../operator/azure-stack-manage-portals.md).

2. Seleccione **Todos los servicios** y, a continuación, en la categoría **Administración**, seleccione **Copia de seguridad de infraestructura**. Elija **Configuración** en la hoja **Copia de seguridad de infraestructura**.

3. Busque el **Nombre** y la **Fecha de finalización** de la copia de seguridad en la lista **Copias de seguridad disponibles**.

4. Compruebe que el **Estado** es **Correcto**.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre [Procedimientos recomendados para la copia de seguridad de la infraestructura](azure-stack-backup-best-practices-tzl.md).
