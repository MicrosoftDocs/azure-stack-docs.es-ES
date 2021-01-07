---
title: Copia de seguridad de Azure Stack Hub
description: Aprenda a realizar una copia de seguridad a petición en Azure Stack Hub.
author: PatAltimore
ms.topic: article
ms.date: 02/12/2019
ms.author: patricka
ms.reviewer: hectorl
ms.lastreviewed: 09/05/2019
ms.openlocfilehash: 5df894d32d335b488ad51b09bb38c3011f754e15
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871844"
---
# <a name="back-up-azure-stack-hub"></a>Copia de seguridad de Azure Stack Hub

En este artículo, se explica cómo realizar una copia de seguridad a petición en Azure Stack Hub. Si necesita instrucciones para configurar el entorno de PowerShell, consulte esta artículo sobre la [instalación de PowerShell para Azure Stack Hub](powershell-install-az-module.md). Para iniciar sesión en Azure Stack Hub, consulte [Uso del portal del administrador de Azure Stack Hub](azure-stack-manage-portals.md).

## <a name="start-azure-stack-hub-backup"></a>Inicio de la copia de seguridad de Azure Stack Hub

### <a name="start-a-new-backup-without-job-progress-tracking"></a>Inicio de una nueva copia de seguridad sin seguimiento del progreso de trabajo
Use Start-AzSBackup para iniciar una nueva copia de seguridad inmediatamente sin seguir el progreso de trabajo.

```powershell
   Start-AzsBackup -Force
```

### <a name="start-azure-stack-hub-backup-with-job-progress-tracking"></a>Inicio de una copia de seguridad de Azure Stack Hub con seguimiento del progreso del trabajo
Use Start-AzSBackup para iniciar una nueva copia de seguridad con el parámetro **-AsJob** y guárdelo como una variable para realizar el seguimiento del progreso del trabajo de copia de seguridad.

> [!NOTE]
> El trabajo de copia de seguridad aparece como completado correctamente en el portal unos 10 o 15 minutos antes de que finalice el trabajo.
>
> El estado real se observa mejor mediante el código siguiente.

> [!IMPORTANT]
> El retraso inicial de un milisegundo se presenta porque el código es demasiado rápido para registrar correctamente el trabajo y vuelve sin ningún **PSBeginTime** y, a su vez, sin **Estado** del trabajo.

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
        $Pattern = '^P?T?((?<Years>\d+)Y)?((?<Months>\d+)M)?((?<Weeks>\d+)W)?((?<Days>\d+)D)?(T((?<Hours>\d+)H)?((?<Minutes>\d+)M)?((?<Seconds>\d*(\.)?\d*)S)?)$'
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

```powershell
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
Use el portal del administrador de Azure Stack Hub para verificar si esa copia de seguridad se ha completado correctamente; para ello, siga estos pasos:

1. Abra el [portal del administrador de Azure Stack Hub](azure-stack-manage-portals.md).
2. Seleccione **Todos los servicios** y, luego, en la categoría **ADMINISTRACIÓN**, seleccione > **Copia de seguridad de infraestructura**. Elija **Configuración** en la hoja **Copia de seguridad de infraestructura**.
3. Busque el **Nombre** y la **Fecha de finalización** de la copia de seguridad en la lista **Copias de seguridad disponibles**.
4. Compruebe que el **Estado** es **Correcto**.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre el flujo de trabajo para [recuperarse de un evento de pérdida de datos](azure-stack-backup-recover-data.md).
