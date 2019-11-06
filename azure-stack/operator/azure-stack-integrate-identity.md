---
title: Integración de la identidad de AD FS con el centro de recursos de Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo integrar el proveedor de identidades de AD FS de Azure Stack con AD FS del centro de datos.
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 05/10/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: 4d4ece9946d257bce5cf19876b940cf4d828872d
ms.sourcegitcommit: cc3534e09ad916bb693215d21ac13aed1d8a0dde
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2019
ms.locfileid: "73167172"
---
# <a name="integrate-ad-fs-identity-with-your-azure-stack-datacenter"></a>Integración de la identidad de AD FS con el centro de datos de Azure Stack

Puede implementar Azure Stack mediante Azure Active Directory (Azure AD) o con los Servicios de federación de Active Directory (AD FS) como proveedores de identidades. Deberá escoger antes de implementar Azure Stack. En un escenario conectado, puede elegir Azure AD o AD FS. Para un escenario desconectado, se admite solo AD FS. En este artículo se muestra cómo integrar AD FS de Azure Stack con AD FS del centro de datos.

> [!IMPORTANT]
> No se puede cambiar el proveedor de identidades sin volver a implementar la solución completa de Azure Stack.

## <a name="active-directory-federation-services-and-graph"></a>Servicios de federación de Active Directory y Graph

La implementación con AD FS permite autenticar las identidades de un bosque de Active Directory existente con los recursos de Azure Stack. Este bosque de Active Directory existente requiere una implementación de AD FS para permitir la creación de una confianza de federación de AD FS.

La autenticación es parte de la identidad. Para administrar el control de acceso basado en rol (RBAC) de Azure Stack, se debe configurar el componente de Graph. Cuando se delega el acceso a un recurso, el componente de Graph busca la cuenta de usuario en el bosque de Active Directory existente mediante el protocolo LDAP.

![Arquitectura de AD FS de Azure Stack](media/azure-stack-integrate-identity/Azure-Stack-ADFS-architecture.png)

La instancia existente de AD FS es el servicio de token de seguridad (STS) de la cuenta que envía notificaciones a AD FS de Azure Stack (el STS del recurso). En Azure Stack, la automatización crea la relación de confianza del proveedor de notificaciones con el punto de conexión de metadatos para la instancia de AD FS existente.

En la instancia de AD FS existente, se debe configurar una relación de confianza para usuario autenticado. Este paso no se realiza mediante la automatización y debe estar configurado por el operador. El punto de conexión VIP de Azure Stack para AD FS se puede crear con el patrón `https://adfs.<Region>.<ExternalFQDN>/`.

La configuración de la relación de confianza para usuario autenticado también requiere que configure las reglas de transformación de notificaciones que proceden de Microsoft.

Para la configuración de Graph, se debe proporcionar una cuenta de servicio que tenga permiso de lectura en la instancia de Active Directory existente. Esta cuenta es necesaria como entrada para que la automatización habilite escenarios RBAC.

Para el último paso, se configura un nuevo propietario para la suscripción del proveedor predeterminado. Esta cuenta tiene acceso completo a todos los recursos cuando se inicia sesión en el portal de administrador de Azure Stack.

Requisitos:

|Componente|Requisito|
|---------|---------|
|Grafo|Microsoft Active Directory 2012/2012 R2/2016|
|AD FS|Windows Server 2012/2012 R2/2016|

## <a name="setting-up-graph-integration"></a>Configuración de la integración de Graph

Graph solo permite realizar la integración con un único bosque de Active Directory. Si existen varios bosques, solamente el bosque especificado en la configuración se utilizará para capturar los usuarios y grupos.

Se requiere la siguiente información como entrada para los parámetros de automatización:

|Parámetro|Parámetro de hoja de cálculo de implementación|DESCRIPCIÓN|Ejemplo|
|---------|---------|---------|---------|
|`CustomADGlobalCatalog`|FQDN del bosque de AD FS|FQDN del bosque de Active Directory de destino con el que desea realizar la integración|Contoso.com|
|`CustomADAdminCredentials`| |Un usuario con permiso de lectura de LDAP|YOURDOMAIN\graphservice|

### <a name="configure-active-directory-sites"></a>Configuración de los sitios de Active Directory

