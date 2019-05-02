---
title: Implementación del agente local | Microsoft Docs
description: Implemente al agente local para la validación como servicio de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1e194583b583bfc442a3c7b99a842ee788fc423c
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64312852"
---
# <a name="deploy-the-local-agent"></a>Implementación del agente local

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Aprenda a usar el agente local de validación como servicio (VaaS) para ejecutar pruebas de validación. Antes de ejecutar las pruebas de validación, se debe implementar el agente local.

> [!Note]  
> Debe asegurarse de que la máquina en la que se ejecuta el agente local no pierda el acceso saliente a Internet. Esta máquina debe ser accesible solo para los usuarios que tienen autorización para usar VaaS en nombre de su inquilino.

Para implementar el agente local:

1. Instale el agente local.
2. Realice las comprobaciones de integridad.
3. Ejecute el agente local.

## <a name="download-and-start-the-local-agent"></a>Descarga e inicio del agente local

Descargue el agente en una máquina que cumpla los requisitos previos del centro de datos y que tenga acceso a todos los puntos de conexión de Azure Stack. La máquina no debe formar parte del sistema de Azure Stack ni estar hospedada en la nube de Azure Stack.

### <a name="machine-prerequisites"></a>Requisitos previos del equipo

Compruebe que el equipo cumple los criterios siguientes:

- Acceso a todos los puntos de conexión de Azure Stack
- .NET 4.6 y PowerShell 5.0 instalados
- Al menos 8 GB de RAM
- Procesadores de 8 núcleos como mínimo
- Espacio en disco mínimo de 200 GB
- Conectividad de red estable a Internet

### <a name="download-and-install-the-agent"></a>Descarga e instalación del agente

1. Abra Windows PowerShell en un símbolo del sistema con privilegios elevados en el equipo que se usará para ejecutar las pruebas.
2. Ejecute el comando siguiente para descargar el agente local:

    ```powershell
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "OnPremAgent.zip"
    Expand-Archive -Path ".\OnPremAgent.zip" -DestinationPath VaaSOnPremAgent -Force
    Set-Location VaaSOnPremAgent\lib\net46
    ```

3. Ejecute el siguiente comando para instalar las dependencias del agente local:

    ```powershell
    $ServiceAdminCreds = New-Object System.Management.Automation.PSCredential "<aadServiceAdminUser>", (ConvertTo-SecureString "<aadServiceAdminPassword>" -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $AadTenantId `
                              -ServiceAdminCreds $ServiceAdminCreds `
                              -ArmEndpoint https://adminmanagement.$ExternalFqdn `
                              -Region $Region
    ```

    **Parámetros**

    | Parámetro | DESCRIPCIÓN |
    | --- | --- |
    | aadServiceAdminUser | Usuario administrador global del inquilino de Azure AD. Por ejemplo, puede ser vaasadmin@contoso.onmicrosoft.com. |
    | aadServiceAdminPassword | Contraseña del usuario administrador global. |
    | AadTenantId | Identificador del inquilino de Azure AD para la cuenta de Azure registrada en la validación como servicio. |
    | ExternalFqdn | Puede obtener el nombre de dominio completo en el archivo de configuración. Para obtener instrucciones, consulte [Parámetros comunes del flujo de trabajo en la validación de Azure Stack como servicio](azure-stack-vaas-parameters.md). |
    | Region | Región del inquilino de Azure AD. |

El comando descarga una imagen (VHD del sistema operativo) del repositorio de imágenes públicas (PIR) y la copia desde Azure Blob Storage al almacenamiento de Azure Stack.

![Requisitos previos para la descarga](media/installingprereqs.png)

> [!Note]
> Si tiene baja velocidad de red al descargar estas imágenes, puede descargarlas por separado en un recurso compartido local y especificar el parámetro **-LocalPackagePath** *recurso_compartido_o_ruta_de_acceso_local*. Encontrará más información acerca de la descarga de PIR en la sección [Control de situaciones de conectividad de red lenta](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity) del artículo [Solución de problemas de validación como servicio](azure-stack-vaas-troubleshoot.md).

## <a name="checks-before-starting-the-tests"></a>Comprobaciones antes de iniciar las pruebas

Las pruebas ejecutan operaciones remotas. La máquina que ejecuta las pruebas debe tener acceso a los puntos de conexión de Azure Stack, si no, las pruebas no funcionarán. Si usa el agente local de VaaS, utilice la máquina donde se va a ejecutar este. Puede comprobar que la máquina tiene acceso a los puntos de conexión de Azure Stack mediante la ejecución de las siguientes comprobaciones:

1. Compruebe que es posible acceder al URI base. Abra un símbolo del sistema o shell de Bash y ejecute el siguiente comando, donde `<EXTERNALFQDN>` se reemplaza por el FQDN externo de su entorno:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Abra un explorador web y vaya a `https://adminportal.<EXTERNALFQDN>` para comprobar que se puede acceder al portal de MAS.

3. Inicie sesión con los valores de nombre y contraseña del administrador de servicios de Azure AD proporcionados al crear la prueba superada.

4. Para comprobar el mantenimiento del sistema, ejecute el cmdlet **Test-AzureStack** de PowerShell como se describe en [Ejecución de una prueba de validación para Azure Stack](../operator/azure-stack-diagnostic-test.md). Corrija los errores y las advertencias antes de iniciar las pruebas.

## <a name="run-the-agent"></a>Ejecución del agente

1. Abra Windows PowerShell en un símbolo del sistema con privilegios elevados.

2. Ejecute el siguiente comando:

    ```powershell
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u <VaaSUserId> -t <VaaSTenantId>
    ```

      **Parámetros**  

    | Parámetro | DESCRIPCIÓN |
    | --- | --- |
    | VaaSUserId | Identificador de usuario utilizado para iniciar sesión en el portal de VaaS (por ejemplo, NombreUsuario\@Contoso.com) |
    | VaaSTenantId | Identificador del inquilino de Azure AD para la cuenta de Azure registrada en la validación como servicio. |

    > [!Note]  
    > Cuando se ejecuta el agente, el directorio de trabajo actual debe ser la ubicación del archivo ejecutable del host del motor de tareas **Microsoft.VaaSOnPrem.TaskEngineHost.exe**.

Si no ve notificado ningún error, la ejecución del agente local se realizó correctamente. El texto de ejemplo siguiente aparece en la ventana de la consola.

`Heartbeat Callback at 11/8/2016 4:45:38 PM`

![Agente iniciado](media/startedagent.png)

Un agente se identifica de forma única por su nombre. De forma predeterminada, usa el nombre de dominio completo (FQDN) de la máquina donde se inició. Se debe minimizar la ventana para evitar cualquier selección accidental en ella ya que, al cambiar el foco, se detienen todas las demás acciones.

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de la validación como servicio](azure-stack-vaas-troubleshoot.md)
- [Validation as a Service key concepts](azure-stack-vaas-key-concepts.md) (Conceptos clave de la validación como servicio)
- [Inicio rápido: Uso del portal de validación como servicio para programar la primera prueba](azure-stack-vaas-schedule-test-pass.md)