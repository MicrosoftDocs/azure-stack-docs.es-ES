---
title: Girar secretos
titleSuffix: Azure Stack Hub
description: Aprenda a cambiar los secretos en Azure Stack Hub.
author: BryanLa
ms.topic: how-to
ms.date: 01/07/2021
ms.reviewer: fiseraci
ms.author: bryanla
ms.lastreviewed: 01/07/2021
monikerRange: '>=azs-1803'
ms.openlocfilehash: ec65268a76a8616d5fea213d6c4f0551a5b5ba38
ms.sourcegitcommit: a90b146769279ffbdb09c68ca0506875a867e177
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98123704"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Cambio de secretos en Azure Stack Hub

En este artículo se proporcionan instrucciones para la rotación de secretos, a fin de ayudar a mantener una comunicación segura con los recursos y servicios de la infraestructura de Azure Stack Hub.

## <a name="overview"></a>Información general

Azure Stack Hub usa secretos para mantener una comunicación segura con los recursos y servicios de la infraestructura. Para mantener la integridad de la infraestructura de Azure Stack Hub, los operadores necesitan la posibilidad de rotar los secretos con una frecuencia que se ajuste a los requisitos de seguridad de la organización.

Cuando se aproxime la expiración de los secretos, se generan las siguientes alertas en el portal de administración. La finalización de la rotación de secretos resolverá estas alertas:

- Expiración de contraseña de cuenta de servicio pendiente
- Expiración de certificado interno pendiente
- Expiración de certificado externo pendiente

> [!WARNING]
> Hay dos fases de alertas que se desencadenan en el portal de administración antes de la expiración:
> - 90 días antes de la expiración, se genera una alerta de advertencia.
> - 30 días antes de la expiración, se genera una alerta crítica. 
>
> **La rotación de secretos es un paso *crítico* que debe completar si recibe estas notificaciones. Si no lo hace, puede que se pierdan cargas de trabajo y que deba volver a implementar Azure Stack Hub por su cuenta.**

Para obtener más información sobre la supervisión y la corrección de alertas, consulte [Supervisión de estado y alertas en Azure Stack Hub](azure-stack-monitor-health.md).

::: moniker range="<azs-1811"  
> [!NOTE]
> Los entornos de Azure Stack Hub de las versiones anteriores a la 1811 pueden ver alertas sobre el vencimiento de certificados o secretos internos pendientes. Estas alertas son inexactas y deben omitirse sin ejecutar el cambio de secretos interno. Las alertas de expiración de secretos internos inexactos son un problema conocido resuelto en la versión 1811. Los secretos internos no expirarán a menos que el entorno haya estado activo durante dos años.
::: moniker-end

## <a name="prerequisites"></a>Requisitos previos

1. Se recomienda encarecidamente que actualice primero la instancia de Azure Stack Hub a la [versión más reciente](release-notes.md).

    ::: moniker range="<azs-1811"  
    >[!IMPORTANT]
    > Para versiones anteriores a la 1811:
    > - Si ya se ha realizado la rotación de secretos, debe actualizar a la versión 1811 o posterior antes de ejecutar la rotación de secretos de nuevo. El cambio de secretos se debe ejecutar utilizando el [punto de conexión con privilegios](azure-stack-privileged-endpoint.md) y requiere credenciales de operador de Azure Stack Hub. Si no sabe si la rotación de secretos se ha ejecutado en su entorno, actualice a la versión 1811 antes de realizarla.
    > - No es necesario rotar los secretos para agregar certificados de host de extensiones. Debe seguir las instrucciones del artículo [Preparación de un host de extensiones de Azure Stack Hub de extensiones](azure-stack-extension-host-prepare.md) para agregar certificados de un host de extensiones.
    ::: moniker-end

2. Notifique a los usuarios cualquier operación de mantenimiento planeado. Programe ventanas de mantenimiento normales, en la medida de lo posible, durante horas no laborables. Las operaciones de mantenimiento pueden afectar tanto a las cargas de trabajo del usuario como a las operaciones del portal.

