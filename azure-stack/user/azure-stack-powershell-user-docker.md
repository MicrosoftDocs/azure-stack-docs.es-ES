---
title: Uso de Docker para ejecutar PowerShell en Azure Stack | Microsoft Docs
description: Uso de Docker para ejecutar PowerShell en Azure Stack
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
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 0eacd2c5a058bca68e86f2d34df8d3cc91987c1c
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985553"
---
# <a name="use-docker-to-run-powershell"></a>Uso de Docker para ejecutar PowerShell

Puede usar Docker para crear los contenedores basados en Windows en los que se ejecute la versión específica de PowerShell necesaria para que las distintas interfaces funcionen. Debe usar contenedores basados en Windows en Docker.

## <a name="docker-prerequisites"></a>Requisitos previos de Docker

1. Instale [Docker](https://docs.docker.com/install/).
2. Abra una línea de comandos, como Powershell o Bash, y escriba:

    ```bash
        Docker -version
    ```

3. Deberá ejecutar Docker con contenedores de Windows que requieran Windows 10. Al ejecutar Docker, cambie a los contenedores de Windows.

4. Debe ejecutar Docker desde una máquina unida al mismo dominio que Azure Stack. Si usa el ASDK, deberá instalar [la VPN en la máquina remota](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn).

## <a name="service-principals-for-using-powershell"></a>Entidades de servicio para usar PowerShell

Necesita a una entidad de servicio en su inquilino de Azure AD para usar PowerShell para acceder al recurso en Azure Stack. Los permisos se delegan con el control de acceso basado en rol de usuario.

1. Configure la entidad de servicio siguiendo las instrucciones del artículo [Give applications access to Azure Stack resources by creating service principals](azure-stack-create-service-principals.md) (Concesión de acceso a las aplicaciones a los recursos de Azure Stack mediante la creación de entidades de servicio).
2. Anote el identificador de aplicación, el secreto y el identificador del inquilino.

## <a name="docker---azure-stack-api-profiles-module"></a>Docker: módulo de perfiles de API de Azure Stack

El archivo Dockerfile abre la imagen de Microsoft microsoft/windowsservercore, que tiene instalado Windows PowerShell 5.1. El archivo carga NuGet, los módulos de PowerShell de Azure Stack, y descarga las herramientas de Azure Stack.

1. Descargue este repositorio como archivo ZIP o clónelo:  
[https://github.com/mattbriggs/azure-stack-powershell](https://github.com/mattbriggs/azure-stack-powershell)

2. Abra la carpeta del repositorio desde el terminal.

3. Abra una interfaz de la línea de comandos en el repositorio y escriba:

    ```bash  
    docker build --tag azure-stack-powershell .
    ```

4. Podrá iniciar un contenedor interactivo una vez compilada la imagen. Escriba: 

    ```bash  
        docker run -it azure-stack-powershell powershell
    ```

5. El shell está listo para los cmdlets.

    ```bash
    Windows PowerShell
    Copyright (C) 2016 Microsoft Corporation. All rights reserved.

    PS C:\>
    ```

6. Conéctese a Azure Stack con la entidad de servicio. Ahora está usando un símbolo del sistema de PowerShell en Docker. 

    ```Powershell
    $passwd = ConvertTo-SecureString <Secret> -AsPlainText -Force
    $pscredential = New-Object System.Management.Automation.PSCredential('<ApplicationID>', $passwd)
    Connect-AzureRmAccount -ServicePrincipal -Credential $pscredential -TenantId <TenantID>
    ```

   PowerShell devuelve el objeto de cuenta:

    ```PowerShell  
    Account    SubscriptionName    TenantId    Environment
    -------    ----------------    --------    -----------
    <AccountID>    <SubName>       <TenantID>  AzureCloud
    ```

7. Pruebe la conectividad mediante la creación de un grupo de recursos en Azure Stack.

    ```PowerShell  
    New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
    ```

## <a name="next-steps"></a>Pasos siguientes

-  Obtenga información general de [PowerShell en Azure Stack](azure-stack-powershell-overview.md).
- Obtenga información sobre los [perfiles de API para PowerShell](azure-stack-version-profiles.md) en Azure Stack.
- [Instale PowerShell en Azure Stack](../operator/azure-stack-powershell-install.md).
- Obtenga información sobre la creación de [plantillas de Azure Resource Manager](azure-stack-develop-templates.md) para mantener la coherencia en la nube.