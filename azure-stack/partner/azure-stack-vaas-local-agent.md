---
title: Implementación del agente local
titleSuffix: Azure Stack Hub
description: Aprenda a implementar el agente local para la validación como servicio de Azure Stack Hub.
author: mattbriggs
ms.topic: quickstart
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: a6358b7b9baff76726b2bd1282f525184a071c05
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77704682"
---
# <a name="deploy-the-local-agent"></a>Implementación del agente local

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Aprenda a usar el agente local de validación como servicio (VaaS) para ejecutar pruebas de validación. Antes de ejecutar las pruebas de validación, se debe implementar el agente local.

> [!Note]  
> Debe asegurarse de que la máquina en la que se ejecuta el agente local no pierda el acceso saliente a Internet. Esta máquina debe ser accesible solo para los usuarios que tienen autorización para usar VaaS en nombre de su inquilino.

Para implementar el agente local:

1. Descargue e instale el agente local.
2. Realice las comprobaciones de integridad antes de iniciar las pruebas.
3. Ejecute el agente local.

## <a name="download-and-start-the-local-agent"></a>Descarga e inicio del agente local

Descargue el agente en una máquina que cumpla los requisitos previos del centro de datos y que tenga acceso a todos los puntos de conexión de Azure Stack Hub. La máquina no debe formar parte del sistema de Azure Stack Hub ni estar hospedada en la nube de Azure Stack Hub.

### <a name="machine-prerequisites"></a>Requisitos previos del equipo

Compruebe que el equipo cumple los criterios siguientes:

- Acceso a todos los puntos de conexión de Azure Stack Hub.
- .NET 4.6 y PowerShell 5.0 instalados.
- Al menos 8 GB de RAM.
- Procesadores de 8 núcleos como mínimo.
- Espacio en disco mínimo de 200 GB.
- Conectividad de red estable a Internet.

### <a name="download-and-install-the-local-agent"></a>Descarga e instalación del agente local

1. Abra Windows PowerShell en un símbolo del sistema con privilegios elevados en la máquina que se usará para ejecutar las pruebas.
2. Ejecute el siguiente comando para descargar e instalar las dependencias del agente local y copiar las imágenes del repositorio de imágenes públicas (PIR) (disco duro virtual del sistema operativo) en el entorno de Azure Stack Hub.

    ```powershell
    # Review and update the following five parameters
    $RootFolder = "c:\VaaS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $AadServiceAdminUserName = "<AAD service admin user name>"
    $AadServiceAdminPassword = "<AAD service admin password>"

    if (-not(Test-Path($RootFolder))) {
        mkdir $RootFolder
    }
    Set-Location $RootFolder
    Invoke-WebRequest -Uri "https://storage.azurestackvalidation.com/packages/Microsoft.VaaSOnPrem.TaskEngineHost.latest.nupkg" -outfile "$rootFolder\OnPremAgent.zip"
    Expand-Archive -Path "$rootFolder\OnPremAgent.zip" -DestinationPath "$rootFolder\VaaSOnPremAgent" -Force
    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"

    $cloudAdminCredential = New-Object System.Management.Automation.PSCredential($cloudAdmindUserName, (ConvertTo-SecureString $cloudAdminPassword -AsPlainText -Force))
    $getStampInfoUri = "https://ASAppGateway:4443/ServiceTypeId/4dde37cc-6ee0-4d75-9444-7061e156507f/CloudDefinition/GetStampInformation" 
    $stampInfo = Invoke-RestMethod -Method Get -Uri $getStampInfoUri -Credential $cloudAdminCredential -ErrorAction Stop
    $serviceAdminCreds = New-Object System.Management.Automation.PSCredential $aadServiceAdminUserName, (ConvertTo-SecureString $aadServiceAdminPassword -AsPlainText -Force)
    Import-Module .\VaaSPreReqs.psm1 -Force
    Install-VaaSPrerequisites -AadTenantId $stampInfo.AADTenantID `
                            -ServiceAdminCreds $serviceAdminCreds `
                            -ArmEndpoint $stampInfo.AdminExternalEndpoints.AdminResourceManager `
                            -Region $stampInfo.RegionName
    ```

> [!Note]  
> El cmdlet `Install-VaaSPrerequisites` descarga archivos de imagen de máquina virtual de gran tamaño. Si la red funciona con lentitud, puede descargar archivos en el servidor de archivos local y agregar manualmente las imágenes de las máquinas virtuales a su entorno de pruebas. Para más información, consulte [Control de situaciones de conectividad de red lenta](azure-stack-vaas-troubleshoot.md#handle-slow-network-connectivity).

**Parámetros**

| Parámetro | Descripción |
| --- | --- |
| `AadServiceAdminUser` | Usuario administrador global del inquilino de Azure AD. Por ejemplo: vaasadmin@contoso.onmicrosoft.com. |
| `AadServiceAdminPassword` | Contraseña del usuario administrador global. |
| `CloudAdminUserName` | El usuario administrador de la nube puede acceder a comandos permitidos, y ejecutarlos, en el punto de conexión con privilegios. Por ejemplo: AzusreStack\CloudAdmin. Para más información, consulte [Parámetros comunes de flujo de trabajo para VaaS](azure-stack-vaas-parameters.md). |
| `CloudAdminPassword` | La contraseña de la cuenta de administrador de la nube.|

![Descarga de los requisitos previos del agente local](media/installing-prereqs.png)

## <a name="perform-sanity-checks-before-starting-the-tests"></a>Realice las comprobaciones de integridad antes de iniciar las pruebas

Las pruebas ejecutan operaciones remotas. La máquina que ejecuta las pruebas debe tener acceso a los puntos de conexión de Azure Stack Hub ya que, en caso contrario, las pruebas no funcionarán. Si usa el agente local de VaaS, utilice la máquina donde se va a ejecutar este. Puede comprobar que la máquina tiene acceso a los puntos de conexión de Azure Stack Hub mediante la ejecución de las siguientes comprobaciones:

1. Compruebe que es posible acceder al URI base. Abra un símbolo del sistema o shell de Bash y ejecute el siguiente comando, donde `<EXTERNALFQDN>` se reemplaza por el nombre de dominio completo externo de su entorno:

    ```bash
    nslookup adminmanagement.<EXTERNALFQDN>
    ```

2. Abra un explorador y vaya a `https://adminportal.<EXTERNALFQDN>` para comprobar que se puede acceder al portal de MAS.

