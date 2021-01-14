---
title: Estación de trabajo de acceso del operador de Azure Stack Hub
description: Aprenda a descargar y configurar una estación de trabajo de acceso del operador de Azure Stack Hub.
author: ashika789
ms.topic: article
ms.date: 11/04/2020
ms.author: patricka
ms.reviewer: asganesh
ms.lastreviewed: 11/04/2020
ms.openlocfilehash: f65235e83e0086ad340efbabc311966a0aa6d621
ms.sourcegitcommit: 5f3adb99b40fa4473955fa408e7ff63d5e1b439f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2021
ms.locfileid: "98105118"
---
# <a name="azure-stack-hub-operator-access-workstation"></a>Estación de trabajo de acceso del operador de Azure Stack Hub 

La estación de trabajo de acceso del operador (OAW) se usa para implementar una máquina virtual host de salto en el host de ciclo de vida de hardware (HLH) que ejecute la versión 2005 u otra posterior para que un operador de Azure Stack Hub pueda acceder al punto de conexión con privilegios y al portal de administración en escenarios de soporte técnico. 

La máquina virtual de la estación de trabajo de acceso del operador debe crearse cuando un operador realiza una nueva tarea. Una vez que se completa una tarea necesaria en la máquina virtual, esta debe apagarse y eliminarse ya que Azure Stack Hub no siempre necesita ejecutarla.  

## <a name="oaw-scenarios"></a>Escenarios de la estación de trabajo de acceso del operador

En las siguientes tablas se enumeran los escenarios comunes de la estación de trabajo de acceso del operador, pero esta información no es excluyente. Es recomendable usar Escritorio remoto para conectarse a la estación de trabajo de acceso del operador. 

