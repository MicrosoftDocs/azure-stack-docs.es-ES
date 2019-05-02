---
title: Automatización de la validación de Azure Stack con PowerShell | Microsoft Docs
description: Puede automatizar la validación de Azure Stack con PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 39ed9ee9dab7f2ec97d2fb6a0148db333648b227
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "63447601"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatización de la validación de Azure Stack con PowerShell

La validación como servicio (VaaS) permite automatizar el inicio de las pruebas con el script **LaunchVaaSTests.ps1**.

> [!NOTE]  
> Automation solo está disponible para el flujo de trabajo de los pasos de pruebas. Los flujos de trabajo de validación de paquete y de validación de la solución solo se admiten en el portal de VaaS.

Este script se puede utilizar para:

> [!div class="checklist"]
> * Requisitos previos de instalación
> * Instalar e iniciar el agente local
> * Iniciar una categoría de pruebas, como *integración*, *funcional* o *confiabilidad*
> * Notificar resultados de prueba

## <a name="launch-the-test-pass-workflow"></a>Inicio del flujo de trabajo Prueba superada

1. Abra un símbolo del sistema de PowerShell con privilegios elevados.

2. Ejecute el script siguiente para descargar el script de automatización:

    ```powershell
    New-Item -ItemType Directory -Path <VaaSLaunchDirectory>
    Set-Location <VaaSLaunchDirectory>
    Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "LaunchVaaS.zip"
    Expand-Archive -Path ".\LaunchVaaS.zip" -DestinationPath .\ -Force
    ```

3. Ejecute el siguiente script con los valores de parámetros adecuados:

    ```powershell
    $VaaSAccountCreds = New-Object System.Management.Automation.PSCredential "<VaaSUserId>", (ConvertTo-SecureString "<VaaSUserPassword>" -AsPlainText -Force)
    .\LaunchVaaSTests.ps1 -VaaSAccountCreds $VaaSAccountCreds `
                          -VaaSAccountTenantId <VaaSAccountTenantId> `
                          -VaaSSolutionName <VaaSSolutionName> `
                          -VaaSTestPassName <VaaSTestPassName> `
                          -VaaSTestCategories Integration,Functional `
                          -MaxScriptWaitTimeInHours 12 `
                          -ServiceAdminUserName <AzSServiceAdminUser> `
                          -ServiceAdminUserPassword <AzSServiceAdminPassword> `
                          -TenantAdminUserName <AzSTenantAdminUser> `
                          -TenantAdminUserPassword <AzSTenantAdminPassword> `
                          -CloudAdminUserName <AzSCloudAdminUser> `
                          -CloudAdminUserPassword <AzSCloudAdminPassword>
    ```

    **Parámetros**

    | Parámetro | DESCRIPCIÓN |
    | --- | --- |
    | VaaSUserId | Identificador de usuario de VaaS. |
    | VaaSUserPassword | Contraseña de VaaS. |
    | VaaSAccountTenantId | El GUID del inquilino de VaaS. |
    | VaaSSolutionName | El nombre de la solución de VaaS en la que se ejecutará la prueba. |
    | VaaSTestPassName | El nombre del flujo de trabajo de ejecución de la prueba de VaaS que se va a crear. |
    | VaaSTestCategories | `Integration`, `Functional` o. `Reliability`. Si usa varios valores, separe cada valor por una coma.  |
    | ServiceAdminUserName | Cuenta de administrador del servicio de Azure Stack.  |
    | ServiceAdminPassword | Contraseña del servicio de Azure Stack.  |
    | TenantAdminUserName | Administrador del inquilino principal.  |
    | TenantAdminPassword | Contraseña del inquilino principal.  |
    | CloudAdminUserName | El nombre de usuario del administrador de la nube.  |
    | CloudAdminPassword | Contraseña del usuario administrador de la nube.  |

4. Revise los resultados de la prueba. Para otras opciones, consulte [Supervisión y administración de pruebas en el portal de VaaS](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre PowerShell en Azure Stack, revise los módulos más recientes.

> [!div class="nextstepaction"]
> [Módulo de Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
