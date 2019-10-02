---
title: Uso de una identidad de aplicación para acceder a recursos | Microsoft Docs
description: Obtenga información sobre cómo administrar una entidad de servicio que puede usarse con el control de acceso basado en roles para el inicio de sesión y el acceso a los recursos.
services: azure-stack
documentationcenter: na
author: BryanLa
manager: femila
ms.service: azure-stack
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/09/2019
ms.author: bryanla
ms.lastreviewed: 06/20/2019
ms.openlocfilehash: b75c80ca79e2d7c3d2ab4b2003a841882fd20faa
ms.sourcegitcommit: 3af71025e85fc53ce529de2f6a5c396b806121ed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/20/2019
ms.locfileid: "71159711"
---
# <a name="use-an-app-identity-to-access-resources"></a>Uso de una identidad de aplicación para acceder a recursos

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack (ASDK)*

Una aplicación que necesita implementar o configurar recursos a través de Azure Resource Manager, debe estar representada por una entidad de servicio. Igual que un usuario está representado por una entidad de seguridad de usuario, una entidad de servicio es un tipo de entidad de seguridad que representa una aplicación. La entidad de servicio proporciona una identidad para la aplicación, lo que le permite delegar únicamente los permisos necesarios a esa entidad de servicio.  

Por ejemplo, puede tener una aplicación de administración de configuración que use Azure Resource Manager para hacer un inventario de los recursos de Azure. En este escenario, puede crear una entidad de servicio, concederle el rol de lector y limitar la aplicación de administración de configuración al acceso de solo lectura.

## <a name="overview"></a>Información general

Al igual que una entidad de seguridad de usuario, una entidad de servicio debe presentar las credenciales durante la autenticación. Esta autenticación consta de dos elementos:

- Un **Id. de aplicación**, que a veces se conoce como “Id. de cliente”. Se trata de un GUID que identifica de manera única el registro de la aplicación en el inquilino de Active Directory.
- Un **secreto** asociado al id. de la aplicación. Puedes generar una cadena de secreto de cliente (similar a una contraseña), o especificar un certificado X509 (que usa su clave pública).

Ejecutar una aplicación con la identidad de una entidad de servicio es preferible a ejecutarla con una entidad de seguridad de usuario por los siguientes motivos:

 - Una entidad de servicio puede utilizar un certificado X509 para tener **credenciales más seguras**.  
 - Se pueden asignar **permisos más restrictivos** a una entidad de servicio. Normalmente, estos permisos están restringidos solo a aquello que la aplicación debe hacer, lo que se conoce como *principio de privilegio mínimo*.
 - Las **credenciales y los permisos de la entidad de servicio no cambian con tanta frecuencia** como las credenciales de usuario. Por ejemplo, cuando las responsabilidades del usuario cambian, los requisitos de contraseña dictan un cambio o un usuario abandona la empresa.

