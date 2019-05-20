---
title: Mantenimiento de un proveedor de recursos de MySQL en Azure Stack | Microsoft Docs
description: Aprenda cómo puede mantener el servicio de proveedor de recursos MySQL en Azure Stack.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: d9d91dce762265da498a0a1232b023c2956edefd
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617843"
---
# <a name="mysql-resource-provider-maintenance-operations"></a>Operaciones de mantenimiento del proveedor de recursos de MySQL

El proveedor de recursos de MySQL se ejecuta en una máquina virtual bloqueada. Para habilitar las operaciones de mantenimiento, debe actualizar la seguridad de la máquina virtual. Para hacerlo con el principio de privilegio mínimo, puede usar el punto de conexión de JEA (Just Enough Administration) de PowerShell DBAdapterMaintenance. El paquete de instalación del proveedor de recursos incluye un script para esta operación.

## <a name="update-the-virtual-machine-operating-system"></a>Actualización del sistema operativo de la máquina virtual

Dado que el proveedor de recursos se ejecuta en la máquina virtual de un *usuario*, debe aplicar las revisiones y actualizaciones necesarias cuando se publiquen. Puede usar los paquetes de actualización de Windows que se proporcionan como parte del ciclo de revisión y actualización para aplicar actualizaciones a la máquina virtual.

Actualización de la máquina virtual del proveedor mediante uno de los métodos siguientes:

- Instalación del paquete más reciente del proveedor de recursos mediante una imagen de Windows Server 2016 Core actualmente revisada.
- Instalación de un paquete de Windows Update durante la instalación o actualización del proveedor de recursos.

## <a name="update-the-virtual-machine-windows-defender-definitions"></a>Actualización de las definiciones de Windows Defender de máquina virtual

Siga estos pasos para actualizar las definiciones de Defender:

