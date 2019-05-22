---
title: Instalación de PowerShell para Azure Stack | Microsoft Docs
description: Aprenda a instalar PowerShell para Azure Stack.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
origin.date: 04/13/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: thoroet
ms.lastreviewed: 04/13/2019
ms.openlocfilehash: d523e981663218f39815e3bfe3911f8c8f4c363f
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64308139"
---
# <a name="install-powershell-for-azure-stack"></a>Instalación de PowerShell para Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Para trabajar con la nube, debe instalar los módulos de PowerShell compatibles con Azure Stack. La compatibilidad se habilita mediante una característica denominada *perfiles de API*.

Los perfiles de API proporcionan una manera de administrar las diferencias de versión entre Azure y Azure Stack. Un perfil de versión de la API es un conjunto de módulos de PowerShell de Azure Resource Manager con versiones específicas de la API. Cada plataforma de la nube tiene un conjunto de perfiles de versión de API compatibles. Por ejemplo, Azure Stack admite una versión de perfil específica, como **2018-03-01-hybrid**. Cuando se instala un perfil, se instalan los módulos de PowerShell Azure Resource Manager que se corresponden con el perfil especificado. Se usan perfiles para Azure Stack, versión 1811 o anterior. Con Azure Stack, versión 1901 o posterior, los desarrolladores pueden usar módulos de AzureRM **2.4.0** para instalar los módulos correctos de PowerShell de Azure Resource Manager.

Puede instalar los módulos de PowerShell compatibles con Azure Stack en un escenario desconectado, parcialmente conectado o conectado a Internet. Este artículo le guía por las instrucciones detalladas para estos escenarios.

## <a name="1-verify-your-prerequisites"></a>1. Comprobación de los requisitos previos

Antes de empezar con Azure Stack y PowerShell, debe cumplir estos requisitos previos:

