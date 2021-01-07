---
title: Configuraciones posteriores a la implementación del ASDK
description: Obtenga información sobre los cambios de configuración recomendados después de instalar el Kit de desarrollo de Azure Stack (ASDK).
author: PatAltimore
ms.topic: article
ms.date: 12/03/2020
ms.author: patricka
ms.reviewer: misainat
ms.lastreviewed: 12/03/2020
ms.openlocfilehash: f2e039e2b0934e17a4335753e787c6cdf3808644
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97873323"
---
# <a name="post-deployment-configurations-for-asdk"></a>Configuraciones posteriores a la implementación del ASDK

Después de [instalar el Kit de desarrollo de Azure Stack (ASDK)](asdk-install.md), tendrá que efectuar algunos cambios de configuración recomendados posteriores a la implementación con la sesión iniciada como AzureStack\AzureStackAdmin en el equipo host del ASDK.

## <a name="install-azure-stack-powershell"></a>Instalación de PowerShell de Azure Stack

Los módulos de Azure PowerShell compatibles con Azure Stack se requieren para trabajar con Azure Stack.

Los comandos de PowerShell para Azure Stack se instalan a través de la Galería de PowerShell. Para registrar el repositorio PSGallery, abra una sesión de PowerShell con privilegios elevados y ejecute el siguiente comando:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Use perfiles de la versión de la API para especificar los módulos de Az compatibles con Azure Stack.  Los perfiles de versión de la API proporcionan una manera de administrar las diferencias de versión entre Azure y Azure Stack. Un perfil de la versión de la API es un conjunto de módulos Az de PowerShell con versiones específicas de la API. El módulo **Az.Bootstrapper** que está disponible a través de la Galería de PowerShell proporciona cmdlets de PowerShell que son necesarios para trabajar con perfiles de la versión de la API.

El módulo de Azure Stack PowerShell más reciente se puede instalar con o sin conectividad a Internet en el equipo host de ASDK.