3. Inicie sesión con los valores de nombre y contraseña del administrador de servicios de Azure AD proporcionados al crear la prueba superada.

4. Para comprobar el mantenimiento del sistema, ejecute el cmdlet **Test-AzureStack** de PowerShell como se describe en [Ejecución de una prueba de validación para Azure Stack Hub](../operator/azure-stack-diagnostic-test.md). Corrija los errores y las advertencias antes de iniciar las pruebas.

## <a name="run-the-local-agent"></a>Ejecución del agente local

1. Abra Windows PowerShell en un símbolo del sistema con privilegios elevados.

2. Ejecute el siguiente comando:

    ```powershell
   # Review and update the following five parameters
    $RootFolder = "c:\VAAS"
    $CloudAdmindUserName = "<Cloud admin user name>"
    $CloudAdminPassword = "<Cloud admin password>"
    $VaaSUserId = "<VaaS user ID>"
    $VaaSTenantId = "<VaaS tenant ID>"

    Set-Location "$rootFolder\VaaSOnPremAgent\lib\net46"
    .\Microsoft.VaaSOnPrem.TaskEngineHost.exe -u $VaaSUserId -t $VaaSTenantId -x $CloudAdmindUserName -y $CloudAdminPassword
    ```

      **Parámetros**  

    | Parámetro | Descripción |
    | --- | --- |
    | `CloudAdminUserName` | El usuario administrador de la nube puede acceder a comandos permitidos, y ejecutarlos, en el punto de conexión con privilegios. Por ejemplo: AzusreStack\CloudAdmin. Para más información, consulte [Parámetros comunes de flujo de trabajo para VaaS](azure-stack-vaas-parameters.md). |
    | `CloudAdminPassword` | La contraseña de la cuenta de administrador de la nube.|
    | `VaaSUserId` | El identificador de usuario se utilizó para iniciar sesión en el portal de Azure Stack Hub Validation. Por ejemplo: UserName\@Contoso.com). |
    | `VaaSTenantId` | Identificador del inquilino de Azure AD para la cuenta de Azure registrada en la validación como servicio. |

    > [!Note]  
    > Cuando se ejecuta el agente, el directorio de trabajo actual debe ser la ubicación del archivo ejecutable del host del motor de tareas **Microsoft.VaaSOnPrem.TaskEngineHost.exe**.

Si no ve notificado ningún error, la ejecución del agente local se realizó correctamente. El texto de ejemplo siguiente aparece en la ventana de la consola.

`Heartbeat was sent successfully.`

![Agente iniciado](media/started-agent.png)

Un agente se identifica de forma única por su nombre. De forma predeterminada, usa el nombre de dominio completo de la máquina donde se inició. Se debe minimizar la ventana para evitar cualquier selección accidental en ella ya que, al cambiar el foco, se detienen todas las demás acciones.

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de la validación como servicio](azure-stack-vaas-troubleshoot.md)
- [Validation as a Service key concepts](azure-stack-vaas-key-concepts.md) (Conceptos clave de la validación como servicio)
- [Inicio rápido: Uso del portal de Azure Stack Hub Validation para programar la primera prueba](azure-stack-vaas-schedule-test-pass.md)