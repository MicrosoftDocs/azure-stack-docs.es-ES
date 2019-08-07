---
title: Validación de una copia de seguridad de Azure Stack mediante el ASDK | Microsoft Docs
description: Cómo validar una copia de seguridad de sistemas integrados de Azure Stack mediante el ASDK.
services: azure-stack
author: justinha
manager: femila
cloud: azure-stack
ms.service: azure-stack
ms.topic: article
ms.date: 07/31/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: 3ab7dfbaef82868f45b181fb81d9b98050147191
ms.sourcegitcommit: bf4d265a3522cbfdd9dd295a0f4ad0daf2ed5eca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2019
ms.locfileid: "68692116"
---
# <a name="use-the-asdk-to-validate-an-azure-stack-backup"></a>Uso del ASDK para validar una copia de seguridad de Azure Stack
Después de implementar Azure Stack y aprovisionar recursos de usuario, como ofertas, planes, cuotas y suscripciones, debe [habilitar la infraestructura de copia de seguridad de Azure Stack](../operator/azure-stack-backup-enable-backup-console.md). La programación y ejecución de copias de seguridad de infraestructura regulares asegurará que los datos de administración de infraestructura no se pierdan si se produce un error de servicio o de hardware muy grave.

> [!TIP]
> Se recomienda [ejecutar una copia de seguridad bajo petición](../operator/azure-stack-backup-back-up-azure-stack.md) antes de comenzar este procedimiento para asegurarse de que dispone de una copia de los datos de infraestructura más recientes. Asegúrese de que captura el identificador de copia de seguridad una vez finalizada correctamente la copia de seguridad. Este identificador será necesario durante la recuperación en la nube. 

Las copias de seguridad de infraestructura de Azure Stack contienen datos importantes sobre la nube que se pueden restaurar durante la reimplementación de Azure Stack. El ASDK se puede usar para validar estas copias de seguridad sin afectar a la nube de producción. 

La validación de las copias de seguridad en ASDK se admite para los escenarios siguientes:

|Escenario|Propósito|
|-----|-----|
|Validación de las copias de seguridad de infraestructura desde una solución integrada.|Validación de duración breve de que los datos de la copia de seguridad son válidos.|
|Obtención de información sobre el flujo de trabajo de recuperación completo.|Uso del ASDK para validar la experiencia completa de copia de seguridad y restauración.|
|     |     |

El escenario siguiente **no** se admite al validar las copias de seguridad en el ASDK:

|Escenario|Propósito|
|-----|-----|
|Compilación del ASDK para crear la copia de seguridad y la restauración.|Restauración de los datos de copia de seguridad desde una versión anterior del ASDK en una versión más reciente.|
|     |     |


## <a name="cloud-recovery-deployment"></a>Implementación de la recuperación en la nube
Las copias de seguridad de infraestructura de la implementación de sistemas integrados se pueden validar mediante la realización de una implementación de recuperación en la nube del ASDK. En este tipo de implementación, los datos de servicio específicos se restauran a partir de la copia de seguridad después de instalar el ASDK en el equipo host.

### <a name="prereqs"></a>Requisitos previos de recuperación en la nube
Antes de iniciar una implementación de recuperación en la nube del ASDK, asegúrese de que tiene la información siguiente:

**Requisitos del instalador de la interfaz de usuario**

*El instalador de la interfaz de usuario actual solo admite claves de cifrado*

|Requisito previo|DESCRIPCIÓN|
|-----|-----|
|Ruta de acceso del recurso compartido de copia de seguridad|La ruta de acceso del recurso compartido de archivo UNC de la copia de seguridad de Azure Stack más reciente que se va a usar para recuperar información de la infraestructura de Azure Stack. Este recurso compartido local se creará durante el proceso de implementación de recuperación en la nube.|
|Id. de la copia de seguridad que se va a restaurar|El identificador de copia de seguridad, en formato alfanumérico "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", que identifica la copia de seguridad que se va a restaurar durante la recuperación en la nube.|
|Dirección IP del servidor horario|Se necesita una dirección IP de servidor horario válida, como 132.163.97.2, para la implementación de Azure Stack.|
|Contraseña del certificado externo|La contraseña para el certificado externo que se usa en Azure Stack. La copia de seguridad de la entidad de certificación contiene los certificados externos que se deben restaurar con esta contraseña.|
|Clave de cifrado de copia de seguridad|Es obligatoria si la configuración de copia de seguridad está establecida con una clave de cifrado, que ya está en desuso. El instalador admitirá la clave de cifrado en el modo de compatibilidad con versiones anteriores durante al menos 3 versiones. Una vez actualizada la configuración de copia de seguridad para usar un certificado, consulte la siguiente tabla para la información necesaria.|

|     |     | 

**Requisitos del instalador de PowerShell**

*El instalador de PowerShell actual es compatible con el certificado de descifrado o la clave de cifrado*

