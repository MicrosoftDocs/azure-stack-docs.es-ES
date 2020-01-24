---
title: Supervisión de Azure Stack Hub mediante el punto de conexión con privilegios | Microsoft Docs
description: Aprenda a utilizar el punto de conexión con privilegios para supervisar el estado de las actualizaciones de los sistemas integrados de Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: 2ae4a48f363ba6f96e55bb3da6db0bad95d3ea7d
ms.sourcegitcommit: a1abc27a31f04b703666de02ab39ffdc79a632f6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/23/2020
ms.locfileid: "76535032"
---
# <a name="monitor-updates-in-azure-stack-hub-using-the-privileged-endpoint"></a>Supervisión de las actualizaciones de Azure Stack Hub mediante el punto de conexión con privilegios

Puede utilizar el [punto de conexión con privilegios](azure-stack-privileged-endpoint.md) para supervisar el progreso de la ejecución de las actualizaciones de Azure Stack Hub. Si el portal de Azure Stack Hub deja de estar disponible, también puede usar el punto de conexión con privilegios para reanudar la ejecución de una actualización con errores desde el último paso correcto. El método recomendado para administrar las actualizaciones de Azure Stack es utilizar el portal de Azure Stack Hub.

Los nuevos cmdlets de PowerShell para la administración de actualizaciones que se indican a continuación están incluidos en la actualización 1710 de los sistemas integrados de Azure Stack Hub.

| Cmdlet  | Descripción  |
|---------|---------|
| `Get-AzureStackUpdateStatus` | Devuelve el estado de la actualización actualmente en ejecución, completada o con errores. Proporciona el estado de alto nivel de la operación de actualización y un documento XML que describe el paso actual y el estado correspondiente. |
| `Resume-AzureStackUpdate` | Reanuda una actualización con errores en el punto en el que se produjo un error. En determinados escenarios, es posible que tenga que realizar una serie de pasos de mitigación antes de reanudar la actualización.         |
| | |

## <a name="verify-the-cmdlets-are-available"></a>Comprobación de la disponibilidad de los cmdlets
Como los cmdlets son nuevos en el paquete de actualización 1710 de Azure Stack Hub, es necesario que el proceso de la actualización 1710 avance hasta cierto punto para que la funcionalidad de supervisión esté disponible. Normalmente, los cmdlets están disponibles si el estado en el portal de administrador indica que la actualización 1710 ha llegado al paso **Restart Storage Hosts** (Reiniciar hosts de almacenamiento). En concreto, la actualización del cmdlet se produce durante el **Paso: Ejecución del paso 2.6: actualización de la lista de permitidos PrivilegedEndpoint**.

