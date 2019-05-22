---
title: Configuraciones después de la implementación del Kit de desarrollo de Azure Stack (ASDK) | Microsoft Docs
description: Describe los cambios de configuración que se recomiendan realizar después de instalar el Kit de desarrollo de Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: jeffgilb
ms.reviewer: misainat
ms.lastreviewed: 10/10/2018
ms.openlocfilehash: 6ef30cc182160f1065f8e98a91208f067e5ae353
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64983853"
---
# <a name="post-asdk-installation-configuration-tasks"></a>Tareas de configuración posteriores a la instalación de ASDK

Después de realizar la [instalación del Kit de desarrollo de Azure Stack (ASDK)](asdk-install.md), tendrá que efectuar algunos cambios de configuración recomendados mientras inicia sesión como AzureStack\AzureStackAdmin en el equipo host de ASDK. 

## <a name="install-azure-stack-powershell"></a>Instalación de PowerShell de Azure Stack

Los módulos de Azure PowerShell compatibles con Azure Stack se requieren para trabajar con Azure Stack.

Los comandos de PowerShell para Azure Stack se instalan a través de la Galería de PowerShell. Para registrar el repositorio PSGallery, abra una sesión de PowerShell con privilegios elevados y ejecute el siguiente comando:

``` Powershell
Set-PSRepository -Name "PSGallery" -InstallationPolicy Trusted
```

Puede usar perfiles de la versión de API para especificar los módulos AzureRM compatibles con Azure Stack.  Los perfiles de versión de la API proporcionan una manera de administrar las diferencias de versión entre Azure y Azure Stack. Un perfil de la versión de la API es un conjunto de módulos de PowerShell AzureRM con versiones específicas de la API. El módulo **AzureRM.Bootstrapper** que está disponible a través de la Galería de PowerShell proporciona cmdlet de PowerShell que son necesarios para trabajar con perfiles de la versión de API.

El módulo de Azure Stack PowerShell más reciente se puede instalar con o sin conectividad a Internet en el equipo host de ASDK:

> [!IMPORTANT]
> Antes de instalar la versión requerida, asegúrese de [desinstalar los módulos de Azure PowerShell existentes](../operator/azure-stack-powershell-install.md#3-uninstall-existing-versions-of-the-azure-stack-powershell-modules).

- **Con conexión a internet** desde el equipo host de ASDK. Ejecute el siguiente script de PowerShell para instalar estos módulos en la instalación del kit de desarrollo:

- Azure Stack 1901, o cualquier versión posterior:

    ```powershell
    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Install-Module AzureRM -RequiredVersion 2.4.0
    Install-Module -Name AzureStack -RequiredVersion 1.7.1
    ```

    > [!Note]  
    > La versión del módulo de Azure Stack 1.7.1 es un cambio importante. Para migrar de Azure Stack 1.6.0, consulte la [Guía de migración](https://aka.ms/azspshmigration171).

  - Azure Stack 1811:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.6.0.
    Install-Module -Name AzureStack -RequiredVersion 1.6.0
    ```

  - Azure Stack 1810, o cualquier versión anterior:

    ``` PowerShell
    # Install the AzureRM.Bootstrapper module. Select Yes when prompted to install NuGet. 
    Install-Module -Name AzureRm.BootStrapper

    # Install and import the API Version Profile required by Azure Stack into the current PowerShell session.
    Use-AzureRmProfile -Profile 2018-03-01-hybrid -Force

    # Install Azure Stack Module Version 1.5.0.
    Install-Module -Name AzureStack -RequiredVersion 1.5.0
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
  invoke-webrequest `
    https://github.com/Azure/AzureStack-Tools/archive/master.zip `
    -OutFile master.zip

  # Expand the downloaded files.
  expand-archive master.zip -DestinationPath . -Force

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

## <a name="reset-the-password-expiration-policy"></a>Restablecimiento de la directiva de expiración de contraseñas 

Para asegurarse de que la contraseña del host del kit de desarrollo no expire antes de que termine el período de evaluación, siga estos pasos después de implementar ASDK.

### <a name="to-change-the-password-expiration-policy-from-powershell"></a>Para cambiar la directiva de expiración de contraseña desde Powershell

En una consola de Powershell con privilegios elevados, ejecute el comando:

```powershell
Set-ADDefaultDomainPasswordPolicy -MaxPasswordAge 180.00:00:00 -Identity azurestack.local
```

### <a name="to-change-the-password-expiration-policy-manually"></a>Para cambiar la directiva de expiración de contraseña manualmente

1. En el host del kit de desarrollo, abra **Administración de directivas de grupo** (GPMC.MMC) y vaya a **Administración de directivas de grupo** - **Bosque: azurestack.local** - **Dominios** - **azurestack.local**.
2. Haga clic con el botón derecho en **Directiva predeterminada de dominio**  y, después, haga clic en **Editar**.
3. En el Editor de administración de directivas de grupo, vaya a **Configuración del equipo** - **Directivas** - **Configuración de Windows** - **Configuración de seguridad** - **Directivas de cuenta** - **Directiva de contraseñas**.
4. En el panel derecho, haga doble clic en **Vigencia máxima de la contraseña**.
5. En el cuadro de diálogo **Maximum password age Properties** (Propiedades de Vigencia máxima de la contraseña), cambie el valor **Password will expire in** (La contraseña expirará en) a **180** y, después, haga clic en **Aceptar**.

![Consola de administración de directivas de grupo](media/asdk-post-deploy/gpmc.png)

## <a name="enable-multi-tenancy"></a>Habilitación de servicios multiinquilino

Para las implementaciones con Azure AD, debe [habilitar los servicios multiinquilino](../operator/azure-stack-enable-multitenancy.md#enable-multi-tenancy) para la instalación de ASDK.

> [!NOTE]
> Cuando para iniciar sesión en un portal de Azure Stack se usan cuentas de administrador o de usuario de dominios que no sean el usado para registrar Azure Stack, el nombre de dominio usado para registrar Azure Stack se debe anexar a la dirección URL del portal. Por ejemplo, si Azure Stack se ha registrado con fabrikam.onmicrosoft.com y el registro la cuenta de usuario es admin@contoso.com, la dirección URL que se usa para iniciar sesión en el portal de usuarios sería: https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.

## <a name="next-steps"></a>Pasos siguientes

[Registro del Kit de desarrollo de Azure Stack en Azure](asdk-register.md)
