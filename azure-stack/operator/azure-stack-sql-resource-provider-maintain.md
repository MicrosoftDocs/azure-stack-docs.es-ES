---
title: Operaciones de mantenimiento del proveedor de recursos de SQL
titleSuffix: Azure Stack Hub
description: Más información sobre las operaciones de mantenimiento de un proveedor de recursos de SQL en Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.author: bryanla
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: 6fc476b1f373c8f21481b979d1eefcdbe356766b
ms.sourcegitcommit: 08a421ab5792ab19cc06b849763be22f051e6d78
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/02/2020
ms.locfileid: "89364837"
---
# <a name="sql-resource-provider-maintenance-operations"></a>Operaciones de mantenimiento del proveedor de recursos de SQL

El proveedor de recursos de SQL se ejecuta en una máquina virtual bloqueada. Para habilitar las operaciones de mantenimiento, debe actualizar la seguridad de la VM. Para hacerlo con el principio de privilegio mínimo, use el punto de conexión [Just Enough Administration (JEA) de PowerShell](/powershell/scripting/learn/remoting/jea/overview)*DBAdapterMaintenance*. El paquete de instalación del proveedor de recursos incluye un script para esta acción.

## <a name="patching-and-updating"></a>Revisiones y actualizaciones

El proveedor de recursos de SQL no se suministra como parte de Azure Stack Hub porque es un componente complementario. Microsoft proporciona las actualizaciones para el proveedor de recursos de SQL según sea necesario. Cuando se publica un adaptador de SQL actualizado, se proporciona un script para aplicar la actualización. Este script crea una nueva VM de proveedor de recursos, que migra el estado de la VM del proveedor antigua a la VM nueva. Para más información, consulte [Actualización del proveedor de recursos de SQL](azure-stack-sql-resource-provider-update.md).

### <a name="provider-vm"></a>Máquina virtual de proveedor

Dado que el proveedor de recursos se ejecuta en la VM de un *usuario*, debe aplicar las revisiones y actualizaciones necesarias cuando se publiquen. Use los paquetes de actualización de Windows que se proporcionan como parte del ciclo de revisión y actualización para aplicar actualizaciones a la máquina virtual.

## <a name="updating-sql-credentials"></a>Actualización de las credenciales de SQL

Es responsable de crear y mantener cuentas sysadmin en los servidores SQL Server. El proveedor de recursosnecesita una cuenta con estos privilegios para administrar las bases de datos para los usuarios, pero no necesita acceso a los datos de los usuarios. Si tiene que actualizar las contraseñas de sysadmin en los servidores SQL Server, puede utilizar la interfaz del administrador del proveedor de recursos para cambiar una contraseña almacenada. Estas contraseñas se almacenan en un almacén de claves en la instancia de Azure Stack Hub.

Para modificar la configuración, seleccione **Examinar** &gt; **RECURSOS ADMINISTRATIVOS** &gt; **Servidores de hospedaje SQL** &gt; **Inicios de sesión SQL** y seleccione un nombre de usuario. El cambio se debe efectuar en la instancia de SQL en primer lugar (y en cualquier réplica, si es necesario). En **Configuración**, seleccione **Contraseña**.

![Actualización de la contraseña de administrador de SQL](./media/azure-stack-sql-rp-deploy/sql-rp-update-password.png)

## <a name="secrets-rotation"></a>Cambio de secretos

*Estas instrucciones se aplican solo a sistemas integrados de Azure Stack Hub.*

Al usar los proveedores de recursos SQL y MySQL con sistemas integrados de Azure Stack Hub, el operador de Azure Stack Hub se encarga de rotar los siguientes secretos de la infraestructura del proveedor de recursos para asegurarse de que no caduquen:

- Certificado SSL externo [proporcionado durante la implementación](azure-stack-pki-certs.md).
- La contraseña de la cuenta de administrador local de la máquina virtual del proveedor de recursos proporcionada durante la implementación.
- Contraseña de usuario de diagnóstico del proveedor de recursos (dbadapterdiag).
- (versión >= 1.1.47.0) Certificado de Key Vault generado durante la implementación.

### <a name="powershell-examples-for-rotating-secrets"></a>Ejemplos de PowerShell de cambio de secretos

**Cambio de todos los secretos al mismo tiempo.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword $passwd `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd  `
    -VMLocalCredential $localCreds `
    -KeyVaultPfxPassword $keyvaultCertPasswd
```

**Cambio de la contraseña de usuario de diagnóstico.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DiagnosticsUserPassword  $passwd
```

**Cambio de la contraseña de la cuenta de administrador local de la VM.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -VMLocalCredential $localCreds
```

**Cambio de la contraseña del certificado SSL.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -DependencyFilesLocalPath $certPath `
    -DefaultSSLCertificatePassword $certPasswd
```

**Cambio de la contraseña del certificado de Key Vault.**

