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
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: eb676d914a345c5fe36e063ee08ea226cd1e3209
ms.sourcegitcommit: 1cb84d8417367b553194614d53a477a94a8e9be9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2020
ms.locfileid: "75755175"
---
# <a name="release-notes-for-validation-as-a-service"></a>Notas de la versión para la validación como servicio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

En este artículo se incluyen las notas de la versión para la validación como servicio de Azure Stack.

## <a name="version-4421"></a>Versión 4.4.2.1

9 de enero de 2020

- Prueba de las actualizaciones de contenido
  - Flujo de trabajo de validación de OEM (Versión 5.1.52.0 -> 5.1.53.0): Se ha reducido el número de parámetros necesarios del panel de programación de pruebas. 
  - Corrección para la prueba de Compute: TestVMOperations
    
- Problemas conocidos
  - Póngase en contacto con vaashelp@microsoft.com si los siguientes casos de prueba no se pueden ejecutar durante el flujo de trabajo de validación de OEM:
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk

3 de diciembre de 2019

- Prueba de las actualizaciones de contenido
  - Se ha actualizado la documentación en línea para el flujo de trabajo mensual de actualización de Azure Stack y el flujo de trabajo de validación de paquetes de OEM. Revise la documentación actualizada aquí en "Validación de paquetes de OEM" y en "Validación de las actualizaciones de software de Microsoft"
  - Actualización del flujo de trabajo de validación de paquetes de VaaS: El flujo de trabajo de validación de OEM es la única prueba necesaria para la comprobación de actualizaciones mensual de Azure Stack y la validación de paquetes OEM. La prueba actualiza la marca con los paquetes de AzureStack y OEM proporcionados, y ejecuta las pruebas de comprobación de Cloud Simulation Engine.
  - Actualización de la extensión de PowerShell de VaaS: Ahora se admite la automatización del flujo de trabajo de validación de paquetes. Consulte la automatización de VaaS de Azure Stack con PowerShell para más información sobre la ubicación y las instrucciones paso a paso para usar esta extensión.

- Problemas conocidos
  - Póngase en contacto con vaashelp@microsoft.com si los siguientes casos de prueba no se pueden ejecutar durante el flujo de trabajo de validación de OEM:
    - Test101LinuxEmptyAttachedDiskManagedDisk
    - Test101WindowsEmptyAttachedDiskManagedDisk


## <a name="version-4353"></a>Versión 4.3.5.3

7 de noviembre de 2019

- Prueba de las actualizaciones de contenido
  - Comprobación mensual de actualizaciones de Azure Stack (versiones 5.1.46.0 -> 5.1.49.0)
  - Comprobación del paquete de extensión de OEM (versiones 5.1.46.0 -> 5.1.49.0)
  - Se han conservado los resultados de 5.1.46.0. Si se ha ejecutado correctamente en 5.1.46.0, envíe una notificación a vaashelp@microsoft.com al enviar resultados.

- Corrección de errores
  - Se corrigió un problema por el que no se podía ejecutar la comprobación mensual de actualizaciones de Azure Stack si el archivo .zip de la actualización contenía caracteres especiales.

- Problemas conocidos
  - Las pruebas de VaaS producirán un error si no se encuentra el archivo mstest.exe. Solución alternativa:
    1. Presione CTRL + C en el agente en la ventana de PowerShell.
    1. Escriba mstest.exe para comprobar que es un programa reconocido.
    1. Si no se reconoce mstest.exe, cierre la ventana actual de PowerShell.
    1. Haga clic en Inicio (no en PowerShell en la barra de tareas), busque PowerShell y ábralo como administrador.
    1. Escriba mstest.exe y compruebe si está disponible como comando.
    1. Reinicie el agente y vuelva a ejecutar la prueba.
  - En ocasiones, Cloud Simulation Engine informará de errores en las pruebas de \*máquinas virtuales. Póngase en contacto con vaashelp@microsoft.com antes de intentar una nueva ejecución. 


29 de octubre de 2019

- Se ha actualizado la documentación en línea para el flujo de trabajo mensual de actualización de Azure Stack y el flujo de trabajo de validación de paquetes de OEM.

    Revise la documentación actualizada aquí en "Validación de paquetes de OEM" y en "Validación de las actualizaciones de software de Microsoft"
- Actualización del flujo de trabajo de VaaS: Actualización mensual de Azure Stack (versiones 5.1.30.0 -> 5.1.46.0): se ha actualizado el flujo de trabajo mensual de prueba de comprobación de actualizaciones de Azure Stack.

    El flujo de trabajo ya no requiere ninguna intervención manual y se puede programar para que se ejecute sin problemas.
