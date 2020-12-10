---
title: Registro de Azure Stack Hub en Azure
description: Obtenga información sobre cómo registrar Azure Stack Hub en Azure de forma que pueda descargar elementos de Azure Marketplace y configurar los informes de datos.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/20/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/20/2019
ms.openlocfilehash: 94de29942f49aa7151de201bfa00d99625a1fee8
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941337"
---
# <a name="register-azure-stack-hub-with-azure"></a>Registro de Azure Stack Hub en Azure

Para configurar la sindicación de Marketplace, debe registrar y activar el Centro de datos modular (MDC) o Azure Stack Hub resistente, en función de la instancia de Azure Stack Hub, una vez completada la implementación. Con la sindicación de Marketplace, un administrador rellena el Marketplace local de Azure Stack Hub con imágenes descargadas desde Azure Marketplace.

El registro es necesario tanto para los sistemas que se van conectar a la nube de Azure como para los sistemas que estarán desconectados.

## <a name="overview"></a>Información general

El proceso de registro consta de los pasos siguientes:

1. Exporte un token de registro desde Azure Stack Hub.
2. Registre Azure Stack Hub con Azure.
3. Exporte una clave de activación desde Azure.
4. Activa Azure Stack Hub.

Si un sistema está conectado a la nube de Azure, los cuatro pasos se pueden ejecutar con un solo script. Si el sistema está desconectado, el administrador debe realizar los pasos de forma individual.

> [!NOTE]
> En el caso de MDC, cada uno de los sistemas de Azure Stack Hub se debe registrar por separado. Por lo tanto, se deben realizar las siguientes instrucciones tres veces.

## <a name="prerequisites"></a>Requisitos previos

Necesita los siguientes requisitos previos antes de proceder al registro:

- Comprobar las credenciales
- Establecer el modo de lenguaje de PowerShell
- Instalación de PowerShell para Azure Stack Hub
- Instalación del módulo de herramientas de Azure Stack Hub
- Determinar el escenario de registro

### <a name="verify-your-credentials"></a>Comprobar las credenciales

Antes de registrar Azure Stack Hub en Azure, debe tener:

- El identificador de suscripción de una suscripción de Azure.  

    > [!Note]  
    > Las suscripciones de Azure están asociadas a entornos en la nube de Azure (Azure comercial, Azure Government, etc.). Esto determina la nube a la que se conectará para acceder al contenido de Marketplace.
    > 
    > La suscripción que use para el registro se debe aprobar para los servicios JEDI. Esto garantiza que el dispositivo que registre tendrá derecho a un uso ilimitado de los recursos, sin informar del uso a Azure. Para aprobar la suscripción, envíe un correo electrónico a azshregistration@microsoft.com con el identificador de suscripción que se debe aprobar junto con la instancia de Azure Stack Hub resistente o el Centro de datos modular (MDC) que va a registrar.

- El nombre de usuario y la contraseña de una cuenta que sea propietaria de la suscripción. 
- La cuenta de usuario debe tener acceso a la suscripción de Azure y contar con permisos para crear aplicaciones de identidad y entidades de servicio en el directorio asociado a esa suscripción. Le recomendamos que registre Azure Stack Hub en Azure mediante la administración con privilegios mínimos. Para más información sobre cómo crear una definición de roles personalizada que limite el acceso de su suscripción al registro, consulte [Creación de un rol de registro para Azure Stack Hub](../../operator/azure-stack-registration-role.md).
- Registro del proveedor de recursos de Azure Stack Hub (consulte las siguientes secciones para más información).

Después del registro, no se necesita el permiso de administrador global de Azure Active Directory (Azure AD). Sin embargo, algunas operaciones podrían requerir la credencial de administrador global (por ejemplo, un script de instalación del proveedor de recursos o una nueva característica que necesita la concesión de un permiso). Puede restablecer temporalmente los permisos de administrador global de la cuenta o usar una cuenta de administrador global independiente que sea propietaria de la *suscripción del proveedor predeterminada*.