- **PowerShell versión 5.0** Para comprobar la versión, ejecute **$PSVersionTable.PSVersion** y compare la versión **principal**. Si no tiene PowerShell 5.0, siga las instrucciones de [Actualización de Windows PowerShell existente](https://docs.microsoft.com/powershell/scripting/setup/installing-windows-powershell?view=powershell-6#upgrading-existing-windows-powershell).

  > [!Note]
  > PowerShell 5.0 requiere una máquina Windows.

- **Ejecute Powershell en un símbolo del sistema con privilegios elevados**.

- **Acceso a la Galería de PowerShell** Necesita tener acceso a la [Galería de PowerShell](https://www.powershellgallery.com). La Galería es el repositorio central para el contenido de PowerShell. El módulo **PowerShellGet** contiene cmdlets para detectar, instalar, actualizar y publicar los artefactos de PowerShell como los módulos, recursos DSC, funcionalidades de rol y scripts desde la Galería de PowerShell y otros repositorios privados. Si usa PowerShell en un escenario desconectado, debe recuperar los recursos desde una máquina con conexión a Internet y almacenarlos en una ubicación accesible para la máquina desconectada.

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
Register-PsRepository -Default
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

## <a name="3-uninstall-existing-versions-of-the-azure-stack-powershell-modules"></a>3. Desinstalar las versiones existentes de los módulos de PowerShell de Azure Stack

Antes de instalar la versión requerida, asegúrese de desinstalar los módulos de PowerShell de Azure Stack AzureRM instalados previamente. Puede desinstalarlos mediante uno de los dos métodos siguientes:

1. Para desinstalar los módulos de PowerShell de AzureRM existentes, cierre todas las sesiones de PowerShell activas y ejecute los siguientes cmdlets:

    ```powershell
    Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
    Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose
    ```

    Si se produce algún error, como "el módulo ya se está utilizando", cierre las sesiones de PowerShell que usan los módulos y vuelva a ejecutar el script anterior.

2. Elimine todas las carpetas que empiecen con `Azure` o `Azs.` de las carpetas `C:\Program Files\WindowsPowerShell\Modules` y `C:\Users\{yourusername}\Documents\WindowsPowerShell\Modules`. Al eliminar estas carpetas se quitan todos los módulos de PowerShell existentes.

## <a name="4-connected-install-powershell-for-azure-stack-with-internet-connectivity"></a>4. Escenario conectado: Instalación de PowerShell para Azure Stack con conectividad a Internet

Use AzureRM 2.4.0, con la versión de Azure Stack 1901 o posterior. Además de los módulos de AzureRM, instale los módulos de PowerShell específicos de Azure Stack. El perfil de la versión de API y los módulos de PowerShell de Azure Stack que necesite dependerán de la versión de Azure Stack que esté ejecutando.

La instalación consta de tres pasos:

1. Instalación de Azure Stack PowerShell según la versión de Azure Stack
2. Habilitación de las características adicionales de almacenamiento
3. Confirmación de la instalación de PowerShell

### <a name="install-azure-stack-powershell"></a>Instalación de PowerShell de Azure Stack

Ejecute el siguiente script de PowerShell para instalar estos módulos en la estación de trabajo de desarrollo:

- Con Azure Stack 1901 o posterior, instale solo los dos módulos siguientes:

    ```powershell  
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.

    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > - La versión del módulo de Azure Stack 1.7.1 presenta cambios importantes. Para migrar de Azure Stack 1.6.0, consulte la [Guía de migración](https://aka.ms/azspshmigration171).
    > - La versión 2.4.0 del módulo de AzureRm incluye un cambio importante en el cmdlet Remove-AzureRmStorageAccount. Este cmdlet espera que el parámetro -Force esté especificado para quitar la cuenta de almacenamiento sin pedir confirmación.
    > - No es necesario instalar **AzureRM.Bootstrapper** para instalar los módulos de Azure Stack 1901 o posterior.
    > - No instale el perfil híbrido de 2018-03-01 al mismo tiempo que usa los módulos de AzureRM anteriores en Azure Stack 1901 o posterior.

- Con Azure Stack 1811, instale el perfil mediante **AzureRM.Bootstrapper**, además de las versiones indicadas en los cmdlets:

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

- Con Azure Stack 1810 o anterior, instale el perfil mediante **AzureRM.Bootstrapper**:

    ```powershell  
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    Install-Module -Name AzureStack -RequiredVersion 1.5.0
    ```

> [!Note]  
> Para actualizar Azure PowerShell de **2017-03-09-profile** a **2018-03-01-hybrid**, consulte la [Guía de migración](https://github.com/azure/azure-powershell/blob/AzureRM/documentation/migration-guides/Stack/migration-guide.2.3.0.md).

### <a name="enable-additional-storage-features"></a>Habilitación de las características adicionales de almacenamiento

Para asegurarse de usar las características adicionales de almacenamiento (mencionadas en la sección del escenario conectado), descargue e instale también los siguientes paquetes.

```powershell
# Install the Azure.Storage module version 4.5.0
Install-Module -Name Azure.Storage -RequiredVersion 4.5.0 -Force -AllowClobber

# Install the AzureRm.Storage module version 5.0.4
Install-Module -Name AzureRM.Storage -RequiredVersion 5.0.4 -Force -AllowClobber

# Remove incompatible storage module installed by AzureRM.Storage
Uninstall-Module Azure.Storage -RequiredVersion 4.6.1 -Force

# Load the modules explicitly specifying the versions
Import-Module -Name Azure.Storage -RequiredVersion 4.5.0
Import-Module -Name AzureRM.Storage -RequiredVersion 5.0.4
```

### <a name="confirm-the-installation-of-powershell"></a>Confirmación de la instalación de PowerShell

Ejecute el comando siguiente para comprobar la instalación:

```powershell
Get-Module -Name "Azure*" -ListAvailable
Get-Module -Name "Azs*" -ListAvailable
```

Si la instalación es correcta, los módulos AzureRM y AzureStack se muestran en la salida.

## <a name="5-disconnected-install-powershell-without-an-internet-connection"></a>5. Escenario desconectado: Instalación de PowerShell sin una conexión a Internet

En un escenario desconectado, primero debe descargar los módulos de PowerShell en un equipo que tenga conectividad a Internet y luego transferirlos al Kit de desarrollo de Azure Stack para la instalación.

Inicie sesión en un equipo con conexión a Internet y use estos scripts para descargar los paquetes de Azure Resource Manager y Azure Stack, según la versión de Azure Stack.

La instalación consta de cuatro pasos:

1. Instalación de PowerShell de Azure Stack en un equipo conectado
2. Habilitación de las características adicionales de almacenamiento
3. Transporte de los paquetes de PowerShell a su estación de trabajo desconectada
4. Confirmación de la instalación de PowerShell

### <a name="install-azure-stack-powershell"></a>Instalación de PowerShell de Azure Stack

- Azure Stack 1901 o una versión posterior.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.4.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > La versión del módulo de Azure Stack 1.7.1 es un cambio importante. Para migrar de Azure Stack 1.6.0, consulte la [Guía de migración](https://github.com/Azure/azure-powershell/tree/AzureRM/documentation/migration-guides/Stack).

  - Azure Stack 1811 o una versión anterior.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.6.0
    ```

  - Azure Stack 1809 o una versión anterior.

    ```powershell
    Import-Module -Name PowerShellGet -ErrorAction Stop
    Import-Module -Name PackageManagement -ErrorAction Stop

    $Path = "<Path that is used to save the packages>"
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
    Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
    ```

    > [!NOTE]
    > En máquinas sin conexión a Internet, se recomienda ejecutar el siguiente cmdlet para deshabilitar la colección de datos de telemetría. Puede experimentar una degradación del rendimiento de los cmdlets sin necesidad de deshabilitar la colección de datos de telemetría. Esto solo es aplicable para las máquinas sin conexión a Internet.
    > ```powershell
    > Disable-AzureRmDataCollection
    > ```

### <a name="enable-additional-storage-features"></a>Habilitación de las características adicionales de almacenamiento

Para asegurarse de usar las características adicionales de almacenamiento (mencionadas en la sección del escenario conectado), descargue e instale también los siguientes paquetes.

```powershell
$Path = "<Path that is used to save the packages>"
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Azure.Storage -Path $Path -Force -RequiredVersion 4.5.0
Save-Package -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRm.Storage -Path $Path -Force -RequiredVersion 5.0.4
```

### <a name="add-your-packages-to-your-workstation"></a>Adición de los paquetes a la estación de trabajo

1. Copie los paquetes descargados en un dispositivo USB.

2. Inicie sesión en la estación de trabajo desconectada y copie los paquetes desde el dispositivo USB en una ubicación en dicha estación de trabajo.

3. Ahora registre esta ubicación como el repositorio predeterminado e instale los módulos AzureRM y AzureStack desde este repositorio:

   ```powershell
   #requires -Version 5
   #requires -RunAsAdministrator
   #requires -Module PowerShellGet
   #requires -Module PackageManagement

   $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
   $RepoName = "MyNuGetSource"

   Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation  -InstallationPolicy Trusted

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

En escenarios donde se necesita un servidor proxy para acceder a Internet, primero debe configurar PowerShell para que use un servidor proxy existente:

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