|Requisito previo|DESCRIPCIÓN|
|-----|-----|
|Ruta de acceso del recurso compartido de copia de seguridad|La ruta de acceso del recurso compartido de archivo UNC de la copia de seguridad de Azure Stack más reciente que se va a usar para recuperar información de la infraestructura de Azure Stack. Este recurso compartido local se creará durante el proceso de implementación de recuperación en la nube.|
|Id. de la copia de seguridad que se va a restaurar|El identificador de copia de seguridad, en formato alfanumérico "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx", que identifica la copia de seguridad que se va a restaurar durante la recuperación en la nube.|
|Dirección IP del servidor horario|Se necesita una dirección IP de servidor horario válida, como 132.163.97.2, para la implementación de Azure Stack.|
|Contraseña del certificado externo|La contraseña para el certificado externo que se usa en Azure Stack. La copia de seguridad de la entidad de certificación contiene los certificados externos que se deben restaurar con esta contraseña.|
|Contraseña de certificación de descifrado|Opcional. Necesario únicamente si la copia de seguridad se cifra mediante un certificado. Es la contraseña para el certificado autofirmado (.pfx) que contiene la clave privada necesaria para descifrar los datos de copia de seguridad.|
|Clave de cifrado de copia de seguridad|Opcional. Es obligatoria si la configuración de copia de seguridad sigue establecida con una clave de cifrado. El instalador admitirá la clave de cifrado en el modo de compatibilidad con versiones anteriores durante al menos 3 versiones. Una vez actualizada la configuración de copia de seguridad para usar un certificado, debe proporcionar la contraseña del certificado de descifrado.|
|     |     | 

## <a name="prepare-the-host-computer"></a>Preparación del equipo host 
Como en una implementación normal del ASDK, se debe preparar el entorno del sistema de host del ASDK para la instalación. Cuando el equipo host del kit de desarrollo esté preparado, se iniciará desde el disco duro de la máquina virtual CloudBuilder.vhdx para comenzar la implementación del Kit de desarrollo de Azure Stack.

En el equipo host del ASDK, descargue un archivo cloudbuilder.vhdx nuevo correspondiente a la misma versión de Azure Stack de la que se realizó la copia de seguridad y siga las instrucciones para [preparar el equipo host del ASDK](asdk-prepare-host.md).

Una vez reiniciado el servidor host desde el archivo cloudbuilder.vhdx, se debe crear un recurso compartido de archivos y copiar los datos de copia de seguridad en él. El recurso compartido de archivos debe ser accesible para la cuenta que ejecuta el programa de instalación, que es la de Administrador en estos comandos de PowerShell de ejemplo: 

```powershell
$shares = New-Item -Path "c:\" -Name "Shares" -ItemType "directory"
$azsbackupshare = New-Item -Path $shares.FullName -Name "AzSBackups" -ItemType "directory"
New-SmbShare -Path $azsbackupshare.FullName -FullAccess ($env:computername + "\Administrator")  -Name "AzSBackups"
```