1.  Valide los requisitos previos en la máquina Windows. Para obtener instrucciones, consulte [Requisitos previos para Windows](../operator/powershell-install-az-module.md#prerequisites-for-windows).
2. Antes de instalar la versión de PowerShell requerida, asegúrese de [desinstalar los módulos de Azure PowerShell existentes](../operator/powershell-install-az-module.md#3-uninstall-existing-versions-of-the-azure-stack-hub-powershell-modules). 

- **Con conexión a Internet** desde el equipo host del ASDK: Ejecute el siguiente script de PowerShell para instalar estos módulos en la instalación del ASDK:

### <a name="az-modules"></a>[Modules de Az](#tab/az1)

  ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12

    Install-Module -Name Az.BootStrapper -Force -AllowPrerelease
    Install-AzProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 2.0.2-preview -AllowPrerelease

    Get-Module -Name "Az*" -ListAvailable
    Get-Module -Name "Azs*" -ListAvailable
  ```

Si la instalación es correcta, los módulos Az y AzureStack se muestran en la salida.

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm1)

  ```powershell  
    [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
    
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet
    Install-Module -Name AzureRM.BootStrapper
    
    # Install and import the API Version Profile required by Azure Stack Hub into the current PowerShell session.
    Use-AzureRmProfile -Profile 2019-03-01-hybrid -Force
    Install-Module -Name AzureStack -RequiredVersion 1.8.2
    
    Get-Module -Name "Az*" -ListAvailable
    Get-Module -Name "Azs*" -ListAvailable
  ```

Si la instalación es correcta, los módulos AzureRM y AzureStack se muestran en la salida.

---

- **Sin conexión a Internet** desde el equipo host del ASDK: En un escenario sin conexión, primero hay que descargar los módulos de PowerShell en un equipo que tenga conexión a Internet con los siguientes comandos de PowerShell:

### <a name="az-modules"></a>[Modules de Az](#tab/az2)

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name Az -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Después, se copian los paquetes descargados al equipo de ASDK y, seguidamente, se registra esta ubicación como el repositorio predeterminado y se instalan los módulos Az y AzureStack desde este repositorio:

  ```powershell  
  $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
  $RepoName = "MyNuGetSource"

  Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

  Install-Module Az -Repository $RepoName

  Install-Module AzureStack -Repository $RepoName
  ```

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm2)

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.5.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.8.0
  ```

  Después, se copian los paquetes descargados al equipo de ASDK y, seguidamente, se registra esta ubicación como el repositorio predeterminado y se instalan los módulos AzureRM y AzureStack desde este repositorio:

  ```powershell  
  $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
  $RepoName = "MyNuGetSource"

  Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

  Install-Module AzureRM -Repository $RepoName

  Install-Module AzureStack -Repository $RepoName
  ```

---

## <a name="download-the-azure-stack-tools"></a>Descarga de las herramientas de Azure Stack

[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) es un repositorio de GitHub que hospeda módulos de PowerShell para administrar e implementar recursos en Azure Stack. Use las herramientas con los módulos Az PowerShell o los módulos AzureRM.

### <a name="az-modules"></a>[Modules de Az](#tab/az3)

Para obtener estas herramientas, clone el repositorio de GitHub de la rama `az` o descargue la carpeta **AzureStack-Tools** mediante la ejecución del script siguiente:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/az.zip `
  -OutFile az.zip

# Expand the downloaded files.
expand-archive az.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-az

```
### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm3)

Para obtener estas herramientas, clone el repositorio de GitHub de la rama `master` o descargue la carpeta **AzureStack-Tools** mediante la ejecución del script siguiente en un símbolo del sistema de PowerShell con privilegios elevados:

```powershell
# Change directory to the root directory.
cd \

# Download the tools archive.
[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
invoke-webrequest `
  https://github.com/Azure/AzureStack-Tools/archive/master.zip `
  -OutFile master.zip

# Expand the downloaded files.
expand-archive master.zip `
  -DestinationPath . `
  -Force

# Change to the tools directory.
cd AzureStack-Tools-master

```
Para más información sobre el uso del módulo AzureRM de Azure Stack Hub, consulte [Instalación del módulo AzureRM de PowerShell para Azure Stack Hub](../operator/azure-stack-powershell-install.md).

---

## <a name="validate-the-asdk-installation"></a>Validación de la instalación de ASDK

Para asegurarse de que el ASDK se ha implementado correctamente, siga estos pasos mediante el uso del cmdlet Test-AzureStack:

1. Inicie sesión como AzureStack\AzureStackAdmin en el equipo host de ASDK.
2. Abra PowerShell como administrador (no como PowerShell ISE).
3. Ejecute `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`.
4. Ejecute `Test-AzureStack`.

Las pruebas tardarán algunos minutos en completarse. Si la instalación no se realizó correctamente, el resultado sería similar a este:

![Prueba de Azure Stack: instalación correcta](media/asdk-post-deploy/test-azurestack.png)

Si se produjo un error, siga los pasos de la solución de problemas para obtener ayuda.

## <a name="enable-multi-tenancy"></a>Habilitación de servicios multiinquilino

Para las implementaciones con Azure AD, debe [habilitar los servicios multiinquilino](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) para la instalación de ASDK.

> [!NOTE]
> Cuando se utilizan cuentas de administrador o usuario de dominios distintos de los usados para registrarse en Azure Stack para iniciar sesión en un portal de Azure Stack, el nombre de dominio utilizado para registrar Azure Stack debe agregarse a la URL del portal. Por ejemplo, si Azure Stack se ha registrado en fabrikam.onmicrosoft.com y el registro de la cuenta de usuario es admin@contoso.com, la dirección URL que se usa para iniciar sesión en el portal de usuarios sería: https:\:/portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Pasos siguientes

[Registro del Kit de desarrollo de Azure Stack en Azure](asdk-register.md)
