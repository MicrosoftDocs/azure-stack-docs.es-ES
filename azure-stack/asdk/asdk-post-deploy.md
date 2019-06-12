---
title: Configuraciones después de la implementación del Kit de desarrollo de Azure Stack (ASDK) | Microsoft Docs
description: Describe los cambios de configuración que se recomiendan realizar después de instalar el Kit de desarrollo de Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/10/2018
ms.openlocfilehash: aac9bb8edce4b15d3d058cdb3b6cc6e23aa58493
ms.sourcegitcommit: 23816ec68f67f3ac51f78de925b7631590743a29
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66835008"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Tareas de configuración posteriores a la instalación de ASDK

Después de realizar la [instalación del Kit de desarrollo de Azure Stack (ASDK)](asdk-install.md), tendrá que efectuar algunos cambios de configuración recomendados mientras inicia sesión como AzureStack\AzureStackAdmin en el equipo host de ASDK.

## <a name="install-azure-stack-powershell"></a>Instalación de PowerShell de Azure Stack

Los módulos de Azure PowerShell compatibles con Azure Stack se requieren para trabajar con Azure Stack.

Los comandos de PowerShell para Azure Stack se instalan a través de la Galería de PowerShell. Para registrar el repositorio PSGallery, abra una sesión de PowerShell con privilegios elevados y ejecute el siguiente comando:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Puede usar perfiles de la versión de API para especificar los módulos AzureRM compatibles con Azure Stack.  Los perfiles de versión de la API proporcionan una manera de administrar las diferencias de versión entre Azure y Azure Stack. Un perfil de la versión de la API es un conjunto de módulos de PowerShell AzureRM con versiones específicas de la API. El módulo **AzureRM.BootStrapper** que está disponible a través de la Galería de PowerShell proporciona cmdlet de PowerShell que son necesarios para trabajar con perfiles de la versión de API.

El módulo de Azure Stack PowerShell más reciente se puede instalar con o sin conectividad a Internet en el equipo host de ASDK:

> [!IMPORTANT]
> Antes de instalar la versión requerida, asegúrese de [desinstalar los módulos de Azure PowerShell existentes](../operator/azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Con conexión a internet** desde el equipo host de ASDK. Ejecute el siguiente script de PowerShell para instalar estos módulos en la instalación del kit de desarrollo:

  - Para las compilaciones 1904 o posteriores:

    ```powershell  
      Get-Module -Name Azs.* -ListAvailable | Uninstall-Module -Force -Verbose
      Get-Module -Name Azure* -ListAvailable | Uninstall-Module -Force -Verbose

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
    > La versión del módulo de Azure Stack 1.7.1 es un cambio importante. Para migrar de Azure Stack 1.6.0, consulte la [Guía de migración](https://aka.ms/azspshmigration171).

  - Azure Stack 1811:

    ``` PowerShell
    # Install the AzureRM.BootStrapper module. Select Yes when prompted to install NuGet.
    Install-Module -Name AzureRM.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.6.0.
    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

  Si la instalación es correcta, los módulos AzureRM y AzureStack se muestran en la salida.

- **Sin conexión a internet** desde el equipo host de ASDK. En un escenario sin conexión, primero hay que descargar los módulos de PowerShell en un equipo que tenga conexión a Internet con los siguientes comandos de PowerShell:

  ```powershell
  $Path = "<Path that is used to save the packages>"

  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureRM -Path $Path -Force -RequiredVersion 2.3.0
  
  Save-Package `
    -ProviderName NuGet -Source https://www.powershellgallery.com/api/v2 -Name AzureStack -Path $Path -Force -RequiredVersion 1.5.0
  ```

  Después, se copian los paquetes descargados al equipo de ASDK y, seguidamente, se registra esta ubicación como el repositorio predeterminado y se instalan los módulos AzureRM y AzureStack desde este repositorio:

    ```powershell  
    $SourceLocation = "<Location on the development kit that contains the PowerShell packages>"
    $RepoName = "MyNuGetSource"

    Register-PSRepository -Name $RepoName -SourceLocation $SourceLocation -InstallationPolicy Trusted

    Install-Module AzureRM -Repository $RepoName

    Install-Module AzureStack -Repository $RepoName
    ```

## <a name="download-the-azure-stack-tools"></a>Descarga de las herramientas de Azure Stack

[AzureStack-Tools](https://github.com/Azure/AzureStack-Tools) es un repositorio de GitHub que hospeda módulos de PowerShell para administrar e implementar recursos en Azure Stack. Para obtener estas herramientas, clone el repositorio de GitHub o descargue la carpeta AzureStack-Tools mediante la ejecución del script siguiente:

  ```powershell
  # Change directory to the root directory.
  cd \

  # Enforce usage of TLSv1.2 to download the Azure Stack tools archive from GitHub
  [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
  Invoke-WebRequest `
    -Uri https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  Expand-Archive -Path master.zip -DestinationPath . -Force

  # Change to the tools directory.
  cd AzureStack-Tools-master
  ```

## <a name="validate-the-asdk-installation"></a>Validación de la instalación de ASDK

Para asegurarse de que ASDK se ha implementado correctamente, puede usar el cmdlet Test-AzureStack siguiendo estos pasos:

1. Inicie sesión como AzureStack\AzureStackAdmin en el equipo host de ASDK.
2. Abra PowerShell como administrador (no ISE de PowerShell).
3. Ejecute: `Enter-PSSession -ComputerName AzS-ERCS01 -ConfigurationName PrivilegedEndpoint`
4. Ejecute: `Test-AzureStack`

Las pruebas tardarán algunos minutos en completarse. Si la instalación no se realizó correctamente, el resultado sería similar a este:

![test-azurestack](media/asdk-post-deploy/test-azurestack.png)

Si se produjo un error, siga los pasos de la solución de problemas para obtener ayuda.

## <a name="enable-multi-tenancy"></a>Habilitación de servicios multiinquilino

Para las implementaciones con Azure AD, debe [habilitar los servicios multiinquilino](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) para la instalación de ASDK.

> [!NOTE]
> Cuando para iniciar sesión en un portal de Azure Stack se usan cuentas de administrador o de usuario de dominios que no sean el usado para registrar Azure Stack, el nombre de dominio usado para registrar Azure Stack se debe anexar a la dirección URL del portal. Por ejemplo, si Azure Stack se ha registrado con fabrikam.onmicrosoft.com y el registro la cuenta de usuario es admin@contoso.com, la dirección URL que se usa para iniciar sesión en el portal de usuarios sería: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Pasos siguientes

[Registro del Kit de desarrollo de Azure Stack en Azure](asdk-register.md)