Primero debe crear un nuevo registro de aplicación en su directorio, que crea un [objeto de entidad de servicio](/azure/active-directory/develop/developer-glossary#service-principal-object) asociado para representar la identidad de la aplicación en el directorio. Este documento describe el proceso de creación y administración de una entidad de servicio, según el directorio que eligió para su instancia de Azure Stack:

- Azure Active Directory (Azure AD). Azure AD es un directorio multiinquilino, basado en la nube y un servicio de administración de identidades. Puede usar Azure AD con una instancia de Azure Stack conectada.
- Servicios de federación de Active Directory (AD FS). AD FS proporciona funcionalidades de una federación de identidades simplificada y protegida, así como de inicio de sesión único (SSO) web. Puede usar AD FS con instancias de Azure Stack tanto conectadas como desconectadas.

En primer lugar, debe aprender a administrar a una entidad de servicio y, después, cómo asignarla a un rol y limitar así el acceso a su recurso.

## <a name="manage-an-azure-ad-service-principal"></a>Administración de una entidad de servicio de Azure AD

Si implementó Azure Stack con Azure AD como servicio de administración de identidades, puede crear entidades de servicio igual que haría para Azure. En este tema se muestra cómo realizar estos pasos a través de Azure Portal. Compruebe que tiene los [permisos de Azure AD necesarios](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) antes de comenzar.

### <a name="create-a-service-principal-that-uses-a-client-secret-credential"></a>Creación de una entidad de servicio mediante un secreto de cliente

En esta sección, debe registrar la aplicación mediante Azure Portal, que crea el objeto de entidad de servicio en su inquilino de Azure AD. En este ejemplo, se crea la entidad de servicio con una credencial de secreto de cliente, pero el portal también admite credenciales basadas en certificados X509.

1. Inicie sesión en [Azure Portal](https://portal.azure.com) con su cuenta de Azure.
2. Haga clic en **Azure Active Directory** > **Registros de aplicaciones** > **Nuevo registro**.
3. Proporcione un **nombre** para la aplicación.
4. Seleccione los **tipos de cuenta admitidos** adecuados.
5. En **URI de redirección**, seleccione **Web** como tipo de aplicación y, de manera opcional, especifique un URI de redirección si así lo requiere la aplicación.
6. Después de configurar los valores, seleccione **Registrar**. Se crea el registro de aplicación y se muestra la página **Información general**.
7. Copie el **Id. de aplicación** para usarlo en el código de la aplicación. Este nombre también se conoce como “id. de cliente”.
8. Para generar un secreto de cliente, seleccione la página **Certificados y secretos**. Seleccione **Nuevo secreto de cliente**.
9. Proporcione una **descripción** para el secreto y una duración en **Expira**.
10. Cuando haya terminado, seleccione **Agregar**.
11. Se muestra el valor del secreto. Copie y guarde este valor en otra ubicación, ya que no podrá recuperarlo más adelante. Proporcione el secreto con el id. de la aplicación en la aplicación cliente durante el inicio de sesión de la entidad de servicio.

    ![Clave guardada en secretos de cliente](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

## <a name="manage-an-ad-fs-service-principal"></a>Administración de una entidad de servicio de AD FS

Si ha implementado Azure Stack con AD FS como servicio de administración de identidades, debe usar PowerShell para administrar la entidad de servicio. A continuación, se proporcionan ejemplos de administración de credenciales de entidad de servicio, que muestran ambas opciones de certificado X509 y de secreto de cliente.

Los scripts se deben ejecutar en una consola de PowerShell con privilegios elevados ("Ejecutar como administrador"), que abre otra sesión en una VM que hospeda un punto de conexión con privilegios para la instancia de Azure Stack. Una vez establecida la sesión de conexión con privilegios, cmdlets adicionales ejecutarán y administrarán la entidad de servicio. Para más información sobre el punto de conexión con privilegios, consulte [Uso del punto de conexión con privilegios en Azure Stack](azure-stack-privileged-endpoint.md).

### <a name="create-a-service-principal-that-uses-a-certificate-credential"></a>Creación de una entidad de servicio que utiliza una credencial de certificado

Al crear un certificado para una credencial de entidad de servicio, deben cumplirse los siguientes requisitos:

 - Para producción, el certificado se debe emitir desde una entidad de certificación interna o pública. Si se usa una entidad de certificación pública, es preciso incluirla en la imagen del sistema operativo base como parte del programa de entidades de certificación raíz de confianza de Microsoft (Microsoft Trusted Root Certificate Program). Puede ver la lista completa en el artículo [Microsoft Trusted Root Certificate Program: Participants](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca) (Programa de entidades de certificación raíz de confianza de Microsoft: Participantes). Más adelante, durante la [actualización de la credencial de certificado de una entidad de servicio](#update-a-service-principals-certificate-credential), se mostrará un ejemplo de creación de un certificado de prueba autofirmado. 
 - El proveedor de servicios criptográficos debe especificarse como un proveedor de claves del proveedor de servicios criptográficos (CSP) heredado de Microsoft.
 - El certificado debe estar en formato PFX, ya que se requieren claves públicas y privadas. Los servidores de Windows utilizan archivos .pfx que contienen el archivo de claves públicas (archivo de certificado SSL) y el archivo de claves privadas asociado.
 - La infraestructura de Azure Stack debe tener acceso de red a la ubicación de la lista de revocación de certificados (CRL) de la entidad de certificación publicada en el certificado. Esta lista de revocación de certificados debe ser un punto de conexión de HTTP.

Cuando tenga un certificado, use el siguiente script de PowerShell para registrar la aplicación y crear una entidad de servicio. También debe usar la entidad de servicio para iniciar sesión en Azure. Sustituya sus propios valores por los marcadores de posición siguientes:

| Marcador de posición | DESCRIPCIÓN | Ejemplo |
| ----------- | ----------- | ------- |
| \<PepVM\> | Nombre de la VM del extremo de conexión con privilegios en la instancia de Azure Stack. | "AzS-ERCS01" |
| \<YourCertificateLocation\> | Ubicación de su certificado X509 en el almacén de certificados local. | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<YourAppName\> | Nombre descriptivo del registro de la nueva aplicación. | "Mi herramienta de administración" |

1. Abra una sesión de Windows PowerShell con privilegios elevados y ejecute el script siguiente:

   ```powershell  
    # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
    $Creds = Get-Credential

    # Create a PSSession to the Privileged Endpoint VM
    $Session = New-PSSession -ComputerName "<PepVm>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # Use the Get-Item cmdlet to retrieve your certificate.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    # Use the privileged endpoint to create the new app registration (and service principal object)
    $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # Using the stamp info for your Azure Stack instance, populate the following variables:
    # - AzureRM endpoint used for Azure Resource Manager operations 
    # - Audience for acquiring an OAuth token used to access Graph API 
    # - GUID of the directory tenant
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
    $TenantID = $AzureStackInfo.AADTenantID

    # Register and set an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint
    Set-AzureRmEnvironment -Name "AzureStackUser" -GraphAudience $GraphAudience -EnableAdfsAuthentication:$true

    # Sign in using the new service principal identity
    $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $SpObject.Thumbprint `
    -ApplicationId $SpObject.ClientId `
    -TenantId $TenantID

    # Output the service principal details
    $SpObject

   ```
   
2. Cuando el script finaliza, muestra la información de registro de la aplicación, incluidas las credenciales de la entidad de servicio. Como se muestra, `ClientID` y `Thumbprint` se usan para iniciar sesión con la identidad de la entidad de servicio. Tras iniciar sesión correctamente, la identidad de la entidad de servicio se usará para la posterior autorización y el acceso a los recursos que administra Azure Resource Manager.

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   ClientSecret          :
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

Mantenga la sesión de la consola de PowerShell abierta, ya que la usará con el valor `ApplicationIdentifier` en la sección siguiente.

### <a name="update-a-service-principals-certificate-credential"></a>Actualización de la credencial de certificado de una entidad de servicio

Ahora que ha creado una entidad de servicio, esta sección le mostrará cómo realizar las acciones siguientes:

1. Crear un nuevo certificado X509 autofirmado para pruebas.
2. Actualizar las credenciales de la entidad de servicio mediante la actualización de su propiedad **Thumbprint** para que coincida con el nuevo certificado.

Actualice la credencial de certificado con PowerShell y sustituya sus propios valores para los marcadores de posición siguientes:

| Marcador de posición | DESCRIPCIÓN | Ejemplo |
| ----------- | ----------- | ------- |
| \<PepVM\> | Nombre de la VM del extremo de conexión con privilegios en la instancia de Azure Stack. | "AzS-ERCS01" |
| \<YourAppName\> | Nombre descriptivo del registro de la nueva aplicación. | "Mi herramienta de administración" |
| \<YourCertificateLocation\> | Ubicación de su certificado X509 en el almacén de certificados local. | "Cert:\CurrentUser\My\AB5A8A3533CC7AA2025BF05120117E06DE407B34" |
| \<AppIdentifier\> | Identificador asignado al registro de la aplicación. | "S-1-5-21-1512385356-3796245103-1243299919-1356" |

1. Con la sesión de Windows PowerShell con privilegios elevados, ejecute los siguientes cmdlets:

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Create a self-signed certificate for testing purposes. 
     $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
     # In production, use Get-Item and a managed certificate instead.
     # $Cert = Get-Item "<YourCertificateLocation>"

     # Use the privileged endpoint to update the certificate thumbprint, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ClientCertificates $using:NewCert}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. Cuando el script finaliza, muestra la información de registro de la aplicación actualizada, incluido el valor de huella digital del nuevo certificado autofirmado.

     ```Shell  
     ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
     ClientId              : 
     Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
     ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
     ClientSecret          : 
     PSComputerName        : azs-ercs01
     RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-that-uses-client-secret-credentials"></a>Creación de una entidad de servicio que usa credenciales de secreto de cliente

> [!IMPORTANT]
> Es menos seguro utilizar un secreto de cliente que una credencial de certificado X509. No solo es el mecanismo de autenticación menos seguro, si no que además suele requerir la inserción del secreto en el código fuente de la aplicación cliente. Por lo tanto, para las aplicaciones de producción, se recomienda encarecidamente usar una credencial de certificado.

Ahora cree otro registro de aplicación, pero esta vez especifique una credencial de secreto de cliente. A diferencia de una credencial de certificado, el directorio tiene la capacidad de generar una credencial de secreto de cliente. En lugar de especificar el secreto de cliente, use el conmutador `-GenerateClientSecret` para solicitar que se genere. Sustituya sus propios valores por los marcadores de posición siguientes:

| Marcador de posición | DESCRIPCIÓN | Ejemplo |
| ----------- | ----------- | ------- |
| \<PepVM\> | Nombre de la VM del extremo de conexión con privilegios en la instancia de Azure Stack. | "AzS-ERCS01" |
| \<YourAppName\> | Nombre descriptivo del registro de la nueva aplicación. | "Mi herramienta de administración" |

1. Abra una sesión de Windows PowerShell con privilegios elevados y ejecute los siguientes cmdlets:

     ```powershell  
     # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
     $Creds = Get-Credential

     # Create a PSSession to the Privileged Endpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to create the new app registration (and service principal object)
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name "<YourAppName>" -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Using the stamp info for your Azure Stack instance, populate the following variables:
     # - AzureRM endpoint used for Azure Resource Manager operations 
     # - Audience for acquiring an OAuth token used to access Graph API 
     # - GUID of the directory tenant
     $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager
     $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"
     $TenantID = $AzureStackInfo.AADTenantID

     # Register and set an AzureRM environment that targets your Azure Stack instance
     Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint $ArmEndpoint
     Set-AzureRmEnvironment -Name "AzureStackUser" -GraphAudience $GraphAudience -EnableAdfsAuthentication:$true

     # Sign in using the new service principal identity
     $securePassword = $SpObject.ClientSecret | ConvertTo-SecureString -AsPlainText -Force
     $credential = New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $SpObject.ClientId, $securePassword
     $SpSignin = Connect-AzureRmAccount -Environment "AzureStackUser" -ServicePrincipal -Credential $credential -TenantId $TenantID

     # Output the service principal details
     $SpObject
     ```

2. Cuando el script finaliza, muestra la información de registro de la aplicación, incluidas las credenciales de la entidad de servicio. Como se muestra, el `ClientID` y el `ClientSecret` generado se usan para iniciar sesión con la identidad de la entidad de servicio. Tras iniciar sesión correctamente, la identidad de la entidad de servicio se usará para la posterior autorización y el acceso a los recursos que administra Azure Resource Manager.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUWLRoBw3EebBLgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : azs-ercs01
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

Mantenga la sesión de la consola de PowerShell abierta, ya que la usará con el valor `ApplicationIdentifier` en la sección siguiente.

### <a name="update-a-service-principals-client-secret"></a>Actualización del secreto de cliente de una entidad de servicio

Actualice la credencial de secreto de cliente con PowerShell, mediante el parámetro **ResetClientSecret**, que cambia inmediatamente el secreto de cliente. Sustituya sus propios valores por los marcadores de posición siguientes:

| Marcador de posición | DESCRIPCIÓN | Ejemplo |
| ----------- | ----------- | ------- |
| \<PepVM\> | Nombre de la VM del extremo de conexión con privilegios en la instancia de Azure Stack. | "AzS-ERCS01" |
| \<AppIdentifier\> | Identificador asignado al registro de la aplicación. | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

1. Con la sesión de Windows PowerShell con privilegios elevados, ejecute los siguientes cmdlets:

     ```powershell
     # Create a PSSession to the PrivilegedEndpoint VM
     $Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Use the privileged endpoint to update the client secret, used by the service principal associated with <AppIdentifier>
     $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<AppIdentifier>" -ResetClientSecret}
     $Session | Remove-PSSession

     # Output the updated service principal details
     $SpObject
     ```

2. Cuando el script finaliza, muestra la información de registro de la aplicación actualizada, incluido el secreto de cliente recién generado.

     ```shell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
     PSComputerName        : azs-ercs01
     RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal"></a>Eliminación de una entidad de servicio

Ahora verá cómo quitar o eliminar un registro de aplicación del directorio, así como su objeto de entidad de servicio asociado, mediante PowerShell. 

Sustituya sus propios valores por los marcadores de posición siguientes:

| Marcador de posición | DESCRIPCIÓN | Ejemplo |
| ----------- | ----------- | ------- |
| \<PepVM\> | Nombre de la VM del extremo de conexión con privilegios en la instancia de Azure Stack. | "AzS-ERCS01" |
| \<AppIdentifier\> | Identificador asignado al registro de la aplicación. | "S-1-5-21-1634563105-1224503876-2692824315-2623" |

```powershell  
# Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint (typically <domain>\cloudadmin)
$Creds = Get-Credential

# Create a PSSession to the PrivilegedEndpoint VM
$Session = New-PSSession -ComputerName "<PepVM>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

# OPTIONAL: Use the privileged endpoint to get a list of applications registered in AD FS
$AppList = Invoke-Command -Session $Session -ScriptBlock {Get-GraphApplication}

# Use the privileged endpoint to remove the application and associated service principal object for <AppIdentifier>
Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier "<AppIdentifier>"}
```

No se devolverá ninguna salida de la llamada al cmdlet Remove-GraphApplication en el punto de conexión con privilegios, pero verá la salida de confirmación textual en la consola durante la ejecución del cmdlet:

```shell
VERBOSE: Deleting graph application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623.
VERBOSE: Remove-GraphApplication : BEGIN on AZS-ADFS01 on ADFSGraphEndpoint
VERBOSE: Application with identifier S-1-5-21-1634563105-1224503876-2692824315-2623 was deleted.
VERBOSE: Remove-GraphApplication : END on AZS-ADFS01 under ADFSGraphEndpoint configuration
```

## <a name="assign-a-role"></a>Asignar un rol

El acceso a los recursos de Azure para usuarios y aplicaciones se autoriza a través del control de acceso basado en roles (RBAC). Para permitir que una aplicación acceda a los recursos de su suscripción con su entidad de servicio, debe *asignar* la entidad de servicio un *rol* para un determinado *recurso*. Primero decida qué rol representa los *permisos* adecuados para la aplicación. Para obtener más información sobre los roles disponibles, vea [Roles integrados en los recursos de Azure](/azure/role-based-access-control/built-in-roles).

El tipo de recurso elegido también establece el *ámbito de acceso* de la entidad de servicio. Puede establecer el ámbito de acceso en el nivel de la suscripción, el grupo de recursos o el recurso. Los permisos se heredan en los niveles inferiores del ámbito. Por ejemplo, el hecho de agregar una aplicación al rol "Lector" para un grupo de recursos significa que esta puede leer el grupo de recursos y los recursos que contiene.

1. Inicie sesión en el portal adecuado, según el directorio especificado durante la instalación de Azure Stack (Azure Portal de Azure AD o portal de usuarios de Azure Stack de AD FS, por ejemplo). En este ejemplo, se muestra un usuario que ha iniciado sesión en el portal de usuarios de Azure Stack.

   > [!NOTE]
   > Para agregar asignaciones de rol para un recurso determinado, su cuenta de usuario debe pertenecer a un rol que declare el permiso `Microsoft.Authorization/roleAssignments/write`. Por ejemplo, los roles integrados [Propietario](/azure/role-based-access-control/built-in-roles#owner) o [Administrador de acceso de usuario](/azure/role-based-access-control/built-in-roles#user-access-administrator).  
2. Navegue al recurso para el que quiere conceder acceso a la entidad de servicio. En este ejemplo, asigne la entidad de servicio a un rol en el ámbito de la suscripción. Para ello, seleccione **Suscripciones** y, a continuación, una suscripción específica. En su lugar, también puede seleccionar un grupo de recursos o un recurso específico, como una máquina virtual.

     ![Seleccionar suscripción para la asignación](./media/azure-stack-create-service-principal/select-subscription.png)

3. Seleccione la página **Access Control (IAM)** página, que es universal en todos los recursos compatibles con RBAC.
4. Seleccione **+ Agregar**.
5. En **Rol**, seleccione el rol que quiere asignar a la aplicación.
6. En **Seleccionar**, busque la aplicación con un nombre de aplicación completo o parcial. Durante el registro, el nombre de la aplicación se genera como *Azurestack-\<YourAppName\>-\<ClientId\>* . Por ejemplo, si usa un nombre de aplicación *App2* y se asignó el ClientId *2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff* durante la creación, el nombre completo sería  *Azurestack-App2-2bbe67d8-3fdb-4b62-87cf-cc41dd4344ff*. Puede buscar la cadena exacta o una parte de esta, como *Azurestack* o *Azurestack App2*.
7. Cuando encuentre la aplicación, selecciónela y se mostrará en **Miembros seleccionados**.
8. Haga clic en **Guardar** para finalizar la asignación del rol.

     [![Asignación de un rol](media/azure-stack-create-service-principal/assign-role.png)](media/azure-stack-create-service-principal/assign-role.png#lightbox)

9. Cuando termine, la aplicación se mostrará en la lista de entidades de seguridad asignadas para el ámbito actual y el rol especificado.

     [![Rol asignado](media/azure-stack-create-service-principal/assigned-role.png)](media/azure-stack-create-service-principal/assigned-role.png#lightbox)

Ahora que ha creado una entidad de servicio y le ha asignado un rol, puede empezar a usarla dentro de la aplicación para tener acceso a los recursos de Azure Stack.  

## <a name="next-steps"></a>Pasos siguientes

[Adición de usuarios a AD FS](azure-stack-add-users-adfs.md)  
[Administración de permisos de usuario](azure-stack-manage-permissions.md)  
[Documentación de Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Servicios de federación de Active Directory](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