1. Descargue la actualización de las definiciones de Windows Defender desde [esta página](https://www.microsoft.com/en-us/wdsi/definitions).

    En la página de definiciones, desplácese a "Manually download and install the definitions" (Descargar e instalar manualmente las definiciones). Descargue el archivo "Windows Defender Antivirus for Windows 10 and Windows 8.1" de 64 bits.

    De forma alternativa, use [este vínculo directo](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) para descargar o ejecutar el archivo fpam-fe.exe.

2. Abra una sesión de PowerShell en el punto de conexión de mantenimiento de la máquina virtual del adaptador del proveedor de recursos MySQL.

3. Copie el archivo de actualización de definiciones en la máquina virtual del adaptador del proveedor de recursos mediante la sesión del punto de conexión de mantenimiento.

4. En la sesión de PowerShell de mantenimiento, ejecute el comando _Update-DBAdapterWindowsDefenderDefinitions_.

5. Después de instalar las definiciones, se recomienda eliminar el archivo de actualización de definiciones mediante el comando _Remove-ItemOnUserDrive_.

**Ejemplo de script de PowerShell para actualizar definiciones.**

Para actualizar las definiciones de Defender se puede editar y ejecutar el script siguiente. Reemplace los valores del script por los de su entorno.

```powershell
# Set credentials for the local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download Windows Defender update definitions file from https://www.microsoft.com/en-us/wdsi/definitions.  
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate  

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance

# Copy the defender update file to the adapter virtual machine.
Copy-Item -ToSession $session -Path $localPathToDefenderUpdate `
     -Destination "User:\"

# Install the update definitions.
Invoke-Command -Session $session -ScriptBlock `
    {Update-AzSDBAdapterWindowsDefenderDefinition -DefinitionsUpdatePackageFile "User:\mpam-fe.exe"}

# Cleanup the definitions package file and session.
Invoke-Command -Session $session -ScriptBlock `
    {Remove-AzSItemOnUserDrive -ItemPath "User:\mpam-fe.exe"}
$session | Remove-PSSession

```

## <a name="secrets-rotation"></a>Cambio de secretos

*Estas instrucciones se aplican solo a sistemas integrados de Azure Stack.*

Al usar los proveedores de recursos de SQL y MySQL con los sistemas integrados de Azure Stack, el operador de Azure Stack se encarga de rotar los siguientes secretos de infraestructura del proveedor de recursos para asegurarse de que no caduquen:

- Certificado SSL externo [proporcionado durante la implementación](azure-stack-pki-certs.md).
- La contraseña de la cuenta de administrador local de la máquina virtual del proveedor de recursos proporcionada durante la implementación.
- Contraseña de usuario de diagnóstico del proveedor de recursos (dbadapterdiag).

### <a name="powershell-examples-for-rotating-secrets"></a>Ejemplos de PowerShell de cambio de secretos

**Cambio de todos los secretos al mismo tiempo.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd `  
    -VMLocalCredential $localCreds

```

**Cambio de la contraseña de usuario de diagnóstico.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd

```

**Cambio de la contraseña de la cuenta de administrador local de la máquina virtual.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds

```

**Cambio de la contraseña del certificado SSL.**

```powershell
.\SecretRotationMySQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd

```

### <a name="secretrotationmysqlproviderps1-parameters"></a>Parámetros de SecretRotationMySQLProvider.ps1

|Parámetro|DESCRIPCIÓN|
|-----|-----|
|AzCredential|Credencial de la cuenta de administrador de servicio de Azure Stack.|
|CloudAdminCredential|Credencial de cuenta de dominio de administración en la nube de Azure Stack.|
|PrivilegedEndpoint|Punto de conexión con privilegios para acceder a Get-AzureStackStampInformation.|
|DiagnosticsUserPassword|Contraseña de la cuenta de usuario de diagnóstico.|
|VMLocalCredential|La cuenta de administrador local de la máquina virtual MySQLAdapter.|
|DefaultSSLCertificatePassword|Contraseña del certificado SSL predeterminado (*.pfx).|
|DependencyFilesLocalPath|Ruta de acceso local de los archivos de dependencia.|
|     |     |

### <a name="known-issues"></a>Problemas conocidos

**Problema:**<br>
Los registros para el cambio de secretos no se recopilan automáticamente si el script de cambio de secretos produce un error cuando se ejecuta.

**Solución alternativa**:<br>
Utilice el cmdlet Get-AzsDBAdapterLogs para recopilar todos los registros del proveedor de recursos, incluido AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, guardado en C:\Logs.

## <a name="collect-diagnostic-logs"></a>Recopilación de registros de diagnóstico

Para recopilar registros de la máquina virtual bloqueada, se puede usar el punto de conexión DBAdapterDiagnostics de JEA (Just Enough Administration) de PowerShell. Este punto de conexión proporciona los comandos siguientes:

- **Get-AzsDBAdapterLog**. Este comando crea un paquete ZIP con los registros de diagnóstico del proveedor de recursos y guarda el archivo en la unidad del usuario de la sesión. Este comando se puede ejecutar sin ningún parámetro y se recopilan los registros de las últimas cuatro horas.

- **Remove-AzsDBAdapterLog**. Este comando quita los paquetes de registros existentes en la máquina virtual del proveedor de recursos.

### <a name="endpoint-requirements-and-process"></a>Requisitos y proceso del punto de conexión

Cuando se instala o actualiza un proveedor de recursos, se crea la cuenta de usuario dbadapterdiag. Esta cuenta se usará para recopilar registros de diagnóstico.

>[!NOTE]
>La contraseña de la cuenta dbadapterdiag es la misma que se usa para el administrador local en la máquina virtual creada durante la implementación o actualización de un proveedor.

Para usar los comandos _DBAdapterDiagnostics_, cree una sesión remota de PowerShell para la máquina virtual del proveedor de recursos y ejecute el comando **Get-AzsDBAdapterLog**.

Establezca el intervalo de tiempo para la recopilación de registros mediante los parámetros **FromDate** y **ToDate**. Si no se especifica uno de estos parámetros (o ambos), se utilizan los valores predeterminados siguientes:

* FromDate es cuatro horas antes de la hora actual.
* ToDate es la hora actual.

**Ejemplo de script de PowerShell para recopilar registros.**

El siguiente script muestra cómo recopilar registros de diagnóstico de la máquina virtual del proveedor de recursos.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'
$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds
        -ConfigurationName DBAdapterDiagnostics

# Sample that captures logs from the previous hour.
$fromDate = (Get-Date).AddHours(-1)
$dateNow = Get-Date
$sb = {param($d1,$d2) Get-AzSDBAdapterLog -FromDate $d1 -ToDate $d2}
$logs = Invoke-Command -Session $session -ScriptBlock $sb -ArgumentList $fromDate,$dateNow

# Copy the logs to the user drive.
$sourcePath = "User:\{0}" -f $logs
$destinationPackage = Join-Path -Path (Convert-Path '.') -ChildPath $logs
Copy-Item -FromSession $session -Path $sourcePath -Destination $destinationPackage

# Cleanup the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession

```

## <a name="next-steps"></a>Pasos siguientes

[Eliminación del proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-remove.md)
