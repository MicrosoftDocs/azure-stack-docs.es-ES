---
title: Administración de una entidad de servicio de Azure Stack | Microsoft Docs
description: Describe cómo administrar una nueva entidad de servicio que puede usarse con el control de acceso basado en roles en Azure Resource Manager para administrar el acceso a los recursos.
services: azure-resource-manager
documentationcenter: na
author: PatAltimore
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/17/2019
ms.author: patricka
ms.lastreviewed: 05/17/2019
ms.openlocfilehash: b08d2b59653b099b0cd0a314347ea2667fa42ca8
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691304"
---
# <a name="provide-applications-access-to-azure-stack"></a>Proporcionar a las aplicaciones acceso a Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Cuando una aplicación necesite acceso para implementar o configurar recursos a través de Azure Resource Manager en Azure Stack, cree una entidad de servicio, que es una credencial para la aplicación. A continuación, puede delegar únicamente los permisos necesarios para esa entidad de servicio.  

Por ejemplo, puede tener una herramienta de administración de configuración que use Azure Resource Manager para hacer un inventario de los recursos de Azure. En este escenario, puede crear una entidad de servicio, concederle el rol de lector y limitar la herramienta de administración de configuración al acceso de solo lectura. 

Es preferible que las entidades de servicio ejecuten la aplicación con sus propias credenciales por los siguientes motivos:

 - Puede asignar permisos a la entidad de servicio diferentes a los de su cuenta. Normalmente, estos permisos están restringidos a exactamente aquello que la aplicación debe hacer.
 - No es necesario cambiar las credenciales de la aplicación si las responsabilidades cambian.
 - Puede usar un certificado para automatizar la autenticación al ejecutar un script desatendido.  

## <a name="getting-started"></a>Introducción

Dependiendo de cómo ha implementado Azure Stack, primero debe crear una entidad de servicio. Este documento describe la creación de una entidad de servicio para:

- Azure Active Directory (Azure AD). Azure AD es un directorio multiinquilino, basado en la nube y un servicio de administración de identidades. Puede usar Azure AD con una instancia de Azure Stack conectada.
- Servicios de federación de Active Directory (AD FS). AD FS proporciona funcionalidades de una federación de identidades simplificada y protegida, así como de inicio de sesión único (SSO) web. Puede usar AD FS con instancias de Azure Stack tanto conectadas como desconectadas.

Una vez que haya creado la entidad de servicio, se usa un conjunto de pasos comunes a AD FS y Azure Active Directory para delegar permisos en el rol.

## <a name="manage-service-principal-for-azure-ad"></a>Administración de una entidad de servicio para Azure AD

Si ha implementado Azure Stack con Azure Active Directory (Azure AD) como servicio de administración de identidades, puede crear entidades de servicio igual que haría para Azure. En este tema se muestra cómo realizar estos pasos a través del portal. Compruebe que tiene los [permisos de Azure AD necesarios](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) antes de comenzar.

### <a name="create-service-principal"></a>Creación de una entidad de servicio

En esta sección, creará una aplicación (entidad de servicio) en Azure AD que representará su aplicación.

