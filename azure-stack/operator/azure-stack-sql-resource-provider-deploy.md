---
title: Implementación de un proveedor de recursos de SQL Server
titleSuffix: Azure Stack Hub
description: Aprenda a implementar el proveedor de recursos de SQL Server en Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 10/02/2019
ms.lastreviewed: 03/18/2019
ms.author: bryanla
ms.reviewer: xiao
ms.openlocfilehash: bc59808b0b1ebb954812882442cb6dc2d8b02e83
ms.sourcegitcommit: 41195d1ee8ad14eda102cdd3fee3afccf1d83aca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2020
ms.locfileid: "82908480"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack-hub"></a>Implementación del proveedor de recursos de SQL Server en Azure Stack Hub

Use el proveedor de recursos de SQL Server de Azure Stack Hub para exponer las bases de datos SQL como un servicio de Azure Stack Hub. El proveedor de recursos de SQL se ejecuta como un servicio en una máquina virtual (VM) Server Core de Windows Server 2016.

> [!IMPORTANT]
> Solo el proveedor de recursos puede crear elementos en servidores que hospedan SQL o MySQL. Los elementos creados en un servidor host que no se crean con el proveedor de recursos podrían dar lugar a un error de coincidencia de estado.

## <a name="prerequisites"></a>Prerrequisitos

Hay varios requisitos previos que se deben cumplir antes de implementar el proveedor de recursos SQL de Azure Stack Hub. Para cumplir estos requisitos, realice los pasos siguientes en un equipo que pueda acceder a la máquina virtual de punto de conexión con privilegios:

- Si aún no lo ha hecho, [registre Azure Stack Hub](azure-stack-registration.md) en Azure para poder descargar elementos de Azure Marketplace.

- Agregue la máquina virtual de Windows Server Core necesaria a Marketplace de Azure Stack Hub mediante la descarga de la imagen de **Windows Server 2016 Datacenter - Server Core**.

