---
title: Uso de bases de datos MySQL en Azure Stack | Microsoft Docs
description: Aprenda a implementar bases de datos MySQL como servicio en Azure Stack y los pasos para implementar de forma rápida el adaptador del proveedor de recursos de MySQL Server.
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
ms.date: 03/29/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: 197c5002378be8229a6ec60fc0f3d7659b7c56b9
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618152"
---
# <a name="deploy-the-mysql-resource-provider-on-azure-stack"></a>Implementación del proveedor de recursos MySQL en Azure Stack

Use el proveedor de recursos MySQL Server de Azure Stack para exponer las bases de datos MySQL como un servicio de Azure Stack. El proveedor de recursos MySQL se ejecuta como un servicio en una máquina virtual (VM) Server Core de Windows Server 2016.

> [!IMPORTANT]
> Solo el proveedor de recursos puede crear elementos en servidores que hospedan SQL o MySQL. Los elementos creados en un servidor host que no se crean con el proveedor de recursos podrían dar lugar a un error de coincidencia de estado.

## <a name="prerequisites"></a>Requisitos previos

Hay varios requisitos previos que se deben cumplir antes de implementar el proveedor de recursos MySQL en Azure Stack. Para cumplir estos requisitos, realice los pasos de este artículo en un equipo que pueda acceder a la máquina virtual de punto de conexión con privilegios.

* Si aún no lo ha hecho, [registre Azure Stack](./azure-stack-registration.md) en Azure para poder descargar elementos de Azure Marketplace.
* Debe instalar los módulos de Azure y Azure Stack PowerShell en el sistema donde se ejecutará esta instalación. Dicho sistema debe ser una imagen de Windows 10 o Windows Server 2016 con la versión más reciente del entorno de ejecución de .NET. Consulte [Instalación de PowerShell para Azure Stack](./azure-stack-powershell-install.md).
* Agregue la máquina virtual Windows Server Core a Marketplace de Azure Stack mediante la descarga de la imagen **Windows Server 2016 Datacenter - Server Core**.

* Descargue el archivo binario del proveedor de recursos MySQL y ejecute el extractor automático para extraer el contenido en un directorio temporal.

  >[!NOTE]
  >Para implementar el proveedor MySQL en un sistema sin acceso a Internet, copie el archivo [mysql-connector-net-6.10.5.msi](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) en una ruta de acceso local. Proporcione el nombre de ruta de acceso mediante el parámetro **DependencyFilesLocalPath**.

