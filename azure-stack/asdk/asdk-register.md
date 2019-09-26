---
title: Registro del Kit de desarrollo de Azure Stack en Azure | Microsoft Docs
description: Obtenga información sobre cómo registrar el Kit de desarrollo de Azure Stack (ASDK) con Azure para habilitar la redifusión del Marketplace y los informes de uso.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/14/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 06/14/2019
ms.openlocfilehash: 2dea14ab5d43aab8deab2632b07277d7094e33c4
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974623"
---
# <a name="register-the-asdk-with-azure"></a>Registro del Kit de desarrollo de Azure Stack en Azure

La instalación del Kit de desarrollo de Azure Stack se puede registrar en Azure para descargar elementos de Marketplace de Azure y configurar informes de datos comerciales para Microsoft. Es necesario que el registro admita todas funcionalidades de Azure Stack, incluida la redifusión de Marketplace. Dicho registro es necesario para permitir probar una funcionalidad importante de Azure Stack, como es la redifusión de Marketplace y los informes de uso. Después de registrar Azure Stack, se informa del uso para el comercio de Azure. Puede verlo en la suscripción que usó para el registro. Sin embargo, a los usuarios del ASDK no se les cobra ningún uso que comuniquen.

Si no registra su ASDK, puede que vea la alerta de advertencia **Activación necesaria** que le informa que debe registrar el ASDK. Este comportamiento es normal.

## <a name="prerequisites"></a>Requisitos previos

Antes de usar estas instrucciones para registrar el ASDK en Azure, asegúrese de que ha instalado PowerShell para Azure Stack y ha descargado las herramientas de Azure Stack como se describe en el artículo de [configuración posterior a la implementación](asdk-post-deploy.md).

El modo de lenguaje de PowerShell también se debe establecer en **FullLanguage** en el equipo que se usa para registrar el ASDK con Azure. Para comprobar que el modo de lenguaje actual está establecido en FullLanguageMode, abra una ventana de PowerShell con privilegios elevados y ejecute los siguientes comandos de PowerShell:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Asegúrese de que la salida devuelve **FullLanguage**. Si se devuelve cualquier otro modo de lenguaje, el registro tendrá que ejecutarse en otro equipo o el modo de lenguaje tendrá que configurarse en **FullLanguage** antes de continuar.

La cuenta de Azure AD usada para el registro debe tener acceso a la suscripción a Azure y contar con permisos para crear aplicaciones de identidad y entidades de servicio en el directorio asociado a esa suscripción. Se recomienda que registre Azure Stack con Azure mediante la [creación de una cuenta de servicio para usar para el registro](../operator/azure-stack-registration-role.md) en lugar de usar credenciales de administrador global.

## <a name="register-the-asdk"></a>Registro del Kit de desarrollo de Azure Stack

Siga estos pasos para registrar el ASDK en Azure.

> [!NOTE]
> Todos estos pasos se deben ejecutar desde un equipo que tenga acceso al punto de conexión con privilegios. En el caso del ASDK, este es el equipo host del kit de desarrollo.

1. Abra una consola de PowerShell como administrador.  