1. Inicie sesión en su cuenta de Azure mediante [Azure Portal](https://portal.azure.com).
2. Haga clic en **Azure Active Directory** > **Registros de aplicaciones** > **Nuevo registro**.
3. Proporcione un nombre y una dirección URL para la aplicación. 
4. Seleccione los **tipos de cuenta admitidos**.
5.  Agregue un identificador URI para la aplicación. Seleccione **Web** para indicar el tipo de aplicación que quiere crear. Después de configurar los valores, seleccione **Registrar**.

Creó una entidad de servicio para la aplicación.

### <a name="get-credentials"></a>Obtener credenciales

Al iniciar sesión mediante programación, deberá usar el identificador de la aplicación y, para una aplicación web o API, una clave de autenticación. Para obtener estos valores, use los pasos siguientes:

1. Seleccione **Azure Active Directory** > **Registros de aplicaciones**. Seleccione la aplicación.

2. Copie el **id. de aplicación** y almacénelo en el código de la aplicación. Las aplicaciones de la sección de aplicaciones de ejemplo hacen referencia a este valor como el identificador de cliente.

3. Para generar una clave de autenticación para una aplicación web o API, seleccione **Certificados y secretos**. Seleccione **Nuevo secreto de cliente**.

4. Proporcione una descripción de la clave y una duración. Cuando haya terminado, seleccione **Agregar**.

Después de guardar la clave, se muestra el valor de la clave. Copie este valor en el Bloc de notas o en cualquier otra ubicación temporal, ya que no se podrá recuperar la clave más adelante. Proporcione el valor de clave junto con el identificador de la aplicación para firmar en nombre de la aplicación. Guarde el valor de clave en un lugar donde la aplicación pueda recuperarlo.

![Clave guardada](./media/azure-stack-create-service-principal/create-service-principal-in-azure-stack-secret.png)

Una vez que haya finalizado, puede asignar un rol a la aplicación.

## <a name="manage-service-principal-for-ad-fs"></a>Administración de una entidad de servicio para AD FS

Si ha implementado Azure Stack con los Servicios de federación de Active Directory (AD FS) como servicio de administración de identidades, use PowerShell para crear una entidad de servicio, asignar un rol para el acceso e iniciar sesión con esa identidad.

Puede usar dos métodos para crear su entidad de servicio con AD FS. Puede:
 - [Crear una entidad de servicio mediante un certificado](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate).
 - [Crear una entidad de servicio mediante un secreto de cliente](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret).

Tareas de administración de entidades de servicio de AD FS.

| Type | . |
| --- | --- |
| Certificado de AD FS | [Creación](azure-stack-create-service-principals.md#create-a-service-principal-using-a-certificate) |
| Certificado de AD FS | [Actualizar](azure-stack-create-service-principals.md#update-certificate-for-service-principal-for-ad-fs) |
| Certificado de AD FS | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |
| Secreto de cliente de AD FS | [Creación](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Secreto de cliente de AD FS | [Actualizar](azure-stack-create-service-principals.md#create-a-service-principal-using-a-client-secret) |
| Secreto de cliente de AD FS | [Remove](azure-stack-create-service-principals.md#remove-a-service-principal-for-ad-fs) |

### <a name="create-a-service-principal-using-a-certificate"></a>Creación de una entidad de servicio mediante un certificado

Puede usar un certificado para crear una entidad de servicio mientras usa AD FS para la identidad.

#### <a name="certificate"></a>Certificate

Se requiere un certificado.

**Requisitos del certificado**

 - El proveedor de servicios criptográficos (CSP) debe ser un proveedor de claves heredado.
 - El certificado debe estar en formato PFX, ya que se requieren claves públicas y privadas. Los servidores de Windows utilizan archivos .pfx que contienen el archivo de claves públicas (archivo de certificado SSL) y el archivo de claves privadas asociado.
 - Para producción, el certificado se debe emitir desde una entidad de certificación interna o pública. Si se usa una entidad de certificación pública, es preciso incluirla en la imagen del sistema operativo base como parte del programa de entidades de certificación raíz de confianza de Microsoft (Microsoft Trusted Root Certificate Program). Puede ver la lista completa en el artículo [Microsoft Trusted Root Certificate Program: Participants](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca) (Programa de entidades de certificación raíz de confianza de Microsoft: Participantes).
 - La infraestructura de Azure Stack debe tener acceso de red a la ubicación de la lista de revocación de certificados (CRL) de la entidad de certificación publicada en el certificado. Esta lista de revocación de certificados debe ser un punto de conexión de HTTP.

#### <a name="parameters"></a>Parámetros

Se requiere la siguiente información como entrada para los parámetros de automatización:

|Parámetro|DESCRIPCIÓN|Ejemplo|
|---------|---------|---------|
|NOMBRE|Nombre de la cuenta SPN|MyAPP|
|ClientCertificates|Matriz de objetos de certificado|Certificado X509|
|ClientRedirectUris<br>(Opcional)|URI de redireccionamiento de aplicación|-|

#### <a name="use-powershell-to-create-a-service-principal"></a>Uso de PowerShell para crear una entidad de servicio

1. Abra una sesión de Windows PowerShell con privilegios elevados y ejecute los siguientes cmdlets:

   ```powershell  
    # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
    $Creds = Get-Credential

    # Creating a PSSession to the ERCS PrivilegedEndpoint
    $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # If you have a managed certificate use the Get-Item command to retrieve your certificate from your certificate location.
    # If you don't want to use a managed certificate, you can produce a self signed cert for testing purposes: 
    # $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange
    $Cert = Get-Item "<YourCertificateLocation>"
    
    $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -ClientCertificates $using:cert}
    $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
    $Session | Remove-PSSession

    # For Azure Stack development kit, this value is set to https://management.local.azurestack.external. This is read from the AzureStackStampInformation output of the ERCS VM.
    $ArmEndpoint = $AzureStackInfo.TenantExternalEndpoints.TenantResourceManager

    # For Azure Stack development kit, this value is set to https://graph.local.azurestack.external/. This is read from the AzureStackStampInformation output of the ERCS VM.
    $GraphAudience = "https://graph." + $AzureStackInfo.ExternalDomainFQDN + "/"

    # TenantID for the stamp. This is read from the AzureStackStampInformation output of the ERCS VM.
    $TenantID = $AzureStackInfo.AADTenantID

    # Register an AzureRM environment that targets your Azure Stack instance
    Add-AzureRMEnvironment `
    -Name "AzureStackUser" `
    -ArmEndpoint $ArmEndpoint

    # Set the GraphEndpointResourceId value
    Set-AzureRmEnvironment `
    -Name "AzureStackUser" `
    -GraphAudience $GraphAudience `
    -EnableAdfsAuthentication:$true

    Add-AzureRmAccount -EnvironmentName "AzureStackUser" `
    -ServicePrincipal `
    -CertificateThumbprint $ServicePrincipal.Thumbprint `
    -ApplicationId $ServicePrincipal.ClientId `
    -TenantId $TenantID

    # Output the SPN details
    $ServicePrincipal

   ```
   > [!Note]  
   > Para fines de validación, se puede crear un certificado autofirmado mediante el ejemplo siguiente:

   ```powershell  
   $Cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<yourappname>" -KeySpec KeyExchange
   ```


2. Una vez finalizada la automatización, esta muestra los detalles necesarios para usar el SPN. Se recomienda guardar los resultados para su uso posterior.

   Por ejemplo:

   ```shell
   ApplicationIdentifier : S-1-5-21-1512385356-3796245103-1243299919-1356
   ClientId              : 3c87e710-9f91-420b-b009-31fa9e430145
   Thumbprint            : 30202C11BE6864437B64CE36C8D988442082A0F1
   ApplicationName       : Azurestack-MyApp-c30febe7-1311-4fd8-9077-3d869db28342
   PSComputerName        : azs-ercs01
   RunspaceId            : a78c76bb-8cae-4db4-a45a-c1420613e01b
   ```

### <a name="update-certificate-for-service-principal-for-ad-fs"></a>Actualización del certificado de entidad de servicio de AD FS

Si ha implementado Azure Stack con AD FS, puede usar PowerShell para actualizar el secreto de una entidad de servicio.

El script se ejecuta desde el punto de conexión con privilegios de una máquina virtual ERCS.

#### <a name="parameters"></a>Parámetros

Se requiere la siguiente información como entrada para los parámetros de automatización:

|Parámetro|DESCRIPCIÓN|Ejemplo|
|---------|---------|---------|
|NOMBRE|Nombre de la cuenta SPN|MyAPP|
|ApplicationIdentifier|Identificador único|S-1-5-21-1634563105-1224503876-2692824315-2119|
|ClientCertificate|Matriz de objetos de certificado|Certificado X509|

#### <a name="example-of-updating-service-principal-for-ad-fs"></a>Ejemplo de actualización de entidad de servicio de AD FS

En este ejemplo se crea un certificado autofirmado. Al ejecutar los cmdlets en una implementación de producción, use [Get-Item](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Management/Get-Item) para recuperar el objeto de certificado del certificado que desee usar.

1. Abra una sesión de Windows PowerShell con privilegios elevados y ejecute los siguientes cmdlets:

     ```powershell
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $RemoveServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ClientCertificates $NewCert}

          $Session | Remove-PSSession
     ```

2. Una vez finalizada la automatización, muestra el valor de huella digital actualizada necesario para la autenticación de SPN.

     ```Shell  
          ClientId              : 
          Thumbprint            : AF22EE716909041055A01FE6C6F5C5CDE78948E9
          ApplicationName       : Azurestack-ThomasAPP-3e5dc4d2-d286-481c-89ba-57aa290a4818
          ClientSecret          : 
          RunspaceId            : a580f894-8f9b-40ee-aa10-77d4d142b4e5
     ```

### <a name="create-a-service-principal-using-a-client-secret"></a>Creación de una entidad de servicio mediante un secreto de cliente

Puede usar un certificado para crear una entidad de servicio mientras usa AD FS para la identidad. Usará el punto de conexión con privilegios para ejecutar los cmdlets.

Estos scripts se ejecutan desde el punto de conexión con privilegios de una máquina virtual ERCS. Para más información sobre el punto de conexión con privilegios, consulte [Uso del punto de conexión con privilegios en Azure Stack](azure-stack-privileged-endpoint.md).

#### <a name="parameters"></a>Parámetros

Se requiere la siguiente información como entrada para los parámetros de automatización:

| Parámetro | DESCRIPCIÓN | Ejemplo |
|----------------------|--------------------------|---------|
| NOMBRE | Nombre de la cuenta SPN | MyAPP |
| GenerateClientSecret | Creación de un secreto |  |

#### <a name="use-the-ercs-privilegedendpoint-to-create-the-service-principal"></a>Uso de PrivilegedEndpoint de ERCS para crear la entidad de servicio

1. Abra una sesión de Windows PowerShell con privilegios elevados y ejecute los siguientes cmdlets:

     ```powershell  
      # Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     # Creating a SPN with a secre
     $ServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {New-GraphApplication -Name '<YourAppName>' -GenerateClientSecret}
     $AzureStackInfo = Invoke-Command -Session $Session -ScriptBlock {Get-AzureStackStampInformation}
     $Session | Remove-PSSession

     # Output the SPN details
     $ServicePrincipal
     ```

2. Después de que se ejecuten los cmdlets, el shell muestra los detalles necesarios para usar el SPN. Asegúrese de guardar el secreto de cliente.

     ```powershell  
     ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2623
     ClientId              : 8e0ffd12-26c8-4178-a74b-f26bd28db601
     Thumbprint            : 
     ApplicationName       : Azurestack-YourApp-6967581b-497e-4f5a-87b5-0c8d01a9f146
     ClientSecret          : 6RUZLRoBw3EebMDgaWGiowCkoko5_j_ujIPjA8dS
     PSComputerName        : 192.168.200.224
     RunspaceId            : 286daaa1-c9a6-4176-a1a8-03f543f90998
     ```

#### <a name="update-client-secret-for-a-service-principal-for-ad-fs"></a>Actualización del secreto de cliente para una entidad de servicio de AD FS

El cmdlet de PowerShell genera automáticamente un nuevo secreto de cliente.

El script se ejecuta desde el punto de conexión con privilegios de una máquina virtual ERCS.

##### <a name="parameters"></a>Parámetros

Se requiere la siguiente información como entrada para los parámetros de automatización:

| Parámetro | DESCRIPCIÓN | Ejemplo |
|-----------------------|-----------------------------------------------------------------------------------------------------------|------------------------------------------------|
| ApplicationIdentifier | Identificador único. | S-1-5-21-1634563105-1224503876-2692824315-2119 |
| ChangeClientSecret | Cambia el secreto de cliente con un período de sustitución incremental de 2880 minutos, donde el secreto anterior sigue siendo válido. |  |
| ResetClientSecret | Cambia el secreto de cliente inmediatamente. |  |

##### <a name="example-of-updating-a-client-secret-for-ad-fs"></a>Ejemplo de actualización de un secreto de cliente de AD FS

En el ejemplo se usa el parámetro **ResetClientSecret**, que cambia inmediatamente el secreto de cliente.

1. Abra una sesión de Windows PowerShell con privilegios elevados y ejecute los siguientes cmdlets:

     ```powershell  
          # Creating a PSSession to the ERCS PrivilegedEndpoint
          $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

          # This produces a self signed cert for testing purposes. It is preferred to use a managed certificate for this.
          $NewCert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=<YourAppName>" -KeySpec KeyExchange

          $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier  S-1-5-21-1634563105-1224503876-2692824315-2120 -ResetClientSecret}

          $Session | Remove-PSSession
     ```

2. Una vez finalizada la automatización, muestra el nuevo secreto generado necesario para la autenticación de SPN. Asegúrese de guardar el nuevo secreto de cliente.

     ```powershell  
          ApplicationIdentifier : S-1-5-21-1634563105-1224503876-2692824315-2120
          ClientId              :  
          Thumbprint            : 
          ApplicationName       : Azurestack-Yourapp-6967581b-497e-4f5a-87b5-0c8d01a9f146
          ClientSecret          : MKUNzeL6PwmlhWdHB59c25WDDZlJ1A6IWzwgv_Kn
          RunspaceId            : 6ed9f903-f1be-44e3-9fef-e7e0e3f48564
     ```

### <a name="remove-a-service-principal-for-ad-fs"></a>Eliminación de una entidad de servicio para AD FS

Si ha implementado Azure Stack con AD FS, puede usar PowerShell para eliminar una entidad de servicio.

El script se ejecuta desde el punto de conexión con privilegios de una máquina virtual ERCS.

#### <a name="parameters"></a>Parámetros

Se requiere la siguiente información como entrada para los parámetros de automatización:

|Parámetro|DESCRIPCIÓN|Ejemplo|
|---------|---------|---------|
| Parámetro | DESCRIPCIÓN | Ejemplo |
| ApplicationIdentifier | Identificador único | S-1-5-21-1634563105-1224503876-2692824315-2119 |

> [!Note]  
> Para ver una lista de todas las entidades de servicio existentes y su identificador de aplicación, puede usar el comando get-graphapplication.

#### <a name="example-of-removing-the-service-principal-for-ad-fs"></a>Ejemplo de eliminación de entidad de servicio de AD FS

```powershell  
     Credential for accessing the ERCS PrivilegedEndpoint, typically domain\cloudadmin
     $Creds = Get-Credential

     # Creating a PSSession to the ERCS PrivilegedEndpoint
     $Session = New-PSSession -ComputerName <ERCS IP> -ConfigurationName PrivilegedEndpoint -Credential $Creds

     $UpdateServicePrincipal = Invoke-Command -Session $Session -ScriptBlock {Remove-GraphApplication -ApplicationIdentifier S-1-5-21-1634563105-1224503876-2692824315-2119}

     $Session | Remove-PSSession
```

## <a name="assign-a-role"></a>Asignar un rol

Para acceder a los recursos de la suscripción, debe asignarle a la aplicación un rol. Decida qué rol representa los permisos adecuados para la aplicación. Para obtener más información sobre los roles disponibles, vea [RBAC: Roles integrados](/azure/role-based-access-control/built-in-roles).

Puede establecer el ámbito en el nivel de suscripción, grupo de recursos o recurso. Los permisos se heredan en los niveles inferiores del ámbito. Por ejemplo, el hecho de agregar una aplicación al rol Lector para un grupo de recursos significa que esta puede leer el grupo de recursos y los recursos que contenga.

1. En el portal de Azure Stack, desplácese hasta el nivel de ámbito al que desea asignar la aplicación. Por ejemplo, para asignar un rol en el ámbito de suscripción, seleccione **Suscripciones**. También puede seleccionar un grupo de recursos o un recurso.

2. Seleccione la suscripción específica (grupo de recursos o recurso) a la que quiere asignar la aplicación.

     ![Seleccionar suscripción para la asignación](./media/azure-stack-create-service-principal/image16.png)

3. Seleccione **Access Control (IAM)** .

     ![Seleccionar acceso](./media/azure-stack-create-service-principal/image17.png)

4. Seleccione **Agregar asignación de roles**.

5. Seleccione el rol que quiere asignar a la aplicación.

6. Busque la aplicación y selecciónela.

7. Seleccione **Aceptar** para finalizar la asignación del rol. Verá la aplicación en la lista de usuarios asignados a un rol para ese ámbito.

Ahora que ha creado una entidad de servicio y le ha asignado un rol, puede empezar a usarla dentro de la aplicación para tener acceso a los recursos de Azure Stack.  

## <a name="next-steps"></a>Pasos siguientes

[Adición de usuarios a AD FS](azure-stack-add-users-adfs.md)  
[Administración de permisos de usuario](azure-stack-manage-permissions.md)  
[Documentación de Azure Active Directory](https://docs.microsoft.com/azure/active-directory)  
[Servicios de federación de Active Directory](https://docs.microsoft.com/windows-server/identity/active-directory-federation-services)
