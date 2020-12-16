---
title: Implementación del proveedor de recursos MySQL en Azure Stack Hub
description: Aprenda a implementar el adaptador del proveedor de recursos MySQL y las bases de datos MySQL como servicio en Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 12/07/2020
ms.author: bryanla
ms.reviewer: caoyang
ms.lastreviewed: 12/07/2020
ms.openlocfilehash: b6d345ecfecaa3859420087bc7cff051b39fbb36
ms.sourcegitcommit: 62eb5964a824adf7faee58c1636b17fedf4347e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/07/2020
ms.locfileid: "96778162"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack-hub"></a>Implementación del proveedor de recursos MySQL en Azure Stack Hub

Use el proveedor de recursos MySQL Server para ofrecer las bases de datos de MySQL como un servicio de Azure Stack Hub. El proveedor de recursos de MySQL se ejecuta como un servicio en una máquina virtual de Windows Server 2016 Server Core (para una versión del adaptador < = 1.1.47.0 >) o en una instancia del complemento RP para Windows Server (para una versión del adaptador > = 1.1.93.0).

> [!IMPORTANT]
> Solo el proveedor de recursos debe crear elementos en servidores que hospedan SQL o MySQL. No se admiten los elementos creados en un servidor host que no se crean con el proveedor de recursos, y dichos elementos podrían dar lugar a un error de coincidencia de estado.

## <a name="prerequisites"></a>Prerrequisitos

Hay varios requisitos previos que se deben cumplir antes de implementar el proveedor de recursos MySQL en Azure Stack Hub:

- Necesitará un equipo y una cuenta con acceso:
   - al [portal de administración de Azure Stack Hub](azure-stack-manage-portals.md).
   - al [punto de conexión con privilegios](azure-stack-privileged-endpoint.md).
   - al punto de conexión de administración de Azure Resource Manager, `https://management.region.<fqdn>`, donde `<fqdn>` es el nombre de dominio completo (o `https://management.local.azurestack.external` si se usa ASDK)
   - a Internet, si Azure Stack Hub se implementó para que usara Azure Active Directory (AD) como proveedor de identidades.

- Si aún no lo ha hecho, [registre Azure Stack Hub](azure-stack-registration.md) en Azure para poder descargar elementos de Azure Marketplace.

- Agregue la máquina virtual de Windows Server necesaria a Marketplace de Azure Stack Hub.
  - Para una versión <= 1.1.47.0 de MySQL RP, descargue la imagen de **Windows Server 2016 Datacenter - Server Core**.
  - Para una versión > = 1.1.93.0 de MySQL RP, descargue la imagen de **Complemento de Microsoft Azure Stack RP Windows Server (SOLO INTERNO)** . Esta versión de Windows Server está especializada en la infraestructura del complemento Azure Stack RP y no es visible para el marketplace de inquilinos.

