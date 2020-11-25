---
title: Instalación del módulo Az de PowerShell para Azure Stack Hub
description: Aprenda a instalar PowerShell para Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 11/17/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/27/2020
ms.openlocfilehash: 3666965dd3cfe7827fc9ea4c12b06d40ad210ac6
ms.sourcegitcommit: 2562b86f47db20e2652d4636227afb9cfd0e03ae
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/18/2020
ms.locfileid: "94785845"
---
# <a name="install-powershell-az-module-for-azure-stack-hub"></a>Instalación del módulo Az de PowerShell para Azure Stack Hub

En este artículo se explica cómo instalar el módulo Az de Azure PowerShell y los módulos del administrador de Azure Stack Hub compatibles con PowerShellGet. Los módulos Az se pueden instalar en plataformas Windows, macOS y Linux.

También puede ejecutar módulos Az para Azure Stack Hub en un contenedor de Docker. Para obtener instrucciones, consulte [Uso de Docker para ejecutar PowerShell para Azure Stack Hub](../user/azure-stack-powershell-user-docker.md).

Si desea instalar el módulo PowerShell Resource Modules (AzureRM) para Azure Stack Hub, consulte [Instalación del módulo AzureRM de PowerShell para Azure Stack Hub](azure-stack-powershell-install.md).

> [!IMPORTANT]
> Probablemente no habrá nuevas versiones del módulo Azure Resource Modules. Los módulos Azure Resource Modules solo tienen soporte técnico para correcciones críticas. En el futuro, solo habrá publicaciones de Az para Azure Stack Hub.

Puede usar *perfiles de API* para especificar los puntos de conexión compatibles para los proveedores de recursos de Azure Stack Hub.

Los perfiles de API proporcionan una manera de administrar las diferencias de versión entre Azure y Azure Stack Hub. Un perfil de versión de la API es un conjunto de módulos de PowerShell de Azure Resource Manager con versiones específicas de la API. Cada plataforma de la nube tiene un conjunto de perfiles de versión de API compatibles. Por ejemplo, Azure Stack Hub admite una versión de perfil específica, como **2019-03-01-hybrid**. Cuando se instala un perfil, se instalan los módulos de PowerShell Azure Resource Manager que se corresponden con el perfil especificado.

Puede instalar los módulos Az de PowerShell compatibles con Azure Stack Hub en un escenario desconectado, parcialmente conectado o conectado a Internet. Este artículo le guía por las instrucciones detalladas para estos escenarios.

## <a name="1-verify-your-prerequisites"></a>1. Comprobación de los requisitos previos

Ahora se admiten los módulos de Az en Azure Stack Hub con la actualización 2002 o posterior y con las revisiones actuales instaladas. Consulte las [Notas de la versión de Azure Stack Hub](release-notes.md) para obtener más información.

