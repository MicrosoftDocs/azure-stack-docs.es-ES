---
title: Instalación de PowerShell para Azure Stack | Microsoft Docs
description: Aprenda a instalar PowerShell para Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 07/09/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 07/09/2019
ms.openlocfilehash: d760eb4a9ca0f958ab8be09810b97820b09f5621
ms.sourcegitcommit: 58c28c0c4086b4d769e9d8c5a8249a76c0f09e57
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/12/2019
ms.locfileid: "68959487"
---
# <a name="install-powershell-for-azure-stack"></a>Instalación de PowerShell para Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Azure PowerShell ofrece un conjunto de cmdlets que usan el modelo Azure Resource Manager para administrar los recursos de Azure Stack.

Para trabajar con la nube, debe instalar los módulos de PowerShell compatibles con Azure Stack. Azure Stack usa el módulo **AzureRM** en lugar del módulo más reciente **AzureAZ** que se usa en Azure global. También debe usar *perfiles de API* para especificar los puntos de conexión compatibles para los proveedores de recursos de Azure Stack.

Los perfiles de API proporcionan una manera de administrar las diferencias de versión entre Azure y Azure Stack. Un perfil de versión de la API es un conjunto de módulos de PowerShell de Azure Resource Manager con versiones específicas de la API. Cada plataforma de la nube tiene un conjunto de perfiles de versión de API compatibles. Por ejemplo, Azure Stack admite una versión de perfil específica, como **2019-03-01-hybrid**. Cuando se instala un perfil, se instalan los módulos de PowerShell Azure Resource Manager que se corresponden con el perfil especificado.

Puede instalar los módulos de PowerShell compatibles con Azure Stack en un escenario desconectado, parcialmente conectado o conectado a Internet. Este artículo le guía por las instrucciones detalladas para estos escenarios.

## <a name="1-verify-your-prerequisites"></a>1. Comprobación de los requisitos previos

Antes de empezar con Azure Stack y PowerShell, debe cumplir estos requisitos previos:

- **PowerShell versión 5.0** <br>
Para comprobar la versión, ejecute **$PSVersionTable.PSVersion** y compare la versión **principal**. Si no tiene PowerShell 5.0, siga las instrucciones sobre cómo [instalar Windows PowerShell](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]
  > PowerShell 5.0 requiere una máquina Windows.

- **Ejecute Powershell en un símbolo del sistema con privilegios elevados**.

