---
title: Girar secretos
titleSuffix: Azure Stack Hub
description: Aprenda a cambiar los secretos en Azure Stack Hub.
author: BryanLa
ms.topic: how-to
ms.date: 06/29/2020
ms.reviewer: ppacent
ms.author: bryanla
ms.lastreviewed: 08/15/2020
monikerRange: '>=azs-1803'
ms.openlocfilehash: aca163df1026193933ffb9d09dbdf4a854638a75
ms.sourcegitcommit: 362081a8c19e7674c3029c8a44d7ddbe2deb247b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "91899812"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Cambio de secretos en Azure Stack Hub

*Estas instrucciones se aplican exclusivamente a los sistemas integrados en Azure Stack Hub con la versión 1803 y posteriores. No intente la rotación de secretos en versiones anteriores a la 1803*

En este artículo se proporcionan instrucciones y scripts de PowerShell para la rotación de secretos, para ayudar a mantener una comunicación segura con los recursos y servicios de la infraestructura de Azure Stack Hub. 

## <a name="overview"></a>Información general

Azure Stack Hub usa secretos para mantener una comunicación segura con los recursos y servicios de la infraestructura. Para mantener la integridad de la infraestructura de Azure Stack Hub, los operadores necesitan la posibilidad de rotar los secretos con una frecuencia que se ajuste a los requisitos de seguridad de la organización.

### <a name="internal-vs-external-secrets"></a>Secretos internos y externos

A partir de la versión 1811, la rotación de secretos se ha separado para los certificados internos y externos:

- **Secretos internos**: Certificados, contraseñas, cadenas seguras y claves que se utilizan en la infraestructura de Azure Stack Hub sin la intervención del operador de este servicio.

- **Secretos externos**: Certificados de servicio de infraestructura para servicios de uso externo que proporciona el operador de Azure Stack Hub. Los secretos externos incluyen los certificados de los siguientes servicios:

    - Administrator Portal
    - Public Portal
    - Administrator Azure Resource Manager
    - Global Azure Resource Manager
    - Administrator Key Vault
    - Key Vault
    - Host de extensiones de administración
    - ACS (incluido Blob Storage, Table Storage y Queue Storage)
    - ADFS*
    - Graph*
    
    \* Solo es aplicable si el proveedor de identidades del entorno es los Servicios de federación de Active Directory (AD FS).

