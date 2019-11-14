---
title: Actualización del proveedor de recursos de SQL de Azure Stack | Microsoft Docs
description: Aprenda cómo actualizar el proveedor de recursos de SQL de Azure Stack.
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
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: b37e4c9f5e7b1aaa1a476b0665a9558e8e86365f
ms.sourcegitcommit: 102ef41963b5d2d91336c84f2d6af3fdf2ce11c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2019
ms.locfileid: "73955414"
---
# <a name="update-the-sql-resource-provider"></a>Actualización del proveedor de recursos de SQL

*Se aplica a: Sistemas integrados de Azure Stack.*

Es posible que cuando Azure Stack se actualice a una nueva compilación, se lance un nuevo proveedor de recursos de SQL. Aunque el proveedor de recursos existente continúa funcionando, se recomienda actualizar a la compilación más reciente lo antes posible. 

A partir de la versión 1.1.33.0 del proveedor de recursos de SQL, las actualizaciones son acumulativas y no es necesario instalarlas en el orden en el que se publicaron; siempre y cuando empiece desde la versión 1.1.24.0 o posterior. Por ejemplo, si está ejecutando la versión 1.1.24.0 del proveedor de recursos de SQL, puede actualizar a la versión 1.1.33.0 o posterior sin necesidad de instalar primero la versión 1.1.30.0. Para revisar las versiones del proveedor de recursos disponibles y la versión de Azure Stack compatible, consulte la lista de versiones en [Requisitos previos para implementar el proveedor de recursos](./azure-stack-sql-resource-provider-deploy.md#prerequisites).

Para actualizar el proveedor de recursos, use el script *UpdateSQLProvider.ps1*. El script se incluye con la descarga del nuevo proveedor de recursos de SQL. El proceso de actualización es similar al proceso usado para [implementar el proveedor de recursos](./azure-stack-sql-resource-provider-deploy.md). El script de actualización usa los mismos argumentos que el script DeploySqlProvider.ps1, y se deberá proporcionar información del certificado.

 > [!IMPORTANT]
 > Antes de actualizar el proveedor de recursos, revise las notas de la versión para obtener información sobre las nuevas funciones, correcciones y problemas conocidos que podrían afectar a la implementación.

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

| Nombre de parámetro | DESCRIPCIÓN | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios. | _Obligatorio_ |
| **AzCredential** | Las credenciales de la cuenta de administrador del servicio Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack. | _Obligatorio_ |
| **VMLocalCredential** | Credenciales para la cuenta de administrador local de la VM del proveedor de recursos de SQL. | _Obligatorio_ |
| **PrivilegedEndpoint** | Dirección IP o nombre DNS del punto de conexión con privilegios. |  _Obligatorio_ |
| **AzureEnvironment** | El entorno de Azure de la cuenta de administrador de servicio que ha usado para la implementación de Azure Stack. Requerido solo para implementaciones de Azure AD. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una instancia de Azure AD de China, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | También debe incluir el archivo .pfx del certificado en este directorio. | _Opcional para un único nodo, pero obligatorio para varios nodos_ |
| **DefaultSSLCertificatePassword** | Contraseña para el certificado .pfx. | _Obligatorio_ |
| **MaxRetryCount** | El número de veces que quiere volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** |Intervalo de tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Quita el proveedor de recursos y todos los recursos asociados. | Sin |
| **DebugMode** | Impide la limpieza automática en caso de error. | Sin |

## <a name="update-script-powershell-example"></a>Ejemplo de actualización del script de PowerShell
El siguiente es un ejemplo del uso del script *UpdateSQLProvider.ps1* que puede ejecutar desde una consola elevada de PowerShell. Asegúrese de cambiar la información de la variable y las contraseñas según sea necesario:  

> [!NOTE]
> Este proceso de actualización solo se aplica a sistemas integrados de Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but this might have been changed at installation.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service administrator account (this can be Azure AD or AD FS).
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

## <a name="next-steps"></a>Pasos siguientes

[Mantenimiento del proveedor de recursos SQL](azure-stack-sql-resource-provider-maintain.md)
