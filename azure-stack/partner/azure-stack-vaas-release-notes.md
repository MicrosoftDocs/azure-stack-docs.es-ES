---
title: Notas de la versión de validación como servicio de Azure Stack | Microsoft Docs
description: Notas de la versión de validación como servicio de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 9360e8e5f4db14fbebba2642ed11ca2e3830bdf1
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828402"
---
# <a name="release-notes-for-validation-as-a-service"></a>Notas de la versión para la validación como servicio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

En este artículo se incluyen las notas de la versión para la validación como servicio de Azure Stack.

## <a name="version-405"></a>Versión 4.0.5

7 de junio de 2019

- Se ha actualizado Cloud Simulation Engine en el flujo de trabajo de validación de paquetes para acelerar el tiempo de validación:  
    Tiempo de ejecución: Reducido a 6 horas  
    Versión: 5.1.13.0 -> 5.1.22.0  


17 de enero de 2019

- Prueba de identificación de disco actualizada para abordar las incoherencias de bloque de almacenamiento. Versión: 5.1.14.0 -> 5.1.15.0
- Comprobación de actualización mensual de Azure Stack actualizada para abordar las incoherencias de validación de contenido y software aprobados. Versión: 5.1.14.0 -> 5.1.17.0
- Comprobación del paquete de extensión de OEM actualizado para llevar a cabo las comprobaciones necesarias antes del paso de actualización de Azure Stack. Versión: 5.1.14.0 -> 5.1.16.0
- Correcciones de errores internos

## <a name="version-402"></a>Versión 4.0.2

7 de enero de 2019

Si ejecuta el flujo de trabajo de verificación de actualización mensual de Azure Stack, y la versión para el paquete de actualización de OEM no es 1810 o posterior, recibirá un error cuando se encuentre en el paso de actualización de OEM. Este es un error. Se está desarrollando una corrección. Los pasos de navegación son los siguientes:

1. Ejecute la actualización de OEM con normalidad.
2. Ejecute Test-AzureStack después de la aplicación correcta del paquete y guarde el resultado.
3. Cancele la prueba.
4. Envíe el resultado guardado a VaaSHelp@microsoft.com para recibir resultados satisfactorios para la ejecución.

## <a name="version-402"></a>Versión 4.0.2

30 de noviembre de 2018

- Correcciones de errores internos

## <a name="version-401"></a>Versión 4.0.1

8 de octubre de 2018

- Requisitos previos de VaaS

    `Install-VaaSPrerequisites` ya no requiere credenciales de administrador en la nube. Si ejecuta la versión más reciente de este cmdlet, consulte [Download and install the agent](azure-stack-vaas-local-agent.md#download-and-install-the-agent) (Descarga e instalación del agente) para los comandos revisados para instalar los requisitos previos. Estos son los comandos:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

## <a name="version-400"></a>Versión 4.0.0

29 de agosto de 2018

- Requisitos previos de VaaS y actualizaciones de VHD

    `Install-VaaSPrerequisites` ahora requiere credenciales de administrador en la nube para solucionar un problema durante la validación del paquete. La documentación en [Download and install the agent](azure-stack-vaas-local-agent.md#download-and-install-the-agent) (Descarga e instalación del agente) se ha actualizado con lo siguiente:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    $CloudAdminCreds = New-Object System.Management.Automation.PSCredential "<cloudAdminDomain\username>", (ConvertTo-SecureString "<cloudAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region `
                              -CloudAdminCredentials $CloudAdminCreds
    ```
    > [!NOTE]
    > Los valores de `$CloudAdminCreds` que requiere el script corresponden a la instancia de Azure Stack que se está validando. No son las credenciales de Azure Active Directory usadas por el inquilino de VaaS.

- Actualización del agente local

    La versión anterior del agente local no es compatible con la versión actual 4.0.0 del servicio. Todos los usuarios deben actualizar sus agentes locales. Consulte [Download and install the agent](azure-stack-vaas-local-agent.md#download-and-install-the-agent) (Descarga e instalación del agente) para obtener instrucciones sobre cómo instalar el agente más reciente.

- Actualización de automatización de PowerShell

    Se realizaron cambios en scripts `LaunchVaaSTests` de PowerShell que requieren la versión más reciente de los paquetes de scripting. Consulte [Inicio del flujo de trabajo de la ejecución de la prueba](azure-stack-vaas-automate-with-powershell.md#launch-the-test-pass-workflow) para obtener instrucciones sobre cómo instalar la versión más reciente del paquete de scripting.

- Portal de validación como servicio

  - Notificaciones de firma de paquetes

    Cuando se envía un paquete de personalización de OEM como parte del flujo de trabajo de validación del paquete, se valida el formato del paquete para asegurarse de que sigue la especificación publicada. Si el paquete no cumple, se producirá un error en la ejecución. Se enviarán notificaciones por correo electrónico a la dirección de correo electrónico del contacto de Azure Active Directory registrada para el inquilino.

  - Categoría de prueba interactiva

    Se ha agregado la categoría de prueba **interactiva**. Estas pruebas usan escenarios de Azure Stack interactivos, no automatizados.

  - Comprobación de características interactivas

    La capacidad para proporcionar comentarios enfocados para determinadas características ahora está disponible en el flujo de trabajo de pruebas superadas. La prueba `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` comprueba si actualizaciones específicas se aplicaron correctamente y, a continuación, recopila comentarios.

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de la validación como servicio](azure-stack-vaas-troubleshoot.md)