El usuario que registra Azure Stack Hub es el propietario de la entidad de servicio en Azure AD. Solo el usuario que registró Azure Stack Hub puede modificar su registro. Si un usuario que no sea administrador ni propietario de la entidad de servicio de registro intenta registrar o volver a registrar Azure Stack Hub, es posible que obtenga una respuesta 403. Una respuesta 403 indica que el usuario no tiene permisos suficientes para completar la operación.

El registro de Azure Stack Hub no supone ningún costo sobre su suscripción de Azure.

### <a name="powershell-language-mode"></a>Modo de lenguaje de PowerShell

Para registrar correctamente Azure Stack Hub, el modo de lenguaje de PowerShell debe establecerse en **FullLanguageMode**. Para comprobar que el modo de lenguaje actual está establecido como completo, abra una ventana de PowerShell con privilegios elevados y ejecute los siguientes cmdlets de PowerShell:

```powershell  
$ExecutionContext.SessionState.LanguageMode
```

Asegúrese de que la salida devuelve **FullLanguageMode**. Si se devuelve cualquier otro modo de lenguaje, el registro se tiene que ejecutar en otra máquina o el modo de lenguaje se tiene que establecer en **FullLanguageMode** antes de continuar.

### <a name="install-powershell-for-azure-stack-hub"></a>Instalación de PowerShell para Azure Stack Hub

Para registrarse en Azure, use la versión más reciente de PowerShell para Azure Stack Hub.

Si la versión más reciente aún no está instalada, consulte [Instalación de PowerShell para Azure Stack Hub](../../operator/azure-stack-powershell-install.md).

### <a name="install-the-azure-stack-hub-tools-module"></a>Instalación del módulo de herramientas de Azure Stack Hub

El [módulo de herramientas de Azure Stack Hub](https://www.powershellgallery.com/packages/azs.tools.admin/0.1.0) contiene módulos de PowerShell que admiten la funcionalidad de Azure Stack Hub, incluida la funcionalidad de registro. Durante el proceso de registro, es preciso que importe y use el submódulo **RegisterWithAzure.psm1** para registrar la instancia de Azure Stack Hub en Azure.

Para instalar la herramienta de Azure Stack Hub más reciente:

1. Abra un símbolo del sistema de PowerShell con privilegios elevados.
2. Ejecute el siguiente cmdlet:

    ```powershell  
        Install-Module -Name Azs.Tools.Admin
    ```

### <a name="determine-your-registration-scenario"></a>Determinar el escenario de registro

La implementación de Azure Stack Hub se puede *conectar* o *desconectar*.

- **Conectado**: Conectada significa que ha implementado Azure Stack Hub, así que puede conectarse a Internet y a Azure. Puede usar Azure AD o Servicios de federación de Active Directory (AD FS) como almacén de identidades.

- **Desconectado**: La opción de implementación desconectada de Azure permite implementar y usar Azure Stack Hub sin conexión a Internet.

### <a name="determine-a-unique-registration-name-to-use"></a>Determinar un nombre de registro único para su uso

Al registrar Azure Stack Hub en Azure, debe proporcionar un nombre de registro único. Una manera fácil de asociar la suscripción de Azure Stack Hub con un registro de Azure es usar el **Id. de nube** de Azure Stack Hub.

Para determinar el identificador de nube de la implementación de Azure Stack Hub, abra PowerShell como administrador en un equipo que pueda acceder al punto de conexión con privilegios, ejecute los siguientes comandos y registre el valor de **CloudID**:

```PowerShell
Enter-PSSession -ComputerName <privileged endpoint computer name> -ConfigurationName PrivilegedEndpoint
Get-AzureStackStampInformation
```

## <a name="registration-and-activation-for-systems-connected-to-the-azure-cloud"></a>Registro y activación de sistemas conectados a la nube de Azure

Siga estos pasos para registrar un sistema de Azure Stack Hub que tenga conectividad con Azure.

> [!NOTE]  
> Todos estos pasos se deben ejecutar desde un equipo que tenga acceso al punto de conexión con privilegios (PEP). Para información detallada sobre PEP, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](../../operator/azure-stack-privileged-endpoint.md).

Los entornos conectados pueden acceder a Internet y a Azure. En estos entornos, registre el proveedor de recursos de Azure Stack Hub en Azure y, después, configure el modelo de facturación.

