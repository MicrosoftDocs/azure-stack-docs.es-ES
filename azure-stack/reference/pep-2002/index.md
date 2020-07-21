---
title: Referencia del punto de conexión con privilegios elevados de Azure Stack Hub
description: Referencia del punto de conexión con privilegios elevados de Azure Stack de PowerShell
author: mattbriggs
ms.topic: reference
ms.date: 04/27/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/27/2020
ms.openlocfilehash: 76eb340da04e9254bcf8d8a626822c65362f44d2
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2020
ms.locfileid: "86486043"
---
# <a name="azure-stack-hub-privileged-endpoint-reference"></a>Referencia del punto de conexión con privilegios elevados de Azure Stack Hub

El punto de conexión con privilegios elevados de Azure Stack de PowerShell (PEP) es una consola remota de PowerShell preconfigurada que proporciona las funcionalidades necesarias para ayudarle a realizar las tareas necesarias. El punto de conexión usa PowerShell JEA (Just Enough Administration) para exponer únicamente un conjunto restringido de cmdlets.

## <a name="privilege-endpoint-cmdlets"></a>Cmdlets de punto de conexión con privilegios elevados

| Cmdlet | Descripción |
| --- | --- |
| [Close-PrivilegedEndpoint](Close-PrivilegedEndpoint.md) | Sin descripción. |
| [Get-ActionStatus](Get-ActionStatus.md) | Obtiene el estado de la acción más reciente de la operación con el nombre de función especificado. |
| [Get-AzureStackLog](Get-AzureStackLog.md) | Obtiene registros de varios roles de AzureStack con el tiempo de expiración. |
| [Get-AzureStackStampInformation](Get-AzureStackStampInformation.md) | Obtiene la información de la unidad de escalado. |
| [Get-AzureStackSupportConfiguration](Get-AzureStackSupportConfiguration.md) | Obtiene las opciones de configuración del servicio de soporte técnico. |
| [Get-CloudAdminPasswordRecoveryToken](Get-CloudAdminPasswordRecoveryToken.md) | Sin descripción. |
| [Get-CloudAdminUserList](Get-CloudAdminUserList.md) | Sin descripción. |
| [Get-ClusterLog](Get-ClusterLog.md) | Sin descripción. |
| [Get-GraphApplication](Get-GraphApplication.md) | Get-GraphApplication es una función contenedora para obtener la información de la aplicación de Graph para el nombre o identificador de la aplicación especificados. |
| [Get-StorageJob](Get-StorageJob.md) | Sin descripción. |
| [Get-SupportSessionInfo](Get-SupportSessionInfo.md) | Sin descripción. |
| [Get-SupportSessionToken](Get-SupportSessionToken.md) | Sin descripción. |
| [Get-SyslogClient](Get-SyslogClient.md) | Obtiene la configuración del cliente de syslog. |
| [Get-SyslogServer](Get-SyslogServer.md) | Obtiene el punto de conexión del servidor de syslog. |
| [Get-ThirdPartyNotices](Get-ThirdPartyNotices.md) | Sin descripción. |
| [Get-TLSPolicy](Get-TLSPolicy.md) | Sin descripción. |
| [Get-VirtualDisk](Get-VirtualDisk.md) | Sin descripción. |
| [Invoke-AzureStackOnDemandLog](Invoke-AzureStackOnDemandLog.md) | Genera registros a petición de los roles de AzureStack cuando corresponda. |
| [New-AzureBridgeServicePrincipal](New-AzureBridgeServicePrincipal.md) | Crea una entidad de servicio en Azure Active Directory. |
| [New-AzureStackActivation](New-AzureStackActivation.md) | Activa Azure Stack. |
| [New-CloudAdminUser](New-CloudAdminUser.md) | Sin descripción. |
| [New-GraphApplication](New-GraphApplication.md) | New-GraphApplication es una función contenedora para llamar a los cmdlets de Graph de ADFS en AD FS. |
| [New-RegistrationToken](New-RegistrationToken.md) | Crea un nuevo token de registro. |
| [Register-CustomAdfs](Register-CustomAdfs.md) | Script para registrar Servicios de federación de Active Directory (ADFS) como proveedor de notificaciones con Azure Stack AD FS. |
| [Register-CustomDnsServer](Register-CustomDnsServer.md) | Script para registrar servidores DNS personalizados en Azure Stack DNS. |
| [Register-DirectoryService](Register-DirectoryService.md) | Script para registrar Active Directory del cliente en el servicio Graph. |
| [Remove-AzureStackActivation](Remove-AzureStackActivation.md) | Sin descripción. |
| [Remove-CloudAdminUser](Remove-CloudAdminUser.md) | Sin descripción. |
| [Remove-GraphApplication](Remove-GraphApplication.md) | Remove-GraphApplication es una función contenedora para llamar a los cmdlets de Graph de ADFS en AD FS. |
| [Repair-VirtualDisk](Repair-VirtualDisk.md) | Sin descripción. |
| [Reset-DatacenterIntegrationConfiguration](Reset-DatacenterIntegrationConfiguration.md) | Script para restablecer los cambios en la integración del centro de datos. |
| [Send-AzureStackDiagnosticLog](Send-AzureStackDiagnosticLog.md) | Envía registros de diagnóstico de Azure Stack a Microsoft. |
| [Set-CloudAdminUserPassword](Set-CloudAdminUserPassword.md) | Sin descripción. |
| [Set-GraphApplication](Set-GraphApplication.md) | Set-GraphApplication es una función contenedora para llamar a los cmdlets de Graph de ADFS en AD FS. |
| [Set-ServiceAdminOwner](Set-ServiceAdminOwner.md) | Script para actualizar el administrador de servicios. |
| [Set-SyslogClient](Set-SyslogClient.md) | Importa y aplica el certificado de punto de conexión del cliente de syslog. |
| [Set-SyslogServer](Set-SyslogServer.md) | Establece el punto de conexión del servidor de syslog. |
| [Set-Telemetry](Set-Telemetry.md) | Habilita o deshabilita la transferencia de datos de telemetría a Microsoft. |
| [Set-TLSPolicy](Set-TLSPolicy.md) | Sin descripción. |
| [Start-AzureStack](Start-AzureStack.md) | Inicia todos los servicios de Azure Stack. |
| [Start-SecretRotation](Start-SecretRotation.md) | Desencadena la rotación de secretos en una unidad de escalado. |
| [Stop-AzureStack](Stop-AzureStack.md) | Detiene todos los servicios de Azure Stack. |
| [Test-AzureStack](Test-AzureStack.md) | Valida el estado de Azure Stack. |
| [Unlock-SupportSession](Unlock-SupportSession.md) | Sin descripción. |

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el punto de conexión con privilegios elevados en Azure Stack Hub, consulte [Uso del punto de conexión con privilegios en Azure Stack](../../operator/azure-stack-privileged-endpoint.md).
