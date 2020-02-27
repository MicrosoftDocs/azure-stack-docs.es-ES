---
title: Girar secretos
titleSuffix: Azure Stack Hub
description: Aprenda a cambiar los secretos en Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 12/13/2019
ms.reviewer: ppacent
ms.author: inhenkel
ms.lastreviewed: 12/13/2019
monikerRange: '>=azs-1802'
ms.openlocfilehash: 22be9075f6c1d8b25c6ce241ad24ed8e10630261
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77509575"
---
# <a name="rotate-secrets-in-azure-stack-hub"></a>Cambio de secretos en Azure Stack Hub

*Estas instrucciones se aplican exclusivamente a los sistemas integrados en Azure Stack Hub con la versión 1803 y posteriores. No intente cambiar los secretos en versiones de Azure Stack Hub anteriores a la 1802*

Los secretos ayudan a proteger la comunicación entre los servicios y los recursos de infraestructura de Azure Stack Hub.

## <a name="rotate-secrets-overview"></a>Información general del cambio de secretos

1. Prepare los certificados que se usarán para el cambio de secretos.
2. Revise los [requisitos de certificados de infraestructura de clave pública de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-pki-certs).
3. [Use el punto de conexión con privilegios](azure-stack-privileged-endpoint.md) y ejecute **Test-azurestack** para confirmar que todo está bien.  
4. Revise los [pasos previos para el cambio de secretos](#pre-steps-for-secret-rotation).
5. [Valide los certificados PKI de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-validate-pki-certs). Asegúrese de que no haya ningún carácter especial en la contraseña como `*` o `)`.
6. Asegúrese de que el cifrado PFX es **TripleDES-SHA1**. Si surge un problema, consulte [Corrección de problemas comunes con certificados de PKI en Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-remediate-certs#pfx-encryption).
7. Prepare la estructura de carpetas.  Encontrará un ejemplo en la sección [Rotación de secretos externos](https://docs.microsoft.com/azure-stack/operator/azure-stack-rotate-secrets#rotating-external-secrets).
8. [Inicie la rotación de secretos](#use-powershell-to-rotate-secrets).

## <a name="rotate-secrets"></a>Girar secretos

Azure Stack Hub usa diversos secretos para proteger las comunicaciones entre los recursos y los servicios de la infraestructura de Azure Stack Hub.

- **Secretos internos**

    Todos los certificados, contraseñas, cadenas seguras y claves que se utilizan en la infraestructura de Azure Stack Hub sin la intervención del operador de este servicio.

- **Secretos externos**

    Certificados de servicio de infraestructura para servicios de uso externo que proporciona el operador de Azure Stack Hub. Los secretos externos incluyen los certificados de los siguientes servicios:

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

> [!Note]
> Todas las demás claves y cadenas seguras, incluido el BMC, las contraseñas de cambio, así como las contraseñas de cuentas de usuario y administrador, aún las actualiza manualmente el administrador.

> [!Important]
> A partir de la versión 1811 de Azure Stack Hub, el cambio de secretos se ha separado en los certificados internos y externos.

Para mantener la integridad de la infraestructura de Azure Stack Hub, los operadores necesitan poder cambiar periódicamente los secretos de la infraestructura con una frecuencia que se ajuste a los requisitos de seguridad de la organización.

### <a name="rotating-secrets-with-external-certificates-from-a-new-certificate-authority"></a>Rotación de secretos con certificados externos desde una nueva entidad de certificación

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

<sup>*</sup>Indica que las entidades de certificación públicas son aquellas que forman parte del programa de raíz de confianza de Windows. Puede ver la lista completa en el artículo [Microsoft Trusted Root Certificate Program: Participants (as of June 27, 2017)](https://gallery.technet.microsoft.com/Trusted-Root-Certificate-123665ca) [Programa de certificados raíz de confianza de Microsoft: participantes (a partir del 27 de junio de 2017)].

## <a name="fixing-alerts"></a>Corrección de alertas

Cuando faltan menos de 30 días para la expiración de los secretos, se generan las siguientes alertas en el portal de administración:

- Expiración de contraseña de cuenta de servicio pendiente
- Expiración de certificado interno pendiente
- Expiración de certificado externo pendiente

Si ejecuta el cambio de secretos mediante las instrucciones que aparecen a continuación, se corregirán estas alertas.

> [!Note]
> Los entornos de Azure Stack Hub de las versiones anteriores a la 1811 pueden ver alertas sobre el vencimiento de certificados o secretos internos pendientes. Estas alertas son inexactas y deben omitirse sin ejecutar el cambio de secretos interno. Las alertas de expiración de secretos internos inexactos son un problema conocido resuelto en la versión 1811. Los secretos internos no expirarán a menos que el entorno haya estado activo durante dos años.

## <a name="pre-steps-for-secret-rotation"></a>Pasos previos para el cambio de secretos

   > [!IMPORTANT]
   > Si ya se ha realizado el cambio de secretos en su entorno de Azure Stack Hub, debe actualizar el sistema a la versión 1811 u otra posterior antes de ejecutar un nuevo cambio de secretos. El cambio de secretos se debe ejecutar utilizando el [punto de conexión con privilegios](azure-stack-privileged-endpoint.md) y requiere credenciales de operador de Azure Stack Hub. Si los operadores de Azure Stack Hub no saben si se ha ejecutado el cambio de secretos en el entorno, actualice a la versión 1811 antes de ejecutar un nuevo cambio.

1. Se recomienda encarecidamente que actualice la instancia de Azure Stack Hub a la versión 1811.

    > [!Note]
    > Para las versiones anteriores a 1811 no es necesario cambiar los secretos para agregar certificados de host de extensiones. Debe seguir las instrucciones del artículo [Preparación de un host de extensiones de Azure Stack Hub de extensiones](azure-stack-extension-host-prepare.md) para agregar certificados de un host de extensiones.

2. Es posible que los operadores observen que las alertas se abren y cierran automáticamente durante el cambio de secretos de Azure Stack Hub.  Este comportamiento es el esperado y puede hacerse caso omiso de las alertas.  Los operadores pueden comprobar la validez de estas alertas mediante la ejecución de **Test-AzureStack**.  En el caso de los operadores que usan System Center Operations Manager para supervisar los sistemas de Azure Stack Hub, si ponen el sistema en modo de mantenimiento, evitarán que estas alertas lleguen a sus sistemas ITSM, pero se seguirán generando alertas si no se puede acceder al sistema de Azure Stack Hub.

3. Avise a los usuarios de cualquier operación de mantenimiento. Programe ventanas de mantenimiento normales, en la medida de lo posible, durante horas no laborables. Las operaciones de mantenimiento pueden afectar tanto a las cargas de trabajo del usuario como a las operaciones del portal.

    > [!Note]
    > Los pasos siguientes solo se aplican al cambio de secretos externos de Azure Stack Hub.

4. Ejecute **[Test-AzureStack](azure-stack-diagnostic-test.md)** y confirme que todos los resultados de prueba estén en buen estado antes de cambiar los secretos.
5. Prepare un nuevo conjunto de certificados externos de reemplazo. El nuevo conjunto coincide con las especificaciones de certificado que se describen en [Requisitos de certificados de infraestructura de clave pública de Azure Stack Hub](azure-stack-pki-certs.md). Puede generar una solicitud de firma de certificados (CSR) para comprar o crear nuevos certificados mediante los pasos que se describen en [Generación de certificados PKI](azure-stack-get-pki-certs.md) y prepararlos para utilizarlos en su entorno de Azure Stack Hub siguiendo los pasos de [Preparación de certificados PKI](azure-stack-prepare-pki-certs.md). No olvide validar los certificados que prepare con los pasos descritos en [Validación de certificados PKI](azure-stack-validate-pki-certs.md).
6. Guarde una copia de seguridad de los certificados usados para el cambio en una ubicación segura. Si se ejecuta el cambio y, después, se produce un error, reemplace los certificados del recurso compartido de archivos por las copias de seguridad antes de volver a ejecutar el cambio. Conserve las copias de seguridad en la ubicación segura.
7. Cree un recurso compartido de archivos al que pueda acceder desde las máquinas virtuales de ERCS. El recurso compartido de archivos debe ser de lectura y escritura para la identidad **CloudAdmin**.
8. Abra una consola de PowerShell ISE desde un equipo que tenga acceso al recurso compartido de archivos. Vaya al recurso compartido de archivos.
9. Ejecute **[CertDirectoryMaker.ps1](https://www.aka.ms/azssecretrotationhelper)** para crear los directorios necesarios para los certificados externos.

> [!IMPORTANT]
> El script CertDirectoryMaker creará una estructura de carpetas a la que se adherirá:
>
> **.\Certificates\AAD** o ***.\Certificates\ADFS*** en función del proveedor de identidades que se utiliza para Azure Stack Hub.
>
> Es de suma importancia que la estructura de carpetas termine con las carpetas **AAD** o **ADFS** y que todos los subdirectorios estén dentro de esta estructura; de lo contrario, **Start-SecretRotation** mostrará lo siguiente:
>
> ```powershell
> Cannot bind argument to parameter 'Path' because it is null.
> + CategoryInfo          : InvalidData: (:) [Test-Certificate], ParameterBindingValidationException
> + FullyQualifiedErrorId : ParameterArgumentValidationErrorNullNotAllowed,Test-Certificate
> + PSComputerName        : xxx.xxx.xxx.xxx
> ```
>
> El mensaje de error indica que hay un problema para acceder al recurso compartido de archivos, pero en realidad es la estructura de carpetas lo que se aplica aquí. Puede encontrar más información en Microsoft AzureStack Readiness Checker: [módulo PublicCertHelper](https://www.powershellgallery.com/packages/Microsoft.AzureStack.ReadinessChecker/1.1811.1101.1/Content/CertificateValidation%5CPublicCertHelper.psm1).
>
> También es importante que la estructura de la carpeta del recurso compartido de archivos comience con la carpeta **Certificates**; de lo contrario, también se producirá un error en la validación.
> El montaje del recurso compartido de archivos debe ser similar a **\\\\\<IPAddress>\\\<ShareName>\\** y debe contener la carpeta **Certificates\AAD** o **Certificates\ADFS** dentro.
>
> Por ejemplo:
> - Fileshare = **\\\\\<IPAddress>\\\<ShareName>\\**
> - CertFolder = **Certificates\AAD**
> - FullPath = **\\\\\<IPAddress>\\\<ShareName>\Certificates\AAD**

## <a name="rotating-external-secrets"></a>Cambio de secretos externos

Para cambiar los secretos externos:

1. En el directorio **\Certificates\\\<IdentityProvider>** que se creó en los pasos anteriores, coloque el nuevo conjunto de certificados externos de reemplazo en la estructura de directorios siguiendo el formato que se indica en la sección **Certificados obligatorios** de [Requisitos de certificados de infraestructura de clave pública de Azure Stack Hub](azure-stack-pki-certs.md#mandatory-certificates).

    Ejemplo de la estructura de carpetas para el proveedor de identidades de Azure AD:
    ```powershell
        <ShareName>
        │   │
        │   └───Certificates
        │         └───AAD
        │             ├───ACSBlob
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSQueue
        │             │       <CertName>.pfx
        │             │
        │             ├───ACSTable
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Extension Host
        │             │       <CertName>.pfx
        │             │
        │             ├───Admin Portal
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Admin
        │             │       <CertName>.pfx
        │             │
        │             ├───ARM Public
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVault
        │             │       <CertName>.pfx
        │             │
        │             ├───KeyVaultInternal
        │             │       <CertName>.pfx
        │             │
        │             ├───Public Extension Host
        │             │       <CertName>.pfx
        │             │
        │             └───Public Portal
        │                     <CertName>.pfx

    ```

2. Cree una sesión de PowerShell con el [punto de conexión con privilegios](azure-stack-privileged-endpoint.md) mediante la cuenta **CloudAdmin** y almacene las sesiones como una variable. Utilizará esta variable como parámetro en el paso siguiente.

    > [!IMPORTANT]  
    > No especifique la sesión. Almacene la sesión como una variable.

3. Ejecute **[Invoke-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/Invoke-Command?view=powershell-5.1)** . Pase la variable de sesión de PowerShell del punto de conexión con privilegios como parámetro **Session**.

4. Ejecute **Start-SecretRotation** con los siguientes parámetros:
    - **PfxFilesPath**  
    Especifique la ruta de acceso de red al directorio de certificados que creó anteriormente.  
    - **PathAccessCredential**  
    Un objeto PSCredential con las credenciales del recurso compartido.
    - **CertificatePassword**  
    Una cadena segura de la contraseña usada para todos los archivos de certificados pfx creados.

5. Espere mientras cambian los secretos. El cambio de secretos externos tarda aproximadamente una hora.

    Cuando se complete correctamente el cambio de secretos, la consola mostrará el mensaje **Overall action status: Success** (Estado global de la acción: correcto).

    > [!Note]
    > Si se produce un error en el cambio de secretos, siga las instrucciones del mensaje de error y vuelva a ejecutar **Start-SecretRotation** con el parámetro **-ReRun**.

    ```powershell
    Start-SecretRotation -ReRun
    ```

    Póngase en contacto con el soporte técnico si el cambio de secretos falla en reiteradas ocasiones.

6. Después de la finalización correcta del cambio de secretos, quite los certificados del recurso compartido creado en el paso anterior y almacénelos en la ubicación segura de copia de seguridad.

## <a name="use-powershell-to-rotate-secrets"></a>Uso de PowerShell para la rotación de secretos

El siguiente ejemplo de PowerShell muestra los cmdlets y parámetros que debe ejecutar para cambiar los secretos.

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Run Secret Rotation
$CertPassword = ConvertTo-SecureString "<CertPasswordHere>" -AsPlainText -Force
$CertShareCreds = Get-Credential
$CertSharePath = "<NetworkPathOfCertShare>"
Invoke-Command -Session $PEPSession -ScriptBlock {
    Start-SecretRotation -PfxFilesPath $using:CertSharePath -PathAccessCredential $using:CertShareCreds -CertificatePassword $using:CertPassword
}
Remove-PSSession -Session $PEPSession
```

## <a name="rotating-only-internal-secrets"></a>Cambio solo de secretos internos

> [!Note]
> El cambio de secretos internos solo debe realizarse si sospecha que un secreto interno se ha visto comprometido por una entidad malintencionada o si ha recibido una alerta (en la compilación 1811 o posterior) que indica que los certificados internos van a expirar. Los entornos de Azure Stack Hub de las versiones anteriores a la 1811 pueden ver alertas sobre el vencimiento de certificados o secretos internos pendientes. Estas alertas son inexactas y deben omitirse sin ejecutar el cambio de secretos interno. Las alertas de expiración de secretos internos inexactos son un problema conocido resuelto en la versión 1811. Los secretos internos no expirarán a menos que el entorno haya estado activo durante dos años.

1. Cree una sesión de PowerShell con el [punto de conexión con privilegios](azure-stack-privileged-endpoint.md).
2. En la sesión del punto de conexión con privilegios, ejecute **Start-SecretRotation** sin argumentos.

    > [!Note]
    > Los entornos de Azure Stack Hub de las versiones anteriores a la 1811 no requerirán la marca **-Internal**. **Start-SecretRotation** cambiará solo los secretos internos.

3. Espere mientras cambian los secretos.

   Cuando se complete correctamente el cambio de secretos, la consola mostrará el mensaje **Overall action status: Success** (Estado global de la acción: correcto).
    > [!Note]
    > Si se produce un error en el cambio de secretos, siga las instrucciones del mensaje de error y vuelva a ejecutar **Start-SecretRotation** con los parámetros **-Internal** y **-ReRun**.  

```powershell
Start-SecretRotation -Internal -ReRun
```

Póngase en contacto con el soporte técnico si el cambio de secretos falla en reiteradas ocasiones.

## <a name="start-secretrotation-reference"></a>Referencia de Start-SecretRotation

Cambia los secretos de un sistema de Azure Stack Hub. Solo se ejecuta en el punto de conexión con privilegios de Azure Stack Hub.

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

### <a name="description"></a>Descripción

El cmdlet **Start-SecretRotation** permite cambiar los secretos de la infraestructura de un sistema de Azure Stack Hub. De forma predeterminada, solo se cambian los certificados de todos los puntos de conexión de la infraestructura de red externa. Si se usa con la marca -Internal, se cambian los secretos de la infraestructura interna. Si se cambian los puntos de conexión de la infraestructura de red externa, **Start-SecretRotation** debe ejecutarse con un bloque de script **Invoke-Command** utilizando la sesión del punto de conexión con privilegios del entorno de Azure Stack Hub que se pasó en el parámetro **Session**.

### <a name="parameters"></a>Parámetros

| Parámetro | Tipo | Obligatorio | Posición | Valor predeterminado | Descripción |
| -- | -- | -- | -- | -- | -- |
| `PfxFilesPath` | String  | False  | con nombre  | None  | La ruta de acceso del recurso compartido de archivos al directorio **\Certificates** que contiene todos los certificados del punto de conexión de la red externa. Solo se necesita al rotar secretos externos. El directorio final debe ser **\Certificates**. |
| `CertificatePassword` | SecureString | False  | con nombre  | None  | La contraseña de todos los certificados que se proporcionan en -PfXFilesPath. Valor obligatorio si se proporciona PfxFilesPath al cambiar secretos externos. |
| `Internal` | String | False | con nombre | None | La marca Internal se debe utilizar cada vez que un operador de Azure Stack Hub quiera cambiar los secretos de infraestructura interna. |
| `PathAccessCredential` | PSCredential | False  | con nombre  | None  | La credencial de PowerShell para el recurso compartido de archivos al directorio **\Certificates** que contiene todos los certificados del punto de conexión de la red externa. Solo se necesita al rotar secretos externos.  |
| `ReRun` | SwitchParameter | False  | con nombre  | None  | ReRun se debe usar en cualquier momento en que se vuelva a intentar el cambio de secretos después de un intento fallido. |

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
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

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

#### <a name="rotate-internal-and-external-infrastructure-secrets-pre-1811-only"></a>Cambio de secretos de la infraestructura interna y externa (solo **anterior a 1811**)

> [!IMPORTANT]
> Este comando solo se aplica a las **versiones de Azure Stack Hub anteriores a la 1811**, ya que el cambio ahora es diferente para los certificados internos y para los certificados externos.
>
> **Desde las versiones posteriores a *1811* ya no se pueden cambiar los certificados internos y externos.**

```powershell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

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

Este comando cambia todos los secretos de la infraestructura expuestos a la red interna de Azure Stack Hub, así como los certificados TLS que se usan con los puntos de conexión de la infraestructura de red externa de Azure Stack Hub. Start-SecretRotation cambia todos los secretos generados por Stack, y dado que se han proporcionado certificados, los certificados de punto de conexión externos también se cambiarán.  

## <a name="update-the-baseboard-management-controller-bmc-credential"></a>Actualización de la credencial del controlador de administración de placa base (BMC)

El controlador de administración de placa base (BMC) supervisa el estado físico de sus servidores. Consulte con el proveedor de hardware del fabricante de equipos originales (OEM) para obtener instrucciones sobre cómo actualizar el nombre y la contraseña de la cuenta de usuario del BMC.

>[!NOTE]
> El OEM puede proporcionar aplicaciones de administración adicionales. La actualización del nombre de usuario o la contraseña para otras aplicaciones de administración no tiene ningún efecto en el nombre de usuario o la contraseña del BMC.

1. **Versiones anteriores a 1910**: Actualice el BMC de los servidores físicos de Azure Stack Hub siguiendo las instrucciones del OEM. El nombre de usuario y la contraseña para cada BMC de su entorno deben ser los mismos. Los nombres de usuario de BMC no pueden superar los 16 caracteres.

   **Versión 1910 y posteriores**: Ya no es necesario que actualice primero las credenciales del BMC en los servidores físicos de Azure Stack Hub siguiendo las instrucciones del OEM. El nombre de usuario y la contraseña para cada BMC de su entorno deben ser los mismos. Los nombres de usuario de BMC no pueden superar los 16 caracteres.

    | Parámetro | Descripción | State |
    | --- | --- | --- |
    | BypassBMCUpdate | Cuando se usa el parámetro, las credenciales del BMC no se actualizan. Solo se actualiza el almacén de datos interno de Azure Stack Hub. | Opcional |

2. Abra un punto de conexión con privilegios en sesiones de Azure Stack Hub. Para obtener instrucciones, consulte [Uso del punto de conexión con privilegios en Azure Stack Hub](azure-stack-privileged-endpoint.md).

3. Cuando el símbolo del sistema de PowerShell cambie a **[IP address or ERCS VM name]: PS >** o a **[azs-ercs01]: PS >** , en función del entorno, ejecute `Set-BmcCredential` mediante `Invoke-Command`. Pase la variable de sesión del punto de conexión con privilegios como parámetro. Por ejemplo:

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

    También puede usar la versión de PowerShell estática con las contraseñas como líneas de código:

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

## <a name="next-steps"></a>Pasos siguientes

[Más información acerca de la seguridad de Azure Stack](azure-stack-security-foundations.md)
