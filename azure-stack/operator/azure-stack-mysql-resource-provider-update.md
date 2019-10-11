---
title: Actualización del proveedor de recursos de MySQL de Azure Stack | Microsoft Docs
description: Aprenda cómo actualizar el proveedor de recursos de MySQL de Azure Stack.
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
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 4e482be6cc47ce79b299d77592436b70420978ea
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2019
ms.locfileid: "71908982"
---
# <a name="update-the-mysql-resource-provider"></a>Actualización del proveedor de recursos de MySQL 

*Se aplica a: Sistemas integrados de Azure Stack.*

Cuando las compilaciones de Azure Stack se actualicen, podría lanzarse un nuevo adaptador del proveedor de recursos de MySQL. Aunque el adaptador existente continúa funcionando, se recomienda actualizar a la compilación más reciente lo antes posible. 

A partir de la versión 1.1.33.0 del proveedor de recursos de MySQL, las actualizaciones son acumulativas y no es necesario instalarlas en el orden en el que se publicaron; siempre y cuando empiece desde la versión 1.1.24.0 o posterior. Por ejemplo, si está ejecutando la versión 1.1.24.0 del proveedor de recursos de MySQL, puede actualizar a la versión 1.1.33.0 o posterior sin necesidad de instalar primero la versión 1.1.30.0. Para revisar las versiones del proveedor de recursos disponibles y la versión de Azure Stack compatible, consulte la lista de versiones en [Requisitos previos para implementar el proveedor de recursos](./azure-stack-mysql-resource-provider-deploy.md#prerequisites).

Para actualizar el proveedor de recursos, use el script **UpdateMySQLProvider.ps1**. El procedimiento es similar al usado para instalar un proveedor de recursos, como se describe en la sección Implementar el proveedor de recursos de este artículo. El script se incluye con la descarga del proveedor de recursos. 

 > [!IMPORTANT]
 > Antes de actualizar el proveedor de recursos, revise las notas de la versión para obtener información sobre las nuevas funciones, correcciones y problemas conocidos que podrían afectar a la implementación.

## <a name="update-script-processes"></a>Procesos de script de actualización

El script **UpdateMySQLProvider.ps1** crea una nueva máquina virtual con el código del proveedor de recursos más reciente y migra la configuración de la máquina virtual antigua a la nueva. La configuración que se migra incluye información de base de datos y del servidor de hospedaje, así como el registro DNS necesario. 

>[!NOTE]
>Se recomienda descargar la imagen de Windows Server 2016 Core más reciente de Marketplace Management (Administración de Marketplace). Si tiene que instalar una actualización, puede colocar un **único** paquete MSU en la ruta de acceso local de la dependencia. El script dará error si hay más de un archivo MSU en esta ubicación.

El script requiere el uso de los mismos argumentos que se describen para el script DeployMySqlProvider.ps1. Proporcione aquí también el certificado.  


## <a name="update-script-parameters"></a>Actualización de los parámetros de script 
Puede especificar los siguientes parámetros desde la línea de comandos al ejecutar el script **UpdateMySQLProvider.ps1** de PowerShell. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pedirá que proporcione los parámetros necesarios. 

| Nombre de parámetro | DESCRIPCIÓN | Comentario o valor predeterminado | 
| --- | --- | --- | 
| **CloudAdminCredential** | Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios. | _Obligatorio_ | 
| **AzCredential** | Credenciales de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack. | _Obligatorio_ | 
| **VMLocalCredential** |Credenciales para la cuenta de administrador local de la VM del proveedor de recursos de SQL. | _Obligatorio_ | 
| **PrivilegedEndpoint** | Dirección IP o nombre DNS del punto de conexión con privilegios. |  _Obligatorio_ | 
| **AzureEnvironment** | El entorno de Azure de la cuenta de administrador de servicio que ha usado para la implementación de Azure Stack. Requerido solo para implementaciones de Azure AD. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una instancia de Azure AD de China, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | El archivo .pfx de certificados se debe colocar también en este directorio. | _Opcional_ (_obligatorio_ para varios nodos) | 
| **DefaultSSLCertificatePassword** | Contraseña para el certificado .pfx. | _Obligatorio_ | 
| **MaxRetryCount** | Número de veces que quiere volver a intentar cada operación si se produce un error.| 2 | 
| **RetryDuration** | Intervalo de tiempo de expiración entre reintentos, en segundos. | 120 | 
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes). | Sin | 
| **DebugMode** | Impide la limpieza automática en caso de error. | Sin | 
| **AcceptLicense** | Omite el aviso para aceptar la licencia GPL.  (https://www.gnu.org/licenses/old-licenses/gpl-2.0.html) | | 

## <a name="update-script-example"></a>Ejemplo de actualización del script
El siguiente es un ejemplo del uso del script *UpdateMySQLProvider.ps1* que puede ejecutar desde una consola elevada de PowerShell. Asegúrese de cambiar la información de la variable y las contraseñas según sea necesario:  

> [!NOTE] 
> El proceso de actualización solo se aplica a sistemas integrados. 

```powershell 
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell.
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time. 
$domain = "AzureStack" 

# For integrated systems, use the IP address of one of the ERCS virtual machines 
$privilegedEndpoint = "AzS-ERCS01" 

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
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

## <a name="next-steps"></a>Pasos siguientes
[Mantenimiento del proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-maintain.md)
