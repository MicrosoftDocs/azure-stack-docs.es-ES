---
title: Uso de Docker para ejecutar PowerShell en Azure Stack Hub
description: Uso de Docker para ejecutar PowerShell en Azure Stack Hub
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 802a173239534c6826e90a1d4d4c2095c1abcf9a
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525615"
---
# <a name="use-docker-to-run-powershell-for-azure-stack-hub"></a>Uso de Docker para ejecutar PowerShell para Azure Stack Hub

En este artículo, puede usar Docker para crear un contenedor en el que se ejecutará la versión de PowerShell necesaria para trabajar con las distintas interfaces. Puede encontrar instrucciones para usar tanto los módulos de AzureRM como los módulos de Az más recientes. AzureRM requiere un contenedor basado en Windows. Az usa un contenedor basado en Linux.

## <a name="docker-prerequisites"></a>Requisitos previos de Docker

### <a name="install-docker"></a>Instalación de Docker

1. Instale [Docker](https://docs.docker.com/install/).

1. En un programa de línea de comandos, como PowerShell o Bash, escriba:

    ```bash
    docker --version
    ```

### <a name="set-up-a-service-principal-for-using-powershell"></a>Configurar una entidad de servicio para usar PowerShell

Para usar PowerShell para acceder a los recursos de Azure Stack Hub, necesita una entidad de servicio en su inquilino de Azure Active Directory (Azure AD). Los permisos se delegan con el control de acceso basado en rol de usuario (RBAC). Es posible que tenga que solicitar la entidad de servicio a su operador de nube.

1. Para configurar la entidad de servicio, siga las instrucciones del artículo [Creación de entidades de servicio para otorgar a las aplicaciones acceso a los recursos de Azure Stack Hub](../operator/azure-stack-create-service-principals.md?view=azs-2002).

2. Anote el identificador de aplicación, el secreto, el identificador del inquilino y el identificador del objeto para usarlos más tarde.

## <a name="run-powershell-in-docker"></a>Ejecución de PowerShell en Docker

### <a name="az-modules"></a>[Modules de Az](#tab/az)

En estas instrucciones, ejecutará una imagen de contenedor basada en Linux que contenga PowerShell y los módulos necesarios para Azure Stack Hub.

1. Debe ejecutar Docker con el contenedor de Linux. Cuando ejecute Docker, cambie a los contenedores de Linux.

1. Ejecute Docker desde una máquina unida al mismo dominio que Azure Stack Hub. Si usa el Kit de desarrollo de Azure Stack (ASDK), deberá instalar [la VPN en la máquina remota](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn).


## <a name="install-azure-stack-hub-az-module-on-a-linux-container"></a>Instalación del módulo de Az de Azure Stack Hub en un contenedor de Linux

1. Desde la línea de comandos, ejecute el siguiente comando de Docker para ejecutar PowerShell en un contenedor de Ubuntu:

    ```bash
    docker run -it mcr.microsoft.com/azurestack/powershell
    ```

    Puede ejecutar Ubuntu, Debian o CentOS. Puede encontrar los siguientes archivos de Docker en el repositorio de GitHub: [azurestack-powershell](https://github.com/Azure/azurestack-powershell). Consulte el repositorio de GitHub para ver los últimos cambios en los archivos de Docker. Cada sistema operativo está etiquetado. Reemplace la etiqueta, la sección después de los dos puntos, por la etiqueta del sistema operativo que desee.

    | Linux | Imagen de Docker |
    | --- | --- |
    | Ubuntu | `docker run -it mcr.microsoft.com/azurestack/powershell:ubuntu-18.04` |
    | Debian | `docker run -it mcr.microsoft.com/azurestack/powershell:debian-9` |
    | CentOS | `docker run -it mcr.microsoft.com/azurestack/powershell:centos-7` |

2. El shell está listo para los cmdlets. Para probar la conectividad de Shell, inicie sesión y ejecute `Test-AzureStack.ps1`.

    En primer lugar, cree las credenciales de la entidad de servicio. Necesitará el **secreto** y el **identificador de aplicación**. También necesitará el **identificador de objeto**  al ejecutar `Test-AzureStack.ps1` para comprobar el contenedor. Es posible que tenga que solicitar una entidad de servicio a su operador de nube.

    Escriba los siguientes cmdlets para crear un objeto de entidad de servicio:

    ```powershell  
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    ```

5. Conéctese a su entorno mediante la ejecución del siguiente script con los siguientes valores de la instancia de Azure Stack Hub.

    | Value | Descripción |
    | --- | --- |
    | El nombre del entorno. | Nombre del entorno de Azure Stack Hub. |
    | Punto de conexión de Resource Manager | Dirección URL de Resource Manager. Si no lo sabe, póngase en contacto con su operador de nube. Se parecerá a esta `https://management.region.domain.com`. | 
    | Id. de inquilino del directorio | El identificador del inquilino del directorio de Azure Stack Hub. | 
    | Credential: | Objeto que contiene la entidad de servicio. En este caso, `$pscredential`.  |

    ```powershell
    ./Login-Environment.ps1 -Name <String> -ResourceManagerEndpoint <resource manager endpoint> -DirectoryTenantId <String> -Credential $pscredential
    ```

   PowerShell devuelve el objeto de cuenta.

7. Pruebe el entorno ejecutando el script de `Test-AzureStack.ps1` en el contenedor. Especifique el **identificador de objeto** de la entidad de servicio. Si no indica el identificador de objeto, el script se seguirá ejecutando, pero solo probará los módulos del inquilino (usuario) y se producirá un error en los módulos que requieren privilegios de administrador.

    ```powershell  
    ./Test-AzureStack.ps1 <Object ID>
    ```

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/rm)

En estas instrucciones, ejecutará una imagen de contenedor basada en Windows e instalará PowerShell y los módulos necesarios para Azure Stack Hub.

1. Deberá ejecutar Docker mediante contenedores de Windows que requieran Windows 10. Cuando ejecute Docker, cambie a los contenedores de Windows. Las imágenes que admiten el módulo de Az requerirán Docker 17.05 o una versión más reciente.

1. Ejecute Docker desde una máquina unida al mismo dominio que Azure Stack Hub. Si usa el Kit de desarrollo de Azure Stack (ASDK), deberá instalar [la VPN en la máquina remota](azure-stack-connect-azure-stack.md#connect-to-azure-stack-hub-with-vpn).

### <a name="install-azure-stack-hub-azurerm-module-on-a-windows-container"></a>Instalación del módulo de AzureRM de Azure Stack Hub en un contenedor de Windows

El archivo Dockerfile abre la imagen de Microsoft *microsoft/windowsservercore*, que tiene instalado Windows PowerShell 5.1. A continuación, el archivo carga NuGet y los módulos de PowerShell de Azure Stack Hub, y descarga las herramientas de Azure Stack Hub.

1. [Descargue el repositorio azure-stack-powershell](https://github.com/Azure-Samples/azure-stack-hub-powershell-in-docker.git) como archivo ZIP o clone el repositorio.

2. Abra la carpeta del repositorio desde el terminal.

3. Abra una interfaz de línea de comandos en el repositorio y escriba el comando siguiente:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. Una vez compilada la imagen, para iniciar un contenedor interactivo, escriba:

    ```bash  
    docker run -it azure-stack-powershell powershell
    ```

    Anote el nombre del contenedor. Puede usar el mismo contenedor en lugar de crear uno nuevo cada vez. Para ello, ejecute el siguiente comando de Docker:

    ```bash  
        docker exec -it "Container name" powershell
    ```

5. El shell está listo para los cmdlets.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Conéctese a la instancia de Azure Stack Hub mediante la entidad de servicio. Ahora está usando un símbolo del sistema de PowerShell en Docker. 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint <Your Azure Resource Manager endoint>
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId <TenantID> -ServicePrincipal -Credential $pscredential
    ```

   PowerShell devuelve el objeto de cuenta:

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Pruebe la conectividad mediante la creación de un grupo de recursos en Azure Stack Hub.

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

---

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información general de [PowerShell en Azure Stack Hub](azure-stack-powershell-overview.md).
- Obtenga más información sobre los [Perfiles de API para PowerShell](azure-stack-version-profiles.md) en Azure Stack Hub.
- Instalación de [PowerShell de Azure Stack Hub](../operator/azure-stack-powershell-install.md).
- Lea sobre la creación de [plantillas de Azure Resource Manager](azure-stack-develop-templates.md) para mantener la coherencia en la nube.
