---
title: Uso de Docker para ejecutar PowerShell en Azure Stack | Microsoft Docs
description: Uso de Docker para ejecutar PowerShell en Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: powershell
ms.topic: article
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 07/09/2019
ms.openlocfilehash: 118f29c46a1b11c07c62407f19b86aa28ada3bd1
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277789"
---
# <a name="use-docker-to-run-powershell-in-azure-stack"></a>Uso de Docker para ejecutar PowerShell en Azure Stack

En este artículo, se usa Docker para crear contenedores basados en Windows en los que se ejecutará la versión de PowerShell necesaria para trabajar con las distintas interfaces. En Docker, debe usar contenedores basados en Windows.

## <a name="docker-prerequisites"></a>Requisitos previos de Docker

1. Instale [Docker](https://docs.docker.com/install/).

1. En una herramienta de línea de comandos, como Powershell o Bash, escriba:

    ```bash
        Docker --version
    ```

1. Deberá ejecutar Docker mediante contenedores de Windows que requieran Windows 10. Cuando ejecute Docker, cambie a los contenedores de Windows.

1. Ejecute Docker desde una máquina unida al mismo dominio que Azure Stack. Si usa el Kit de desarrollo de Azure Stack (ASDK), deberá instalar [la VPN en la máquina remota](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="set-up-a-service-principal-for-using-powershell"></a>Configurar una entidad de servicio para usar PowerShell

Para usar PowerShell para acceder a los recursos de Azure Stack, necesita una entidad de servicio en su inquilino de Azure Active Directory (Azure AD). Los permisos se delegan con el control de acceso basado en rol de usuario (RBAC).

1. Para configurar la entidad de servicio, siga las instrucciones del artículo [Creación de entidades de servicio para otorgar a las aplicaciones acceso a los recursos de Azure Stack](azure-stack-create-service-principals.md).

2. Anote el identificador de aplicación, el secreto y el identificador del inquilino para usarlos más tarde.

## <a name="docker---azure-stack-api-profiles-module"></a>Docker: módulo de perfiles de la API de Azure Stack

El archivo Dockerfile abre la imagen de Microsoft *microsoft/windowsservercore*, que tiene instalado Windows PowerShell 5.1. A continuación, el archivo carga NuGet y los módulos de PowerShell de Azure Stack, y descarga las herramientas de Azure Stack.

1. [Descargue el repositorio azure-stack-powershell](https://github.com/mattbriggs/azure-stack-powershell) como archivo ZIP o clone el repositorio.

2. Abra la carpeta del repositorio desde el terminal.

3. Abra una interfaz de línea de comandos en el repositorio y escriba el comando siguiente:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. Una vez compilada la imagen, para iniciar un contenedor interactivo, escriba:

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. El shell está listo para los cmdlets.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Conéctese a la instancia de Azure Stack mediante la entidad de servicio. Ahora está usando un símbolo del sistema de PowerShell en Docker. 

    ```powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell devuelve el objeto de cuenta:

    ```powershell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Pruebe la conectividad mediante la creación de un grupo de recursos en Azure Stack.

    ```powershell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>Pasos siguientes

-  Obtenga información general de [PowerShell en Azure Stack](azure-stack-powershell-overview.md).
- Obtenga más información sobre los [Perfiles de API para PowerShell](azure-stack-version-profiles.md) en Azure Stack.
- Instale [PowerShell de Azure Stack](../operator/azure-stack-powershell-install.md).
- Lea sobre la creación de [plantillas de Azure Resource Manager](azure-stack-develop-templates.md) para mantener la coherencia en la nube.
