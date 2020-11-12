---
title: Habilitación de la copia de seguridad para Azure Stack Hub con PowerShell
description: Aprenda cómo habilitar el servicio Copia de seguridad de infraestructura con PowerShell para que Azure Stack Hub se pueda restaurar si se produce un error.
author: justinha
ms.topic: article
ms.date: 04/25/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 03/14/2019
ms.openlocfilehash: bc6ce6c36e3aca05015e6acbef3de0d5fd841eb6
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94543925"
---
# <a name="enable-backup-for-azure-stack-hub-with-powershell"></a>Habilitación de la copia de seguridad para Azure Stack Hub con PowerShell

Habilite el servicio Copia de seguridad de infraestructura con Windows PowerShell para realizar copias de seguridad periódicas de:
 - Servicio de identidades interno y certificados raíz.
 - Planes de usuario, ofertas, suscripciones.
 - Cuotas de usuario de red, almacenamiento y proceso.
 - Secretos de Key Vault de usuario.
 - Directivas y roles de RBAC de usuario.
 - Cuentas de almacenamiento de usuario.

Puede tener acceso a los cmdlets de PowerShell para habilitar la copia de seguridad, iniciar la copia de seguridad y obtener información de la copia de seguridad a través del punto de conexión de administración del operador.

## <a name="prepare-powershell-environment"></a>Preparación del entorno de PowerShell

Si necesita instrucciones para configurar el entorno de PowerShell, consulte esta artículo sobre la [instalación de PowerShell para Azure Stack Hub](powershell-install-az-module.md). Para iniciar sesión en Azure Stack Hub, consulte [Configuración del entorno de operador e inicio de sesión en Azure Stack Hub](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Provisión del recurso compartido de copia de seguridad, las credenciales y la clave de cifrado para habilitar la copia de seguridad

En la misma sesión de PowerShell, modifique el siguiente script de PowerShell agregando las variables del entorno. Ejecute el script actualizado para proporcionar la clave de cifrado, las credenciales y la clave de cifrado al servicio Infrastructure Backup.

| Variable        | Descripción   |
|---              |---                                        |
| `$username`       | Escriba el **nombre de usuario** con el dominio y el nombre de usuario para la ubicación de la unidad compartida con acceso suficiente para leer y escribir archivos. Por ejemplo, `Contoso\backupshareuser`. |
| `$password`       | Escriba la **Contraseña** del usuario. |
| `$sharepath`      | Escriba la ruta de acceso a la **ubicación de almacenamiento de la copia de seguridad**. Debe utilizar una cadena de convención de nomenclatura universal (UNC) para la ruta de acceso de un recurso compartido de archivos hospedado en un dispositivo independiente. Una cadena UNC especifica la ubicación de recursos como archivos compartidos o dispositivos. Para garantizar la disponibilidad de los datos de copia de seguridad, el dispositivo debe estar en una ubicación independiente. |
| `$frequencyInHours` | La frecuencia en horas determina con qué frecuencia se crean las copias de seguridad. El valor predeterminado es 12. Scheduler admite un máximo de 12 y un mínimo de 4.|
| `$retentionPeriodInDays` | El período de retención en días determina cuántos días de copias de seguridad se conservan en la ubicación externa. El valor predeterminado es 7. Scheduler admite un máximo de 14 y un mínimo de 2. Las copias de seguridad anteriores al período de retención se eliminan automáticamente de la ubicación externa.|
| `$encryptioncertpath` | Se aplica a la compilación 1901 y posteriores. El parámetro está disponible en el módulo de Azure Stack Hub de la versión 1.7 y posterior. La ruta de acceso del certificado de cifrado especifica la ruta de acceso al archivo .CER con la clave pública que se usa para cifrar los datos. |
| `$encryptionkey` | Se aplica a la compilación 1811 o anteriores. El parámetro está disponible en el módulo de Azure Stack Hub de la versión 1.6 o anterior. Clave de cifrado usada para el cifrado de datos. Use el cmdlet [New-AzsEncryptionKeyBase64](/powershell/module/azs.backup.admin/new-azsencryptionkeybase64) para generar una nueva clave. |
|     |     |

### <a name="enable-backup-on-1901-and-later-using-certificate"></a>Habilitación de copias de seguridad en la compilación 1901 y versiones posteriores con un certificado
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certificate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
### <a name="enable-backup-on-1811-or-earlier-using-certificate"></a>Habilitación de la copia de seguridad en la compilación 1811 o versiones anteriores con un certificado
```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $key = New-AzsEncryptionKeyBase64
    $Securekey = ConvertTo-SecureString -String ($key) -AsPlainText -Force

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionKey $Securekey
```

   
##  <a name="confirm-backup-settings"></a>Confirmación de la configuración de copia de seguridad

En la misma sesión de PowerShell, ejecute los comandos siguientes:

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

El resultado debe tener una apariencia similar a la del ejemplo siguiente:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Actualización de la configuración de copia de seguridad
En la misma sesión de PowerShell, puede actualizar los valores predeterminados para el período de retención y la frecuencia de las copias de seguridad. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

El resultado debe tener una apariencia similar a la del ejemplo siguiente:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

### <a name="azure-stack-hub-powershell"></a>PowerShell de Azure Stack Hub 
El cmdlet de PowerShell para configurar la copia de seguridad de la infraestructura es Set-AzsBackupConfiguration. En versiones anteriores, el cmdlet era Set-AzsBackupShare. Este cmdlet requiere proporcionar un certificado. Si Copia de seguridad de infraestructura se configura con una clave de cifrado, no podrá actualizar la clave de cifrado ni ver la propiedad. Debe usar la versión 1.6 del administrador de PowerShell.

Si Copia de seguridad de infraestructura se ha configurado antes de actualizar a 1901, puede usar la versión 1.6 del administrador de PowerShell para establecer y ver la clave de cifrado. Con la versión 1.6, no podrá actualizar de una clave de cifrado a un archivo de certificado.
Consulte [Instalación de PowerShell para Azure Stack Hub](powershell-install-az-module.md) para más información sobre cómo instalar la versión correcta del módulo.


## <a name="next-steps"></a>Pasos siguientes

Para aprender a ejecutar una copia de seguridad, consulte [Copia de seguridad de Azure Stack Hub](azure-stack-backup-back-up-azure-stack.md).

Aprenda a comprobar que la copia de seguridad se ejecutó: consulte [Confirm backup completed in administration portal](azure-stack-backup-back-up-azure-stack.md) (Confirmación de copia de seguridad completada en el portal de administración).