* El proveedor de recursos tiene una compilación mínima correspondiente de Azure Stack.

  |Versión mínima de Azure Stack|MySQL RP, versión|
  |-----|-----|
  |Versión 1808 (1.1808.0.97)|[MySQL RP, versión 1.1.33.0](https://aka.ms/azurestackmysqlrp11330)|  
  |Versión 1808 (1.1808.0.97)|[MySQL RP, versión 1.1.30.0](https://aka.ms/azurestackmysqlrp11300)|
  |Versión 1804 (1.0.180513.1)|[MySQL RP, versión 1.1.24.0](https://aka.ms/azurestackmysqlrp11240)
  |     |     |

* Asegúrese de que se cumplen los requisitos previos de la integración del centro de datos:

    |Requisito previo|Referencia|
    |-----|-----|
    |El reenvío condicional de DNS se ha establecido correctamente.|[Integración del centro de datos de Azure Stack: DNS](azure-stack-integrate-dns.md)|
    |Los puertos de entrada para los proveedores de recursos están abiertos.|[Integración del centro de datos de Azure Stack: publicar puntos de conexión](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Se han establecido correctamente el SAN y el asunto del certificado.|[Requisitos previos de PKI obligatorios para la implementación de Azure Stack](azure-stack-pki-certs.md#mandatory-certificates)[Requisitos previos de certificado de PaaS para la implementación de Azure Stack](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Certificados

_Solo para las instalaciones de sistemas integrados_. Debe proporcionar el certificado PKI de PaaS de SQL que se describe en la sección de certificados de PaaS opcionales de los [requisitos de PKI de la implementación de Azure Stack](./azure-stack-pki-certs.md#optional-paas-certificates). Coloque el archivo .pfx en la ubicación especificada por el parámetro **DependencyFilesLocalPath**. No proporcione un certificado para los sistemas ASDK.

## <a name="deploy-the-resource-provider"></a>Implementar el proveedor de recursos

Una vez instalados todos los requisitos previos, ejecute el script **DeployMySqlProvider.ps1** para implementar el proveedor de recursos de MySQL. El script DeployMySqlProvider.ps1 se extrae como parte de los archivos de instalación del proveedor de recursos MySQL descargado para su versión de Azure Stack.

 > [!IMPORTANT]
 > Antes de implementar el proveedor de recursos, revise las notas de la versión para obtener información sobre las nuevas funciones, correcciones y problemas conocidos que podrían afectar a la implementación.

Para implementar el proveedor de recursos de MySQL, abra una nueva ventana de PowerShell con privilegios elevados (no de PowerShell ISE) y cambie al directorio en el que ha extraído los archivos binarios del proveedor de recursos de MySQL. Se recomienda usar una nueva ventana de PowerShell para evitar posibles problemas ocasionados por los módulos de PowerShell que ya están cargados.

Ejecute el script **DeployMySqlProvider.ps1**, que realiza las tareas siguientes:

* Carga los certificados y los demás artefactos en una cuenta de almacenamiento de Azure Stack.
* Publica paquetes de la galería para poder implementar las bases de datos MySQL mediante esta.
* Publica un paquete de galería para implementar los servidores de hospedaje.
* Implementa una máquina virtual mediante la imagen principal de Windows Server 2016 descargada y, luego, instala el proveedor de recursos MySQL.
* Registra un registro de DNS local que se asigna a la VM del proveedor de recursos.
* Registra el proveedor de recursos en la instancia local de Azure Resource Manager para las cuentas de operador.

> [!NOTE]
> Cuando se inicia la implementación del proveedor de recursos MySQL, se crea el grupo de recursos **system.local.mysqladapter**. Las implementaciones necesarias de este grupo de recursos pueden tardar hasta 75 minutos en finalizar.

### <a name="deploymysqlproviderps1-parameters"></a>Parámetros de DeployMySqlProvider.ps1

Puede especificar estos parámetros en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pedirá que proporcione los parámetros necesarios.

| Nombre de parámetro | DESCRIPCIÓN | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios. | _Obligatorio_ |
| **AzCredential** | Credenciales de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack. | _Obligatorio_ |
| **VMLocalCredential** | Las credenciales para la cuenta de administrador local de la VM del proveedor de recursos de MySQL. | _Obligatorio_ |
| **PrivilegedEndpoint** | Dirección IP o nombre DNS del punto de conexión con privilegios. |  _Obligatorio_ |
| **AzureEnvironment** | Entorno de Azure de la cuenta de administrador de servicios que se usó para la implementación de Azure Stack. Requerido solo para implementaciones de Azure AD. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una instancia de Azure AD de China, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | El archivo .pfx de certificados se debe colocar en este directorio, pero solo en los sistemas integrados. En el caso de entornos desconectados, descargue [mysql-connector-net-6.10.5](https://dev.mysql.com/get/Downloads/Connector-Net/mysql-connector-net-6.10.5.msi) a este directorio. También puede copiar un paquete de Windows Update MSU aquí. | _Opcional_ (_obligatorio_ para sistemas integrados o entornos desconectados) |
| **DefaultSSLCertificatePassword** | Contraseña para el certificado .pfx. | _Obligatorio_ |
| **MaxRetryCount** | Número de veces que quiere volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** | Intervalo de tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes). | Sin  |
| **DebugMode** | Impide la limpieza automática en caso de error. | Sin  |
| **AcceptLicense** | Omite el aviso para aceptar la licencia GPL.  <https://www.gnu.org/licenses/old-licenses/gpl-2.0.html> | |

## <a name="deploy-the-mysql-resource-provider-using-a-custom-script"></a>Implementación del proveedor de recursos MySQL con un script personalizado

Para eliminar cualquier configuración manual al implementar el proveedor de recursos, puede personalizar el script siguiente. Cambie la información de cuenta predeterminada y las contraseñas según sea necesario para su implementación de Azure Stack.

```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell
Install-Module -Name AzureRm.BootStrapper -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"  

# For integrated systems, use the IP address of one of the ERCS virtual machines.
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported environment names are AzureCloud, AzureUSGovernment, or AzureChinaCloud. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\MYSQLRP'

# The service admin account (can be Azure Active Directory or Active Directory Federation Services).
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set the credentials for the new resource provider VM local administrator account
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("mysqlrpadmin", $vmLocalAdminPass)

# And the cloudadmin credential required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
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

Cuando finalice el script de instalación del proveedor de recursos, actualice el explorador para asegurarse de que puede ver las actualizaciones más recientes.

## <a name="verify-the-deployment-by-using-the-azure-stack-portal"></a>Comprobación de la implementación mediante Azure Stack Portal

1. Inicie sesión en el portal de administración como administrador de servicios.
2. Seleccione **Resource Groups** (Grupos de recursos).
3. Seleccione el grupo de recursos **system.\<ubicación\>.mysqladapter**.
4. En la página de resumen de la información general del grupo de recursos no debería haber implementaciones con errores.
5. Por último, seleccione **Máquinas virtuales** en el portal de administración para comprobar que la VM del proveedor de recursos de MySQL se ha creado correctamente y está en ejecución.

## <a name="next-steps"></a>Pasos siguientes

[Add hosting servers](azure-stack-mysql-resource-provider-hosting-servers.md) (Agregar servidores de hospedaje)