- **Acceso a la Galería de PowerShell** <br>
  Necesitará acceso a la [Galería de PowerShell](https://www.powershellgallery.com). La Galería es el repositorio central para el contenido de PowerShell. El módulo **PowerShellGet** contiene cmdlets para detectar, instalar, actualizar y publicar artefactos de PowerShell. Algunos ejemplos de estos artefactos son módulos, recursos de DSC, funcionalidades de rol y scripts de la Galería de PowerShell y otros repositorios privados. Si usa PowerShell en un escenario desconectado, debe recuperar los recursos desde una máquina con conexión a Internet y almacenarlos en una ubicación accesible para la máquina desconectada.

## <a name="2-validate-the-powershell-gallery-accessibility"></a>2. Confirmación de la accesibilidad de la Galería de PowerShell

Confirme si PSGallery está registrado como repositorio.

> [!Note]  
> Este paso requiere acceso a Internet.

Abra un símbolo de sistema de PowerShell con privilegios elevados y ejecute los siguientes cmdlet:

```powershell
Import-Module -Name PowerShellGet -ErrorAction Stop
Import-Module -Name PackageManagement -ErrorAction Stop
Get-PSRepository -Name "PSGallery"
```

Si el repositorio no está registrado, abra una sesión de PowerShell con privilegios elevados y ejecute el siguiente comando:

```powershell
Register-PSRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Desinstalar las versiones existentes de los módulos de PowerShell de Azure Stack

Antes de instalar la versión requerida, asegúrese de desinstalar los módulos de PowerShell de Azure Stack AzureRM instalados previamente. Desinstale los módulos mediante uno de los dos métodos siguientes:

1. Para desinstalar los módulos de PowerShell de AzureRM existentes, cierre todas las sesiones de PowerShell activas y ejecute los siguientes cmdlets:

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    Si se produce algún error, como "el módulo ya se está utilizando", cierre las sesiones de PowerShell que usan los módulos y vuelva a ejecutar el script anterior.

2. Elimine todas las carpetas que empiecen con `Azure` o `Azs.` de las carpetas `C:\Program Files\WindowsPowerShell\Modules` y `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules`. Al eliminar estas carpetas se quitan todos los módulos de PowerShell existentes.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Escenario conectado: Instalación de PowerShell para Azure Stack con conectividad a Internet

El perfil de la versión de API y los módulos de PowerShell de Azure Stack que necesite dependerán de la versión de Azure Stack que esté ejecutando.

### <a name="install-azure-stack-powershell"></a>Instalación de PowerShell de Azure Stack

Ejecute el siguiente script de PowerShell para instalar estos módulos en la estación de trabajo de desarrollo:

- Para Azure Stack 1904, o cualquier versión posterior:

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.7.2
    ```
  
- Con la versión de Azure Stack 1903 o anterior, instale solo los dos módulos siguientes:

    ```powershell  
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.

    Install-Module -Name AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > - La versión del módulo de Azure Stack 1.7.1 presenta cambios importantes. Para migrar de Azure Stack 1.6.0, consulte la [guía de migración](https://aka.ms/azspshmigration171).
    > - La versión 2.4.0 del módulo de AzureRM incluye un cambio importante en el cmdlet Remove-AzureRmStorageAccount. Este cmdlet espera que el parámetro `-Force` esté especificado para quitar la cuenta de almacenamiento sin pedir confirmación.
    > - No es necesario instalar **AzureRM.Bootstrapper** para instalar los módulos de Azure Stack 1901 o posterior.
    > - No instale el perfil híbrido de 2018-03-01 al mismo tiempo que usa los módulos de AzureRM anteriores en Azure Stack 1901 o posterior.

### <a name="confirm-the-installation-of-powershell"></a>Confirmación de la instalación de PowerShell

Ejecute el comando siguiente para comprobar la instalación:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Si la instalación es correcta, los módulos AzureRM y AzureStack se muestran en la salida.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Escenario desconectado: Instalación de PowerShell sin una conexión a Internet

En un escenario sin conexión, primero hay que descargar los módulos de PowerShell en una máquina que tenga conexión a Internet. A continuación, debe transferirlos al Kit de desarrollo de Azure Stack (ASDK) para instalarlos.

Inicie sesión en un equipo con conexión a Internet y use los scripts siguientes para descargar los paquetes de Azure Resource Manager y Azure Stack, según la versión de Azure Stack.

La instalación consta de cuatro pasos:

1. Instale PowerShell de Azure Stack en una máquina conectada.
2. Habilite las características adicionales de almacenamiento.
3. Transporte los paquetes de PowerShell a su estación de trabajo desconectada.
4. Realice manualmente el arranque del proveedor de NuGet en su estación de trabajo desconectada.
5. Confirme la instalación de PowerShell.

### <a name="install-azure-stack-powershell"></a>Instalación de PowerShell de Azure Stack

- Azure Stack 1904, o cualquier versión posterior.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.2
    ```

- Azure Stack 1903, o cualquier versión anterior.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > La versión del módulo de Azure Stack 1.7.1 es un cambio importante. Para migrar de Azure Stack 1.6.0, consulte la [Guía de migración](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

    > [!NOTE]
    > En máquinas sin conexión a Internet, le recomendamos ejecutar el siguiente cmdlet para deshabilitar la colección de datos de telemetría. Puede experimentar una degradación del rendimiento de los cmdlets sin necesidad de deshabilitar la colección de datos de telemetría. Esto solo es aplicable para las máquinas sin conexión a Internet.
    > ```powershell
    > Disable-AzureRmDataCollection
    > ```

### <a name="add-your-packages-to-your-workstation"></a>Adición de los paquetes a la estación de trabajo

1. Copie los paquetes descargados en un dispositivo USB.

2. Inicie sesión en la estación de trabajo desconectada y copie los paquetes desde el dispositivo USB en una ubicación en dicha estación de trabajo.

3. Realice manualmente el arranque del proveedor de NuGet en su estación de trabajo desconectada. Para obtener instrucciones, consulte [Arranque manual del proveedor de NuGet en una máquina no conectada a Internet](https://docs.microsoft.com/powershell/gallery/how-to/getting-support/bootstrapping-nuget#manually-bootstrapping-the-nuget-provider-on-a-machine-that-is-not-connected-to-the-internet).

4. Registre esta ubicación como el repositorio predeterminado e instale los módulos AzureRM y AzureStack desde este repositorio:

   ```powershell
   # requires -Version 5
   # requires -RunAsAdministrator
   # requires -Module PowerShellGet
   # requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

   Install-Module -Name AzureRM -Repository $RepoName

   Install-Module -Name AzureStack -Repository $RepoName
   ```

### <a name="confirm-the-installation-of-powershell"></a>Confirmación de la instalación de PowerShell

Ejecute el comando siguiente para comprobar la instalación:

```powershell
Get-Module -Name "Azure*" -ListAvailable
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

## <a name="next-steps"></a>Pasos siguientes

- [Descarga de herramientas de Azure Stack desde GitHub](azure-stack-powershell-download.md)
- [Configuración del entorno de PowerShell del usuario de Azure Stack](../user/azure-stack-powershell-configure-user.md)
- [Configuración del entorno de PowerShell del operador de Azure Stack](azure-stack-powershell-configure-admin.md)
- [Administración de perfiles de la versión de API en Azure Stack](../user/azure-stack-version-profiles.md)
