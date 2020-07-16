---
title: Actualización del proveedor de recursos de SQL de Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Aprenda a actualizar el proveedor de recursos de SQL de Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 11/11/2019
ms.author: bryanla
ms.reviewer: xiaofmao
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: 5f714d5ae2a73a026268a9585cab17ffee14f25b
ms.sourcegitcommit: c1f48c19c8a9c438fd22298bc570c12a9b19bb45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2020
ms.locfileid: "86410630"
---
# <a name="update-the-sql-resource-provider"></a>Actualización del proveedor de recursos de SQL

> [!IMPORTANT]
> Antes de actualizar el proveedor de recursos, revise las notas de la versión para obtener información sobre las nuevas funciones, correcciones y problemas conocidos que podrían afectar a la implementación. Las notas de la versión también especifican la versión mínima de Azure Stack Hub requerida para el proveedor de recursos.

Es posible que cuando Azure Stack Hub se actualice a una nueva compilación, se lance un nuevo proveedor de recursos de SQL. Aunque el proveedor de recursos existente continúa funcionando, se recomienda actualizar a la compilación más reciente lo antes posible.

A partir de la versión 1.1.33.0 del proveedor de recursos de SQL, las actualizaciones son acumulativas y no es necesario instalarlas en el orden en el que se han publicado, siempre y cuando empiece desde la versión 1.1.24.0 o posterior. Por ejemplo, si está ejecutando la versión 1.1.24.0 del proveedor de recursos de SQL, puede actualizar a la versión 1.1.33.0 o posterior sin necesidad de instalar primero la versión 1.1.30.0. Para revisar las versiones disponibles del proveedor de recursos y la versión de Azure Stack Hub compatible, consulte la lista de versiones en la sección Requisitos previos de [Implementación del proveedor de recursos de SQL Server en Azure Stack Hub](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Para actualizar el proveedor de recursos, use el script *UpdateSQLProvider.ps1*. Use la cuenta de servicio con derechos de administrador local y de **propietario** de la suscripción. El script se incluye con la descarga del nuevo proveedor de recursos de SQL. El proceso de actualización es similar al proceso usado para [implementar el proveedor de recursos](./azure-stack-sql-resource-provider-deploy.md). El script de actualización usa los mismos argumentos que el script DeploySqlProvider.ps1, y se deberá proporcionar información del certificado.

## <a name="update-script-processes"></a>Procesos de script de actualización

El script *UpdateSQLProvider.ps1* crea una nueva máquina virtual (VM) con el código del proveedor de recursos más reciente.

> [!NOTE]
> Se recomienda descargar la imagen de Windows Server 2016 Core más reciente de Marketplace Management (Administración de Marketplace). Si tiene que instalar una actualización, puede colocar un **único** paquete MSU en la ruta de acceso local de la dependencia. El script dará error si hay más de un archivo MSU en esta ubicación.

Después de que el script *UpdateSQLProvider.ps1* crea una nueva máquina virtual, migra la siguiente configuración desde la máquina virtual del proveedor antiguo:

* La información de base de datos
* La información del servidor de hospedaje
* El registro DNS necesario

## <a name="update-script-parameters"></a>Actualización de los parámetros de script

Puede especificar los siguientes parámetros desde la línea de comandos al ejecutar el script **UpdateSQLProvider.ps1** de PowerShell. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pedirá que proporcione los parámetros necesarios.

| Nombre de parámetro | Descripción | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | Credencial del administrador de la nube necesaria para el acceso al punto de conexión con privilegios. | _Obligatorio_ |
| **AzCredential** | Credenciales de la cuenta de administrador de servicios de Azure Stack Hub. Use las mismas credenciales que para la implementación de Azure Stack Hub. | _Obligatorio_ |
| **VMLocalCredential** | Credenciales de la cuenta de administrador local de la VM del proveedor de recursos SQL. | _Obligatorio_ |
| **PrivilegedEndpoint** | Dirección IP o nombre DNS del punto de conexión con privilegios. |  _Obligatorio_ |
| **AzureEnvironment** | El entorno de Azure de la cuenta de administrador de servicios que ha usado para la implementación de Azure Stack Hub. Requerido solo para implementaciones de Azure AD. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una instancia de Azure AD de China, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | También debe incluir el archivo .pfx del certificado en este directorio. | _Opcional para un único nodo, pero obligatorio para varios nodos_ |
| **DefaultSSLCertificatePassword** | Contraseña para el certificado .pfx. | _Obligatorio_ |
| **MaxRetryCount** | El número de veces que quiere volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** |Intervalo de tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Quita el proveedor de recursos y todos los recursos asociados. | No |
| **DebugMode** | Impide la limpieza automática en caso de error. | No |

## <a name="update-script-powershell-example"></a>Ejemplo de actualización del script de PowerShell
> [!NOTE]
> Este proceso de actualización solo se aplica a sistemas integrados de Azure Stack Hub.

Si va a actualizar a la versión 1.1.33.0 del proveedor de recursos de SQL o versiones anteriores, debe instalar versiones específicas de los módulos AzureRm.BootStrapper y Azure Stack Hub en PowerShell. Si va a actualizar a la versión 1.1.47.0 del proveedor de recursos de SQL, el script de implementación descargará e instalará automáticamente los módulos de PowerShell necesarios en la ruta de acceso C:\Archivos de programa\SqlMySqlPsh.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module.
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> Si trabaja en un escenario desconectado, deberá descargar los módulos de PowerShell necesarios y registrar el repositorio manualmente como requisito previo. Puede obtener más información en [Implementación del proveedor de recursos de SQL Server en Azure Stack Hub](azure-stack-sql-resource-provider-deploy.md).

El siguiente es un ejemplo del uso del script *UpdateSQLProvider.ps1* que puede ejecutar desde una consola elevada de PowerShell. Asegúrese de cambiar la información de la variable y las contraseñas según sea necesario:  

```powershell
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account (this can be Azure AD or AD FS).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath

# Change directory to the folder where you extracted the installation files.
# Then adjust the endpoints.
. $tempDir\UpdateSQLProvider.ps1 -AzCredential $AdminCreds `
  -VMLocalCredential $vmLocalAdminCreds `
  -CloudAdminCredential $cloudAdminCreds `
  -PrivilegedEndpoint $privilegedEndpoint `
  -AzureEnvironment $AzureEnvironment ` 
  -DefaultSSLCertificatePassword $PfxPass `
  -DependencyFilesLocalPath $tempDir\cert 

 ```

Cuando finalice el script de actualización del proveedor de recursos, cierre la sesión actual de PowerShell.

## <a name="next-steps"></a>Pasos siguientes

[Mantenimiento del proveedor de recursos SQL](azure-stack-sql-resource-provider-maintain.md)