1. Para registrar el proveedor de recursos de Azure Stack Hub en Azure, inicie PowerShell ISE como administrador y use los siguientes cmdlets de PowerShell con el parámetro **EnvironmentName** establecido en el tipo de suscripción de Azure apropiado (a continuación puede ver los parámetros).

2. Agregue la cuenta de Azure que usó para registrar Azure Stack Hub. Para agregar la cuenta, ejecute el cmdlet **Add-AzureRmAccount**. Se le pedirá que escriba sus credenciales de cuenta de Azure y puede que tenga que utilizar la autenticación en dos fases, en función de la configuración de la cuenta.

   ```powershell
   Add-AzureRmAccount -EnvironmentName "<environment name>"
   ```

   | Parámetro | Descripción |  
   |-----|-----|
   | EnvironmentName | El nombre de entorno de suscripción de nube de Azure. Los nombres de entorno admitidos son **AzureCloud** o **AzureUSGovernment**.  |

   >[!NOTE]
   > Si la sesión expira, la contraseña ha cambiado o desea cambiar de cuenta, ejecute el siguiente cmdlet antes de iniciar sesión con **Add-AzureRmAccount**: **Proceso Remove-AzureRmAccount-Scope**.

3. En la misma sesión de PowerShell, asegúrese de que ha iniciado sesión en el contexto correcto de Azure PowerShell. Este contexto es la cuenta de Azure que se usó para registrar el proveedor de recursos de Azure Stack Hub anteriormente:

   ```powershell  
   Connect-AzureRmAccount -Environment "<environment name>"
   ```

   | Parámetro | Descripción |  
   |-----|-----|
   | EnvironmentName | El nombre de entorno de suscripción de nube de Azure. Los nombres de entorno admitidos son **AzureCloud** o **AzureUSGovernment**.  |

4. Si tiene varias suscripciones, ejecute el siguiente comando para seleccionar la suscripción con la que quiera trabajar:

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   ```

5. Ejecute el siguiente comando para registrar el proveedor de recursos de Azure Stack Hub en la suscripción de Azure:

   ```powershell  
   Register-AzureRmResourceProvider -ProviderNamespace Microsoft.AzureStack
   ```

   Como alternativa, puede registrar el proveedor de recursos de Azure Stack Hub en la suscripción de Azure mediante Azure Portal. Inicie sesión en Azure Portal con la cuenta asociada a la suscripción de Azure. Vaya a **Categorías** > **General** > **Suscripciones** y seleccione el identificador de suscripción que necesita para registrar el proveedor de recursos de Azure Stack Hub. En el panel izquierdo, vaya a **Configuración** > **Proveedores de recursos**. Seleccione el proveedor de recursos **Microsoft.AzureStack** y seleccione **Registrar**.

   ![Registro del proveedor de recursos de Azure Stack Hub](./media/registration-tzl/register-azure-resource-provider-portal.png)


6. En la misma sesión de PowerShell, ejecute el cmdlet **Set-AzsRegistration**:

   ```powershell  
   $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."
   $msAssetTag = "Enter the value printed on the product"
   $RegistrationName = "<unique-registration-name>"

   Set-AzsRegistration `
      -PrivilegedEndpointCredential $CloudAdminCred `
      -PrivilegedEndpoint <PrivilegedEndPoint computer name> `
      -BillingModel Custom `
      -RegistrationName $RegistrationName `
      -msAssetTag $msAssetTagName `
      -UsageReportingEnabled: $false
   ```
   La etiqueta de recurso de MS (`msAssetTag`) es obligatoria para el registro del modelo de facturación personalizado y se imprime en el producto.
    
   El proceso tarda entre 10 y 15 minutos. Cuando se complete el comando, verá el mensaje. **Your environment is now registered and activated using the provided parameters** (El entorno ya se ha registrado y activado utilizando los parámetros proporcionados).

## <a name="registration-and-activation-for-systems-not-connected-to-the-azure-cloud"></a>Registro y activación de sistemas no conectados a la nube de Azure 

Si va a registrar Azure Stack Hub en un entorno desconectado (sin conectividad a Internet), siga estos pasos:

1. Obtenga un token de registro del entorno de Azure Stack Hub.

2. Conéctese a Azure y regístrese con el token de registro del paso 1. Use un equipo que pueda conectarse a Azure y que tenga instalado PowerShell para Azure Stack Hub.

3. Recupere una clave de activación del registro de Azure.

4. Active Azure Stack Hub con la clave de activación de Azure.

### <a name="get-a-registration-token-from-the-azure-stack-hub-environment"></a>Obtención de un token de registro del entorno de Azure Stack Hub

Obtenga un token de registro del entorno de Azure Stack Hub. Después, usará ese token en un equipo que pueda conectarse a Azure y que tenga instalados PowerShell para Azure Stack Hub y las herramientas de Azure Stack Hub.

1. Para obtener el token de registro, ejecute los siguientes cmdlets de PowerShell desde un símbolo del sistema con privilegios elevados:

   ```PowerShell
    $FilePathForRegistrationToken = "$env:SystemDrive\RegistrationToken.txt" 
    
    $RegistrationToken = Get-AzsRegistrationToken `
      -PrivilegedEndpointCredential $YourCloudAdminCredential `
      -UsageReportingEnabled:$False `
      -PrivilegedEndpoint $YourPrivilegedEndpoint `
      -BillingModel Custom -msAssetTag '<MS Asset tag>' `
      -TokenOutputFilePath $FilePathForRegistrationToken 
   ```

   > [!TIP]  
   > El token del registro se guarda en el archivo especificado en `$FilePathForRegistrationToken`. Puede cambiar la ruta de acceso o el nombre de archivo según sea necesario.