3. Los operadores pueden notar que las alertas se abren y cierran automáticamente durante la rotación de secretos. Este comportamiento es el esperado y puede hacerse caso omiso de las alertas. Los operadores pueden comprobar la validez de estas alertas mediante el [cmdlet Test-AzureStack de PowerShell](azure-stack-diagnostic-test.md). En el caso de los operadores que usan System Center Operations Manager para supervisar los sistemas de Azure Stack Hub, si ponen el sistema en modo de mantenimiento, evitarán que estas alertas lleguen a sus sistemas ITSM, pero se seguirán generando alertas si no se puede acceder al sistema de Azure Stack Hub.

::: moniker range=">=azs-1811"
## <a name="rotate-external-secrets"></a>Rotación de secretos externos

> [!Important]
> Rotación de secretos externos para:
> - **El administrador debe administrar manualmente los secretos que no son del certificado, como las claves seguras y las cadenas**. Esto incluye las contraseñas de cuentas de usuario y administrador y las [contraseñas de conmutador de red](azure-stack-customer-defined.md).
> - **Los secretos del proveedor de recursos (RP) de valor agregado** se describen con instrucciones independientes:
>    - [App Service en Azure Stack Hub](app-service-rotate-certificates.md)
>    - [IoT Hub en Azure Stack Hub](iot-hub-rp-rotate-secrets.md)
>    - [MySQL en Azure Stack Hub](azure-stack-mysql-resource-provider-maintain.md#secrets-rotation)
>    - [SQL en Azure Stack Hub](azure-stack-sql-resource-provider-maintain.md#secrets-rotation)
> - **Las credenciales del controlador de administración de placa base (BMC)** también es un proceso que se administra manualmente y que [se tratará más adelante en este artículo](#update-the-bmc-credential). 

En esta sección se describe la rotación de certificados que se usan para proteger los servicios orientados a externos. Estos certificados los proporciona el operador de Azure Stack Hub para los siguientes servicios:

- Administrator Portal
- Public Portal
- Administrator Azure Resource Manager
- Global Azure Resource Manager
- Administrator Key Vault
- Key Vault
- Host de extensiones de administración
- ACS (incluido Blob Storage, Table Storage y Queue Storage)
- ADFS<sup>*</sup>
- Graph<sup>*</sup>

<sup>*</sup>Aplicable al usar los Servicios de federación de Active Directory (AD FS).

### <a name="preparation"></a>Preparación

Antes de la rotación de secretos externos:

1. Ejecute el cmdlet **[`Test-AzureStack`](azure-stack-diagnostic-test.md)** de PowerShell con el parámetro `-group SecretRotationReadiness`, para confirmar que todas las salidas de prueba son correctas antes de rotar los secretos.
2. Prepare un nuevo conjunto de certificados externos de reemplazo:
   - El nuevo conjunto debe coincidir con las especificaciones de certificado que se describen en [Requisitos de certificados de infraestructura de clave pública de Azure Stack Hub](azure-stack-pki-certs.md). 
   - Genere una solicitud de firma de certificado (CSR) para enviarla a la entidad de certificación (CA). Use los pasos que se describen en [Generación de solicitudes de firma de certificados](azure-stack-get-pki-certs.md) y prepárelos para usarlos en su entorno de Azure Stack Hub siguiendo los pasos de [Preparación de certificados PKI](azure-stack-prepare-pki-certs.md). Azure Stack Hub permite cambiar los secretos con certificados externos de una nueva entidad de certificación (CA) en los contextos siguientes:

     |Rotar desde CA|Rotar a CA|Compatibilidad con la versión de Azure Stack Hub|
     |-----|-----|-----|-----|
     |Autofirmado|Enterprise| 1903 y posteriores|
     |Autofirmado|Autofirmado|No compatible|
     |Autofirmado|Público<sup>*</sup>|1803 y posteriores|
     |Enterprise|Enterprise|1803 y posteriores; de 1803 a 1903 si se usa la misma CA de empresa en la implementación|
     |Enterprise|Autofirmado|No compatible|
     |Enterprise|Público<sup>*</sup>|1803 y posteriores|
     |Público<sup>*</sup>|Enterprise|1903 y posteriores|
     |Público<sup>*</sup>|Autofirmado|No compatible|
     |Público<sup>*</sup>|Público<sup>*</sup>|1803 y posteriores|

     <sup>*</sup>Parte del [programa raíz de confianza de Windows](/security/trusted-root/participants-list).

   - No olvide validar los certificados que prepare con los pasos descritos en [Validación de certificados PKI](azure-stack-validate-pki-certs.md).
   - Asegúrese de que no haya ningún carácter especial en la contraseña como `*` o `)`.
   - Asegúrese de que el cifrado PFX es **TripleDES-SHA1**. Si surge un problema, consulte [Corrección de problemas comunes con certificados de PKI en Azure Stack Hub](azure-stack-remediate-certs.md#pfx-encryption).

3. Guarde una copia de seguridad de los certificados usados para el cambio en una ubicación segura. Si se ejecuta el cambio y, después, se produce un error, reemplace los certificados del recurso compartido de archivos por las copias de seguridad antes de volver a ejecutar el cambio. Conserve las copias de seguridad en la ubicación segura.
4. Cree un recurso compartido de archivos al que pueda acceder desde las máquinas virtuales de ERCS. El recurso compartido de archivos debe ser de lectura y escritura para la identidad **CloudAdmin**.
5. Abra una consola de PowerShell ISE desde un equipo que tenga acceso al recurso compartido de archivos. Vaya al recurso compartido de archivos donde se crean los directorios para colocar los certificados externos.
6. Descargue **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** en el recurso compartido de red y ejecute el script. El script creará una estructura de carpetas que se parece a **_.\Certificates\AAD_ *_ o _* _.\Certificates\ADFS_ *_, en función del proveedor de identidades. La estructura de carpetas debe empezar por una carpeta _* \\Certificates**, seguida SOLO de una carpeta **\\AAD** o **\\AD FS**. El resto de subdirectorios estarán incluidos en la estructura anterior. Por ejemplo:
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

### <a name="rotation"></a>Rotación

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

2. El cambio de secretos externos tarda aproximadamente una hora. Después de la finalización correcta del proceso, la consola mostrará el mensaje `ActionPlanInstanceID ... CurrentStatus: Completed`, seguido de `DONE`. Elimine los certificados del recurso compartido creado en la sección Preparación y almacénelos en su ubicación segura de copia de seguridad.

    > [!Note]
    > Si se produce un error en el cambio de secretos, siga las instrucciones del mensaje de error y vuelva a ejecutar `Start-SecretRotation` con el parámetro `-ReRun`.
    >
    >```powershell
    >Start-SecretRotation -ReRun
    >```  
    >
    >Póngase en contacto con el soporte técnico si el cambio de secretos falla en reiteradas ocasiones.
::: moniker-end

## <a name="rotate-internal-secrets"></a>Rotación de secretos internos

Los secretos internos incluyen los certificados, las contraseñas, las cadenas seguras y las claves que utilizan la infraestructura de Azure Stack Hub sin la intervención del operador de este servicio. La rotación de secretos internos solo es necesaria si sospecha que alguno de ellos se ha puesto en peligro o si ha recibido una alerta de expiración. 
::: moniker range="<azs-1811"  
En las implementaciones anteriores a 1811, puede ver alertas para las expiraciones de certificados o secretos internos pendientes. Estas alertas no son correctas y deben ignorarse, y son un problema conocido resuelto en la versión 1811.
::: moniker-end

Realice los pasos siguientes para rotar los secretos internos:

1. Ejecute el siguiente script de PowerShell. Tenga en cuenta que, para la rotación de secretos internos, en la sección "Run Secret Rotation", solo se usa el parámetro `-Internal` en el cmdlet [Start-SecretRotation](../reference/pep-2002/start-secretrotation.md):

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
        Start-SecretRotation -Internal
    }
    Remove-PSSession -Session $PEPSession
    ```

    ::: moniker range="<azs-1811"
    > [!Note]
    > Las versiones anteriores a la 1811 no requieren la marca `-Internal`. 
    ::: moniker-end


2. Después de la finalización correcta del proceso, la consola mostrará el mensaje `ActionPlanInstanceID ... CurrentStatus: Completed`, seguido del estado `DONE`.

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