Los módulos de Az de Azure PowerShell funcionan con PowerShell 5.1 o versiones posteriores en Windows o con PowerShell Core 6.x y versiones posteriores en cualquier plataforma. Debe instalar la [última versión de PowerShell Core](/powershell/scripting/install/installing-powershell#powershell-core) disponible para su sistema operativo. No hay ningún requisito adicional para Azure PowerShell cuando se usa PowerShell Core.

Para comprobar la versión de PowerShell, ejecute el comando:

```powershell  
$PSVersionTable.PSVersion
```

### <a name="prerequisites-for-windows"></a>Requisitos previos para Windows
Para usar Azure PowerShell en PowerShell 5.1 en Windows:

1. Actualice a [Windows PowerShell 5.1](/powershell/scripting/windows-powershell/install/installing-windows-powershell#upgrading-existing-windows-powershell) si es necesario. Si tiene Windows 10, ya tiene PowerShell 5.1 instalado.
2. Instale [.NET Framework 4.7.2 o posterior](/dotnet/framework/install).
3. Asegúrese de tener instalada la versión más reciente de PowerShellGet. Ejecute `Install-Module PowerShellGet -MinimumVersion 2.2.3 -Force`. 

## <a name="2-prerequisites-for-linux-and-mac"></a>2. Requisitos previos para Linux y Mac
Se necesita PowerShell Core 6.x o una versión posterior. Consulte este [vínculo](/powershell/scripting/install/installing-powershell-core-on-windows) para obtener instrucciones.

## <a name="3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules"></a>3. Desinstale las versiones existentes de los módulos de PowerShell para Azure Stack Hub.

Antes de instalar la versión requerida, asegúrese de desinstalar los módulos de PowerShell Azure Resource Modules o Az para Azure Stack Hub instalados previamente. Desinstale los módulos mediante uno de los dos métodos siguientes:

1. Para desinstalar los módulos de PowerShell Azure Resource Modules y Az existentes, cierre todas las sesiones de PowerShell activas y ejecute los siguientes cmdlets:

    ```powershell
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    Get-Module -Name Az.* -ListAvailable | Uninstall-Module -Force -Verbose -ErrorAction Continue
    ```
    Si se produce algún error, como "el módulo ya se está utilizando", cierre las sesiones de PowerShell que usan los módulos y vuelva a ejecutar el script anterior.

2. Elimine todas las carpetas que empiecen con `Azure`, `Az` o `Azs.` de las carpetas `C:\Program Files\WindowsPowerShell\Modules` y `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules`. Al eliminar estas carpetas se quitan todos los módulos de PowerShell existentes.

## <a name="4-connected-install-with-internet-connectivity"></a>4. Escenario conectado: Instalación con conectividad a Internet

El módulo Az de Azure Stack funcionará en Azure Stack Hub 2002 o versiones posteriores. Además, el módulo Az de Azure Stack funcionará con PowerShell 5.1 o versiones posteriores en una máquina Windows o con PowerShell 6.x o versiones posteriores en una plataforma Linux o macOS. El método de instalación preferido es mediante los cmdlets de PowerShellGet. Este método funciona igual en las plataformas admitidas.

Ejecute el siguiente comando desde una sesión de PowerShell:

```powershell  
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
Install-AzProfile -Profile 2019-03-01-hybrid -Force
Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease
```

> [!Note]  
> La versión del módulo de Azure Stack Hub 2.0.0 es un cambio importante. Consulte [Migración desde AzureRM a Az de Azure PowerShell en Azure Stack Hub](migrate-azurerm-az.md) para más información.

> [!WARNING]
> No puede tener los módulos Azure Resource Modules (AzureRM) y Az instalados para PowerShell 5.1 para Windows al mismo tiempo. Si necesita mantener Azure Resource Modules disponible en el sistema, instale el módulo Az para PowerShell Core 6.x o versiones posteriores. Para ello, [instale PowerShell Core 6.x o versiones posteriores](/powershell/scripting/install/installing-powershell-core-on-windows) y, a continuación, siga estas instrucciones en un terminal de PowerShell Core.

## <a name="5-disconnected-install-without-internet-connection"></a>5. Escenario desconectado: Instalación sin conexión a Internet

En un escenario sin conexión, primero hay que descargar los módulos de PowerShell en una máquina que tenga conexión a Internet. A continuación, debe transferirlos al Kit de desarrollo de Azure Stack (ASDK) para instalarlos.

Inicie sesión en un equipo con conexión a Internet y use los scripts siguientes para descargar los paquetes de Azure Resource Manager y Azure Stack Hub, según la versión de Azure Stack Hub.

La instalación consta de cinco pasos:

1. Instale PowerShell de Azure Stack Hub en una máquina conectada.
2. Habilite las características adicionales de almacenamiento.
3. Transporte los paquetes de PowerShell a su estación de trabajo desconectada.
4. Realice manualmente el arranque del proveedor de NuGet en su estación de trabajo desconectada.
5. Confirme la instalación de PowerShell.

### <a name="install-azure-stack-hub-powershell"></a>Instalación de PowerShell de Azure Stack Hub

::: moniker range=">=azs-2002"
Azure Stack Hub 2002 o una versión posterior.

Puede usar módulos Azure Resource Modules o AZ. Para Azure Resource Modules, consulte las instrucciones en [Instalación del módulo AzureRM de PowerShell](powershell-install-az-module.md). En el código siguiente se guardan los módulos del repositorio en línea de confianza https://www.powershellgallery.com/.

```powershell
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

Install-module -Name PowerShellGet -MinimumVersion 2.2.3 -Force
Import-Module -Name PackageManagement -ErrorAction Stop

$savedModulesPath = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $savedModulesPath -Force -RequiredVersion 0.10.0-preview
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $savedModulesPath -Force -RequiredVersion 2.0.2-preview
```
::: moniker-end

> [!NOTE]  
> En máquinas sin conexión a Internet, le recomendamos ejecutar el siguiente cmdlet para deshabilitar la colección de datos de telemetría. Puede experimentar una degradación del rendimiento de los cmdlets sin necesidad de deshabilitar la colección de datos de telemetría. Esto solo es aplicable para las máquinas sin conexión a Internet.
> ```powershell
> Disable-AzDataCollection
> ```

### <a name="add-your-packages-to-your-workstation"></a>Adición de los paquetes a la estación de trabajo

1. Copie los paquetes descargados en un dispositivo USB.

2. Inicie sesión en la estación de trabajo desconectada y copie los paquetes desde el dispositivo USB en una ubicación en dicha estación de trabajo.

3. Realice manualmente el arranque del proveedor de NuGet en su estación de trabajo desconectada. Para obtener instrucciones, consulte [Arranque manual del proveedor de NuGet en una máquina no conectada a Internet](/powershell/scripting/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet).

4. Registre esta ubicación como el repositorio predeterminado e instale los módulos `AzureRM` y `AzureStack` desde este repositorio:

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

   Install-Module -Name AzureStack -Repository $RepoName -RequiredVersion 2.0.2-preview -AllowPrerelease -Scope AllUsers

   Install-Module -Name Az -Repository $RepoName -RequiredVersion 0.10.0-preview -AllowPrerelease -Scope AllUsers
   ```

### <a name="confirm-the-installation-of-powershell"></a>Confirmación de la instalación de PowerShell

Ejecute el comando siguiente para comprobar la instalación:

```powershell
Get-Module -Name "Az*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

## <a name="6-configure-powershell-to-use-a-proxy-server"></a>6. Configuración de PowerShell para usar un servidor proxy

En escenarios donde se necesita un servidor proxy para acceder a Internet, primero configure PowerShell para que use un servidor proxy existente:

1. Abra un símbolo del sistema de PowerShell con privilegios elevados.
2. Ejecute los comandos siguientes:

   ```powershell
   #To use Windows credentials for proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = [System.Net.CredentialCache]::DefaultCredentials

   #Alternatively, to prompt for separate credentials that can be used for #proxy authentication
   [System.Net.WebRequest]::DefaultWebProxy.Credentials = Get-Credential
   ```

## <a name="7-use-the-az-module"></a>7. Uso del módulo Az

Puede usar los cmdlets y los ejemplos de código basados en Azure Resource Modules. Sin embargo, querrá cambiar el nombre de los módulos y los cmdlets. Los nombres de módulo se han cambiado para que `AzureRM` y Azure se conviertan en `Az` e igual para los cmdlets. Por ejemplo, el nombre del módulo `AzureRM.Compute` ha cambiado a `Az.Compute`. ` New-AzureRMVM` se ha convertido en `Get-AzureStorageBlob` y `Get-AzStorageBlob` ahora es ` New-AzVM`.

Para obtener una explicación más detallada e instrucciones para migrar el script de AzureRM a Az y conocer los cambios importantes en el módulo Az de Azure Stack Hub, consulte [Migración desde AzureRM a Az de Azure PowerShell](migrate-azurerm-az.md).

## <a name="next-steps"></a>Pasos siguientes

- [Descarga de herramientas de Azure Stack Hub desde GitHub](azure-stack-powershell-download.md)
- [Configuración del entorno de PowerShell del usuario de Azure Stack Hub](../user/azure-stack-powershell-configure-user.md)
- [Configuración del entorno de PowerShell del operador de Azure Stack Hub](azure-stack-powershell-configure-admin.md)
- [Administración de perfiles de la versión de API en Azure Stack Hub](../user/azure-stack-version-profiles.md)
