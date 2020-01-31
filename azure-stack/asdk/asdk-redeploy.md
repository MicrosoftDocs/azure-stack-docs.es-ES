---
title: Reimplementación del Kit de desarrollo de Azure Stack
description: Obtenga información sobre cómo implementar el Kit de desarrollo de Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: d0fc4539b581474c9db2a2dbb05495c9b1bce695
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76873522"
---
# <a name="redeploy-the-asdk"></a>Reimplementación del Kit de desarrollo de Azure Stack
En este artículo, aprenderá a volver a implementar el Kit de desarrollo de Azure Stack (ASDK) en un entorno que no sea de producción. Dado que la actualización del Kit de desarrollo de Azure Stack, debe volver a implementarla completamente para pasar a una versión más reciente. También puede volver a implementar el ASDK cuando quiera comenzar desde cero.

> [!IMPORTANT]
> No se admite la actualización del Kit de desarrollo de Azure Stack a una nueva versión. Tendrá que volver a implementar el ASDK en el equipo host de dicho ASDK cada vez que quiera evaluar una versión más reciente de Azure Stack.

## <a name="remove-azure-registration"></a>Quitar el registro de Azure 
Si ha registrado anteriormente la instalación del ASDK con Azure, debe quitar el recurso de registro antes de volver a implementar el ASDK. Vuelva a registrar el Kit de desarrollo de Azure Stack para habilitar la disponibilidad de los elementos en Marketplace cuando se vuelve a implementar el kit de desarrollo. Si no ha registrado previamente el ASDK con su suscripción a Azure, puede omitir esta sección.

Para quitar el recurso de registro, utilice el cmdlet **Remove-AzsRegistration** para anular el registro de Azure Stack. Después, use el cmdlet **Remove-AzureRMResourceGroup** para eliminar el grupo de recursos de Azure Stack de su suscripción de Azure:

1. Abra una consola de PowerShell como administrador en un equipo que tenga acceso al punto de conexión con privilegios. En el caso del ASDK, este es el equipo host del kit de desarrollo.

2. Ejecute los siguientes comandos de PowerShell para anular el registro de la instalación de Kit de desarrollo de Azure Stack y eliminar el grupo de recursos **azurestack** de su suscripción de Azure:

   ```powershell    
   #Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Provide Azure subscription admin credentials
   Add-AzureRmAccount

   # Provide ASDK admin credentials
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the cloud domain credentials to access the privileged endpoint"

   # Unregister Azure Stack
   Remove-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint AzS-ERCS01

   # Remove the Azure Stack resource group
   Remove-AzureRmResourceGroup -Name azurestack -Force
   ```

3. Se le pedirá que inicie sesión tanto en su suscripción a Azure como en la instalación local del ASDK cuando se ejecute el script.
4. Cuando el script se complete, debería ver mensajes similares a los siguientes ejemplos:

    `De-Activating Azure Stack (this may take up to 10 minutes to complete).` `Your environment is now unable to syndicate items and is no longer reporting usage data.`
    `Remove registration resource from Azure...`
    `"Deleting the resource..." on target "/subscriptions/<subscription information>"`
    `********** End Log: Remove-AzsRegistration *********`



Azure Stack ahora puede anular correctamente el registro de la suscripción de Azure. También se debe eliminar el grupo de recursos de Azure Stack. Este grupo de recursos es el que se creó cuando registró por primera vez el ASDK con Azure.

## <a name="deploy-the-asdk"></a>Implementación del ASDK
Para volver a implementar Azure Stack, debe empezar desde el principio, tal y como se describe a continuación. Los pasos son diferentes dependiendo de si utiliza o no el script del instalador (asdk-installer.ps1) de Azure Stack para instalar el Kit de desarrollo de Azure Stack.

### <a name="redeploy-the-asdk-using-the-installer-script"></a>Reimplementación del Kit de desarrollo de Azure Stack con el script del instalador
1. En el equipo del Kit de desarrollo de Azure Stack, abra una consola de PowerShell con privilegios elevados y vaya al script asdk-installer.ps1 en el directorio **AzureStack_Installer** ubicado en una unidad no sea del sistema. Ejecute el script y haga clic en **Reiniciar**.

   ![Ejecución del script asdk-installer.ps1](media/asdk-redeploy/1.png)

2. Seleccione el sistema operativo base (no **Azure Stack**) y haga clic en **Siguiente**.

   ![Reinicio en el sistema operativo host](media/asdk-redeploy/2.png)

3. Después de que el host del ASDK se reinicie en el sistema operativo base, inicie sesión como administrador local. Localice y elimine el archivo **C:\CloudBuilder.vhdx** que se utilizó como parte de la implementación anterior.

4. Repita los mismos pasos realizados para [implementar primero el Kit de desarrollo de Azure Stack](asdk-install.md).

### <a name="redeploy-the-asdk-without-using-the-installer"></a>Reimplementación del Kit de desarrollo de Azure Stack sin utilizar el instalador
Si no usó el script asdk-installer.ps1 para instalar el ASDK, debe reconfigurar manualmente el equipo host del ASDK antes de volver a implementar el ASDK.

1. Inicie la utilidad de configuración del sistema mediante la ejecución de **msconfig.exe** en el equipo del Kit de desarrollo de Azure Stack. En la pestaña **Arranque**, seleccione el sistema operativo del equipo host (no Azure Stack), haga clic en **Establecer como predeterminado** y, a continuación, haga clic en **Aceptar**. Haga clic en **Reiniciar** cuando se le solicite.

      ![Establecimiento de la configuración de arranque](media/asdk-redeploy/4.png)

2. Después de que el host del ASDK se reinicie en el sistema operativo base, inicie sesión como administrador local para el equipo host del ASDK. Localice y elimine el archivo **C:\CloudBuilder.vhdx** que se utilizó como parte de la implementación anterior.

3. Repita los mismos pasos realizados para [implementar primero el Kit de desarrollo de Azure Stack mediante PowerShell](asdk-deploy-powershell.md).


## <a name="next-steps"></a>Pasos siguientes
[Tareas de implementación posteriores al Kit de desarrollo de Azure Stack](asdk-post-deploy.md)




