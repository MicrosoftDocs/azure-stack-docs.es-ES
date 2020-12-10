---
title: Habilitación de copias de seguridad para Azure Stack con PowerShell | Microsoft Docs
description: Aprenda cómo habilitar el servicio Copia de seguridad de infraestructura con PowerShell para que Azure Stack se pueda restaurar si se produce un error.
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
ms.date: 12/16/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 12/16/2019
ms.openlocfilehash: f0476f3ee331c014436e9ee2f879afcafb3320d7
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941281"
---
# <a name="configure-backup-for-azure-stack-with-powershell"></a>Configuración de la copia de seguridad para Azure Stack con PowerShell

*Se aplica a: Centro de datos modular, Azure Stack Hub resistente*

Puede configurar el servicio Copia de seguridad de infraestructura para exportar las copias de seguridad de la infraestructura a una ubicación de almacenamiento externo mediante PowerShell. El equipo de soporte técnico puede usar las copias de seguridad de infraestructura para corregir los servicios degradados.

## <a name="prepare-powershell-environment"></a>Preparación del entorno de PowerShell

Para obtener instrucciones acerca de cómo configurar el entorno de PowerShell, consulte [Instalación de PowerShell para Azure Stack](../../operator/azure-stack-powershell-install.md). Para iniciar sesión en Azure Stack, consulte [Configuración del entorno de operador e inicio de sesión en Azure Stack](../../operator/azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Provisión del recurso compartido de copia de seguridad, las credenciales y la clave de cifrado para habilitar la copia de seguridad

En la misma sesión de PowerShell, modifique el siguiente script de PowerShell agregando las variables del entorno. Ejecute el script actualizado para proporcionar la clave de cifrado, las credenciales y la clave de cifrado al servicio Infrastructure Backup.

|Variable  |Descripción  |
|---------|---------|
|$username     | Escriba el **nombre de usuario** con el dominio y el nombre de usuario para la ubicación de la unidad compartida con acceso suficiente para leer y escribir archivos. Por ejemplo, Contoso\\backupshareuser.        |
|$password     | Escriba la **Contraseña** del usuario.        |
|$path     | Escriba la ruta de acceso a la **ubicación de almacenamiento de la copia de seguridad**. Debe utilizar una cadena de convención de nomenclatura universal (UNC) para la ruta de acceso de un recurso compartido de archivos hospedado en un dispositivo independiente. Una cadena UNC especifica la ubicación de recursos como archivos compartidos o dispositivos. Para garantizar la disponibilidad de los datos de copia de seguridad, el dispositivo debe estar en una ubicación independiente.        |
|$backupfrequencyinhours     | La frecuencia en horas determina con qué frecuencia se crean las copias de seguridad. El valor predeterminado es 12. Scheduler admite un mínimo de 4 y un máximo de 12.        |
|$backupretentionperiodindays     | El período de retención en días determina cuántos días de copias de seguridad se conservan en la ubicación externa. El valor predeterminado es 7. Scheduler admite un mínimo de 2 y un máximo de 14. Las copias de seguridad anteriores al período de retención se eliminan automáticamente de la ubicación externa.        |
|$encryptioncertpath     | Certificado proporcionado durante la implementación. No es necesario proporcionar uno nuevo al configurar la ubicación de almacenamiento externo. La ruta de acceso del certificado de cifrado especifica la ruta de acceso al archivo .CER con la clave pública que se usa para cifrar los datos.        |
|$isbackupschedulerenabled     | Habilita o deshabilita las copias de seguridad automáticas. Las copias de seguridad automáticas están habilitadas de forma predeterminada después de proporcionar el recurso compartido y las credenciales.        |

## <a name="configure-backup"></a>Configuración de la copia de seguridad

```powershell
# Example username
$username = "domain\backupadmin"

# Example share path
$sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

$password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

# Set the backup settings with the name, password, share, and CER certificate file.
Set-AzsBackupConfiguration -BackupShare path -Username $username -Password $password
```

## <a name="confirm-backup-settings"></a>Confirmación de la configuración de copia de seguridad

En la misma sesión de PowerShell, ejecute los comandos siguientes:

```powershell
Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
```

El resultado debe tener una apariencia similar a la del ejemplo siguiente:

```shell
Path : \\serverIP\AzsBackupStore\contoso.com\seattle
UserName : domain\backupadmin
```

## <a name="update-backup-settings"></a>Actualización de la configuración de copia de seguridad

En la misma sesión de PowerShell, puede actualizar los valores predeterminados para el período de retención y la frecuencia de las copias de seguridad:

```powershell
# Set the backup frequency and retention period values.
$frequencyInHours = 10
$retentionPeriodInDays = 5

Set-AzsBackupConfiguration -BackupFrequencyInHours $backupfrequencyInHours -BackupRetentionPeriodInDays $backupretentionPeriodInDays

Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
```

## <a name="update-encryption-certificate"></a>Actualización del certificado de cifrado

En la misma sesión de PowerShell, puede actualizar el certificado de cifrado que se usa para cifrar los datos de copia de seguridad. Solo las nuevas copias de seguridad usarán el nuevo certificado de cifrado. Todas las copias de seguridad existentes permanecerán cifradas con el certificado anterior. Asegúrese de conservar una copia del certificado anterior durante al menos un mes para asegurarse de que las copias de seguridad anteriores cifradas con el certificado antiguo se han purgado:

```powershell
#Set the new encryption certificate by providing local path to CER file.
Set-AzsBackupConfiguration -EncryptionCertPath "c:\temp\cert.cer"
```

## <a name="enable-or-disable-automatic-backups"></a>Habilitación o deshabilitación de las copias de seguridad automáticas

En la misma sesión de PowerShell, puede habilitar o deshabilitar las copias de seguridad automáticas. Las copias de seguridad automáticas están habilitadas de forma predeterminada.

```powershell
#Disable automatic backups.
Set-AzsBackupConfiguration - Isbackupschedulerenabled $false

#Enable automatic backups.
Set-AzsBackupConfiguration - Isbackupschedulerenabled $true
```

## <a name="next-steps"></a>Pasos siguientes

Para obtener información sobre cómo comprobar que la copia de seguridad se ejecutó, consulte [Confirmación de que la copia de seguridad se ha completado en el portal del administrador](../../operator/azure-stack-backup-back-up-azure-stack.md).