También puede determinar si los cmdlets están disponibles mediante programación al consultar la lista de comandos desde el punto de conexión con privilegios. Para realizar la consulta, ejecute los comandos siguientes desde el host de ciclo de vida de hardware o desde una estación de trabajo de acceso con privilegios. Además, asegúrese de que el punto de conexión con privilegios es un host de confianza. Para obtener más información, vea el paso 1 de [Acceso al punto de conexión con privilegios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

1. Cree una sesión de PowerShell en cualquiera de las máquinas virtuales de ERCS del entorno de Azure Stack Hub (*Prefijo*-ERCS01, *Prefijo*-ERCS02 o *Prefijo*-ERCS03). Reemplace *Prefix* por la cadena de prefijo de la VM que es específica de su entorno.

   ```powershell
   $cred = Get-Credential

   $pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
   ```
   Cuando le pidan las credenciales, use la cuenta &lt;*dominio de Azure Stack Hub*&gt;\cloudadmin o una cuenta que sea miembro del grupo CloudAdmins. Para la cuenta CloudAdmin, escriba la misma contraseña que proporcionó durante la instalación de la cuenta del administrador de dominio de AzureStackAdmin.

2. Obtenga la lista completa de comandos que están disponibles en el punto de conexión con privilegios.

   ```powershell
   $commands = Invoke-Command -Session $pepSession -ScriptBlock { Get-Command } 
   ```
3. Determine si se ha actualizado el punto de conexión con privilegios.

   ```powershell
   $updateManagementModuleName = "Microsoft.Azurestack.UpdateManagement"
    if (($commands | ? Source -eq $updateManagementModuleName)) {
   Write-Host "Privileged endpoint was updated to support update monitoring tools."
    } else {
   Write-Host "Privileged endpoint has not been updated yet. Please try again later."
    } 
   ```

4. Enumere los comandos específicos del módulo Microsoft.AzureStack.UpdateManagement.

   ```powershell
   $commands | ? Source -eq $updateManagementModuleName 
   ```
   Por ejemplo:
   ```powershell
   $commands | ? Source -eq $updateManagementModuleName
   
   CommandType     Name                                               Version    Source                                                  PSComputerName
    -----------     ----                                               -------    ------                                                  --------------
   Function        Get-AzureStackUpdateStatus                         0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   Function        Resume-AzureStackUpdate                            0.0        Microsoft.Azurestack.UpdateManagement                   Contoso-ercs01
   ``` 

## <a name="use-the-update-management-cmdlets"></a>Uso de los cmdlets de administración de actualizaciones

> [!NOTE]
> Ejecute los comandos siguientes desde el host de ciclo de vida de hardware o desde una estación de trabajo de acceso con privilegios. Además, asegúrese de que el punto de conexión con privilegios es un host de confianza. Para obtener más información, vea el paso 1 de [Acceso al punto de conexión con privilegios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint).

### <a name="connect-to-the-privileged-endpoint-and-assign-session-variable"></a>Conexión con el punto de conexión con privilegios y asignación de la variable de sesión

Ejecute los comandos siguientes para crear una sesión de PowerShell en cualquiera de las máquinas virtuales de ERCS del entorno de Azure Stack Hub (*Prefijo*-ERCS01, *Prefijo*-ERCS02 o *Prefijo*-ERCS03) y para asignar una variable de sesión.

```powershell
$cred = Get-Credential

$pepSession = New-PSSession -ComputerName <Prefix>-ercs01 -Credential $cred -ConfigurationName PrivilegedEndpoint 
```
 Cuando le pidan las credenciales, use la cuenta &lt;*dominio de Azure Stack Hub*&gt;\cloudadmin o una cuenta que sea miembro del grupo CloudAdmins. Para la cuenta CloudAdmin, escriba la misma contraseña que proporcionó durante la instalación de la cuenta del administrador de dominio de AzureStackAdmin.

### <a name="get-high-level-status-of-the-current-update-run"></a>Obtención del estado de alto nivel de la ejecución de la actualización actual

Para obtener un estado de alto nivel de la ejecución de la actualización actual, ejecute los comandos siguientes:

```powershell
$statusString = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus -StatusOnly }

$statusString.Value 
```

Los valores posibles son:

- En ejecución
- Completed
- Con error 
- Canceled

Puede ejecutar estos comandos varias veces para ver el estado más reciente. No tiene que volver a establecer una conexión para volver a comprobarlo.

### <a name="get-the-full-update-run-status-with-details"></a>Obtención del estado de la ejecución de la actualización completa con los detalles

Puede obtener un resumen de la ejecución de la actualización completa como una cadena XML. Puede escribir la cadena en un archivo para examinarla, o convertirla en un documento XML y usar PowerShell para analizarla. El comando siguiente analiza el código XML para obtener una lista jerárquica de los pasos que se están ejecutando:

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']")
```

En el ejemplo siguiente, el paso de nivel superior (Cloud Update) tiene un plan secundario para actualizar y reiniciar los hosts de almacenamiento. Muestra que el plan para reiniciar los hosts de almacenamiento está actualizando el servicio Blob Storage en uno de los hosts.

```powershell
[xml]$updateStatus = Invoke-Command -Session $pepSession -ScriptBlock { Get-AzureStackUpdateStatus }

$updateStatus.SelectNodes("//Step[@Status='InProgress']") 

    FullStepIndex : 2
    Index         : 2
    Name          : Cloud Update
    Description   : Perform cloud update.
    StartTimeUtc  : 2017-10-13T12:50:39.9020351Z
    Status        : InProgress
    Task          : Task
    
    FullStepIndex  : 2.9
    Index          : 9
    Name           : Restart Storage Hosts
    Description    : Restart Storage Hosts.
    EceErrorAction : Stop
    StartTimeUtc   : 2017-10-13T15:44:06.7431447Z
    Status         : InProgress
    Task           : Task
    
    FullStepIndex : 2.9.2
    Index         : 2
    Name          : PreUpdate ACS Blob Service
    Description   : Check function level, update deployment artifacts, configure Blob service settings
    StartTimeUtc  : 2017-10-13T15:44:26.0708525Z
    Status        : InProgress
    Task          : Task
```

### <a name="resume-a-failed-update-operation"></a>Reanudar una operación de actualización con errores

Si se produce un error en la actualización, puede reanudar la ejecución de la actualización desde el lugar en el que se interrumpió.

```powershell
Invoke-Command -Session $pepSession -ScriptBlock { Resume-AzureStackUpdate } 
```

## <a name="troubleshoot"></a>Solución de problemas

El punto de conexión con privilegios está disponible en todas las máquinas virtuales de ERCS del entorno de Azure Stack Hub. Dado que la conexión no se realiza a un punto de conexión de alta disponibilidad, puede experimentar algunas interrupciones, advertencias o mensajes de error. Estos mensajes podrían indicar que la sesión se ha desconectado o que se ha producido un error al comunicarse con el servicio ECE. Este comportamiento es normal. Puede volver a intentar la operación al cabo de unos minutos o crear una sesión de punto de conexión con privilegios en una de las otras VM ERCS.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a la administración de actualizaciones en Azure Stack Hub](azure-stack-updates.md)