2. Guarde este token de registro para usarlo en la máquina conectada a Azure. El archivo o el texto se pueden copiar de `$FilePathForRegistrationToken`.

### <a name="connect-to-azure-and-register"></a>Conexión a Azure y posterior registro

En el equipo que está conectado a Internet, inicie sesión en el contexto correcto de Azure PowerShell. A continuación, llame a **Register-AzsEnvironment**. Especifique el token de registro que desea registrar en Azure. Si va a registrar más de una instancia de Azure Stack Hub con el mismo identificador de suscripción de Azure, especifique un nombre de registro único.

Necesita el token de registro y un nombre de token único.

1. En un símbolo del sistema de PowerShell con privilegios elevados, ejecute los siguientes cmdlets para asegurarse de que usa la suscripción correcta.

    ```powershell  
    Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
    ```

2. A continuación, ejecute los siguientes cmdlets de PowerShell:

    ```powershell  
    $RegistrationToken = "<Your Registration Token>"
    $RegistrationName = "<unique-registration-name>"
    Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
    ```

3. Si lo desea, puede usar el cmdlet **Get-Content** para apuntar a un archivo que contenga el token de registro.

   Necesita el token de registro y un nombre de token único.

   1. Inicie PowerShell ISE como administrador y vaya a la carpeta **Registration** del directorio **AzureStack-Tools-master** que creó cuando descargó las herramientas de Azure Stack Hub. Importe el módulo **RegisterWithAzure.psm1**:

      ```powershell  
      Import-Module .\RegisterWithAzure.psm1
      ```

   2. A continuación, ejecute los siguientes cmdlets de PowerShell:

      ```powershell  
      $RegistrationToken = Get-Content -Path '<Path>\<Registration Token File>'
      Register-AzsEnvironment -RegistrationToken $RegistrationToken -RegistrationName $RegistrationName
      ```

> [!NOTE]  
> Guarde el nombre de recurso de registro y el token de registro para futuras referencias.

### <a name="retrieve-an-activation-key-from-azure-registration-resource"></a>Recuperación de una clave de activación del recurso de registro de Azure

A continuación, recupere una clave de activación del recurso de registro creado en Azure durante la ejecución de **Register-AzsEnvironment**.

Para obtener la clave de activación, ejecute los siguientes cmdlets de PowerShell:

```PowerShell
$RegistrationResourceName = "<unique-registration-name>"
$KeyOutputFilePath = "$env:SystemDrive\ActivationKey.txt"
$ActivationKey = Get-AzsActivationKey -RegistrationName $RegistrationResourceName -KeyOutputFilePath $KeyOutputFilePath
```

> [!TIP]  
> La clave de activación se guarda en el archivo especificado en `$KeyOutputFilePath`. Puede cambiar la ruta de acceso o el nombre de archivo según sea necesario.