```powershell
.\SecretRotationSQLProvider.ps1 `
    -Privilegedendpoint $Privilegedendpoint `
    -CloudAdminCredential $cloudCreds `
    -AzCredential $adminCreds `
    -KeyVaultPfxPassword $keyvaultCertPasswd
```

### <a name="secretrotationsqlproviderps1-parameters"></a>Parámetros de SecretRotationSQLProvider.ps1

|Parámetro|Descripción|Comentario|
|-----|-----|-----|
|AzureEnvironment|Entorno de Azure de la cuenta de administrador de servicios que se usó para la implementación de Azure Stack Hub. Requerido solo para implementaciones de Azure AD. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una suscripción a Azure Active Directory de China, **AzureChinaCloud**.|Opcional|
|AzCredential|Credencial de la cuenta de administrador de servicios de Azure Stack Hub.|Mandatory|
|CloudAdminCredential|Credencial de la cuenta de dominio de administración en la nube de Azure Stack Hub.|Mandatory|
|PrivilegedEndpoint|Punto de conexión con privilegios para acceder a Get-AzureStackStampInformation.|Mandatory|
|DiagnosticsUserPassword|Contraseña de la cuenta de usuario de diagnóstico.|Opcional|
|VMLocalCredential|Cuenta de administrador local en la máquina virtual MySQLAdapter.|Opcional|
|DefaultSSLCertificatePassword|Contraseña del certificado SSL predeterminado (*.pfx).|Opcional|
|DependencyFilesLocalPath|Ruta de acceso local de los archivos de dependencia.|Opcional|
|KeyVaultPfxPassword|Contraseña que se usa para generar el certificado de Key Vault para el adaptador de la base de datos.|Opcional|
|     |     |     |

### <a name="known-issues"></a>Problemas conocidos

**Problema**:<br>
Registros de cambio de secretos. Los registros para el cambio de secretos no se recopilan automáticamente si se produce un error en el script personalizado de cambio de secretos cuando se ejecuta.

**Solución alternativa**:<br>
Utilice el cmdlet Get-AzsDBAdapterLogs para recopilar todos los registros del proveedor de recursos, incluido AzureStack.DatabaseAdapter.SecretRotation.ps1_*.log, guardado en C:\Logs.

## <a name="update-the-vm-operating-system"></a>Actualización del sistema operativo de la VM

Use uno de los métodos siguientes para actualizar el sistema operativo de la máquina virtual.

- Instalación del paquete más reciente del proveedor de recursos mediante una imagen de Windows Server 2016 Core actualmente revisada.
- Instalar un paquete de Windows Update durante la instalación o actualización del proveedor de recursos.

## <a name="update-the-vm-windows-defender-definitions"></a>Actualización de las definiciones de Windows Defender de la VM

Para actualizar las definiciones de Windows Defender:

1. Descargue la actualización de definiciones de Windows Defender desde [Actualizaciones de inteligencia de seguridad para Windows Defender](https://www.microsoft.com/wdsi/definitions).

   En la página de actualización de definiciones, desplácese a la sección "Descarga manual de la actualización". Descargue el archivo "Windows Defender Antivirus for Windows 10 and Windows 8.1" de 64 bits.

   También puede usar [este vínculo directo](https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64) para descargar o ejecutar el archivo fpam-fe.exe.

2. Cree una sesión de PowerShell en el punto de conexión de mantenimiento de la VM del adaptador del proveedor de recursos de SQL.

3. Copie el archivo de actualización de definiciones en la máquina virtual mediante la sesión del punto de conexión de mantenimiento.

4. En la sesión de PowerShell de mantenimiento, ejecute el comando *Update-DBAdapterWindowsDefenderDefinitions*.

5. Después de instalar las definiciones, se recomienda eliminar el archivo de actualización de definiciones mediante el comando *Remove-ItemOnUserDrive*.

**Ejemplo de script de PowerShell para actualizar definiciones**

Para actualizar las definiciones de Defender se puede editar y ejecutar el script siguiente. Reemplace los valores del script por los de su entorno.

```powershell
# Set credentials for local admin on the resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "<local admin user password>" -AsPlainText -Force
$vmLocalAdminUser = "<local admin user name>"
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential `
    ($vmLocalAdminUser, $vmLocalAdminPass)

# Provide the public IP address for the adapter VM.
$databaseRPMachine  = "<RP VM IP address>"
$localPathToDefenderUpdate = "C:\DefenderUpdates\mpam-fe.exe"

# Download the Windows Defender update definitions file from https://www.microsoft.com/wdsi/definitions.
Invoke-WebRequest -Uri 'https://go.microsoft.com/fwlink/?LinkID=121721&arch=x64' `
    -Outfile $localPathToDefenderUpdate

# Create a session to the maintenance endpoint.
$session = New-PSSession -ComputerName $databaseRPMachine `
    -Credential $vmLocalAdminCreds -ConfigurationName DBAdapterMaintenance
# Copy the defender update file to the adapter VM.
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

## <a name="collect-diagnostic-logs"></a>Recopilación de registros de diagnóstico

Para recopilar registros de la VM bloqueada, se puede usar el punto de conexión *DBAdapterDiagnostics* de Just Enough Administration (JEA) de PowerShell. Este punto de conexión proporciona los comandos siguientes:

- **Get-AzsDBAdapterLog**. Este comando crea un paquete ZIP con los registros de diagnóstico del proveedor de recursos y guarda el archivo en la unidad del usuario de la sesión. Este comando se puede ejecutar sin ningún parámetro y se recopilan los registros de las últimas cuatro horas.
- **Remove-AzsDBAdapterLog**. Este comando quita los paquetes de registros existentes en la máquina virtual del proveedor de recursos.

### <a name="endpoint-requirements-and-process"></a>Requisitos y proceso del punto de conexión

Cuando se instala o actualiza un proveedor de recursos, se crea la cuenta de usuario **dbadapterdiag**. Esta cuenta se usará para recopilar registros de diagnóstico.

>[!NOTE]
>La contraseña de la cuenta dbadapterdiag es la misma que se usa para el administrador local en la VM creada durante la implementación o actualización de un proveedor.

Para usar los comandos *DBAdapterDiagnostics*, cree una sesión remota de PowerShell para la VM del proveedor de recursos y ejecute el comando **Get-AzsDBAdapterLog**.

Establezca el intervalo de tiempo para la recopilación de registros mediante los parámetros **FromDate** y **ToDate**. Si no se especifica uno de estos parámetros (o ambos), se utilizan los valores predeterminados siguientes:

- FromDate es cuatro horas antes de la hora actual.
- ToDate es la hora actual.

**Ejemplo de script de PowerShell para recopilar registros**

El siguiente script muestra cómo recopilar registros de diagnóstico de la máquina virtual del proveedor de recursos.

```powershell
# Create a new diagnostics endpoint session.
$databaseRPMachineIP = '<RP VM IP address>'
$diagnosticsUserName = 'dbadapterdiag'
$diagnosticsUserPassword = '<Enter Diagnostic password>'

$diagCreds = New-Object System.Management.Automation.PSCredential `
        ($diagnosticsUserName, (ConvertTo-SecureString -String $diagnosticsUserPassword -AsPlainText -Force))
$session = New-PSSession -ComputerName $databaseRPMachineIP -Credential $diagCreds `
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

# Clean up the logs.
$cleanup = Invoke-Command -Session $session -ScriptBlock {Remove-AzsDBAdapterLog}
# Close the session.
$session | Remove-PSSession
```
## <a name="configure-azure-diagnostics-extension-for-sql-resource-provider"></a>Configure la extensión Azure Diagnostics para un proveedor de recursos de SQL
La extensión Azure Diagnostics está instalada de manera predeterminada en la máquina virtual del adaptador del proveedor de recursos de SQL. Los siguientes pasos muestran cómo personalizar la extensión para recopilar los registros de eventos de operaciones del proveedor de recursos de SQL y los registros de IIS, con el fin de solucionar problemas o realizar auditorías.

1. Inicie sesión en el Portal de administración de Azure Stack Hub.

2. Select **Máquinas virtuales** en el panel de la izquierda, busque la máquina virtual del adaptador del proveedor de recursos de SQL y selecciónela.

3. En **Configuración de diagnóstico** de la máquina virtual, vaya a la pestaña **Registros** y elija **Personalizado** para personalizar los registros de eventos que se recopilan.
![Vaya a Configuración de diagnóstico](media/azure-stack-sql-resource-provider-maintain/sqlrp-diagnostics-settings.png)

4. Agregue **Microsoft-AzureStack-DatabaseAdapter/Operational!\*** para recopilar los registros de eventos operativos del proveedor de recursos de SQL.
![Agregar registros de eventos](media/azure-stack-sql-resource-provider-maintain/sqlrp-event-logs.png)

5. Para habilitar la colección de registros de IIS, marque **Registros de IIS** y **Error en registros de solicitudes**.
![Agregar registros de IIS](media/azure-stack-sql-resource-provider-maintain/sqlrp-iis-logs.png)

6. Por último, seleccione **Guardar** para guardar la configuración de diagnóstico.

Una vez que la colección de registros de eventos y registros de IIS está configurada para el proveedor de recursos de SQL, los registros se pueden encontrar en una cuenta de almacenamiento del sistema denominada **sqladapterdiagaccount**.

Para más información acerca de la extensión Azure Diagnostics, consulte [Qué es la extensión Azure Diagnostics](/azure/azure-monitor/platform/diagnostics-extension-overview).

## <a name="next-steps"></a>Pasos siguientes

[Adición de servidores de hospedaje de SQL Server](azure-stack-sql-resource-provider-hosting-servers.md)