Para las implementaciones de Active Directory con varios sitios, configure el sitio de Active Directory más cercano a la implementación de Azure Stack. La configuración evita que el servicio Graph de Azure Stack resuelva las consultas con un servidor de catálogo global desde un sitio remoto.

Agregue la subred de la [red de IP virtual pública](azure-stack-network.md#public-vip-network) de Azure Stack al sitio de Azure Active Directory más cercano a Azure Stack. Por ejemplo, supongamos que su instancia de Active Directory tiene dos sitios: Seattle y Redmond. Si Azure Stack está implementado en el sitio de Seattle, podría agregar la subred de la red de IP virtual pública de Azure Stack al sitio de Azure Active Directory para Seattle.

Para más información sobre los sitios de Active Directory, consulte [Diseño de la topología de sitio](https://docs.microsoft.com/windows-server/identity/ad-ds/plan/designing-the-site-topology).

> [!Note]  
> Si su instancia de Active Directory consta de un único sitio, puede omitir este paso. Si tiene una subred comodín configurada, valide que la subred de la red de VIP pública de Azure Stack no forma parte de dicha subred.

### <a name="create-user-account-in-the-existing-active-directory-optional"></a>Creación de una cuenta de usuario en Active Directory existente (opcional)

Si lo desea, puede crear una cuenta para el servicio Graph en la instancia de Active Directory existente. Realice este paso si ya no tiene una cuenta que desea utilizar.

1. En la instancia de Active Directory existente, cree la siguiente cuenta de usuario (recomendación):
   - **Nombre de usuario**: graphservice
   - **Contraseña**: Utilice una contraseña segura y configúrela para que no expire nunca.

   No se necesita ningún permiso especial ni pertenencia.

#### <a name="trigger-automation-to-configure-graph"></a>Automatización de un desencadenador para configurar un grafo

Para este procedimiento, use un equipo de la red del centro de datos que pueda comunicarse con el punto de conexión con privilegios de Azure Stack.

1. Abra una sesión de Windows PowerShell con privilegios elevados (ejecútela como administrador) y conéctese a la dirección IP del punto de conexión con privilegios. Use las credenciales para la autenticación con **CloudAdmin**.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ahora que se ha conectado al punto de conexión con privilegios, ejecute el siguiente comando: 

   ```powershell  
   Register-DirectoryService -CustomADGlobalCatalog contoso.com
   ```

   Cuando se le solicite, especifique la credencial para la cuenta de usuario que desea utilizar para el servicio Graph (por ejemplo, graphservice). La entrada para el cmdlet Register-DirectoryService debe ser el nombre de bosque o la raíz del dominio del bosque en lugar de cualquier otro dominio del bosque.

   > [!IMPORTANT]
   > Espere las credenciales emergentes (no se admite Get-Credential en el punto de conexión con privilegios) y escriba las credenciales de cuenta del servicio Graph.

3. El cmdlet **Register-DirectoryService** tiene parámetros opcionales que puede usar en determinados escenarios donde se produce un error en la validación de Active Directory existente. Cuando se ejecuta este cmdlet, valida que el dominio proporcionado es el dominio raíz, permite acceder a un servidor de catálogo global y la cuenta proporcionada concede acceso de lectura.

   |Parámetro|DESCRIPCIÓN|
   |---------|---------|
   |`-SkipRootDomainValidation`|Especifica que se debe usar un dominio secundario, en lugar del dominio raíz recomendado.|
   |`-Force`|Omite todas las comprobaciones de validación.|

#### <a name="graph-protocols-and-ports"></a>Puertos y protocolos de Graph

El servicio Graph de Azure Stack utiliza los siguientes protocolos y puertos para comunicarse con un servidor de catálogo global (GC) de escritura permitida y un centro de distribución de claves (KDC) que pueda procesar solicitudes de inicio de sesión en el bosque de Active Directory de destino.

El servicio Graph en Azure Stack usa los protocolos y puertos siguientes para comunicarse con la instancia de Active Directory de destino:

|type|Port|Protocolo|
|---------|---------|---------|
|LDAP|389|TCP y UDP|
|SSL de LDAP|636|TCP|
|GC DE LDAP|3268|TCP|
|SSL de GC de LDAP|3269|TCP|

## <a name="setting-up-ad-fs-integration-by-downloading-federation-metadata"></a>Configuración de la integración de AD FS mediante la descarga de metadatos de federación

Se requiere la siguiente información como entrada para los parámetros de automatización:

|Parámetro|Parámetro de hoja de cálculo de implementación|DESCRIPCIÓN|Ejemplo|
|---------|---------|---------|---------|
|CustomAdfsName|Nombre de proveedor de AD FS|Nombre del proveedor de notificaciones.<br>Aparece de este modo en la página de aterrizaje de AD FS.|Contoso|
|CustomAD<br>FSFederationMetadataEndpointUri|URI de metadatos de AD FS|Vínculo de metadatos de federación| https:\//ad01.contoso.com/federationmetadata/2007-06/federationmetadata.xml |
|SigningCertificateRevocationCheck|N/D|Parámetro opcional para omitir la comprobación de CRL|None|


### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automatización de desencadenador para configurar la relación de confianza de proveedor de notificaciones en Azure Stack

Para este procedimiento, use un equipo de la red del centro de datos que pueda comunicarse con el punto de conexión con privilegios de Azure Stack. Se espera que el certificado utilizado por la instancia de **AD FS del servicio de token de seguridad** de la cuenta sea de confianza para Azure Stack.

1. Abra una sesión de Windows PowerShell con privilegios elevados y conéctese al punto de conexión con privilegios.

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ahora que está conectado al punto de conexión con privilegios, ejecute el comando siguiente con los parámetros adecuados para su entorno:

   ```powershell  
   Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataEndpointUri https://win-SQOOJN70SGL.contoso.com/federationmetadata/2007-06/federationmetadata.xml
   ```

3. Ejecute el siguiente comando para actualizar el propietario de la suscripción del proveedor predeterminado, con los parámetros adecuados para su entorno:

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

## <a name="setting-up-ad-fs-integration-by-providing-federation-metadata-file"></a>Configuración de la integración de AD FS mediante el archivo de metadatos de federación

A partir de la versión 1807, utilice este método si se cumple alguna de las condiciones siguientes:

- La cadena de certificados es diferente para AD FS, en comparación con todos los demás puntos de conexión de Azure Stack.
- No hay ninguna conectividad de red para el servidor de AD FS existente desde la instancia de AD FS de Azure Stack.

Se requiere la siguiente información como entrada para los parámetros de automatización:


|Parámetro|DESCRIPCIÓN|Ejemplo|
|---------|---------|---------|
|CustomAdfsName|Nombre del proveedor de notificaciones. Aparece de este modo en la página de aterrizaje de AD FS.|Contoso|
|CustomADFSFederationMetadataFileContent|Contenido de los metadatos.|$using:federationMetadataFileContent|

### <a name="create-federation-metadata-file"></a>Creación de un archivo de metadatos de federación

Para el siguiente procedimiento, debe usar un equipo que tenga conectividad de red a la implementación de AD FS existente, que se convierte en el servicio de token de seguridad de la cuenta. También deben instalarse los certificados necesarios.

1. Abra una sesión de Windows PowerShell con privilegios elevados y ejecute el comando siguiente con los parámetros adecuados para su entorno:

   ```powershell  
    $url = "https://win-SQOOJN70SGL.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml"
    $webclient = New-Object System.Net.WebClient
    $webclient.Encoding = [System.Text.Encoding]::UTF8
    $metadataAsString = $webclient.DownloadString($url)
    Set-Content -Path c:\metadata.xml -Encoding UTF8 -Value $metadataAsString
   ```

2. Copie el archivo de metadatos en un equipo que pueda comunicarse con el punto de conexión con privilegios.

### <a name="trigger-automation-to-configure-claims-provider-trust-in-azure-stack"></a>Automatización de desencadenador para configurar la relación de confianza de proveedor de notificaciones en Azure Stack

Para este procedimiento, utilice un equipo que pueda comunicarse con el punto de conexión con privilegios en Azure Stack y que tenga acceso al archivo de metadatos que creó en el paso anterior.

1. Abra una sesión de Windows PowerShell con privilegios elevados y conéctese al punto de conexión con privilegios.

   ```powershell  
   $federationMetadataFileContent = get-content c:\metadata.xml
   $creds=Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Ahora que está conectado al punto de conexión con privilegios, ejecute el comando siguiente con los parámetros adecuados para su entorno:

    ```powershell
    Register-CustomAdfs -CustomAdfsName Contoso -CustomADFSFederationMetadataFileContent $using:federationMetadataFileContent
    ```

3. Ejecute el siguiente comando para actualizar el propietario de la suscripción del proveedor predeterminado. Use los parámetros apropiados para su entorno.

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "administrator@contoso.com"
   ```

   > [!Note]  
   > Al rotar el certificado en la versión existente de AD FS (servicio de token de seguridad de la cuenta) debe configurar la integración de AD FS nuevamente. Debe configurar la integración incluso si el punto de conexión de metadatos es accesible o se ha configurado mediante el archivo de metadatos.

## <a name="configure-relying-party-on-existing-ad-fs-deployment-account-sts"></a>Configuración del usuario de confianza en la implementación de AD FS existente (STS de cuenta)

Microsoft proporciona un script que configura la relación de confianza para usuario autenticado, incluidas las reglas de transformación de notificaciones. El uso del script es opcional, ya que se pueden ejecutar los comandos manualmente.

Puede descargar el script del asistente desde las [herramientas de Azure Stack](https://github.com/Azure/AzureStack-Tools/tree/vnext/DatacenterIntegration/Identity) de GitHub.

Si decide ejecutar manualmente los comandos, siga estos pasos:

1. Copie el siguiente contenido en un archivo .txt (por ejemplo, guardado como c:\ClaimRules.txt) en la instancia de AD FS o como miembro de la granja de servidores del centro de datos:

   ```text
   @RuleTemplate = "LdapClaims"
   @RuleName = "Name claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "UPN claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"), query = ";userPrincipalName;{0}", param = c.Value);

   @RuleTemplate = "LdapClaims"
   @RuleName = "ObjectID claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/primarysid"]
   => issue(Type = "http://schemas.microsoft.com/identity/claims/objectidentifier", Issuer = c.Issuer, OriginalIssuer = c.OriginalIssuer, Value = c.Value, ValueType = c.ValueType);

   @RuleName = "Family Name and Given claim"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname", Issuer == "AD AUTHORITY"]
   => issue(store = "Active Directory", types = ("http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname", "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname"), query = ";sn,givenName;{0}", param = c.Value);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all Group SID claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/groupsid", Issuer =~ "^(AD AUTHORITY|SELF AUTHORITY|LOCAL AUTHORITY)$"]
   => issue(claim = c);

   @RuleTemplate = "PassThroughClaims"
   @RuleName = "Pass through all windows account name claims"
   c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
   => issue(claim = c);
   ```

2. Confirme que está habilitada la autenticación basada en Windows Forms para extranet e intranet. Compruebe si ya está habilitada mediante la ejecución de este cmdlet:

   ```powershell  
   Get-AdfsAuthenticationProvider | where-object { $_.name -eq "FormsAuthentication" } | select Name, AllowedForPrimaryExtranet, AllowedForPrimaryIntranet
   ```

    > [!Note]  
    > Es posible que las cadenas del agente de usuario compatibles con la autenticación integrada de Windows (WIA) estén obsoletas para la implementación de AD FS y que sea necesario actualizarlas para que sean compatibles con los clientes más recientes. Puede leer más sobre cómo actualizar las cadenas del agente de usuario compatibles con WIA en el artículo [Configuración de la autenticación basada en formularios de la intranet para dispositivos que no admiten WIA](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-intranet-forms-based-authentication-for-devices-that-do-not-support-wia).<br><br>Para conocer los pasos para habilitar la directiva de autenticación basada en formularios, consulte [Configuración de directivas de autenticación](https://docs.microsoft.com/windows-server/identity/ad-fs/operations/configure-authentication-policies).

3. Para agregar la relación de confianza para usuario autenticado, ejecute el siguiente comando de Windows PowerShell en la instancia de AD FS o un miembro de la granja de servidores. Asegúrese de actualizar el punto de conexión de AD FS y seleccione el archivo creado en el paso 1.

   **Para AD FS 2016**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -AccessControlPolicyName "Permit everyone" -TokenLifeTime 1440
   ```

   **Para AD FS 2012/2012 R2**

   ```powershell  
   Add-ADFSRelyingPartyTrust -Name AzureStack -MetadataUrl "https://YourAzureStackADFSEndpoint/FederationMetadata/2007-06/FederationMetadata.xml" -IssuanceTransformRulesFile "C:\ClaimIssuanceRules.txt" -AutoUpdateEnabled:$true -MonitoringEnabled:$true -enabled:$true -TokenLifeTime 1440
   ```

   > [!IMPORTANT]  
   > Debe usar el complemento MMC de AD FS para configurar las reglas de autorización de emisión cuando se usa AD FS de Windows Server 2012 o 2012 R2.

4. Cuando se utiliza el explorador Internet Explorer o Microsoft Edge para tener acceso a Azure Stack, debe omitir los enlaces de token. En caso contrario, se producirá un error al intentar iniciar sesión. En la instancia de AD FS o en un miembro de la granja de servidores, ejecute el siguiente comando:

   > [!note]  
   > Este paso no es aplicable cuando se usa AD FS con Windows Server 2012 o 2012 R2. En este caso, no pasa nada si omite este comando y continúa con la integración.

   ```powershell  
   Set-AdfsProperties -IgnoreTokenBinding $true
   ```

## <a name="spn-creation"></a>Creación de SPN

Hay muchos escenarios que requieren el uso de un nombre de entidad de seguridad de servicio (SPN) para la autenticación. A continuación se muestran algunos ejemplos.

- Uso de la CLI con la implementación de AD FS de Azure Stack
- Módulo de administración de System Center para Azure Stack cuando se implementa con AD FS
- Proveedores de recursos de Azure Stack cuando se implementan con AD FS
- Varias aplicaciones.
- Se requiere un inicio de sesión no interactivo.

> [!Important]  
> AD FS solo admite sesiones de inicio de sesión interactivo. Si necesita un inicio de sesión no interactivo para un escenario automatizado, debe utilizar un nombre de entidad de seguridad de servicio.

Para más información sobre cómo crear un nombre de entidad de seguridad de servicio, consulte la sección [Creación de una entidad de servicio para AD FS](azure-stack-create-service-principals.md).


## <a name="troubleshooting"></a>solución de problemas

### <a name="configuration-rollback"></a>Reversión de la configuración

Si se produce un error que deja el entorno en un estado donde ya no se puede realizar la autenticación, está disponible una opción de reversión.

1. Abra una sesión de Windows PowerShell con privilegios elevados y ejecute el siguiente comando:

   ```powershell  
   $creds = Get-Credential
   Enter-PSSession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Después, ejecute el siguiente cmdlet:

   ```powershell  
   Reset-DatacenterIntegrationConfiguration
   ```

   Tras ejecutar la acción de reversión, todos los cambios de configuración se revierten. Solo es posible la autenticación con el usuario **CloudAdmin** integrado.

   > [!IMPORTANT]
   > Debe configurar el propietario original de la suscripción del proveedor predeterminado.

   ```powershell  
   Set-ServiceAdminOwner -ServiceAdminOwnerUpn "azurestackadmin@[Internal Domain]"
   ```

### <a name="collecting-additional-logs"></a>Recopilación de registros adicionales

Si se produce un error en cualquiera de los cmdlets, puede recopilar registros adicionales mediante el cmdlet `Get-Azurestacklogs`.

1. Abra una sesión de Windows PowerShell con privilegios elevados y ejecute el siguiente comando:

   ```powershell  
   $creds = Get-Credential
   Enter-pssession -ComputerName <IP Address of ERCS> -ConfigurationName PrivilegedEndpoint -Credential $creds
   ```

2. Después, ejecute el siguiente cmdlet:

   ```powershell  
   Get-AzureStackLog -OutputPath \\myworkstation\AzureStackLogs -FilterByRole ECE
   ```

## <a name="next-steps"></a>Pasos siguientes

[Integración de soluciones de supervisión externa](azure-stack-integrate-monitor.md)