- Descargue la versión compatible del archivo binario del proveedor de recursos de MySQL según la tabla de asignación de versiones siguiente. Ejecute el autoextractor para extraer el contenido descargado en un directorio temporal. 

  |Versión de Azure Stack Hub compatible|MySQL RP, versión|Windows Server en el que se está ejecutando el servicio RP
  |-----|-----|-----|
  |2008, 2005|[MySQL RP, versión 1.1.93.0](https://aka.ms/azshmysqlrp11930)|Complemento de Microsoft Azure Stack RP Windows Server (SOLO INTERNO)
  |2005, 2002, 1910|[MySQL RP, versión 1.1.47.0](https://aka.ms/azurestackmysqlrp11470)|Windows Server 2016 Datacenter - Server Core|
  |1908|[MySQL RP, versión 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|Windows Server 2016 Datacenter - Server Core|
  |     |     |     |

>[!NOTE]
>Para implementar el proveedor MySQL en un sistema sin acceso a Internet, copie el archivo [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) en una ruta de acceso local. Proporcione el nombre de ruta de acceso mediante el parámetro **DependencyFilesLocalPath**.


- Asegúrese de que se cumplen los requisitos previos de la integración del centro de datos:

    |Requisito previo|Referencia|
    |-----|-----|
    |El reenvío condicional de DNS se ha establecido correctamente.|[Integración de Azure Stack Hub en el centro de datos: DNS](azure-stack-integrate-dns.md)|
    |Los puertos de entrada para los proveedores de recursos están abiertos.|[Integración de Azure Stack Hub en el centro de datos: publicar puntos de conexión](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Se han establecido correctamente el SAN y el asunto del certificado.|[Requisitos de certificados de infraestructura de clave pública de Azure Stack Hub: certificados obligatorios](azure-stack-pki-certs.md)[Requisitos de certificados de PaaS de Azure Stack Hub: certificados opcionales](azure-stack-pki-certs.md)|
    |     |     |

Si trabaja en un escenario desconectado, complete los pasos siguientes para descargar los módulos de PowerShell necesarios y registrar el repositorio manualmente.

1. Inicie sesión en un equipo con conectividad a Internet y use los siguientes scripts para descargar los módulos de PowerShell.

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop

# path to save the packages, c:\temp\azs1.6.0 as an example here
$Path = "c:\temp\azs1.6.0"
```

2. En función de la versión del proveedor de recursos que esté implementando, ejecute uno de estos scripts.

```powershell
# for resource provider version >= 1.1.93.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.2
```
```powershell
# for resource provider version <= 1.1.47.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
```

3. Después, copie los paquetes descargados en un dispositivo USB.

4. Inicie sesión en la estación de trabajo desconectada y copie los paquetes desde el dispositivo USB en una ubicación en dicha estación de trabajo.

5. Registre esta ubicación como un repositorio local.

```powershell
# requires -Version 5
# requires -RunAsAdministrator
# requires -Module PowerShellGet
# requires -Module PackageManagement

$SourceLocation = "C:\temp\azs1.6.0"
$RepoName = "azs1.6.0"

Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

New-Item -Path $env:ProgramFiles -name "SqlMySqlPsh" -ItemType "Directory" 
```

### <a name="certificates"></a>Certificados

_Solo para las instalaciones de sistemas integrados_. Debe proporcionar el certificado PKI de PaaS de SQL que se describe en la sección Certificados de PaaS opcionales de [Requisitos de certificados de infraestructura de clave pública de Azure Stack Hub](./azure-stack-pki-certs.md). Coloque el archivo .pfx en la ubicación especificada por el parámetro **DependencyFilesLocalPath**. No proporcione un certificado para los sistemas ASDK.

## <a name="deploy-the-resource-provider"></a>Implementar el proveedor de recursos

Una vez que haya completado todos los requisitos previos, ejecute el script **DeployMySqlProvider.ps1** desde un equipo que pueda acceder al punto de conexión de administración de Azure Resource Manager para Azure Stack Hub y al punto de conexión con privilegios para implementar el proveedor de recursos de MySQL. El script DeployMySqlProvider.ps1 se extrae como parte de los archivos de instalación del proveedor de recursos MySQL descargado para su versión de Azure Stack Hub.

 > [!IMPORTANT]
 > Antes de implementar el proveedor de recursos, revise las notas de la versión para obtener información sobre las nuevas funciones, correcciones y problemas conocidos que podrían afectar a la implementación.

Para implementar el proveedor de recursos de MySQL, abra una **nueva** ventana de PowerShell con privilegios elevados (no de PowerShell ISE) y cambie al directorio en el que ha extraído los archivos binarios del proveedor de recursos de MySQL. 

> [!IMPORTANT]
> Le recomendamos que use los comandos **Clear-AzureRmContext -Scope CurrentUser** y **Clear-AzureRmContext -Scope Process** para borrar la memoria caché antes de ejecutar el script de actualización.

Ejecute el script **DeployMySqlProvider.ps1**, que realiza las tareas siguientes:

* Carga los certificados y otros artefactos en una cuenta de almacenamiento de Azure Stack Hub.
* Publica paquetes de la galería para poder implementar las bases de datos MySQL mediante esta.
* Publica un paquete de galería para implementar los servidores de hospedaje.
* Implementa una máquina virtual mediante la imagen principal de Windows Server 2016 o del complemento Microsoft Azure Stack RP Windows Server y, luego, instala el proveedor de recursos de MySQL.
* Registra un registro de DNS local que se asigna a la VM del proveedor de recursos.
* Registra el proveedor de recursos en la instancia local de Azure Resource Manager para las cuentas de operador.

> [!NOTE]
> Cuando se inicia la implementación del proveedor de recursos MySQL, se crea el grupo de recursos **system.local.mysqladapter**. Las implementaciones necesarias de este grupo de recursos pueden tardar hasta 75 minutos en finalizar. No debe colocar ningún otro recurso en el grupo de recursos **system.local.mysqladapter**.

### <a name="deploymysqlproviderps1-parameters"></a>Parámetros de DeployMySqlProvider.ps1

Puede especificar estos parámetros en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pedirá que proporcione los parámetros necesarios.

| Nombre de parámetro | Descripción | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios. | _Obligatorio_ |
| **AzCredential** | Credenciales de la cuenta de administrador de servicios de Azure Stack Hub. Use las mismas credenciales que para la implementación de Azure Stack Hub. Se producirá un error en el script si la cuenta que usa con AzCredential requiere autenticación multifactor (MFA). | _Obligatorio_ |
| **VMLocalCredential** | Las credenciales para la cuenta de administrador local de la VM del proveedor de recursos de MySQL. | _Obligatorio_ |
| **PrivilegedEndpoint** | Dirección IP o nombre DNS del punto de conexión con privilegios. |  _Obligatorio_ |
| **AzureEnvironment** | Entorno de Azure de la cuenta de administrador de servicios que se usó para la implementación de Azure Stack Hub. Requerido solo para implementaciones de Azure AD. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una instancia de Azure AD de China, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | El archivo .pfx de certificados se debe colocar en este directorio, pero solo en los sistemas integrados. En el caso de entornos desconectados, descargue [mysql-connector-net-6.10.5](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) a este directorio. También puede copiar un paquete de Windows Update MSU aquí. | _Opcional_ (_obligatorio_ para sistemas integrados o entornos desconectados) |
| **DefaultSSLCertificatePassword** | Contraseña para el certificado .pfx. | _Obligatorio_ |
| **MaxRetryCount** | El número de veces que quiere volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** | Intervalo de tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes). | No |
| **DebugMode** | Impide la limpieza automática en caso de error. | No |
| **AcceptLicense** | Omite el aviso para aceptar la licencia GPL.  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Implementación del proveedor de recursos MySQL con un script personalizado

Si va a implementar la versión 1.1.33.0 del proveedor de recursos MySQL o versiones anteriores, debe instalar versiones específicas de los módulos AzureRm.BootStrapper y Azure Stack Hub en PowerShell. Si va a implementar la versión 1.1.47.0, u otra posterior, del proveedor de recursos de MySQL, el script de implementación descargará e instalará automáticamente los módulos de PowerShell necesarios en la ruta de acceso C:\Archivos de programa\SqlMySqlPsh.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> Si trabaja en un escenario desconectado, deberá descargar los módulos de PowerShell necesarios y registrar el repositorio manualmente como requisito previo.

Para eliminar cualquier configuración manual al implementar el proveedor de recursos, puede personalizar el script siguiente. Cambie la información de cuenta predeterminada y las contraseñas según sea necesario para su implementación de Azure Stack Hub.

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
$AdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local admin account
$vmLocalAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force

# For version 1.1.47.0 or later, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh,
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath

# Change to the directory folder where you extracted the installation files. Don't provide a certificate on ASDK!
. $tempDir\DeployMySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert `
    -AcceptLicense

```

Cuando finalice el script de instalación del proveedor de recursos, actualice el explorador para asegurarse de que puede ver las actualizaciones más recientes y cierre la sesión de PowerShell actual.

## <a name="verify-the-deployment-by-using-the-azure-stack-hub-portal"></a>Comprobación de la implementación mediante el portal de Azure Stack Hub

1. Inicie sesión en el portal de administración como administrador de servicios.
2. Seleccione **Grupos de recursos**.
3. Seleccione el grupo de recursos **system.\<location\>.mysqladapter**.
4. En la página de resumen de la información general del grupo de recursos no debería haber implementaciones con errores.
5. Por último, seleccione **Máquinas virtuales** en el portal de administración para comprobar que la VM del proveedor de recursos MySQL se ha creado correctamente y está en ejecución.

## <a name="next-steps"></a>Pasos siguientes

[Add hosting servers](azure-stack-mysql-resource-provider-hosting-servers.md) (Agregar servidores de hospedaje)