2. Ejecute los siguientes comandos de PowerShell para registrar la instalación de ASDK con Azure. Inicie sesión en su identificador de suscripción de facturación de Azure y en la instalación local del ASDK. Si aún no tiene un identificador de la suscripción de facturación de Azure, puede crear una [cuenta gratuita de Azure aquí](https://azure.microsoft.com/free/?b=17.06). El registro de Azure Stack no supone ningún costo en su suscripción de Azure.<br><br>Establece un nombre único para el registro cuando se ejecuta el cmdlet **Set-AzsRegistration**. El parámetro **RegistrationName** tiene el valor predeterminado **AzureStackRegistration**. Sin embargo, si usa el mismo nombre en más de una instancia de Azure Stack, el script no se ejecutará correctamente.

    ```powershell  
    # Add the Azure cloud subscription environment name. 
    # Supported environment names are AzureCloud, AzureChinaCloud, or AzureUSGovernment depending which Azure subscription you're using.
    Add-AzureRmAccount -EnvironmentName "<environment name>"

    # Register the Azure Stack resource provider in your Azure subscription
    Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

    # Import the registration module that was downloaded with the GitHub tools
    Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

    # If you have multiple subscriptions, run the following command to select the one you want to use:
    # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription
    
    # Register Azure Stack
    $AzureContext = Get-AzureRmContext
    $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
    $RegistrationName = "<unique-registration-name>"
    Set-AzsRegistration `
    -PrivilegedEndpointCredential $CloudAdminCred `
    -PrivilegedEndpoint AzS-ERCS01 `
    -BillingModel Development `
    -RegistrationName $RegistrationName `
    -UsageReportingEnabled:$true
    ```

3. Cuando el script se complete, debería ver este mensaje: **Your environment is now registered and activated using the provided parameters** (El entorno ya se ha registrado y activado utilizando los parámetros proporcionados).

    ![El entorno ya está registrado](media/asdk-register/1.PNG)

## <a name="register-in-disconnected-environments"></a>Registro en entornos desconectados

Si va a registrar Azure Stack en un entorno desconectado (sin conexión a Internet), tendrá que obtener un token de registro del entorno de Azure Stack y, después, utilizar el token en un equipo que pueda conectarse a Azure para registrarse y crear un recurso de activación para el entorno del ASDK.

 > [!IMPORTANT]
 > Antes de usar estas instrucciones para registrar Azure Stack, asegúrese de que ha instalado PowerShell para Azure Stack y de que ha descargado las herramientas de Azure Stack tal como se describe en el artículo de [configuración posterior a la implementación](asdk-post-deploy.md) tanto en el equipo host del ASDK como en el equipo con acceso a Internet que utilizará para conectarse a Azure y realizar el registro.

### <a name="get-a-registration-token-from-the-azure-stack-environment"></a>Obtención de un token de registro del entorno de Azure Stack

En el equipo host del ASDK, inicie PowerShell como administrador y vaya a la carpeta **Registro** del directorio **AzureStack-Tools-master** que creó cuando descargó las herramientas de Azure Stack. Use los siguientes comandos de PowerShell para importar el módulo **RegisterWithAzure.psm1** y, después, use el cmdlet **Get AzsRegistrationToken** para obtener el token de registro:  

   ```powershell  
   # Import the registration module that was downloaded with the GitHub tools
   Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

   # Create registration token
   $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
   # File path to save the token. This example saves the file as C:\RegistrationToken.txt.
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $CloudAdminCred `
   -UsageReportingEnabled:$false `
   -PrivilegedEndpoint AzS-ERCS01 `
   -BillingModel Development `
   -MarketplaceSyndicationEnabled:$false `
   -TokenOutputFilePath $FilePathForRegistrationToken
   ```

Guarde este token de registro para usarlo en el equipo conectado a Internet. Puede copiar el archivo o el texto del archivo creado por el parámetro `$FilePathForRegistrationToken`.

### <a name="connect-to-azure-and-register"></a>Conexión a Azure y posterior registro

En el equipo que está conectado a Internet, utilice los siguientes comandos de PowerShell para importar el módulo **RegisterWithAzure.psm1**. Después, utilice el cmdlet **Register-AzsEnvironment** para realizar el registro en Azure con el token de registro que acaba de crear y un nombre de registro único:  

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  $RegistrationName = "<unique-registration-name>"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

También puede usar el cmdlet **Get-Content** para señalar a un archivo que contenga el token de registro:

  ```powershell  
  # Add the Azure cloud subscription environment name. 
  # Supported environment names are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
  Add-AzureRmAccount -EnvironmentName "<environment name>"

  # If you have multiple subscriptions, run the following command to select the one you want to use:
  # Get-AzureRmSubscription -SubscriptionID "<subscription ID>" | Select-AzureRmSubscription

  # Register the Azure Stack resource provider in your Azure subscription
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack

  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  # Register with Azure 
  # This example uses the C:\RegistrationToken.txt file.
  $registrationToken = Get-Content -Path "$env:SystemDrive\RegistrationToken.txt"
  Register-AzsEnvironment -RegistrationToken $registrationToken `
  -RegistrationName $RegistrationName
  ```

Cuando se complete el registro, debería ver un mensaje similar a: **El entorno de Azure Stack ahora está registrado con Azure.**

> [!IMPORTANT]
> **No** cierre la ventana de PowerShell.

Guarde el token de registro y el nombre de recurso de registro para futuras referencias.

### <a name="retrieve-an-activation-key-from-the-azure-registration-resource"></a>Recuperación de una clave de activación del recurso de registro de Azure

Utilice el equipo que está conectado a Internet **y la misma ventana de la consola de PowerShell** para recuperar la clave de activación del recurso de registro que se creó al realizar el registro con Azure.

Para obtener la clave de activación, ejecute los siguientes comandos de PowerShell. Use el mismo valor de nombre de registro único que proporcionó al registrarse en Azure durante el paso anterior:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  # File path to save the activation key. This example saves the file as C:\ActivationKey.txt.
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName `
  -KeyOutputFilePath $KeyOutputFilePath
  ```

### <a name="create-an-activation-resource-in-azure-stack"></a>Crear un recurso de activación en Azure Stack

Vuelva al entorno de Azure Stack con el archivo o el texto de la clave de activación creada a partir de **Get-AzsActivationKey**. Ejecute los siguientes comandos de PowerShell para crear un recurso de activación en Azure Stack con dicha clave de activación:   

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1
  
  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

También puede usar el cmdlet **Get-Content** para señalar a un archivo que contenga el token de registro:

  ```Powershell
  # Import the registration module that was downloaded with the GitHub tools
  Import-Module C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1

  $CloudAdminCred = Get-Credential -UserName AZURESTACK\CloudAdmin -Message "Enter the credentials to access the privileged endpoint."
  # This example uses the C:\ActivationKey.txt file.
  $ActivationKey = Get-Content -Path "$env:SystemDrive\Activationkey.txt"
  New-AzsActivationResource -PrivilegedEndpointCredential $CloudAdminCred `
  -PrivilegedEndpoint AzS-ERCS01 `
  -ActivationKey $ActivationKey
  ```

Cuando se complete la activación, debería ver un mensaje similar a: **El entorno ha finalizado el proceso de registro y activación.**

## <a name="verify-the-registration-was-successful"></a>Comprobación de que el registro fue correcto

Puede usar el icono de **administración de regiones** para comprobar que el registro de Azure Stack se realizó correctamente. Este icono está disponible en el panel predeterminado del portal del administrador.

1. Inicie sesión en el [Portal de administración de Azure Stack](https://adminportal.local.azurestack.external).

2. En el panel, seleccione **Region management** (Administración de regiones).

    [![Icono de administración de región en el portal de administración de Azure Stack ](media/asdk-register/admin1sm.png "Icono de administración de región")](media/asdk-register/admin1.png#lightbox)

3. Seleccione **Propiedades**. Esta hoja muestra el estado y los detalles de su entorno. El estado puede ser **Registrado** o **No registrado**. Si el estado está registrado, también se muestra el id. de la suscripción de Azure que usó para registrar Azure Stack, junto con el grupo de recursos de registro y el nombre.

## <a name="move-a-registration-resource"></a>Traslado de un recurso de registro
Se **admite** el traslado de un recurso de registro entre grupos de recursos en la misma suscripción. Para más información sobre cómo mover recursos a un nuevo grupo de recursos, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-move-resources).


## <a name="next-steps"></a>Pasos siguientes

- [Incorporación de un elemento de Marketplace de Azure Stack](../operator/azure-stack-marketplace.md)