Después, copie los archivos de copia de seguridad de Azure Stack más recientes en el recurso compartido recién creado. La estructura de carpetas del recurso compartido debe ser: `\\<ComputerName>\AzSBackups\MASBackup\<BackupID>\`.

Por último, copie el certificado de descifrado (.pfx) en el directorio del certificado `C:\CloudDeployment\Setup\Certificates\` y cámbiele el nombre a `BackupDecryptionCert.pfx`.

## <a name="deploy-the-asdk-in-cloud-recovery-mode"></a>Implementación del ASDK en modo de recuperación en la nube

> [!IMPORTANT]
> 1. El instalador de la interfaz de usuario actual solo admite claves de cifrado. Solo puede validar las copias de seguridad de los sistemas que sigan usando clave de cifrado. Si se cifró la copia de seguridad en un sistema integrado o ASDK mediante certificado, debe usar el instalador de PowerShell (**InstallAzureStackPOC.ps1**). 
> 2. El instalador de PowerShell (**InstallAzureStackPOC.ps1**) admite la clave de cifrado o el certificado.
> 3. La instalación de ASDK admite exactamente una tarjeta de interfaz de red (NIC) para la conexión con redes. Si tiene varias NIC, asegúrese de que solo una está habilitada (y todas las demás deshabilitadas) antes de ejecutar el script de implementación.

### <a name="use-the-installer-ui-to-deploy-the-asdk-in-recovery-mode"></a>Uso del instalador de la interfaz de usuario para implementar el ASDK en modo de recuperación
Los pasos de esta sección muestran cómo implementar el ASDK mediante una interfaz gráfica de usuario (GUI) que se proporciona al descargar y ejecutar el script de PowerShell **asdk-installer.ps1**.

> [!NOTE]
> La interfaz de usuario del instalador del Kit de desarrollo de Azure Stack es un script de código abierto basado en WCF y PowerShell.

> [!IMPORTANT]
> El instalador de la interfaz de usuario actual solo admite claves de cifrado.

1. Una vez que el equipo host arranque correctamente en la imagen de CloudBuilder.vhdx, inicie sesión con las credenciales de administrador que especificó cuando [preparó el equipo host del kit de desarrollo](asdk-prepare-host.md) para la instalación del ASDK. Dichas credenciales deben coincidir con las de administrador local del host del kit de desarrollo.
2. Abra una consola de PowerShell con privilegios elevados y ejecute el script de PowerShell **letra de unidad>\AzureStack_Installer\asdk-installer.ps1&lt;** . El script puede estar en una unidad distinta de C:\ en la imagen de CloudBuilder.vhdx. Haga clic en **Recuperar**.

    ![Script del instalador del ASDK](media/asdk-validate-backup/1.PNG) 

3. Escriba la información del directorio de Azure AD (opcional) y la contraseña de administrador local para el equipo host del ASDK en la página del proveedor de identidades y credenciales. Haga clic en **Next**.

    ![Página de identidad y credenciales](media/asdk-validate-backup/2.PNG) 

4. Seleccione el adaptador de red que va a usar el equipo host del ASDK y haga clic en **Siguiente**. Todas las demás interfaces de red se deshabilitarán durante la instalación del ASDK. 

    ![Interfaz del adaptador de red](media/asdk-validate-backup/3.PNG) 

5. En la página Configuración de red, proporcione el servidor de hora válido y las direcciones IP del reenviador DNS. Haga clic en **Next**.

    ![Página de configuración de red](media/asdk-validate-backup/4.PNG) 

6. Cuando se hayan verificado las propiedades de la tarjeta de interfaz de red, haga clic en **Siguiente**. 

    ![Verificación de la configuración de la tarjeta de red](media/asdk-validate-backup/5.PNG) 

7. Proporcione la información necesaria descrita anteriormente en la [sección de requisitos previos](#prereqs) en la página Configuración de copia de seguridad, y el nombre de usuario y la contraseña que se usarán para acceder al recurso compartido. Haga clic en **Siguiente**: 

   ![Página de configuración de copia de seguridad](media/asdk-validate-backup/6.PNG) 

8. Revise el script de implementación que se usará para la implementación del ASDK en la página Resumen. Haga clic en **Implementar** para iniciar la implementación. 

    ![Página de resumen](media/asdk-validate-backup/7.PNG) 


### <a name="use-powershell-to-deploy-the-asdk-in-recovery-mode"></a>Uso de PowerShell para implementar el ASDK en modo de recuperación

Modifique los comandos de PowerShell siguientes para el entorno y ejecútelos para implementar el ASDK en modo de recuperación en la nube:

**Use el script InstallAzureStackPOC.ps1 para iniciar la recuperación en la nube con clave de cifrado.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$key = Read-Host -AsSecureString -Prompt "Your backup encryption key"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupEncryptionKeyBase64 $key `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

**Use el script InstallAzureStackPOC.ps1 para iniciar la recuperación en la nube con certificado de descifrado.**

```powershell
cd C:\CloudDeployment\Setup     
$adminpass = Read-Host -AsSecureString -Prompt "Local Administrator password"
$certPass = Read-Host -AsSecureString -Prompt "Password for the external certificate"
$backupstorecredential = Read-Host -AsSecureString -Prompt "Credential for backup share"
$decryptioncertpassword  = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"

.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass `
 -BackupStorePath ("\\" + $env:COMPUTERNAME + "\AzSBackups") `
 -BackupDecryptionCertPassword $decryptioncertpassword `
 -BackupStoreCredential $backupstorecredential `
 -BackupId "<Backup ID to restore>" `
 -TimeServer "<Valid time server IP>" -ExternalCertPassword $certPass
```

## <a name="complete-cloud-recovery"></a>Finalización de la recuperación en la nube 
Después de una implementación de recuperación en la nube correcta, tendrá que completar la restauración con el cmdlet **Restore-AzureStack**. 

Después de iniciar sesión como operador de Azure Stack, [instale PowerShell de Azure Stack](asdk-post-deploy.md#install-azure-stack-powershell) y ejecute los siguientes comandos para especificar el certificado y la contraseña que se van a usar al restaurar desde copia de seguridad:

**Modo de recuperación con archivo de certificado**

> [!NOTE] 
> La implementación de Azure Stack no conserva el certificado de descifrado por motivos de seguridad. Deberá volver a proporcionar el certificado de descifrado y la contraseña asociada.

```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>" `
 -DecryptionCertPath "<path to decryption certificate with file name (.pfx)>" `
 -DecryptionCertPassword $decryptioncertpassword
```

**Modo de recuperación con clave de cifrado**
```powershell
$decryptioncertpassword = Read-Host -AsSecureString -Prompt "Password for the decryption certificate"
Restore-AzsBackup -ResourceId "<BackupID>"
```

Espere 60 minutos después de llamar a este cmdlet para iniciar la comprobación de los datos de copia de seguridad en el ASDK recuperado en la nube.

## <a name="next-steps"></a>Pasos siguientes
[Registrar Azure Stack](asdk-register.md)