- Actualización del flujo de trabajo de VaaS: Validación de paquetes de OEM (versiones 5.1.30.0 -> 5.1.46.0): se ha actualizado el flujo de trabajo de validación de paquetes de OEM.

    El flujo de trabajo ya no requiere ninguna intervención manual y se puede programar para que se ejecute sin problemas.
- Se ha actualizado Cloud Simulation Engine en el flujo de trabajo de validación de paquetes de OEM (versiones 5.1.30.0 -> 5.1.46.0) para acelerar el tiempo de validación: El tiempo de ejecución se ha reducido a 1 hora.
- Cloud Simulation Engine en el flujo de trabajo de validación de paquetes de OEM y el flujo de trabajo de actualización de Azure Stack (versiones 5.1.30.0 -> 5.1.46.0) requiere que las actualizaciones se validen en dos carpetas primarias distintas sin que haya otras actualizaciones en las carpetas secundarias.
- Cloud Simulation Engine en el flujo de trabajo de validación de paquetes de OEM y el flujo de trabajo de actualización de Azure Stack (versiones 5.1.30.0 -> 5.1.46.0) requiere que las pruebas se programen en el siguiente orden: prueba de comprobación mensual de actualizaciones de Azure Stack, prueba de comprobación de paquetes de extensión de OEM y finalmente Cloud Simulation Engine.
- Actualización del agente de VaaS: El agente de VaaS actualizado usa ahora las credenciales de administrador de la nube de Azure Stack para consultar la marca y obtener la información de esta para rellenar automáticamente los flujos de trabajo. 

    Esta actualización requiere que todos los agentes se actualicen y se reinicien. Consulte estas instrucciones sobre cómo actualizar el agente de VaaS: https://docs.microsoft.com/azure-stack/partner/azure-stack-vaas-local-agent
- Actualización de la interfaz de usuario del portal de VaaS: Se ha trasladado la tabla de selección de agente por encima del panel de programación de pruebas para facilitar las pruebas.

    Al programar un trabajo, ya no es necesario escribir la información de la marca si los agentes de VaaS se han actualizado correctamente.


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

    `Install-VaaSPrerequisites` ya no requiere credenciales de administrador en la nube. Si ejecuta la versión más reciente de este cmdlet, consulte [Descarga e instalación del agente local](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent), ya que ahí encontrará los comandos revisados para instalar los requisitos previos. Estos son los comandos:

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

    `Install-VaaSPrerequisites` ahora requiere credenciales de administrador en la nube para solucionar un problema durante la validación del paquete. La documentación de [Descarga e instalación del agente local](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) se ha actualizado con lo siguiente:

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

    La versión anterior del agente local no es compatible con la versión actual 4.0.0 del servicio. Todos los usuarios deben actualizar sus agentes locales. Consulte [Descarga e instalación del agente local](azure-stack-vaas-local-agent.md#download-and-install-the-local-agent) para obtener instrucciones acerca de la instalación del agente más reciente.

- Actualización de automatización de PowerShell

    Se realizaron cambios en scripts `LaunchVaaSTests` de PowerShell que requieren la versión más reciente de los paquetes de scripting. Consulte [Inicio del flujo de trabajo de la ejecución de la prueba](azure-stack-vaas-automate-with-powershell.md) para obtener instrucciones sobre cómo instalar la versión más reciente del paquete de scripting.

- Portal de validación como servicio

  - Notificaciones de firma de paquetes

    Cuando se envía un paquete de personalización de OEM como parte del flujo de trabajo de validación del paquete, se valida el formato del paquete para asegurarse de que sigue la especificación publicada. Si el paquete no cumple, se producirá un error en la ejecución. Se enviarán notificaciones por correo electrónico a la dirección de correo electrónico del contacto de Azure Active Directory registrada para el inquilino.

  - Categoría de prueba interactiva

    Se ha agregado la categoría de prueba **interactiva**. Estas pruebas usan escenarios de Azure Stack interactivos, no automatizados.

  - Comprobación de características interactivas

    La capacidad para proporcionar comentarios enfocados para determinadas características ahora está disponible en el flujo de trabajo de pruebas superadas. La prueba `OEM Update on Azure Stack 1806 RC Validation 5.1.4.0` comprueba si actualizaciones específicas se aplicaron correctamente y, a continuación, recopila comentarios.

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de la validación como servicio](azure-stack-vaas-troubleshoot.md)
