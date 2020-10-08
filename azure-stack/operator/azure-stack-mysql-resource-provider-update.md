---
title: Actualización del proveedor de recursos MySQL en Azure Stack Hub
description: Aprenda cómo actualizar el proveedor de recursos MySQL de Azure Stack Hub en Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 9/22/2020
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 01/11/2020
ms.openlocfilehash: 93fcbd61003164a959a15c8c6108bc81dabe2b8a
ms.sourcegitcommit: 69cfff119ab425d0fbb71e38d1480d051fc91216
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/30/2020
ms.locfileid: "91572898"
---
# <a name="update-the-mysql-resource-provider-in-azure-stack-hub"></a>Actualización del proveedor de recursos MySQL en Azure Stack Hub

> [!IMPORTANT]
> Antes de actualizar el proveedor de recursos, revise las notas de la versión para obtener información sobre las nuevas funciones, correcciones y problemas conocidos que podrían afectar a la implementación. Las notas de la versión también especifican la versión mínima de Azure Stack Hub requerida para el proveedor de recursos.

Cuando las compilaciones de Azure Stack Hub se actualicen, podría lanzarse un nuevo adaptador del proveedor de recursos de MySQL. Aunque el adaptador existente continúa funcionando, se recomienda actualizar a la compilación más reciente lo antes posible.

  |Versión de Azure Stack Hub compatible|MySQL RP, versión|Windows Server en el que se está ejecutando el servicio RP
  |-----|-----|-----|
  |2005|[MySQL RP, versión 1.1.93.0](https://aka.ms/azshmysqlrp11930)|Complemento de Microsoft Azure Stack RP Windows Server (SOLO INTERNO)
  |2005, 2002, 1910|[MySQL RP, versión 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|Windows Server 2016 Datacenter - Server Core|
  |1908|[MySQL RP, versión 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|Windows Server 2016 Datacenter - Server Core|
  |     |     |     |

La actualización del proveedor de recursos de MySQL es acumulativa. Al actualizar desde una versión anterior, puede actualizar directamente a la versión más reciente. 

Para actualizar el proveedor de recursos, use el script **UpdateMySQLProvider.ps1**. Use la cuenta de servicio con derechos de administrador local y de **propietario** de la suscripción. El script de actualización se incluye con la descarga del proveedor de recursos. 

El proceso de actualización es similar al proceso usado para [implementar el proveedor de recursos](./azure-stack-mysql-resource-provider-deploy.md). El script de actualización usa los mismos argumentos que el script DeployMySqlProvider.ps1, y se deberá proporcionar información del certificado.

## <a name="update-script-processes"></a>Procesos de script de actualización

El script **UpdateMySQLProvider.ps1** crea una nueva máquina virtual con la imagen más reciente del sistema operativo, implementa el código del proveedor de recursos más reciente y migra la configuración del proveedor de recursos antiguo al nuevo.

>[!NOTE]
>Se recomienda que descargue la imagen de Windows Server 2016 Core más reciente o la del complemento Microsoft Azure Stack RP Windows Server de Marketplace Management (Administración de Marketplace). Si tiene que instalar una actualización, puede colocar un **único** paquete MSU en la ruta de acceso local de la dependencia. El script dará error si hay más de un archivo MSU en esta ubicación.

Cuando el script *UpdateMySQLProvider.ps1* crea una nueva máquina virtual, también migra la siguiente configuración desde la máquina virtual del proveedor antiguo:

* La información de base de datos
* La información del servidor de hospedaje
* El registro DNS necesario

## <a name="update-script-parameters"></a>Actualización de los parámetros de script 
Puede especificar los siguientes parámetros desde la línea de comandos al ejecutar el script **UpdateMySQLProvider.ps1** de PowerShell. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pedirá que proporcione los parámetros necesarios.

| Nombre de parámetro | Descripción | Comentario o valor predeterminado | 
| --- | --- | --- | 
| **CloudAdminCredential** | Credencial del administrador de la nube necesaria para el acceso al punto de conexión con privilegios. | _Obligatorio_ | 
| **AzCredential** | Credenciales de la cuenta de administrador de servicios de Azure Stack Hub. Use las mismas credenciales que para la implementación de Azure Stack Hub. Se producirá un error en el script si la cuenta que usa con AzCredential requiere autenticación multifactor (MFA). | _Obligatorio_ | 
| **VMLocalCredential** |Credenciales de la cuenta de administrador local de la VM del proveedor de recursos SQL. | _Obligatorio_ | 
| **PrivilegedEndpoint** | Dirección IP o nombre DNS del punto de conexión con privilegios. |  _Obligatorio_ | 
| **AzureEnvironment** | Entorno de Azure de la cuenta de administrador de servicios que se usó para la implementación de Azure Stack Hub. Requerido solo para implementaciones de Azure AD. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una instancia de Azure AD de China, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | El archivo .pfx de certificados se debe colocar también en este directorio. | _Opcional_ (_obligatorio_ para varios nodos) | 
| **DefaultSSLCertificatePassword** | Contraseña para el certificado .pfx. | _Obligatorio_ | 
| **MaxRetryCount** | El número de veces que quiere volver a intentar cada operación si se produce un error.| 2 | 
| **RetryDuration** | Intervalo de tiempo de expiración entre reintentos, en segundos. | 120 | 
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes). | No | 
| **DebugMode** | Impide la limpieza automática en caso de error. | No | 
| **AcceptLicense** | Omite el aviso para aceptar la licencia GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Ejemplo de actualización del script

Si va a actualizar el proveedor de recursos MySQL a la versión 1.1.33.0 o versiones anteriores, debe instalar versiones específicas de los módulos AzureRm.BootStrapper y Azure Stack Hub en PowerShell. 

Si va a actualizar el proveedor de recursos de MySQL a la versión 1.1.47.0 u otra posterior, este paso se puede omitir. El script de implementación descargará e instalará automáticamente los módulos de PowerShell necesarios en la ruta de acceso C:\Archivos de programa\SqlMySqlPsh. 

>[!NOTE]
>Si la carpeta C:\Archivos de programa\SqlMySqlPsh ya existe con el módulo de PowerShell descargado, se recomienda limpiar esta carpeta antes de ejecutar el script de actualización. Esto se hace para asegurarse de que se descarga y se usa la versión correcta del módulo de PowerShell.

```powershell 
# Run the following scripts when updating to version 1.1.33.0 only.
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> Si trabaja en un escenario desconectado, deberá descargar los módulos de PowerShell necesarios y registrar el repositorio manualmente como requisito previo. Puede obtener más información en [Implementación del proveedor de recursos MySQL](azure-stack-mysql-resource-provider-deploy.md).

El siguiente es un ejemplo que muestra el script *UpdateMySQLProvider.ps1* que puede ejecutar desde una consola elevada de PowerShell. Asegúrese de cambiar la información de la variable y las contraseñas según sea necesario:

```powershell 
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted. 
$tempDir = 'C:\TEMP\MYSQLRP' 

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com" 
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass) 
 
# Set credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass) 
 
# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass) 

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force 

# For version 1.1.47.0 or later, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath 

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
.$tempDir\UpdateMySQLProvider.ps1 -AzCredential $AdminCreds `
-VMLocalCredential $vmLocalAdminCreds `
-CloudAdminCredential $cloudAdminCreds `
-PrivilegedEndpoint $privilegedEndpoint `
-AzureEnvironment $AzureEnvironment `
-DefaultSSLCertificatePassword $PfxPass `
-DependencyFilesLocalPath $tempDir\cert `
-AcceptLicense
```  

Cuando finalice el script de actualización del proveedor de recursos, cierre la sesión actual de PowerShell.

## <a name="next-steps"></a>Pasos siguientes
[Mantenimiento del proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-maintain.md)
