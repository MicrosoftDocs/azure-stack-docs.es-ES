---
title: Registro de Azure Stack Hub en Azure
titleSuffix: Azure Stack Hub
description: Aprenda a registrar los sistemas integrados de Azure Stack Hub en Azure de forma que pueda descargar elementos de Azure Marketplace y configurar informes de datos.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 11/19/2020
ms.author: inhenkel
ms.reviewer: avishwan
ms.lastreviewed: 11/19/2020
ms.custom: contperf-fy20q4
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 28c809f70c479c4037061bd7c1504c0954a3584c
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697646"
---
# <a name="register-azure-stack-hub-with-azure"></a>Registro de Azure Stack Hub en Azure

Puede registrar Azure Stack Hub en Azure para descargar elementos de Marketplace de Azure y configurar informes de datos comerciales para Microsoft. Después de registrar Azure Stack Hub, se notifica el uso a Azure Commerce, y puede verlo en el identificador de la suscripción de facturación de Azure usado para el registro.

La información de este artículo describe el registro de los sistemas integrados de Azure Stack Hub en Azure. Para información sobre cómo registrar el ASDK en Azure, consulte [Registro de Azure Stack Hub](../asdk/asdk-register.md) en la documentación de ASDK.

> [!IMPORTANT]  
> Es necesario que el registro admita todas funcionalidades de Azure Stack Hub, incluida los artículos en oferta de Marketplace. Si no se registra al usar el modelo de facturación de pago por uso, infringirá los términos de licencia de Azure Stack Hub. Para más información sobre los modelos de licencia de Azure Stack Hub, consulte la [página de compra](https://azure.microsoft.com/overview/azure-stack/how-to-buy/).

## <a name="prerequisites"></a>Requisitos previos

Complete los siguientes requisitos previos antes del registro:

- Comprobar las credenciales
- Establecer el modo de lenguaje de PowerShell
- Instalación de PowerShell para Azure Stack Hub
- Descargar las herramientas de Azure Stack Hub
- Determinar el modelo de facturación.
- Determinar el nombre de registro único.

### <a name="verify-your-credentials"></a>Comprobar las credenciales

Antes de registrar Azure Stack Hub en Azure, debe tener:

::: zone pivot="state-connected"
- El identificador de suscripción de una suscripción de Azure. Para el registro solo se admiten suscripciones de EA, CSP o servicios compartidos de CSP (CSPSS). Los CSP deben decidir si quieren [usar una suscripción de CSP o APSS](azure-stack-add-manage-billing-as-a-csp.md#create-a-csp-or-apss-subscription).<br><br>Para obtener el identificador, inicie sesión en Azure y haga clic en **Todos los servicios**. Luego, en la categoría **GENERAL**, seleccione **Suscripciones**, haga clic en la suscripción que quiere usar y, en **Información esencial**, encontrará el identificador de la suscripción. El procedimiento recomendado es usar suscripciones independientes para entornos de producción, desarrollo y pruebas. 
::: zone-end
::: zone pivot="state-disconnected"
- El identificador de suscripción de una suscripción de Azure. Solo se admiten para el registro las suscripciones de EA. 

    Para obtener el identificador, inicie sesión en Azure y haga clic en **Todos los servicios**. Luego, en la categoría **GENERAL**, seleccione **Suscripciones**, haga clic en la suscripción que quiere usar y, en **Información esencial**, encontrará el identificador de la suscripción. El procedimiento recomendado es usar suscripciones independientes para entornos de producción, desarrollo y pruebas. 
::: zone-end
   > [!Note]  
   > Actualmente no se admiten las suscripciones en la nube en Alemania.  

- El nombre de usuario y la contraseña de una cuenta que sea propietaria de la suscripción.

- La cuenta de usuario debe tener acceso a la suscripción de Azure y contar con permisos para crear aplicaciones de identidad y entidades de servicio en el directorio asociado a esa suscripción. Le recomendamos que registre Azure Stack Hub en Azure mediante la administración con privilegios mínimos. Para más información sobre cómo crear una definición de roles personalizada que limite el acceso de su suscripción al registro, consulte [Creación de un rol de registro para Azure Stack Hub](azure-stack-registration-role.md).

- Haber registrado el proveedor de recursos de Azure Stack Hub (para más información, consulte la siguiente sección Registro de un proveedor de recursos de Azure Stack Hub).

Después del registro, no se necesita el permiso de administrador global de Azure Active Directory (Azure AD). Sin embargo, algunas operaciones pueden requerir la credencial de administrador global (por ejemplo, un script de instalación del proveedor de recursos o una nueva característica que necesita la concesión de un permiso). Puede restablecer temporalmente los permisos de administrador global de la cuenta o usar una cuenta de administrador global independiente que sea propietaria de la *suscripción del proveedor predeterminada*.

El usuario que registra Azure Stack Hub es el propietario de la entidad de servicio en Azure AD. Solo el usuario que registró Azure Stack Hub puede modificar su registro. Si un usuario que no sea administrador ni propietario de la entidad de servicio de registro intenta registrar o volver a registrar Azure Stack Hub, es posible que obtenga una respuesta 403. Una respuesta 403 indica que el usuario no tiene permisos suficientes para completar la operación.

Si no tiene una suscripción de Azure que cumpla estos requisitos, puede [crear una cuenta gratuita de Azure aquí](https://azure.microsoft.com/free/?b=17.06). El registro de Azure Stack Hub no supone ningún costo sobre su suscripción de Azure.

> [!NOTE]
> Si tiene más de una instancia de Azure Stack Hub, se recomienda que registre cada una de ellas en su propia suscripción. Así resulta más fácil realizar un seguimiento del uso.

### <a name="set-the-powershell-language-mode"></a>Establecer el modo de lenguaje de PowerShell

Para registrar correctamente Azure Stack Hub, el modo de lenguaje de PowerShell debe establecerse en **FullLanguageMode**.  Para comprobar que el modo de lenguaje actual está establecido como completo, abra una ventana de PowerShell con privilegios elevados y ejecute los siguientes cmdlets de PowerShell:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Asegúrese de que la salida devuelve **FullLanguageMode**. Si se devuelve cualquier otro modo de lenguaje, el registro tiene que ejecutarse en otra máquina o el modo de lenguaje tiene que establecerse en **FullLanguageMode** antes de continuar.

### <a name="install-powershell-for-azure-stack-hub"></a>Instalación de PowerShell para Azure Stack Hub

Para registrarse en Azure, use la versión más reciente de PowerShell para Azure Stack Hub.

Si la versión más reciente aún no está instalada, consulte [Instalación de PowerShell para Azure Stack Hub](powershell-install-az-module.md).

### <a name="download-the-azure-stack-hub-tools"></a>Descarga de las herramientas de Azure Stack Hub

El repositorio de GitHub de las herramientas de Azure Stack Hub contiene módulos de PowerShell que admiten la funcionalidad de Azure Stack Hub, incluida la funcionalidad de registro. Durante el proceso de registro es preciso que importe y use el módulo de PowerShell **RegisterWithAzure.psm1** (que se encuentra en el repositorio de herramientas de Azure Stack Hub) para registrar la instancia de Azure Stack Hub en Azure.

Para asegurarse de que usa la versión más reciente, elimine las versiones existentes de las herramientas de Azure Stack Hub y [descargue la versión más reciente de GitHub](azure-stack-powershell-download.md) antes de registrarse en Azure.

[!INCLUDE [Azure Stack Hub Operator Access Workstation](../includes/operator-note-owa.md)]

### <a name="determine-your-billing-model"></a>Determinar el modelo de facturación
::: zone pivot="state-connected"
 Una implementación conectada permite a Azure Stack Hub conectarse a Internet y a Azure. También puede usar Azure AD o Servicios de federación de Active Directory (AD FS) como almacén de identidades y elegir entre dos modelos de facturación: pago por uso o basado en la capacidad. El modelo de facturación se especifica más adelante, mientras se ejecuta el script de registro.
::: zone-end

::: zone pivot="state-disconnected"
 Una implementación desconectada le permite usar Azure Stack Hub sin conexión a Internet. Con una implementación desconectada, está limitado a un almacén de identidades de AD FS y el modelo de facturación basado en la capacidad. El modelo de facturación se especifica más adelante, mientras se ejecuta el script de registro.
::: zone-end

### <a name="determine-your-unique-registration-name"></a>Determinar el nombre de registro único

Al ejecutar el script de registro, debe proporcionar un nombre de registro único. Una manera fácil de asociar la suscripción de Azure Stack Hub con un registro de Azure es usar el **Id. de nube** de Azure Stack Hub.

> [!NOTE]
> Los registros de Azure Stack Hub mediante el modelo de facturación basado en la capacidad deben cambiar el nombre único al volver a registrarse tras la expiración de las suscripciones anuales, a menos que [elimine el registro expirado](#renew-or-change-registration) y vuelva a registrarse en Azure.

Para determinar el identificador de nube para la implementación de Azure Stack Hub, consulte [Búsqueda del identificador de nube](azure-stack-find-cloud-id.md).

::: zone pivot="state-connected"
## <a name="register-with-pay-as-you-use-billing"></a>Registro con el modelo de facturación de pago por uso

Use estos pasos para registrar Azure Stack Hub en Azure mediante el modelo de facturación de pago por uso.

> [!Note]  
> Todos estos pasos se deben ejecutar desde un equipo que tenga acceso al punto de conexión con privilegios (PEP). Para información detallada sobre PEP, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](azure-stack-privileged-endpoint.md).

Los entornos conectados pueden acceder a Internet y a Azure. En estos entornos, es preciso registrar el proveedor de recursos de Azure Stack Hub en Azure y, después, configurar el modelo de facturación.

### <a name="az-modules"></a>[Modules de Az](#tab/az1)

1. Para registrar el proveedor de recursos de Azure Stack Hub en Azure, inicie PowerShell ISE como administrador y use los siguientes cmdlets de PowerShell con el parámetro **EnvironmentName** establecido en el tipo de suscripción a Azure apropiado (a continuación puede ver los parámetros).

2. Agregue la cuenta de Azure que usó para registrar Azure Stack Hub. Para agregar la cuenta, ejecute el cmdlet **Add-AzAccount**. Se le pedirá que escriba sus credenciales de cuenta de Azure; puede que tenga que utilizar la autenticación en dos fases, en función de la configuración de la cuenta.

   ```powershell
   Add-AzAccount -EnvironmentName "<environment name>"
   ```

   | Parámetro | Descripción |  
   |-----|-----|
   | EnvironmentName | El nombre de entorno de suscripción de nube de Azure. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una suscripción a Azure de China, **AzureChinaCloud**.  |

   >[!Note]
   > Si la sesión expira, la contraseña ha cambiado o simplemente desea cambiar de cuenta, ejecute el siguiente cmdlet antes de iniciar sesión con Add-AzAccount: `Remove-AzAccount-Scope Process`

3. Si tiene varias suscripciones, ejecute el siguiente comando para seleccionar la suscripción con la que quiera trabajar:  

   ```powershell  
   Get-AzSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzSubscription
   ```

4. Ejecute el siguiente comando para registrar el proveedor de recursos de Azure Stack Hub en su suscripción de Azure:

   ```powershell  
   Register-AzResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Inicie PowerShell ISE como administrador y vaya a la carpeta **Registration** del directorio **AzureStack-Tools-az** que creó cuando descargó las herramientas de Azure Stack Hub. Importe el módulo **RegisterWithAzure.psm1** mediante PowerShell:

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. A continuación, en la misma sesión de PowerShell, asegúrese de que ha iniciado sesión en el contexto correcto de Azure PowerShell. El contexto es el que corresponde a la cuenta de Azure que se usó para registrar el proveedor de recursos de Azure Stack Hub anteriormente. PowerShell que se ejecuta:

   ```powershell  
   Connect-AzAccount -Environment "<environment name>"
   ```

   | Parámetro | Descripción |  
   |-----|-----|
   | EnvironmentName | El nombre de entorno de suscripción de nube de Azure. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una suscripción a Azure de China, **AzureChinaCloud**.  |

7. En la misma sesión de PowerShell, ejecute el cmdlet **Set-AzsRegistration**. PowerShell que se ejecuta:  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Para más información acerca del cmdlet Set-AzsRegistration, consulte [Referencia del registro](#registration-reference).

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm1)

1. Para registrar el proveedor de recursos de Azure Stack Hub en Azure, inicie PowerShell ISE como administrador y use los siguientes cmdlets de PowerShell con el parámetro **EnvironmentName** establecido en el tipo de suscripción a Azure apropiado (a continuación puede ver los parámetros).

2. Agregue la cuenta de Azure que usó para registrar Azure Stack Hub. Para agregar la cuenta, ejecute el cmdlet **Add-AzureRMAccount**. Se le pedirá que escriba sus credenciales de cuenta de Azure; puede que tenga que utilizar la autenticación en dos fases, en función de la configuración de la cuenta.

   ```powershell
   Add-AzureRMAccount -EnvironmentName "<environment name>"
   ```

   | Parámetro | Descripción |  
   |-----|-----|
   | EnvironmentName | El nombre de entorno de suscripción de nube de Azure. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una suscripción a Azure de China, **AzureChinaCloud**.  |

   >[!Note]
   > Si la sesión expira, la contraseña ha cambiado o simplemente desea cambiar de cuenta, ejecute el siguiente cmdlet antes de iniciar sesión con Add-AzureRMAccount: `Remove-AzureRMAccount-Scope Process`

3. Si tiene varias suscripciones, ejecute el siguiente comando para seleccionar la suscripción con la que quiera trabajar:  

   ```powershell  
   Get-AzureRMSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRMSubscription
   ```

4. Ejecute el siguiente comando para registrar el proveedor de recursos de Azure Stack Hub en su suscripción de Azure:

   ```powershell  
   Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Inicie PowerShell ISE como administrador y vaya a la carpeta **Registration** del directorio **AzureStack-Tools-az** que creó cuando descargó las herramientas de Azure Stack Hub. Importe el módulo **RegisterWithAzure.psm1** mediante PowerShell:

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

6. A continuación, en la misma sesión de PowerShell, asegúrese de que ha iniciado sesión en el contexto correcto de Azure PowerShell. El contexto es el que corresponde a la cuenta de Azure que se usó para registrar el proveedor de recursos de Azure Stack Hub anteriormente. PowerShell que se ejecuta:

   ```powershell  
   Connect-AzureRMAccount -Environment "<environment name>"
   ```

   | Parámetro | Descripción |  
   |-----|-----|
   | EnvironmentName | El nombre de entorno de suscripción de nube de Azure. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una suscripción a Azure de China, **AzureChinaCloud**.  |

7. En la misma sesión de PowerShell, ejecute el cmdlet **Set-AzsRegistration**. PowerShell que se ejecuta:  

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel PayAsYouUse `
      -RegistrationName $RegistrationName
   ```
   Para más información acerca del cmdlet Set-AzsRegistration, consulte [Referencia del registro](#registration-reference).

---

   El proceso tarda entre 10 y 15 minutos. Cuando finalice el comando, verá el mensaje **"Your environment is now registered and activated using the provided parameters."** (El entorno ya está registrado y se ha activado mediante los parámetros proporcionados).

## <a name="register-with-capacity-billing"></a>Registro con el modelo de facturación basado en la capacidad

Use estos pasos para registrar Azure Stack Hub en Azure mediante el modelo de facturación basado en la capacidad.

> [!Note]  
> Todos estos pasos se deben ejecutar desde un equipo que tenga acceso al punto de conexión con privilegios (PEP). Para información detallada sobre PEP, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](azure-stack-privileged-endpoint.md).

Los entornos conectados pueden acceder a Internet y a Azure. En estos entornos, es preciso registrar el proveedor de recursos de Azure Stack Hub en Azure y, después, configurar el modelo de facturación.

### <a name="az-modules"></a>[Modules de Az](#tab/az2)

1. Para registrar el proveedor de recursos de Azure Stack Hub en Azure, inicie PowerShell ISE como administrador y use los siguientes cmdlets de PowerShell con el parámetro **EnvironmentName** establecido en el tipo de suscripción a Azure apropiado (a continuación puede ver los parámetros).

2. Agregue la cuenta de Azure que usó para registrar Azure Stack Hub. Para agregar la cuenta, ejecute el cmdlet **Add-AzAccount**. Se le pedirá que escriba sus credenciales de cuenta de Azure; puede que tenga que utilizar la autenticación en dos fases, en función de la configuración de la cuenta.

   ```powershell  
   Connect-AzAccount -Environment "<environment name>"
   ```

   | Parámetro | Descripción |  
   |-----|-----|
   | EnvironmentName | El nombre de entorno de suscripción de nube de Azure. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una suscripción a Azure de China, **AzureChinaCloud**.  |

3. Si tiene varias suscripciones, ejecute el siguiente comando para seleccionar la suscripción con la que quiera trabajar:  

   ```powershell  
   Get-AzSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzSubscription
   ```

4. Ejecute el siguiente comando para registrar el proveedor de recursos de Azure Stack Hub en su suscripción de Azure:

   ```powershell  
   Register-AzResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Inicie PowerShell ISE como administrador y vaya a la carpeta **Registration** del directorio **AzureStack-Tools-az** que creó cuando descargó las herramientas de Azure Stack Hub. Importe el módulo **RegisterWithAzure.psm1** mediante PowerShell:

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
   ```
   > [!Note]  
   > Puede deshabilitar el uso de informes con el parámetro UsageReportingEnabled para el cmdlet **Set-AzsRegistration** al establecer el parámetro en false. 
   
   Para más información acerca del cmdlet Set-AzsRegistration, consulte [Referencia del registro](#registration-reference).

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm2)

1. Para registrar el proveedor de recursos de Azure Stack Hub en Azure, inicie PowerShell ISE como administrador y use los siguientes cmdlets de PowerShell con el parámetro **EnvironmentName** establecido en el tipo de suscripción a Azure apropiado (a continuación puede ver los parámetros).

2. Agregue la cuenta de Azure que usó para registrar AzureRMure Stack Hub. Para agregar la cuenta, ejecute el cmdlet **Add-AzureRMAccount**. Se le pedirá que escriba sus credenciales de cuenta de Azure; puede que tenga que utilizar la autenticación en dos fases, en función de la configuración de la cuenta.

   ```powershell  
   Connect-AzureRMAccount -Environment "<environment name>"
   ```

   | Parámetro | Descripción |  
   |-----|-----|
   | EnvironmentName | El nombre de entorno de suscripción de nube de Azure. Los nombres de entorno que se admiten son **AzureCloud**, **AzureUSGovernment** o, si usa una suscripción a Azure de China, **AzureChinaCloud**.  |

3. Si tiene varias suscripciones, ejecute el siguiente comando para seleccionar la suscripción con la que quiera trabajar:  

   ```powershell  
   Get-AzureRMSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRMSubscription
   ```

4. Ejecute el siguiente comando para registrar el proveedor de recursos de Azure Stack Hub en su suscripción de Azure:

   ```powershell  
   Register-AzureRMResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

5. Inicie PowerShell ISE como administrador y vaya a la carpeta **Registration** del directorio **AzureStack-Tools-master** que creó cuando descargó las herramientas de Azure Stack Hub. Importe el módulo **RegisterWithAzure.psm1** mediante PowerShell:

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -AgreementNumber <EA agreement number> `
      -BillingModel Capacity `
      -RegistrationName $RegistrationName
   ```
   > [!Note]  
   > Puede deshabilitar el uso de informes con el parámetro UsageReportingEnabled para el cmdlet **Set-AzsRegistration** al establecer el parámetro en false. 
   
   Para más información acerca del cmdlet Set-AzsRegistration, consulte [Referencia del registro](#registration-reference).

---

::: zone-end

::: zone pivot="state-disconnected"
## <a name="register-with-capacity-billing"></a>Registro con el modelo de facturación basado en la capacidad

Si registra Azure Stack Hub en un entorno desconectado (sin conectividad a Internet), deberá obtener un token de registro del entorno de Azure Stack Hub. Después usará ese token en un equipo que pueda conectarse a Azure y que tenga instalado PowerShell para Azure Stack Hub.  

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>Obtención de un token de registro del entorno de Azure Stack Hub

1. Inicie PowerShell ISE como administrador y vaya a la carpeta **Registration** del directorio **AzureStack-Tools-az** que creó cuando descargó las herramientas de Azure Stack Hub. Importe el módulo **RegisterWithAzure.psm1**:  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Para obtener el token de registro, ejecute los siguientes cmdlets de PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt"
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential $YourCloudAdminCredential -UsageReportingEnabled:$False -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```
   Para más información acerca del cmdlet Get-AzsRegistrationToken, consulte [Referencia del registro](#registration-reference).

   > [!Tip]  
   > El token del registro se guarda en el archivo especificado para *$FilePathForRegistrationToken*. Puede cambiar la ruta del archivo o el nombre de archivo a su entera discreción.

3. Guarde este token de registro para usarlo en la máquina conectada a Azure. El archivo o el texto se pueden copiar de *$FilePathForRegistrationToken*.

### <a name="connect-to-azure-and-register"></a>Conexión a Azure y posterior registro

En el equipo que esté conectado a Internet, siga los mismos pasos para importar el módulo RegisterWithAzure.psm1 e inicie sesión en el contexto correcto de Azure PowerShell. Luego, llame a Register-AzsEnvironment. Especifique el token de registro que desea registrar en Azure. Si registra más de una instancia de Azure Stack Hub mediante el mismo identificador de suscripción de Azure, especifique un nombre de registro único.

Necesita el token de registro y un nombre de token único.

1. Inicie PowerShell ISE como administrador y vaya a la carpeta **Registration** del directorio **AzureStack-Tools-az** que creó cuando descargó las herramientas de Azure Stack Hub. Importe el módulo **RegisterWithAzure.psm1**:  

   ```powershell  
   Import-Module .\RegisterWithAzure.psm1
   ```

2. Ejecute después los siguientes cmdlets de PowerShell:  

    ```powershell  
    $RegistrationToken = "<Your Registration Token>"
    $RegistrationName = "<unique-registration-name>"
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

Si lo desea, puede usar el cmdlet Get-Content para señalar a un archivo que contenga el token de registro.

Necesita el token de registro y un nombre de token único.

1. Inicie PowerShell ISE como administrador y vaya a la carpeta **Registration** del directorio **AzureStack-Tools-az** que creó cuando descargó las herramientas de Azure Stack Hub. Importe el módulo **RegisterWithAzure.psm1**:  

    ```powershell  
    Import-Module .\RegisterWithAzure.psm1
    ```

2. Ejecute después los siguientes cmdlets de PowerShell:  

    ```powershell  
    $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

  > [!Note]  
  > Guarde el nombre de recurso de registro y el token de registro para futuras referencias.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Recuperación de una clave de activación del recurso de registro de Azure

A continuación, debe recuperar una clave de activación del registro de recurso creado en Azure durante la operación de Register-AzsEnvironment.

Para obtener la clave de activación, ejecute los siguientes cmdlets de PowerShell:  

  ```Powershell
  $RegistrationResourceName = "<unique-registration-name>"
  $KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
  $ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
  ```

  > [!Tip]  
  > La clave de activación se guarda en el archivo especificado para *$KeyOutputFilePath*. Puede cambiar la ruta del archivo o el nombre de archivo a su entera discreción.

### <a name="create-an-activation-resource-in-azure-stack-hub"></a>Creación de un recurso de activación en Azure Stack Hub

Vuelva al entorno de Azure Stack Hub con el archivo o el texto de la clave de activación creada con Get-AzsActivationKey. Luego, cree un recurso de activación en Azure Stack Hub con dicha clave de activación. Para crear un recurso de activación, ejecute los siguientes cmdlets de PowerShell:

  ```Powershell
  $ActivationKey = "<activation key>"
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```

Si lo desea, puede usar el cmdlet Get-Content para señalar a un archivo que contenga el token de registro:

  ```Powershell
  $ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
  New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
  ```
::: zone-end

## <a name="verify-azure-stack-hub-registration"></a>Comprobación del registro de Azure Stack Hub

Puede usar el icono de **administración de regiones** para comprobar si el registro de Azure Stack Hub se ha realizado correctamente. Este icono está disponible en el panel predeterminado del portal del administrador. El estado puede ser Registrado o No registrado. Si está registrado, también se muestra el identificador de la suscripción de Azure que usó para registrar Azure Stack Hub, junto con el grupo de recursos de registro y el nombre.

1. Inicie sesión en el portal de administración de Azure Stack Hub en `https://adminportal.local.azurestack.external`.

2. En el panel, seleccione **Region management** (Administración de regiones).

3. Seleccione **Propiedades**. Esta hoja muestra el estado y los detalles de su entorno. El estado puede ser **Registered** (Registrado), **Not registered** (No registrado) o **Expired** (Expirado).

    [![Icono de administración de regiones en el portal del administrador de Azure Stack Hub](media/azure-stack-registration/admin1sm.png "Icono de administración de regiones")](media/azure-stack-registration/admin1.png#lightbox)

    Si ya se ha registrado, las propiedades incluyen:
    
    - **Identificador de suscripción para el registro**: identificador de suscripción de Azure registrado y asociado con Azure Stack Hub.
    - **Grupo de recursos de registro**: grupo de recursos de Azure de la suscripción asociada que contiene los recursos de Azure Stack Hub.

4. Puede usar Azure Portal para ver los recursos de registro de Azure Stack Hub y, luego, comprobar que el registro se ha realizado correctamente. Inicie sesión en [Azure Portal](https://portal.azure.com) con una cuenta asociada a la suscripción que usó para registrar Azure Stack Hub. Seleccione **Todos los recursos**, active la casilla **Mostrar tipos ocultos** y seleccione el nombre del registro.

5. Si el registro no se realizó correctamente, debe volver a registrarlo siguiendo los [pasos que se indican a continuación](#change-the-subscription-you-use) para resolver el problema.  

Como alternativa, puede comprobar si el registro es correcto mediante la característica de administración de Marketplace. Si ve una lista de elementos de Marketplace en la hoja de administración de Marketplace, significa que su registro se realizó correctamente. Sin embargo, en entornos desconectados, no puede ver los elementos de Marketplace en la administración de Marketplace.

> [!NOTE]
> Una vez completado el registro, ya no aparecerá la advertencia activa para no registrar. En versiones de Azure Stack Hub anteriores a la 1904, en escenarios con una implementación desconectada, verá un mensaje en la administración de Marketplace que le pide que se registre y active su instancia de Azure Stack Hub, incluso si se ha registrado correctamente. Este mensaje no aparece en la versión 1904 y posteriores.

## <a name="renew-or-change-registration"></a>Renovación o cambio de registro

::: zone pivot="state-connected"
El registro se tiene que actualizar en las siguientes circunstancias:

- Después de renovar su suscripción anual basada en capacidad.
- Al cambiar el modelo de facturación.
- Cuando se escalan cambios (se agregan o quitan nodos) para la facturación por capacidad.

### <a name="prerequisites"></a>Prerrequisitos

Necesita la siguiente información del [portal de administración](#verify-azure-stack-hub-registration) para renovar o cambiar el registro:

| Administrator Portal | Parámetro del cmdlet | Notas | 
|-----|-----|-----|
| IDENTIFICADOR DE SUSCRIPCIÓN PARA EL REGISTRO | Suscripción | Identificador de suscripción que se usó durante el registro anterior |
| GRUPO DE RECURSOS DE REGISTRO | ResourceGroupName | Grupo de recursos en el que existe el recurso de registro anterior |
| NOMBRE DEL REGISTRO | RegistrationName | Nombre de registro usado durante el registro anterior |

### <a name="change-the-subscription-you-use"></a>Cambio de la suscripción que se usa

Si desea cambiar la suscripción que utiliza, primero debe ejecutar el cmdlet **Remove-AzsRegistration** y, después, asegurarse de que inicia sesión en el contexto de Azure PowerShell correcto. Luego, ejecute **Set-AzsRegistration** con los parámetros modificados, incluido `<billing model>`. Durante el proceso de ejecución de **Remove-AzsRegistration**, es preciso haber iniciado sesión en la suscripción usada durante el registro y usar los valores de los parámetros `RegistrationName` y `ResourceGroupName`, como se muestra en el [portal de administración](#verify-azure-stack-hub-registration):

### <a name="az-modules"></a>[Modules de Az](#tab/az3)

```powershell  
# select the subscription used during the registration (shown in portal)
Select-AzSubscription -Subscription '<Registration subscription ID from portal>'
# unregister using the parameter values from portal
Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
# switch to new subscription id
Select-AzSubscription -Subscription '<New subscription ID>'
# register 
Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel '<Billing model>' -RegistrationName '<Registration name>' -ResourceGroupName '<Registration resource group name>'
```

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm3)

```powershell  
# select the subscription used during the registration (shown in portal)
Select-AzureRMSubscription -Subscription '<Registration subscription ID from portal>'
# unregister using the parameter values from portal
Remove-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
# switch to new subscription id
Select-AzureRMSubscription -Subscription '<New subscription ID>'
# register 
Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel '<Billing model>' -RegistrationName '<Registration name>' -ResourceGroupName '<Registration resource group name>'
```

---

### <a name="change-billing-model-how-features-are-offered-or-re-register-your-instance"></a>Cambio del modelo de facturación, cómo se ofrecen las características o se vuelve a registrar la instancia

Esta sección se aplica si desea cambiar el modelo de facturación, cómo se ofrecen las características o si desea volver a registrar la instancia. En todos estos casos, se llama a la función de registro para establecer los nuevos valores. No es preciso eliminar antes el registro actual. Inicie sesión en el identificador de suscripción que se muestra en el [portal de administración](#verify-azure-stack-hub-registration) y, después, vuelva a ejecutar el registro con un nuevo valor `BillingModel` manteniendo los valores de los parámetros `RegistrationName` y `ResourceGroupName` igual que los que aparecen en el [portal de administración](#verify-azure-stack-hub-registration):

### <a name="az-modules"></a>[Modules de Az](#tab/az4)

```powershell  
# select the subscription used during the registration
Select-AzSubscription -Subscription '<Registration subscription ID from portal>'
# rerun registration with new BillingModel (or same billing model in case of re-registration) but using other parameters values from portal
Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel '<New billing model>' -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
```

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm4)

```powershell  
# select the subscription used during the registration
Select-AzureRMSubscription -Subscription '<Registration subscription ID from portal>'
# rerun registration with new BillingModel (or same billing model in case of re-registration) but using other parameters values from portal
Set-AzsRegistration -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel '<New billing model>' -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
```

---

::: zone-end

::: zone pivot="state-disconnected"
El registro se tiene que actualizar o renovar en las siguientes circunstancias:

- Después de renovar su suscripción anual basada en capacidad.
- Al cambiar el modelo de facturación.
- Cuando se escalan cambios (se agregan o quitan nodos) para la facturación por capacidad.

### <a name="remove-the-activation-resource-from-azure-stack-hub"></a>Eliminación del recurso de activación de Azure Stack Hub

En primer lugar, debe eliminar el recurso de activación de Azure Stack Hub y, después, el recurso de registro de Azure.  

Para quitar el recurso de activación de Azure Stack Hub, ejecute los siguientes cmdlets de PowerShell en su entorno de Azure Stack Hub:  

  ```powershell
  Remove-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint
  ```

A continuación, para quitar el recurso de registro en Azure, asegúrese de que está en un equipo conectado a Azure, inicie sesión en el contexto correcto de Azure PowerShell y ejecute los cmdlets de PowerShell adecuados, como se describe a continuación.

Puede usar el token de registro que se usó para crear el recurso:  

  ```powershell
  $RegistrationToken = "<registration token>"
  Unregister-AzsEnvironment -RegistrationToken $RegistrationToken
  ```

O bien, puede usar el nombre de registro y el nombre del grupo de recursos de registro del [portal de administración](#verify-azure-stack-hub-registration):

  ```powershell
  Unregister-AzsEnvironment -RegistrationName '<Registration name from portal>' -ResourceGroupName '<Registration resource group from portal>'
  ```

### <a name="re-register-using-connected-steps"></a>Nuevo registro con los pasos del modelo conectado

Si cambia el modelo de facturación de la facturación basada en la capacidad en un estado desconectado a la facturación por consumo en un estado conectado, se registrará de nuevo según los [pasos del modelo conectado](azure-stack-registration.md?pivots=state-connected#change-billing-model-how-features-are-offered-or-re-register-your-instance). 

>[!Note] 
>Esto no cambia el modelo de identidades, solo el mecanismo de facturación, de modo que seguirá usando AD FS como su origen de identidades.

### <a name="re-register-using-disconnected-steps"></a>Segundo registro mediante pasos desconectados

Ya ha anulado completamente el registro en un escenario desconectado y debe repetir los pasos para registrar un entorno de Azure Stack Hub de nuevo en un escenario desconectado.
::: zone-end

### <a name="disable-or-enable-usage-reporting"></a>Deshabilitar o habilitar los informes de uso

En entornos de Azure Stack Hub que usan un modelo de facturación por capacidad, desactive los informes de uso con el parámetro **UsageReportingEnabled** mediante los cmdlets **Set-AzsRegistration** o **Get-AzsRegistrationToken**. Azure Stack Hub informa de las métricas de uso de forma predeterminada. Los operadores que tienen usos de capacidad o que admiten un entorno desconectado deben desactivar los informes de uso.

::: zone pivot="state-connected"
Ejecute los siguientes cmdlets de PowerShell:

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $RegistrationName = "<unique-registration-name>"
   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Capacity
      -RegistrationName $RegistrationName
      -UsageReportingEnabled:$false
   ```
::: zone-end
::: zone pivot="state-disconnected"
1. Para cambiar el token de registro, ejecute los siguientes cmdlets de PowerShell:  

   ```Powershell
   $FilePathForRegistrationToken = $env:SystemDrive\RegistrationToken.txt
   $RegistrationToken = Get-AzsRegistrationToken -PrivilegedEndpointCredential -UsageReportingEnabled:$False
   $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -BillingModel Capacity -AgreementNumber '<EA agreement number>' -TokenOutputFilePath $FilePathForRegistrationToken
   ```

   > [!Tip]  
   > El token del registro se guarda en el archivo especificado para *$FilePathForRegistrationToken*. Puede cambiar la ruta del archivo o el nombre de archivo a su entera discreción.

2. Guarde este token de registro para usarlo en la máquina conectada a Azure. El archivo o el texto se pueden copiar de *$FilePathForRegistrationToken*.
::: zone-end

## <a name="move-a-registration-resource"></a>Traslado de un recurso de registro

Se **admite** el traslado de un recurso de registro entre grupos de recursos en la misma suscripción para todos los entornos. Sin embargo, trasladar un recurso de registro entre suscripciones solo se admite para los CSP cuando ambas suscripciones se resuelven en el mismo Id. de partner. Para más información sobre cómo mover recursos a un nuevo grupo de recursos, consulte [Traslado de los recursos a un nuevo grupo de recursos o a una nueva suscripción](/azure/azure-resource-manager/resource-group-move-resources).

> [!IMPORTANT]
> Para evitar la eliminación accidental de recursos de registro en el portal, el script de registro agrega automáticamente un bloqueo al recurso. Debe quitar este bloqueo antes de moverlo o eliminarlo. Se recomienda agregar un bloqueo a su recurso de registro para evitar la eliminación accidental.

## <a name="registration-reference"></a>Referencia del registro

### <a name="set-azsregistration"></a>Set-AzsRegistration

Puede usar **Set-AzsRegistration** para registrar Azure Stack Hub en Azure y habilitar o deshabilitar la oferta de elementos en Marketplace y los informes de uso.

Para ejecutar el cmdlet, necesitará:

- Una suscripción de Azure global de cualquier tipo.
- Una sesión iniciada en Azure PowerShell con una cuenta que sea de un propietario o colaborador de dicha suscripción.

```powershell
Set-AzsRegistration [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String> [[-AzureContext]
    <PSObject>] [[-ResourceGroupName] <String>] [[-ResourceGroupLocation] <String>] [[-BillingModel] <String>]
    [-MarketplaceSyndicationEnabled] [-UsageReportingEnabled] [[-AgreementNumber] <String>] [[-RegistrationName]
    <String>] [<CommonParameters>]
```

| Parámetro | Tipo | Descripción |
|-------------------------------|--------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| PrivilegedEndpointCredential | PSCredential | Las credenciales que se utilizan para [acceder al punto de conexión con privilegios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). El nombre de usuario tiene el formato **dominioDeAzureStack\AdminNube**. |
| PrivilegedEndpoint | String | Es una consola remota de PowerShell previamente configurada que proporciona funcionalidades tales como la recopilación de registros y otras tareas posteriores a la implementación. Para más información, consulte el artículo acerca del [uso del punto de conexión con privilegios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | Es el modelo de facturación que usa la suscripción. Los valores permitidos para este parámetro son: Capacity, PayAsYouUse y Development. |
| MarketplaceSyndicationEnabled | True/False | Determina si la característica de administración de Marketplace está disponible en el portal. Se establece en true si se registra con conectividad a Internet. Se establece en false si se registra en entornos sin conexión. Para los registros sin conexión, puede usar la [herramienta de redifusión sin conexión](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected) para descargar elementos de Marketplace. |
| UsageReportingEnabled | True/False | Azure Stack Hub informa de las métricas de uso de forma predeterminada. Los operadores que tienen usos de capacidad o que admiten un entorno desconectado deben desactivar los informes de uso. Los valores permitidos para este parámetro son: True, False. |
| AgreementNumber | String | Número del contrato Enterprise en el que se ha pedido la SKU de capacidad de este Azure Stack. |
| RegistrationName | String | Establezca un nombre único para el registro si ejecuta el script de registro en más de una instancia de Azure Stack Hub mediante el mismo identificador de suscripción de Azure. El valor predeterminado del parámetro es **AzureStackRegistration**. Sin embargo, si usa el mismo nombre en más de una instancia de Azure Stack Hub, el script no se ejecuta correctamente. |

### <a name="get-azsregistrationtoken"></a>Get-AzsRegistrationToken

Get-AzsRegistrationToken genera un token de registro a partir de los parámetros de entrada.

```powershell  
Get-AzsRegistrationToken [-PrivilegedEndpointCredential] <PSCredential> [-PrivilegedEndpoint] <String>
    [-BillingModel] <String> [[-TokenOutputFilePath] <String>] [-UsageReportingEnabled] [[-AgreementNumber] <String>]
    [<CommonParameters>]
```
| Parámetro | Tipo | Descripción |
|-------------------------------|--------------|-------------|
| PrivilegedEndpointCredential | PSCredential | Las credenciales que se utilizan para [acceder al punto de conexión con privilegios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). El nombre de usuario tiene el formato **dominioDeAzureStack\AdminNube**. |
| PrivilegedEndpoint | String |  Es una consola remota de PowerShell previamente configurada que proporciona funcionalidades tales como la recopilación de registros y otras tareas posteriores a la implementación. Para más información, consulte el artículo acerca del [uso del punto de conexión con privilegios](azure-stack-privileged-endpoint.md#access-the-privileged-endpoint). |
| AzureContext | PSObject |  |
| ResourceGroupName | String |  |
| ResourceGroupLocation | String |  |
| BillingModel | String | Es el modelo de facturación que usa la suscripción. Los valores permitidos para este parámetro son: Capacity, PayAsYouUse y Development. |
| MarketplaceSyndicationEnabled | True/False |  |
| UsageReportingEnabled | True/False | Azure Stack Hub informa de las métricas de uso de forma predeterminada. Los operadores que tienen usos de capacidad o que admiten un entorno desconectado deben desactivar los informes de uso. Los valores permitidos para este parámetro son: True, False. |
| AgreementNumber | String |  |

## <a name="registration-failures"></a>Errores de registro

Es posible que vea uno de los errores siguientes al intentar registrar Azure Stack Hub:

- No se pudo recuperar la información de hardware obligatoria para `$hostName`. Compruebe la conectividad y el host físico y, a continuación, intente volver a ejecutar el registro.

- No se puede conectar a `$hostName` para obtener la información del hardware. Compruebe la conectividad y el host físico y, a continuación, intente volver a ejecutar el registro.

   Causa: suele deberse al intento de obtener los detalles de hardware, como el UUID, el BIOS y la CPU de los hosts, para intentar realizar la activación, que no fue posible debido a la incapacidad de establecer conexión con el host físico.

- El identificador de nube [`GUID`] ya está registrado. La reutilización de identificadores de nube no está permitida.

   Causa: esto sucede si el entorno de Azure Stack ya está registrado. Si desea volver a registrar el entorno con una suscripción o modelo de facturación diferentes, consulte [Pasos de renovación o cambio de registro](#renew-or-change-registration).

- Al intentar acceder a la administración de Marketplace, se produce un error cuando se intenta distribuir productos.

   Causa: suele ocurrir cuando Azure Stack Hub no puede acceder al recurso de registro. Un motivo común es que, cuando cambia el inquilino de directorio de una suscripción a Azure, se restablece el registro. No puede acceder a Marketplace de Azure Stack Hub ni notificar el uso si cambió el inquilino de directorio de la suscripción. Debe volver a realizar el registro para corregir este problema.
::: zone pivot="state-disconnected"
- La administración de Marketplace todavía le pide que registre y active Azure Stack Hub, aunque ya ha registrado su sello mediante el proceso sin conexión.

   Causa: se trata de un problema conocido en entornos desconectados y requiere que [verifique su estado de registro](#verify-azure-stack-hub-registration). Para poder usar la administración de Marketplace, utilice [la herramienta sin conexión](azure-stack-download-azure-marketplace-item.md?pivots=state-disconnected).
::: zone-end

## <a name="next-steps"></a>Pasos siguientes

[Descargar elementos de Marketplace desde Azure](azure-stack-download-azure-marketplace-item.md)