> [!Important]
> El administrador actualiza manualmente todas las demás claves y cadenas seguras. Esto incluye las contraseñas de las cuentas de usuario y de administrador, las [contraseñas y permisos del conmutador de red](azure-stack-customer-defined.md), y las credenciales del controlador de administración de placa base (BMC), que se [tratarán más adelante en este artículo](#update-the-bmc-credential). 
>
>Además, en este artículo no se trata la rotación de secretos de los proveedores de recursos de valor agregado. Para rotar esos secretos, consulte los siguientes artículos en su lugar:
>
> - [Rotación de secretos y certificados de App Service en Azure Stack Hub](app-service-rotate-certificates.md)
> - [Proveedor de recursos MySQL: Rotación de secretos](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation)
> - [Proveedor de recursos SQL: Rotación de secretos](azure-stack-sql-resource-provider-maintain.md#secrets-rotation)

### <a name="expiration-alerts"></a>Alertas de expiración

Cuando faltan menos de 30 días para la expiración de los secretos, se generan las siguientes alertas en el portal de administración:

- Expiración de contraseña de cuenta de servicio pendiente
- Expiración de certificado interno pendiente
- Expiración de certificado externo pendiente

La finalización de los pasos de rotación de secretos que se indican en las siguientes secciones resolverá estas alertas.

> [!Note]
> Los entornos de Azure Stack Hub de las versiones anteriores a la 1811 pueden ver alertas sobre el vencimiento de certificados o secretos internos pendientes. Estas alertas son inexactas y deben omitirse sin ejecutar el cambio de secretos interno. Las alertas de expiración de secretos internos inexactos son un problema conocido resuelto en la versión 1811. Los secretos internos no expirarán a menos que el entorno haya estado activo durante dos años.

### <a name="external-certificates-from-a-new-certificate-authority"></a>Certificados externos de una nueva entidad de certificación

Azure Stack Hub permite cambiar los secretos con certificados externos de una nueva entidad de certificación en los contextos siguientes:

|CA del certificado instalado|CA a la que cambiar|Compatible|Versiones de Azure Stack Hub compatibles|
|-----|-----|-----|-----|
|De autofirmado|A empresarial|Compatible|1903 y posteriores|
|De autofirmado|A autofirmado|No compatible||
|De autofirmado|A pública<sup>*</sup>|Compatible|1803 y posterior|
|De empresarial|A empresarial|Compatible. Desde 1803-1903: compatible siempre y cuando los clientes usen la MISMA entidad de certificación empresarial utilizada en la implementación|1803 y posterior|
|De empresarial|A autofirmado|No compatible||
|De empresarial|A pública<sup>*</sup>|Compatible|1803 y posterior|
|De pública<sup>*</sup>|A empresarial|Compatible|1903 y posteriores|
|De pública<sup>*</sup>|A autofirmado|No compatible||
|De pública<sup>*</sup>|A pública<sup>*</sup>|Compatible|1803 y posterior|

<sup>*</sup>Indica que las entidades de certificación públicas forman parte del programa de certificados raíz de confianza de Windows. Puede ver la lista completa en el artículo [Lista de participantes: Programa de certificados raíz de confianza de Microsoft](/security/trusted-root/participants-list).

## <a name="prerequisites"></a>Prerrequisitos

Para la rotación de secretos internos y externos:

1. Se recomienda encarecidamente que actualice primero la instancia de Azure Stack Hub a la versión más reciente.

    ::: moniker range="<azs-1811"  
    >[!IMPORTANT]
    > Para versiones anteriores a la 1811:
    > - Si ya se ha realizado la rotación de secretos, debe actualizar a la versión 1811 o posterior antes de ejecutar la rotación de secretos de nuevo. El cambio de secretos se debe ejecutar utilizando el [punto de conexión con privilegios](azure-stack-privileged-endpoint.md) y requiere credenciales de operador de Azure Stack Hub. Si no sabe si la rotación de secretos se ha ejecutado en su entorno, actualice a la versión 1811 antes de realizarla.
    > - No es necesario rotar los secretos para agregar certificados de host de extensiones. Debe seguir las instrucciones del artículo [Preparación de un host de extensiones de Azure Stack Hub de extensiones](azure-stack-extension-host-prepare.md) para agregar certificados de un host de extensiones.
    ::: moniker-end

2. Notifique a los usuarios cualquier operación de mantenimiento planeado. Programe ventanas de mantenimiento normales, en la medida de lo posible, durante horas no laborables. Las operaciones de mantenimiento pueden afectar tanto a las cargas de trabajo del usuario como a las operaciones del portal.

3. Los operadores pueden notar que las alertas se abren y cierran automáticamente durante la rotación de secretos. Este comportamiento es el esperado y puede hacerse caso omiso de las alertas. Los operadores pueden comprobar la validez de estas alertas mediante el [cmdlet Test-AzureStack de PowerShell](azure-stack-diagnostic-test.md). En el caso de los operadores que usan System Center Operations Manager para supervisar los sistemas de Azure Stack Hub, si ponen el sistema en modo de mantenimiento, evitarán que estas alertas lleguen a sus sistemas ITSM, pero se seguirán generando alertas si no se puede acceder al sistema de Azure Stack Hub.

En el caso de la rotación de secretos externos, complete estos requisitos previos adicionales:

1. Ejecute el cmdlet **[`Test-AzureStack`](azure-stack-diagnostic-test.md)** de PowerShell con el parámetro `-group SecretRotationReadiness`, para confirmar que todas las salidas de prueba son correctas antes de rotar los secretos.
2. Prepare un nuevo conjunto de certificados externos de reemplazo:
    - El nuevo conjunto debe coincidir con las especificaciones de certificado que se describen en [Requisitos de certificados de infraestructura de clave pública de Azure Stack Hub](azure-stack-pki-certs.md). 
    - Puede generar una solicitud de firma de certificados (CSR) para su envío a la entidad de certificación mediante los pasos que se describen en [Generación de solicitudes de firma de certificados](azure-stack-get-pki-certs.md) y prepararlos para utilizarlos en su entorno de Azure Stack Hub siguiendo los pasos de [Preparación de certificados PKI](azure-stack-prepare-pki-certs.md). 
    - No olvide validar los certificados que prepare con los pasos descritos en [Validación de certificados PKI](azure-stack-validate-pki-certs.md).
    - Asegúrese de que no haya ningún carácter especial en la contraseña como `*` o `)`.
    - Asegúrese de que el cifrado PFX es **TripleDES-SHA1**. Si surge un problema, consulte [Corrección de problemas comunes con certificados de PKI en Azure Stack Hub](azure-stack-remediate-certs.md#pfx-encryption).
3. Guarde una copia de seguridad de los certificados usados para el cambio en una ubicación segura. Si se ejecuta el cambio y, después, se produce un error, reemplace los certificados del recurso compartido de archivos por las copias de seguridad antes de volver a ejecutar el cambio. Conserve las copias de seguridad en la ubicación segura.
4. Cree un recurso compartido de archivos al que pueda acceder desde las máquinas virtuales de ERCS. El recurso compartido de archivos debe ser de lectura y escritura para la identidad **CloudAdmin**.
5. Abra una consola de PowerShell ISE desde un equipo que tenga acceso al recurso compartido de archivos. Vaya al recurso compartido de archivos donde se crean los directorios para colocar los certificados externos.
6. Descargue **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** en un recurso compartido de archivos de red al que se pueda acceder durante la rotación y ejecute el script. El script creará una estructura de carpetas que se parece a ***.\Certificates\AAD*** o ***.\Certificates\ADFS***, en función del proveedor de identidades. La estructura de carpetas debe empezar por una carpeta **\\Certificates**, seguida SOLO de una carpeta **\\AAD** o **\\ADFS**. Todos los subdirectorios adicionales estarán incluidos en la estructura anterior. Por ejemplo:
    - Recurso compartido de archivos = **\\\\\<IPAddress>\\\<ShareName>**
    - Carpeta raíz del certificado para el proveedor de Azure AD = **\\Certificates\AAD**
    - Ruta de acceso completa = **\\\\\<IPAddress>\\\<ShareName>\Certificates\AAD**

    > [!IMPORTANT]
    > Cuando ejecute `Start-SecretRotation` más adelante, se validará la estructura de carpetas. Una estructura de carpetas que no sea compatible producirá el siguiente error:
    >
    > ```powershell
    > Cannot bind argument to parameter 'Path' because it is null.
    > + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
    > + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
    > + PSComputerName        : xxx.xxx.xxx.xxx
    > ```

7. Copie el nuevo conjunto de certificados externos de reemplazo creados en el paso 2, en el directorio **\Certificates\\\<IdentityProvider>** que se creó en el paso 6. Asegúrese de seguir el formato `cert.<regionName>.<externalFQDN>` para \<CertName\>. 

    Este es un ejemplo de la estructura de carpetas para el proveedor de identidades de Azure AD:
    ```powershell
        <ShareName>
            │
            └───Certificates
                  └───AAD
                      ├───ACSBlob
                      │       <CertName>.pfx
                      │
                      ├───ACSQueue
                      │       <CertName>.pfx
                      │
                      ├───ACSTable
                      │       <CertName>.pfx
                      │
                      ├───Admin Extension Host
                      │       <CertName>.pfx
                      │
                      ├───Admin Portal
                      │       <CertName>.pfx
                      │
                      ├───ARM Admin
                      │       <CertName>.pfx
                      │
                      ├───ARM Public
                      │       <CertName>.pfx
                      │
                      ├───KeyVault
                      │       <CertName>.pfx
                      │
                      ├───KeyVaultInternal
                      │       <CertName>.pfx
                      │
                      ├───Public Extension Host
                      │       <CertName>.pfx
                      │
                      └───Public Portal
                              <CertName>.pfx

    ```

## <a name="rotate-external-secrets"></a>Rotación de secretos externos

Realice los pasos siguientes para rotar los secretos externos:

1. Use el siguiente script de PowerShell para rotar los secretos. El script requiere acceso a una sesión de punto de conexión con privilegios (PEP). El acceso a este punto de conexión se realiza mediante una sesión remota de PowerShell en la máquina virtual que lo hospeda. Si va a usar un sistema integrado, hay tres instancias del PEP, cada una de las cuales se ejecuta en una máquina virtual (Prefix-ERCS01, Prefix-ERCS02 o Prefix-ERCS03) en diferentes hosts. Si utiliza ASDK, esta máquina virtual se denomina AzS-ERCS01. Actualice los valores `<placeholder>` antes de ejecutar:

    ```powershell
    # Create a PEP Session
    winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
    $PEPCreds = Get-Credential
    $PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS_Machine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    # Run Secret Rotation
    $CertPassword = ConvertTo-SecureString "<Cert_Password>" -AsPlainText -Force
    $CertShareCreds = Get-Credential
    $CertSharePath = "<Network_Path_Of_CertShare>"
    Invoke-Command -Session $PEPSession -ScriptBlock {
        Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
    }
    Remove-PSSession -Session $PEPSession
    ```

    Este script lleva a cabo los pasos siguientes:

    - Cree una sesión de PowerShell con el [punto de conexión con privilegios](azure-stack-privileged-endpoint.md) mediante la cuenta **CloudAdmin** y almacene la sesión como una variable. Esta variable se usa como parámetro en el paso siguiente. 

    - Ejecuta [Invoke-Command](/powershell/module/microsoft.powershell.core/Invoke-Command) para pasar la variable de la sesión de PEP como parámetro `-Session`.

    - Ejecuta `Start-SecretRotation` en la sesión de PEP, con los parámetros siguientes:
        - `-PfxFilesPath`: la ruta de acceso de red al directorio de certificados que creó anteriormente.  
        - `-PathAccessCredential`: el objeto PSCredential con las credenciales del recurso compartido.
        - `-CertificatePassword`: Una cadena segura de la contraseña usada para todos los archivos de certificados pfx creados.

2. El cambio de secretos externos tarda aproximadamente una hora. Después de la finalización correcta del proceso, la consola mostrará `ActionPlanInstanceID ... CurrentStatus: Completed`, seguido de un estado `DONE`. Elimine los certificados del recurso compartido creado en la sección de requisitos previos y almacénelos en su ubicación segura de copia de seguridad.

    > [!Note]
    > Si se produce un error en el cambio de secretos, siga las instrucciones del mensaje de error y vuelva a ejecutar `Start-SecretRotation` con el parámetro `-ReRun`.
    >
    >```powershell
    >Start-SecretRotation -ReRun
    >```  
    >
    >Póngase en contacto con el soporte técnico si el cambio de secretos falla en reiteradas ocasiones.

## <a name="rotate-internal-secrets"></a>Rotación de secretos internos

La rotación de secretos internos solo es necesaria si sospecha que alguno de ellos se ha puesto en peligro o si ha recibido una alerta de expiración. En las versiones anteriores a la 1811 puede ver alertas para las expiraciones de certificados o secretos internos pendientes. Estas alertas no son correctas y deben ignorarse, y son un problema conocido resuelto en la versión 1811. Los secretos internos no expirarán a menos que el entorno haya estado activo durante dos años.

Consulte el script de PowerShell en el paso 2 de [Rotación de secretos externos](#rotate-external-secrets). El script proporciona un ejemplo que se puede adaptar a la rotación de secretos internos, realizando algunos cambios para ejecutar los pasos siguientes:

1. En la sección "Run Secret Rotation" (Ejecutar rotación de secreto), agregue el parámetro `-Internal` al cmdlet [Start-SecretRotation](../reference/pep-2002/start-secretrotation.md), por ejemplo:

    ```powershell
    # Run Secret Rotation
    ...
    Invoke-Command -Session $PEPSession -ScriptBlock {
        Start-SecretRotation -Internal
    }
    ...
    ```

    ::: moniker range="<azs-1811"
    > [!Note]
    > Las versiones anteriores a la 1811 no requieren la marca `-Internal`. 
    ::: moniker-end

3. Después de la finalización correcta del proceso, la consola mostrará `ActionPlanInstanceID ... CurrentStatus: Completed`, seguido de un estado `DONE`.

    > [!Note]
    > Si se produce un error en el cambio de secretos, siga las instrucciones del mensaje de error y vuelva a ejecutar `Start-SecretRotation` con los parámetros `-Internal` y `-ReRun`.  
    >
    >```powershell
    >Start-SecretRotation -Internal -ReRun
    >```
    >
    > Póngase en contacto con el soporte técnico si el cambio de secretos falla en reiteradas ocasiones.

## <a name="update-the-bmc-credential"></a>Actualización de la credencial de BMC

El controlador de administración de placa base supervisa el estado físico de sus servidores. Consulte con el proveedor de hardware del fabricante de equipos originales (OEM) para obtener instrucciones sobre cómo actualizar el nombre y la contraseña de la cuenta de usuario del BMC.

>[!NOTE]
> El OEM puede proporcionar aplicaciones de administración adicionales. La actualización del nombre de usuario o la contraseña para otras aplicaciones de administración no tiene ningún efecto en el nombre de usuario o la contraseña del BMC. 

::: moniker range="<azs-1910"
1. Actualice el BMC de los servidores físicos de Azure Stack Hub siguiendo las instrucciones del OEM. El nombre de usuario y la contraseña para cada BMC de su entorno deben ser los mismos. Los nombres de usuario de BMC no pueden superar los 16 caracteres.
::: moniker-end

::: moniker range=">=azs-1910"
1. Ya no es necesario que actualice primero las credenciales del BMC en los servidores físicos de Azure Stack Hub siguiendo las instrucciones del OEM. El nombre de usuario y la contraseña para cada BMC de su entorno deben ser los mismos y no pueden superar los 16 caracteres. 
::: moniker-end

2. Abra un punto de conexión con privilegios en sesiones de Azure Stack Hub. Para obtener instrucciones, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](azure-stack-privileged-endpoint.md). 

3. Después de abrir una sesión de punto de conexión con privilegios, ejecute uno de los siguientes scripts de PowerShell que usan Invoke-Command para ejecutar Set-BmcCredential. Si usa el parámetro opcional -BypassBMCUpdate con Set-BMCCredential, las credenciales del BMC no se actualizarán. Solo se actualiza el almacén de datos de la instancia interna de Azure Stack Hub. Pase la variable de la sesión del punto de conexión con privilegios como parámetro.

    Este es un script de ejemplo de PowerShell que solicitará el nombre de usuario y la contraseña: 

    ```powershell
    # Interactive Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPCreds = Get-Credential "<Domain>\CloudAdmin" -Message "PEP Credentials"
    $NewBmcPwd = Read-Host -Prompt "Enter New BMC password" -AsSecureString
    $NewBmcUser = Read-Host -Prompt "Enter New BMC user name"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

    También puede codificar el nombre de usuario y la contraseña en las variables, lo cual puede ser menos seguro:

    ```powershell
    # Static Version
    $PEPIp = "<Privileged Endpoint IP or Name>" # You can also use the machine name instead of IP here.
    $PEPUser = "<Privileged Endpoint user for example Domain\CloudAdmin>"
    $PEPPwd = ConvertTo-SecureString "<Privileged Endpoint Password>" -AsPlainText -Force
    $PEPCreds = New-Object System.Management.Automation.PSCredential ($PEPUser, $PEPPwd)
    $NewBmcPwd = ConvertTo-SecureString "<New BMC Password>" -AsPlainText -Force
    $NewBmcUser = "<New BMC User name>"

    $PEPSession = New-PSSession -ComputerName $PEPIp -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

    Invoke-Command -Session $PEPSession -ScriptBlock {
        # Parameter BmcPassword is mandatory, while the BmcUser parameter is optional.
        Set-BmcCredential -BmcPassword $using:NewBmcPwd -BmcUser $using:NewBmcUser
    }
    Remove-PSSession -Session $PEPSession
    ```

## <a name="reference-start-secretrotation-cmdlet"></a>Referencia: Cmdlet Start-SecretRotation

El [cmdlet Start-SecretRotation](../reference/pep-2002/start-secretrotation.md) permite rotar los secretos de la infraestructura de un sistema de Azure Stack Hub. Este cmdlet solo se puede ejecutar en el punto de conexión con privilegios de Azure Stack Hub, mediante un bloque de script `Invoke-Command` que pasa la sesión de PEP en el parámetro `-Session`. De forma predeterminada, solo se cambian los certificados de todos los puntos de conexión de la infraestructura de red externa.

| Parámetro | Tipo | Obligatorio | Posición | Valor predeterminado | Descripción |
|--|--|--|--|--|--|
| `PfxFilesPath` | String  | False  | con nombre  | None  | La ruta de acceso del recurso compartido de archivos al directorio **\Certificates** que contiene todos los certificados del punto de conexión de la red externa. Solo se necesita al rotar secretos externos. El directorio final debe ser **\Certificates**. |
| `CertificatePassword` | SecureString | False  | con nombre  | None  | La contraseña de todos los certificados que se proporcionan en -PfXFilesPath. Valor obligatorio si se proporciona PfxFilesPath al cambiar secretos externos. |
| `Internal` | String | False | con nombre | None | La marca Internal se debe utilizar cada vez que un operador de Azure Stack Hub quiera cambiar los secretos de infraestructura interna. |
| `PathAccessCredential` | PSCredential | False  | con nombre  | None  | La credencial de PowerShell para el recurso compartido de archivos al directorio **\Certificates** que contiene todos los certificados del punto de conexión de la red externa. Solo se necesita al rotar secretos externos.  |
| `ReRun` | SwitchParameter | False  | con nombre  | None  | Se debe usar en cualquier momento en que se vuelva a intentar la rotación de secretos después de un intento fallido. |

### <a name="syntax"></a>Sintaxis

#### <a name="for-external-secret-rotation"></a>Para el cambio de secretos externos

```powershell
Start-SecretRotation [-PfxFilesPath <string>] [-PathAccessCredential <PSCredential>] [-CertificatePassword <SecureString>]  
```

#### <a name="for-internal-secret-rotation"></a>Para el cambio de secretos internos

```powershell
Start-SecretRotation [-Internal]  
```

#### <a name="for-external-secret-rotation-rerun"></a>Para volver a ejecutar el cambio de secretos externos

```powershell
Start-SecretRotation [-ReRun]
```

#### <a name="for-internal-secret-rotation-rerun"></a>Para volver a ejecutar el cambio de secretos internos

```powershell
Start-SecretRotation [-ReRun] [-Internal]
```

### <a name="examples"></a>Ejemplos

#### <a name="rotate-only-internal-infrastructure-secrets"></a>Cambio de solo los secretos de infraestructura interna

Este comando debe ejecutarse utilizando el [punto de conexión con privilegios del entorno](azure-stack-privileged-endpoint.md) de Azure Stack Hub.

```powershell
PS C:\> Start-SecretRotation -Internal
```

Este comando cambia todos los secretos de la infraestructura expuestos a la red interna de Azure Stack Hub.

#### <a name="rotate-only-external-infrastructure-secrets"></a>Cambio de solo los secretos de infraestructura externa  

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {  
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Este comando cambia los certificados TLS que se usan con los puntos de conexión de la infraestructura de red externa de Azure Stack Hub.

::: moniker range="<azs-1811"
#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Cambio de secretos de la infraestructura interna y externa (solo **anterior a 1811**)

> [!IMPORTANT]
> Este comando solo se aplica a las **versiones de Azure Stack Hub anteriores a la 1811**, ya que el cambio ahora es diferente para los certificados internos y para los certificados externos.
>
> **Desde las versiones posteriores a *1811* ya no se pueden cambiar los certificados internos y externos.**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IP_address_of_ERCS>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IP_address_of_ERCS> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Create Credentials for the fileshare
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
# Run Secret Rotation
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

Este comando rota los secretos de la infraestructura expuestos a la red interna de Azure Stack Hub y los certificados TLS que se usan con los puntos de conexión de la infraestructura de red externa de Azure Stack Hub. Start-SecretRotation cambia todos los secretos generados por Stack, y dado que se han proporcionado certificados, los certificados de punto de conexión externos también se cambiarán.  
::: moniker-end

## <a name="next-steps"></a>Pasos siguientes

[Más información acerca de la seguridad de Azure Stack](azure-stack-security-foundations.md)