|Escenario                                                                                                                          |Descripción                 |
|----------------------------------------------------------------------------------------------------------------------------------|-----------------------------|
|[Acceso al portal de administración](./azure-stack-manage-portals.md)                     |Realiza operaciones administrativas                                                                           |
|[Acceso a PEP](./azure-stack-privileged-endpoint.md)                                     |Recopila y carga de registros:<br>-[Creación de un recurso compartido de SMB](#transfer-files-between-the-hlh-and-oaw) en el HLH para la transferencia de archivos desde Azure Stack Hub<br>\- Uso del Explorador de Azure Storage para cargar los registros guardados en el recurso compartido de SMB |
|[Registro de Azure Stack Hub](./azure-stack-registration.md#renew-or-change-registration) |Para volver a registrarlo, obtenga el nombre de registro y el grupo de recursos anterior en el portal de administración                               |
|[Redifusión de Marketplace](./azure-stack-download-azure-marketplace-item.md)            |[Crea un recurso compartido de SMB](#transfer-files-between-the-hlh-and-oaw) en el HLH para almacenar la imagen o la extensión descargadas                                                        |

## <a name="download-files"></a>Descarga de archivos

Para obtener los archivos para crear la máquina virtual de la estación de trabajo de acceso del operador, [**descárguelos aquí**](https://aka.ms/OAWDownload). Asegúrese de revisar la [declaración de privacidad de Microsoft](https://privacy.microsoft.com/privacystatement) y los [términos legales](/legal/azure-stack-hub/azure-stack-operator-access-workstation-legal-terms) antes de la descarga.

Debido a la naturaleza sin estados de la solución, no hay ninguna actualización de la máquina virtual de la estación de trabajo de acceso del operador. Para cada hito, se lanzará una nueva versión del archivo de imagen de máquina virtual. Use la versión más reciente para crear una nueva máquina virtual de OAW. El archivo de imagen se basa en la versión más reciente de Windows Server 2019. Después de la instalación, puede aplicar las actualizaciones, incluidas las actualizaciones críticas, mediante Windows Update. 

Valide el hash del archivo OAW.zip descargado para asegurarse de que no se ha modificado antes de usarlo para crear la máquina virtual de la estación de trabajo de acceso del operador. Ejecute el siguiente script de PowerShell. Si el valor devuelto es True, puede usar el archivo OAW.zip descargado:

```powershell
param(
    [Parameter(Mandatory=$True)]
    [ValidateNotNullOrEmpty()]
    [ValidateScript({Test-Path $_ -PathType Leaf})]
    [string]
    $DownloadedOAWZipFilePath
)

$expectedHash = '73E16995B79433E79F9EFA9A292443296D112B24B4D86A060FCB4C9403B8D014'
$actualHash = (Get-FileHash -Path $DownloadedOAWZipFilePath).Hash

Write-Host "Expected hash: $expectedHash"

if ($expectedHash -eq $actualHash)
{
    Write-Host 'SUCCESS: OAW.zip file hash matches.'
}
else
{
    Write-Error 'ERROR: OAW.zip file hash does not match! It is not safe to use it, please download it again.'
    Write-Error "Actual hash: $actualHash"
}
```

## <a name="user-account-policy"></a>Directiva de cuenta de usuario 
Se aplica la siguiente directiva de cuenta de usuario a la máquina virtual de OAW:

- Nombre de usuario de la cuenta predefinida de administrador: AdminUser
- MinimumPasswordLength = 14
- PasswordComplexity está habilitado
- MinimumPasswordAge = 1 (día)
- MaximumPasswordAge = 42 (días)
- NewGuestName = GUser (deshabilitado de forma predeterminada)

## <a name="pre-installed-software"></a>Software preinstalado
En la tabla siguiente se muestra el software preinstalado en la máquina virtual de OAW.

| Nombre del software           | Location                                                                                       |
|--------------------------|------------------------------------------------------------------------------------------------|
| [Microsoft Edge para empresas](https://www.microsoft.com/edge/business/)                                            | \[SystemDrive\]\Program Files (x86)\Microsoft\Edge\Application                                                                                        |
| [Modules de Az](./powershell-install-az-module.md)                         | \[SystemDrive\]\ProgramFiles\WindowsPowerShell\Modules                                         |  
| [PowerShell 7](https://devblogs.microsoft.com/powershell/announcing-PowerShell-7-0/)| \[SystemDrive\]\Program Files\PowerShell\7                                                                       |
| [Interfaz de la línea de comandos (CLI) de Azure](/cli/azure/?view=azure-cli-latest) | \[SystemDrive\]\Program Files (x86)\Microsoft SDKs\Azure\CLI2 |
| [Explorador de Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/)   | \[SystemDrive\]\Program Files (x86)\Microsoft Azure Storage Explorer                                                                       |
| [AzCopy](/azure/storage/common/storage-use-azcopy-v10)                             | \[SystemDrive\]\VMSoftware\azcopy_windows_amd64_10.3.4                                         |
| [AzureStack-Tools](https://github.com/Azure/AzureStack-Tools/tree/az)                  | \[SystemDrive\]\VMSoftware\AzureStack-Tools                                                    |

## <a name="check-hlh-version"></a>Comprobación de la versión de HLH

1. Inicie sesión en HLH con sus credenciales.
1. Abra PowerShell ISE y ejecute el siguiente script:

   ```powershell
   'C:\Version\Get-Version.ps1'
   ```

   Por ejemplo:

   ![Captura de pantalla del cmdlet de PowerShell para comprobar la versión de la máquina virtual de OAW](./media/operator-access-workstation/check-hardware-lifecycle-host-version.png)

## <a name="create-the-oaw-vm-using-a-script"></a>Creación de la máquina virtual de OAW mediante un script

El siguiente script prepara la máquina virtual como la estación de trabajo de acceso del operador, la cual se usa para acceder a Microsoft Azure Stack Hub para la administración y el diagnóstico.

1. Inicie sesión en HLH con sus credenciales.
1. Descargue OAW.zip y extraiga los archivos.
1. Abra una sesión de PowerShell con privilegios elevados.
1. Vaya al contenido extraído del archivo OAW.zip.
1. Ejecute el script New-OAW.ps1. 

Por ejemplo, para crear la máquina virtual de OAW en el HLH sin ninguna personalización mediante la versión 2005 de Azure Stack Hub u otra posterior, ejecute el script New-OAW.ps1 solo con el parámetro **-LocalAdministratorPassword**:

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword  
```

Para crear la máquina virtual de la estación de trabajo de acceso del operador en un host con conexión de red a Azure Stack Hub:

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -IPAddress '192.168.0.20' `
   -SubnetMask '255.255.255.0' `
   -DefaultGateway '192.168.0.1' `
   -DNS '192.168.0.10'
```

Para recuperar la dirección IP de la máquina virtual ERCS del archivo AzureStackStampInformation.json:

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -AzureStackCertificatePath 'F:\certroot.cer' `
   -DeploymentDataFilePath 'F:\DeploymentData.json' `
   -AzSStampInfoFilePath 'F:\AzureStackStampInformation.json'
```

Para crear la máquina virtual de la estación de trabajo de acceso del operador en el HLH con DeploymentData.json:

```powershell
$securePassword = Read-Host -Prompt "Enter password for Azure Stack OAW's local administrator" -AsSecureString
New-OAW.ps1 -LocalAdministratorPassword $securePassword `
   -DeploymentDataFilePath 'D:\AzureStack\DeploymentData.json'
```

Si el archivo DeploymentData.json incluye el prefijo de nomenclatura para la máquina virtual de la estación de trabajo de acceso del operador, ese valor se usará para el parámetro **VirtualMachineName**. De lo contrario, el nombre predeterminado será **AzSOAW** o cualquier nombre que especifique el usuario.

Hay dos conjuntos de parámetros disponibles para New-OAW. Los parámetros opcionales aparecen entre corchetes.

```powershell
New-OAW 
-LocalAdministratorPassword <Security.SecureString> `
[-AzureStackCertificatePath <String>] `
[-AzSStampInfoFilePath <String>] `
[-CertificatePassword <Security.SecureString>] `
[-ERCSVMIP <String[]>] `
[-DNS <String[]>] `
[-DeploymentDataFilePath <String>] `
[-SkipNetworkConfiguration] `
[-ImageFilePath <String>] `
[-VirtualMachineName <String>] `
[-VirtualMachineMemory <int64>] `
[-VirtualProcessorCount <int>] `
[-VirtualMachineDiffDiskPath <String>] `
[-PhysicalAdapterMACAddress <String>] `
[-VirtualSwitchName <String>] `
[-ReCreate] `
[-AsJob] `
[-Passthru] `
[-WhatIf] `
[-Confirm] `
[<CommonParameters>]
```

```powershell
New-OAW
-LocalAdministratorPassword <Security.SecureString> `
-IPAddress <String> `
-SubnetMask <String> `
-DefaultGateway <String> `
-DNS <String[]> `
[-AzureStackCertificatePath <String>] `
[-AzSStampInfoFilePath <String>] `
[-CertificatePassword <Security.SecureString>] `
[-ERCSVMIP <String[]>] `
[-ImageFilePath <String>] `
[-VirtualMachineName <String>] `
[-VirtualMachineMemory <int64>] `
[-VirtualProcessorCount <int>] `
[-VirtualMachineDiffDiskPath <String>] `
[-PhysicalAdapterMACAddress <String>] `
[-VirtualSwitchName <String>] `
[-ReCreate] `
[-AsJob] `
[-Passthru] `
[-WhatIf] `
[-Confirm] `
[<CommonParameters>]
```

En la tabla siguiente se muestra la definición de cada parámetro.

| Parámetro   | Obligatorio/opcional  | Descripción       |
|-------------|--------------------|-------------------|
| LocalAdministratorPassword | Obligatorio | La contraseña de la cuenta del administrador local de la máquina virtual. |
| IPAddress                  | Obligatorio | La dirección IPv4 estática para configurar TCP/IP en la máquina virtual.                                                |
| SubnetMask                 | Obligatorio | La máscara de subred de IPv4 para configurar TCP/IP en la máquina virtual.                                                   |
| DefaultGateway             | Obligatorio | La dirección IPv4 de la puerta de enlace predeterminada para configurar TCP/IP en la máquina virtual.                                    |
| DNS                        | Obligatorio | Servidores DNS para configurar TCP/IP en la máquina virtual.                                                          |
| ImageFilePath              | Opcional | Ruta de acceso de OAW.vhdx que proporciona Microsoft. El valor predeterminado es **OAW.vhdx** en la misma carpeta principal de este script. |
| VirtualMachineName         | Opcional | Nombre que se va a asignar a la máquina virtual. Si el prefijo de nomenclatura se puede encontrar en el archivo DeploymentData.json, se usará como nombre predeterminado. De lo contrario, se usará **AzSOAW** como nombre predeterminado. Puede especificar otro nombre para sobrescribir el valor predeterminado. |
| VirtualMachineMemory       | Opcional | Memoria que se va a asignar a la máquina virtual. El valor predeterminado es **4 GB**.                            |
| VirtualProcessorCount      | Opcional | Número de procesadores virtuales que se va a asignar a la máquina virtual. El valor predeterminado es **8**.        |
| VirtualMachineDiffDiskPath | Opcional | Ruta de acceso para almacenar los archivos del disco de diferenciación temporal mientras la máquina virtual de administración estaba activa. El valor predeterminado es el subdirectorio **DiffDisks** en la misma carpeta principal de este script. |
| AzureStackCertificatePath  | Opcional | Ruta de los certificados que se van a importar a la máquina virtual para el acceso a Azure Stack Hub. |
| AzSStampInfoFilePath       | Opcional | Ruta de acceso del archivo AzureStackStampInformation.json donde el script puede recuperar las direcciones IP de la máquina virtual ERCS. |
| CertificatePassword        | Opcional | Contraseña del certificado que se va a importar a la máquina virtual para el acceso a Azure Stack Hub. |
| ERCSVMIP                   | Opcionales | Dirección IP de las máquinas virtuales ERCS de Azure Stack Hub que se van a agregar a la lista de hosts de confianza de la máquina virtual. No surtirá efecto si se establece **-SkipNetworkConfiguration**. |
SkipNetworkConfiguration     | Opcional | Omite la configuración de red de la máquina virtual para que el usuario puede configurarla más adelante. |
| DeploymentDataFilePath     | Opcionales | Ruta de acceso del archivo DeploymentData.json. No surtirá efecto si se establece **-SkipNetworkConfiguration**.            |
| PhysicalAdapterMACAddress  | Opcional | La dirección MAC del adaptador de red del host que se utilizará para conectar la máquina virtual.<br>- Si solo hay un adaptador de red físico, este parámetro no es necesario y se usará el único adaptador de red.<br>- Si hay más de un adaptador de red físico, este parámetro es necesario para especificar cuál se va a usar.<br> |
| VirtualSwitchName          | Opcional | El nombre del conmutador virtual que debe configurarse en Hyper-V para la máquina virtual.<br>- Si está el parámetro VMSwitch con el nombre proporcionado, se seleccionará ese VMSwitch.<br>- Si no hay ningún parámetro VMSwitch con el nombre proporcionado, se creará uno.<br> |
| ReCreate                   | Opcional | Elimina y vuelve a crear la máquina virtual si ya existe una máquina virtual con el mismo nombre. |

## <a name="check-the-oaw-vm-version"></a>Comprobación de la versión de la máquina virtual de OAW

1. Inicie sesión en la máquina virtual de OAW con sus credenciales.
1. Abra PowerShell ISE y ejecute el siguiente script:

   ```powershell
   'C:\Version\Get-Version.ps1'
   ```

   Por ejemplo:

   ![Captura de pantalla del cmdlet de PowerShell para comprobar la versión del host de ciclo de vida de hardware](./media/operator-access-workstation/check-operator-access-workstation-vm-version.png)

## <a name="transfer-files-between-the-hlh-and-oaw"></a>Transferencia de archivos entre HLH y la estación de trabajo de acceso del operador

Si necesita transferir archivos entre HLH y la estación de trabajo de acceso del operador, cree un recurso compartido de SMB mediante el cmdlet [New-SmbShare](/powershell/module/smbshare/new-smbshare?view=win10-ps). New-SmbShare expone una carpeta del sistema de archivos a los clientes remotos como un recurso compartido de bloque de mensajes del servidor (SMB). Por ejemplo:

Para eliminar un recurso compartido creado mediante este cmdlet, use el cmdlet [Remove-SmbShare](/powershell/module/smbshare/remove-smbshare?view=win10-ps). Por ejemplo:

## <a name="remove-the-oaw-vm"></a>Eliminación de la máquina virtual de OAW

El siguiente script elimina la máquina virtual de OAW que se usa para acceder a Azure Stack Hub para la administración y el diagnóstico. Este script también elimina los archivos de disco y la protección asociada a la máquina virtual.

1. Inicie sesión en HLH con sus credenciales.
1. Abra una sesión de PowerShell con privilegios elevados. 
1. Vaya al contenido extraído del archivo OAW.zip instalado.
1. Quite la máquina virtual mediante la ejecución del script Remove-OAW.ps1: 

   ```powershell
   Remove-OAW.ps1 -VirtualMachineName <name>
   ```

   Donde \<name\> es el nombre de la máquina virtual que se va a eliminar. De forma predeterminada, el nombre es **AzSOAW**.

   Por ejemplo:

   ```powershell
   Remove-OAW.ps1 -VirtualMachineName AzSOAW
   ```

## <a name="next-steps"></a>Pasos siguientes

[Tareas de administración de Azure Stack](azure-stack-manage-basics.md)