### <a name="create-an-activation-resource-in-azure-stack-hub"></a>Creación de un recurso de activación en Azure Stack Hub

Vuelva al entorno de Azure Stack Hub con el archivo o el texto de la clave de activación creada con **Get-AzsActivationKey**. Luego, cree un recurso de activación en Azure Stack Hub con dicha clave de activación. Para crear un recurso de activación, ejecute los siguientes cmdlets de PowerShell:

```PowerShell
$ActivationKey = "<activation key>"
New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
```

Si lo desea, puede usar el cmdlet **Get-Content** para apuntar a un archivo que contenga el token de registro:

```PowerShell
$ActivationKey = Get-Content -Path '<Path>\<Activation Key File>'
New-AzsActivationResource -PrivilegedEndpointCredential $YourCloudAdminCredential -PrivilegedEndpoint $YourPrivilegedEndpoint -ActivationKey $ActivationKey
```

## <a name="verify-azure-stack-hub-registration"></a>Comprobación del registro de Azure Stack Hub

Puede usar el icono de **administración de regiones** para comprobar si el registro de Azure Stack Hub se ha realizado correctamente. Este icono está disponible en el panel predeterminado del portal del administrador. El estado puede ser Registrado o No registrado. Si está registrado, también se muestra el identificador de la suscripción de Azure que usó para registrar Azure Stack Hub, junto con el grupo de recursos de registro y el nombre.

1. Inicie sesión en el portal de administración de Azure Stack Hub (`https://adminportal.local.azurestack.external`).

2. En el panel, seleccione **Region management** (Administración de regiones).

3. Seleccione **Propiedades**. Esta hoja muestra el estado y los detalles de su entorno. El estado puede ser **Registered** (Registrado), **Not registered** (No registrado) o **Expired** (Expirado).

   [![Icono de administración de regiones en el portal del administrador de Azure Stack Hub](media/registration-tzl/admin1sm.png "Icono de administración de regiones")](media/registration-tzl/admin1.png#lightbox)

   Si ya se ha registrado, las propiedades incluyen:

    - **Identificador de suscripción para el registro**: identificador de suscripción de Azure registrado y asociado con Azure Stack Hub.
    - **Grupo de recursos de registro**: grupo de recursos de Azure de la suscripción asociada que contiene los recursos de Azure Stack Hub.

4. Puede usar Azure Portal para ver los recursos de registro de Azure Stack Hub y, luego, comprobar que el registro se ha realizado correctamente. Inicie sesión en [Azure Portal](https://portal.azure.com/) con una cuenta asociada a la suscripción que usó para registrar Azure Stack Hub. Seleccione **Todos los recursos**, active la casilla **Mostrar tipos ocultos** y, a continuación, seleccione el nombre del registro.

5. Si el registro no se realizó correctamente, debe volver a registrarlo según las instrucciones que se indican en [Errores de registro](https://docs.microsoft.com/azure-stack/operator/azure-stack-registration#change-the-subscription-you-use) para resolver el problema.

Como alternativa, puede comprobar si el registro es correcto mediante la característica de administración de Marketplace. Si ve una lista de elementos de Marketplace en la hoja **Administración de Marketplace**, significa que su registro se realizó correctamente. Sin embargo, en entornos desconectados, no puede ver los elementos de Marketplace en la administración de Marketplace.

> [!NOTE]
> Una vez completado el registro, ya no aparecerá la advertencia activa sobre la falta de registro.

> [!NOTE]
> El mensaje de error **[InvalidRegistrationToken]:BillingModel 'Custom' is not supported by subscription** ([InvalidRegistrationToken]: la suscripción no admite el modelo de facturación "Personalizado") indica que la suscripción que usa para el registro no se ha aprobado para su uso con Azure Stack Hub resistente o MDC. Póngase en contacto con azshregistration@microsoft.com para que se apruebe una suscripción e indique el tipo de producto (Azure Stack Hub resistente o MDC) que va a registrar.

## <a name="next-steps"></a>Pasos siguientes

[Aspectos básicos de administración de Azure Stack Hub](../../operator/azure-stack-manage-basics.md)  