- Descargue el archivo binario del proveedor de recursos SQL y ejecute el extractor automático para extraer el contenido en un directorio temporal. El proveedor de recursos tiene una compilación mínima correspondiente de Azure Stack Hub.

  |Versión mínima de Azure Stack Hub|Versión de SQL RP|
  |-----|-----|
  |Versión 1910 (1.1910.0.58)|[SQL RP, versión 1.1.47.0](https://aka.ms/azurestacksqlrp11470)|
  |Versión 1808 (1.1808.0.97)|[SQL RP versión 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
  |Versión 1808 (1.1808.0.97)|[SQL RP, versión 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|  
  |Versión 1804 (1.0.180513.1)|[SQL RP, versión 1.1.24.0](https://aka.ms/azurestacksqlrp11240)  
  |     |     |

> [!IMPORTANT]
> Antes de implementar la versión 1.1.47.0 del proveedor de recursos de SQL, debe pasar el sistema Azure Stack Hub a la actualización 1910 o versiones posteriores. La versión 1.1.47.0 del proveedor de recursos de SQL no funciona en versiones anteriores de Azure Stack Hub no compatibles.

- Asegúrese de que se cumplen los requisitos previos de la integración del centro de datos:

    |Requisito previo|Referencia|
    |-----|-----|
    |El reenvío condicional de DNS se ha establecido correctamente.|[Integración de Azure Stack Hub en el centro de datos: DNS](azure-stack-integrate-dns.md)|
    |Los puertos de entrada para los proveedores de recursos están abiertos.|[Integración de Azure Stack Hub en el centro de datos: puertos y protocolos entrantes](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Se han establecido correctamente el SAN y el asunto del certificado.|[Requisitos de certificados de infraestructura de clave pública de Azure Stack Hub: certificados obligatorios](azure-stack-pki-certs.md#mandatory-certificates)<br>[Requisitos de certificados de infraestructura de clave pública de Azure Stack Hub: certificados de PaaS opcionales](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

Si trabaja en un escenario desconectado, complete los pasos siguientes para descargar los módulos de PowerShell necesarios y registrar el repositorio manualmente.

1. Inicie sesión en un equipo con conectividad a Internet y use los siguientes scripts para descargar los módulos de PowerShell.

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop

# path to save the packages, c:\temp\azs1.6.0 as an example here
$Path = "c:\temp\azs1.6.0"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
```

2. Después, copie los paquetes descargados en un dispositivo USB.

3. Inicie sesión en la estación de trabajo desconectada y copie los paquetes desde el dispositivo USB en una ubicación en dicha estación de trabajo.

4. Registre esta ubicación como un repositorio local.

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

_Solo para las instalaciones de sistemas integrados_. Debe proporcionar el certificado PKI de PaaS de SQL que se describe en la sección Certificados de PaaS opcionales de [Requisitos de certificados de infraestructura de clave pública de Azure Stack Hub](./azure-stack-pki-certs.md#optional-paas-certificates). Coloque el archivo .pfx en la ubicación especificada por el parámetro **DependencyFilesLocalPath**. No proporcione un certificado para los sistemas ASDK.

## <a name="deploy-the-sql-resource-provider"></a>Implementar el proveedor de recursos SQL

Una vez que haya instalado todos los requisitos previos, ejecute el script **DeploySqlProvider.ps1** desde un equipo que pueda acceder al punto de conexión de administración de recursos de Azure del administrador de Azure Stack Hub y al punto de conexión con privilegios para implementar el proveedor de recursos de SQL. El script DeploySqlProvider.ps1 se extrae como parte del archivo binario del proveedor de recursos de SQL descargado para su versión de Azure Stack Hub.

 > [!IMPORTANT]
 > Antes de implementar el proveedor de recursos, revise las notas de la versión para obtener información sobre las nuevas funciones, correcciones y problemas conocidos que podrían afectar a la implementación.

Para implementar el proveedor de recursos de SQL, abra una **nueva** ventana de PowerShell con privilegios elevados (no de PowerShell ISE) y cambie al directorio en el que ha extraído los archivos binarios del proveedor de recursos de SQL. Se recomienda usar una nueva ventana de PowerShell para evitar posibles problemas ocasionados por los módulos de PowerShell que ya están cargados.

Ejecute el script DeploySqlProvider.ps1, que realiza las tareas siguientes:

- Carga los certificados y otros artefactos en una cuenta de almacenamiento de Azure Stack Hub.
- Publica paquetes de la galería para poder implementar las bases de datos SQL mediante la galería.
- Publica un paquete de galería para implementar los servidores de hospedaje.
- Implementa una máquina virtual mediante la imagen principal de Windows Server 2016 descargada y, luego, instala el proveedor de recursos de SQL.
- Registra un registro de DNS local que se asigna a la VM del proveedor de recursos.
- Registra el proveedor de recursos en la instancia local de Azure Resource Manager para las cuentas de operador.

> [!NOTE]
> Cuando se inicia la implementación del proveedor de recursos de SQL, se crea el grupo de recursos **system.local.sqladapter**. Las implementaciones necesarias para este grupo de recursos pueden tardar hasta 75 minutos en completarse. No debe colocar ningún otro recurso en el grupo de recursos **system.local.sqladapter**.

### <a name="deploysqlproviderps1-parameters"></a>Parámetros de DeploySqlProvider.ps1

Puede especificar los parámetros siguientes en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pedirá que proporcione los parámetros necesarios.

| Nombre de parámetro | Descripción | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | Credencial del administrador de la nube necesaria para el acceso al punto de conexión con privilegios. | _Obligatorio_ |
| **AzCredential** | Credenciales de la cuenta de administrador de servicios de Azure Stack Hub. Use las mismas credenciales que para la implementación de Azure Stack Hub. Se producirá un error en el script si la cuenta que usa con AzCredential requiere autenticación multifactor (MFA).| _Obligatorio_ |
| **VMLocalCredential** | Credenciales de la cuenta de administrador local de la VM del proveedor de recursos SQL. | _Obligatorio_ |
| **PrivilegedEndpoint** | Dirección IP o nombre DNS del punto de conexión con privilegios. |  _Obligatorio_ |
| **AzureEnvironment** | Entorno de Azure de la cuenta de administrador de servicios que se usó para la implementación de Azure Stack Hub. Requerido solo para implementaciones de Azure AD. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una suscripción a Azure Active Directory de China, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | El archivo .pfx de certificados se debe colocar en este directorio, pero solo en los sistemas integrados. También puede copiar un paquete de Windows Update MSU aquí. | _Opcional_ (_obligatorio_ para sistemas integrados) |
| **DefaultSSLCertificatePassword** | Contraseña para el certificado .pfx. | _Obligatorio_ |
| **MaxRetryCount** | El número de veces que quiere volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** | Intervalo de tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes). | No |
| **DebugMode** | Impide la limpieza automática en caso de error. | No |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Implementar el proveedor de recursos SQL con un script personalizado

Si va a implementar la versión 1.1.33.0 del proveedor de recursos de SQL o versiones anteriores, debe instalar versiones específicas de los módulos AzureRm.BootStrapper y Azure Stack Hub en PowerShell. Si va a implementar la versión 1.1.47.0 del proveedor de recursos de SQL, el script de implementación descargará e instalará automáticamente los módulos de PowerShell necesarios en la ruta de acceso C:\Archivos de programa\SqlMySqlPsh.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile, and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack Hub PowerShell
Install-Module -Name AzureRm.BootStrapper -RequiredVersion 0.5.0 -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0
```

> [!NOTE]
> Si trabaja en un escenario desconectado, deberá descargar los módulos de PowerShell necesarios y registrar el repositorio manualmente como requisito previo.

Para eliminar cualquier configuración manual al implementar el proveedor de recursos, puede personalizar el script siguiente. Cambie la información de cuenta predeterminada y las contraseñas según sea necesario para su implementación de Azure Stack Hub.

```powershell
# Use the NetBIOS name for the Azure Stack Hub domain. On the Azure Stack Hub SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS VMs
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack Hub. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local admin account.
$vmLocalAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString 'P@ssw0rd1' -AsPlainText -Force

# For version 1.1.47.0, the PowerShell modules used by the RP deployment are placed in C:\Program Files\SqlMySqlPsh
# The deployment script adds this path to the system $env:PSModulePath to ensure correct modules are used.
$rpModulePath = Join-Path -Path $env:ProgramFiles -ChildPath 'SqlMySqlPsh'
$env:PSModulePath = $env:PSModulePath + ";" + $rpModulePath 

# Change to the directory folder where you extracted the installation files. Don't provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Cuando finalice el script de instalación del proveedor de recursos, actualice el explorador para asegurarse de que puede ver las actualizaciones más recientes y cierre la sesión de PowerShell actual.

## <a name="verify-the-deployment-using-the-azure-stack-hub-portal"></a>Comprobación de la implementación mediante el portal de Azure Stack Hub

Puede usar los pasos siguientes para verificar que el proveedor de recursos de SQL se implementa correctamente.

1. Inicie sesión en el portal de administración como administrador de servicios.
2. Seleccione **Grupos de recursos**.
3. A continuación, seleccione el grupo de recursos **system.\<location\>.sqladapter**.
4. En la página de resumen de la información general del grupo de recursos no debería haber implementaciones con errores.

    ![Comprobación de la implementación del proveedor de recursos de SQL en el portal del administrador de Azure Stack Hub](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)

5. Por último, seleccione **Máquinas virtuales** en el portal de administración para comprobar que la máquina virtual del proveedor de recursos de SQL se ha creado correctamente y está en ejecución.

## <a name="next-steps"></a>Pasos siguientes

[Add hosting servers](azure-stack-sql-resource-provider-hosting-servers.md) (Agregar servidores de hospedaje)
