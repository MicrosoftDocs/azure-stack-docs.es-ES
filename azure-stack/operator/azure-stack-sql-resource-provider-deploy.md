---
title: Uso de bases de datos SQL en Azure Stack | Microsoft Docs
description: Aprenda a implementar las bases de datos SQL como un servicio en Azure Stack y los pasos para implementar de forma rápida el adaptador del proveedor de recursos de SQL Server.
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
ms.lastreviewed: 03/18/2019
ms.author: mabrigg
ms.reviewer: jiahan
ms.openlocfilehash: ad4f1c2c7830b0b72118d013ef8fd5de2521180c
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419501"
---
# <a name="deploy-the-sql-server-resource-provider-on-azure-stack"></a>Implementación del proveedor de recursos de SQL Server en Azure Stack

Use el proveedor de recursos de SQL Server de Azure Stack para exponer las bases de datos SQL como un servicio de Azure Stack. El proveedor de recursos de SQL se ejecuta como un servicio en una máquina virtual (VM) Server Core de Windows Server 2016.

> [!IMPORTANT]
> Solo el proveedor de recursos puede crear elementos en servidores que hospedan SQL o MySQL. Los elementos creados en un servidor host que no se crean con el proveedor de recursos podrían dar lugar a un error de coincidencia de estado.

## <a name="prerequisites"></a>Requisitos previos

Hay varios requisitos previos que se deben cumplir antes de implementar el proveedor de recursos SQL de Azure Stack. Para cumplir estos requisitos, realice los pasos siguientes en un equipo que pueda acceder a la máquina virtual de punto de conexión con privilegios:

- Si aún no lo ha hecho, [registre Azure Stack](azure-stack-registration.md) en Azure para poder descargar elementos de Azure Marketplace.
- Debe instalar los módulos de Azure y Azure Stack PowerShell en el sistema donde se ejecutará esta instalación. Dicho sistema debe ser una imagen de Windows 10 o Windows Server 2016 con la versión más reciente del entorno de ejecución de .NET. Consulte [Instalación de PowerShell para Azure Stack](./azure-stack-powershell-install.md).
- Agregue la máquina virtual Windows Server Core a Marketplace de Azure Stack mediante la descarga de la imagen **Windows Server 2016 Datacenter - Server Core**.
- Descargue el archivo binario del proveedor de recursos SQL y ejecute el extractor automático para extraer el contenido en un directorio temporal. El proveedor de recursos tiene una compilación mínima correspondiente de Azure Stack.

  |Versión mínima de Azure Stack|Versión de SQL RP|
  |-----|-----|
  |Versión 1808 (1.1808.0.97)|[SQL RP, versión 1.1.33.0](https://aka.ms/azurestacksqlrp11330)|  
  |Versión 1808 (1.1808.0.97)|[SQL RP, versión 1.1.30.0](https://aka.ms/azurestacksqlrp11300)|  
  |Versión 1804 (1.0.180513.1)|[SQL RP, versión 1.1.24.0](https://aka.ms/azurestacksqlrp11240)  
  |     |     |

- Asegúrese de que se cumplen los requisitos previos de la integración del centro de datos:

    |Requisito previo|Referencia|
    |-----|-----|
    |El reenvío condicional de DNS se ha establecido correctamente.|[Integración del centro de datos de Azure Stack: DNS](azure-stack-integrate-dns.md)|
    |Los puertos de entrada para los proveedores de recursos están abiertos.|[Integración del centro de datos de Azure Stack: protocolos y puertos de entrada](azure-stack-integrate-endpoints.md#ports-and-protocols-inbound)|
    |Se han establecido correctamente el SAN y el asunto del certificado.|[Requisitos previos de PKI obligatorios de implementación de Azure Stack](azure-stack-pki-certs.md#mandatory-certificates)<br>[Requisitos previos de certificado de PaaS de implementación de Azure Stack](azure-stack-pki-certs.md#optional-paas-certificates)|
    |     |     |

### <a name="certificates"></a>Certificados

_Solo para las instalaciones de sistemas integrados_. Debe proporcionar el certificado PKI de PaaS de SQL que se describe en la sección de certificados de PaaS opcionales de los [requisitos de PKI de la implementación de Azure Stack](./azure-stack-pki-certs.md#optional-paas-certificates). Coloque el archivo .pfx en la ubicación especificada por el parámetro **DependencyFilesLocalPath**. No proporcione un certificado para los sistemas ASDK.

## <a name="deploy-the-sql-resource-provider"></a>Implementar el proveedor de recursos SQL

Una vez instalados todos los requisitos previos, ejecute el script **DeploySqlProvider.ps1** para implementar el proveedor de recursos de SQL. El script DeploySqlProvider.ps1 se extrae como parte del archivo binario del proveedor de recursos de SQL descargado para su versión de Azure Stack.

 > [!IMPORTANT]
 > Antes de implementar el proveedor de recursos, revise las notas de la versión para obtener información sobre las nuevas funciones, correcciones y problemas conocidos que podrían afectar a la implementación.
 
Para implementar el proveedor de recursos de SQL, abra una **nueva** ventana de PowerShell con privilegios elevados (no de PowerShell ISE) y cambie al directorio en el que ha extraído los archivos binarios del proveedor de recursos de SQL. Se recomienda usar una nueva ventana de PowerShell para evitar posibles problemas ocasionados por los módulos de PowerShell que ya están cargados.

Ejecute el script DeploySqlProvider.ps1, que realiza las tareas siguientes:

- Carga los certificados y los demás artefactos en una cuenta de almacenamiento de Azure Stack.
- Publica paquetes de la galería para poder implementar las bases de datos SQL mediante la galería.
- Publica un paquete de galería para implementar los servidores de hospedaje.
- Implementa una máquina virtual mediante la imagen principal de Windows Server 2016 descargada y, luego, instala el proveedor de recursos de SQL.
- Registra un registro de DNS local que se asigna a la VM del proveedor de recursos.
- Registra el proveedor de recursos en la instancia local de Azure Resource Manager para las cuentas de operador.

> [!NOTE]
> Cuando se inicia la implementación del proveedor de recursos de SQL, se crea el grupo de recursos **system.local.sqladapter**. Las implementaciones necesarias para este grupo de recursos pueden tardar hasta 75 minutos en completarse.

### <a name="deploysqlproviderps1-parameters"></a>Parámetros de DeploySqlProvider.ps1

Puede especificar los parámetros siguientes en la línea de comandos. Si no lo hace, o se produce un error en la validación de algún parámetro, se le pedirá que proporcione los parámetros necesarios.

| Nombre de parámetro | DESCRIPCIÓN | Comentario o valor predeterminado |
| --- | --- | --- |
| **CloudAdminCredential** | Credencial del administrador de la nube, necesaria para el acceso al punto de conexión con privilegios. | _Obligatorio_ |
| **AzCredential** | Credenciales de la cuenta de administrador de servicio de Azure Stack. Use las mismas credenciales que para la implementación de Azure Stack. | _Obligatorio_ |
| **VMLocalCredential** | Credenciales para la cuenta de administrador local de la VM del proveedor de recursos de SQL. | _Obligatorio_ |
| **PrivilegedEndpoint** | Dirección IP o nombre DNS del punto de conexión con privilegios. |  _Obligatorio_ |
| **AzureEnvironment** | Entorno de Azure de la cuenta de administrador de servicios que se usó para la implementación de Azure Stack. Requerido solo para implementaciones de Azure AD. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una suscripción a Azure Active Directory de China, **AzureChinaCloud**. | AzureCloud |
| **DependencyFilesLocalPath** | El archivo .pfx de certificados se debe colocar en este directorio, pero solo en los sistemas integrados. También puede copiar un paquete de Windows Update MSU aquí. | _Opcional_ (_obligatorio_ para sistemas integrados) |
| **DefaultSSLCertificatePassword** | Contraseña para el certificado .pfx. | _Obligatorio_ |
| **MaxRetryCount** | El número de veces que quiere volver a intentar cada operación si se produce un error.| 2 |
| **RetryDuration** | Intervalo de tiempo de expiración entre reintentos, en segundos. | 120 |
| **Desinstalación** | Se quita el proveedor de recursos y todos los recursos asociados (vea las notas siguientes). | Sin |
| **DebugMode** | Impide la limpieza automática en caso de error. | Sin |

## <a name="deploy-the-sql-resource-provider-using-a-custom-script"></a>Implementar el proveedor de recursos SQL con un script personalizado

Para eliminar cualquier configuración manual al implementar el proveedor de recursos, puede personalizar el script siguiente.  

Cambie la información de cuenta predeterminada y las contraseñas según sea necesario para su implementación de Azure Stack.


```powershell
# Install the AzureRM.Bootstrapper module, set the profile and install the AzureStack module
# Note that this might not be the most currently available version of Azure Stack PowerShell
Install-Module -Name AzureRm.BootStrapper -RequiredVersion 0.5.0 -Force
Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 1.6.0

# Use the NetBIOS name for the Azure Stack domain. On the Azure Stack SDK, the default is AzureStack but could have been changed at install time.
$domain = "AzureStack"

# For integrated systems, use the IP address of one of the ERCS virtual machines
$privilegedEndpoint = "AzS-ERCS01"

# Provide the Azure environment used for deploying Azure Stack. Required only for Azure AD deployments. Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using. 
$AzureEnvironment = "<EnvironmentName>"

# Point to the directory where the resource provider installation files were extracted.
$tempDir = 'C:\TEMP\SQLRP'

# The service admin account can be Azure Active Directory or Active Directory Federation Services.
$serviceAdmin = "admin@mydomain.onmicrosoft.com"
$AdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$AdminCreds = New-Object System.Management.Automation.PSCredential ($serviceAdmin, $AdminPass)

# Set credentials for the new resource provider VM local administrator account.
$vmLocalAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$vmLocalAdminCreds = New-Object System.Management.Automation.PSCredential ("sqlrpadmin", $vmLocalAdminPass)

# Add the cloudadmin credential that's required for privileged endpoint access.
$CloudAdminPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force
$CloudAdminCreds = New-Object System.Management.Automation.PSCredential ("$domain\cloudadmin", $CloudAdminPass)

# Change the following as appropriate.
$PfxPass = ConvertTo-SecureString "P@ssw0rd1" -AsPlainText -Force

# Clear the existing login information from the Azure PowerShell context.
Clear-AzureRMContext -Scope CurrentUser -Force
Clear-AzureRMContext -Scope Process -Force

# Change to the directory folder where you extracted the installation files. Do not provide a certificate on ASDK!
. $tempDir\DeploySQLProvider.ps1 `
    -AzCredential $AdminCreds `
    -VMLocalCredential $vmLocalAdminCreds `
    -CloudAdminCredential $cloudAdminCreds `
    -PrivilegedEndpoint $privilegedEndpoint `
    -AzureEnvironment $AzureEnvironment `
    -DefaultSSLCertificatePassword $PfxPass `
    -DependencyFilesLocalPath $tempDir\cert

 ```

Cuando finalice el script de instalación del proveedor de recursos, actualice el explorador para asegurarse de que puede ver las actualizaciones más recientes.

## <a name="verify-the-deployment-using-the-azure-stack-portal"></a>Comprobación de la implementación mediante Azure Stack Portal

Puede usar los pasos siguientes para verificar que el proveedor de recursos de SQL se implementa correctamente.

1. Inicie sesión en el portal de administración como administrador de servicios.
2. Seleccione **Grupos de recursos**.
3. A continuación, seleccione el grupo de recursos **system.\<location\>.sqladapter**.
4. En la página de resumen de la información general del grupo de recursos no debería haber implementaciones con errores.
      ![Comprobar la implementación del proveedor de recursos de SQL](./media/azure-stack-sql-rp-deploy/sqlrp-verify.png)
5. Por último, seleccione **Máquinas virtuales** en el portal de administración para comprobar que la VM del proveedor de recursos de SQL se ha creado correctamente y está en ejecución.

## <a name="next-steps"></a>Pasos siguientes

[Add hosting servers](azure-stack-sql-resource-provider-hosting-servers.md) (Agregar servidores de hospedaje)
