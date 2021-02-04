---
title: Uso del punto de conexión con privilegios en Azure Stack Hub
description: Aprenda a usar el punto de conexión con privilegios (PEP) en Azure Stack Hub como operador.
author: mattbriggs
ms.topic: article
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 04/28/2020
ms.custom: conteperfq4
ms.openlocfilehash: 4e1a00be9f4adadee5d1bf1e647c455773211b61
ms.sourcegitcommit: 5f3d37994b8cb63c76e54136c0cc05bc4f475950
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/03/2021
ms.locfileid: "99495540"
---
# <a name="use-the-privileged-endpoint-in-azure-stack-hub"></a>Uso del punto de conexión con privilegios en Azure Stack Hub

Como operador de Azure Stack Hub, debe usar el portal del administrador, PowerShell o las API de Azure Resource Manager en la mayor parte de las tareas de administración diarias. Sin embargo, con algunas operaciones menos comunes, deberá usar el *punto de conexión con privilegios* (PEP). El PEP es una consola remota de PowerShell preconfigurada que proporciona las funcionalidades suficientes para ayudarle a realizar una tarea necesaria. El punto de conexión usa [PowerShell JEA (Just Enough Administration)](/powershell/scripting/learn/remoting/jea/overview) para exponer únicamente un conjunto restringido de cmdlets. Para acceder al PEP e invocar el conjunto restringido de cmdlets, se usa una cuenta sin privilegios. No se necesita ninguna cuenta de administrador. Para mayor seguridad, no se permite scripting.

Puede usar el PEP para realizar estas tareas:

- Tareas de bajo nivel, como la [recopilación de registros de diagnóstico](azure-stack-get-azurestacklog.md).
- Muchas tareas de integración de centros de datos posteriores a la implementación para sistemas integrados, como agregar reenviadores de Sistema de nombres de dominio (DNS) después de la implementación, configurar la integración con Microsoft Graph y con Active Directory Federation Services (AD FS), la rotación de certificados, etc.
- Trabaje con el soporte técnico para obtener acceso temporal y de alto nivel para la solución de problemas en profundidad de un sistema integrado.

El PEP registra cada acción (y su salida correspondiente) que se realiza en la sesión de PowerShell. De esta forma las operaciones se realizan con total transparencia y se pueden auditar de forma completa. Puede conservar estos archivos de registro para futuras auditorías.

> [!NOTE]
> En el Kit de desarrollo de Azure Stack (ASDK), puede ejecutar algunos de los comandos disponibles en el PEP directamente desde una sesión de PowerShell en el host del Kit de desarrollo. No obstante, puede que quiera probar algunas operaciones usando el PEP, como la recopilación de registros, dado que es el único método disponible para realizar determinadas operaciones en un entorno de sistemas integrados.

[!INCLUDE [Azure Stack Hub Operator Access Workstation](../includes/operator-note-owa.md)]

## <a name="access-the-privileged-endpoint"></a>Acceso al punto de conexión con privilegios

El acceso al PEP se realiza mediante una sesión remota de PowerShell en la máquina virtual que lo hospeda. En el ASDK, esta máquina virtual se denomina **AzS-ERCS01**. Si va a usar un sistema integrado, hay tres instancias del PEP, cada una de las cuales se ejecuta en una máquina virtual (*Prefix*-ERCS01, *Prefix*-ERCS02 o *Prefix*-ERCS03) en diferentes hosts para proporcionar resistencia.

Antes de comenzar este procedimiento en un sistema integrado, asegúrese de que puede acceder a un PEP bien mediante la dirección IP o a través de DNS. Después de la implementación inicial de Azure Stack Hub, solo puede acceder al PEP mediante la dirección IP, dado que la integración de DNS no está configurada aún. El proveedor de hardware OEM le proporcionará un archivo JSON denominado **AzureStackStampDeploymentInfo** que contiene las direcciones IP del PEP.

También puede encontrar la dirección IP en el portal de administración de Azure Stack Hub. Abra el portal, por ejemplo, `https://adminportal.local.azurestack.external`. Seleccione **Region Management (Administración de regiones)**  > **Properties (Propiedades)** .

Tendrá que establecer la configuración de la referencia cultural actual en `en-US` al ejecutar el punto de conexión con privilegios; de lo contrario, los cmdlets como Test-AzureStack o Get-AzureStackLog no funcionarán según lo esperado.

> [!NOTE]
> Por motivos de seguridad, es necesario conectarse al PEP solo desde una máquina virtual protegida que se ejecute en el host de ciclo de vida de hardware, o desde un equipo seguro dedicado, como una [estación de trabajo de acceso con privilegios](https://4sysops.com/archives/understand-the-microsoft-privileged-access-workstation-paw-security-model). No se debe modificar la configuración original del host de ciclo de vida de hardware, (incluida la instalación de software nuevo) ni usarse para la conexión al PEP.

1. Establezca la confianza.

   - En un sistema integrado, ejecute el siguiente comando desde una sesión de Windows PowerShell con privilegios elevados para agregar el PEP como host de confianza a la máquina virtual protegida que se ejecuta en el host de ciclo de vida de hardware o en la estación de trabajo de acceso con privilegios.

      ```powershell  
      Set-Item WSMan:\localhost\Client\TrustedHosts -Value '<IP Address of Privileged Endpoint>' -Concatenate
      ```
      
   - Si va a ejecutar el ADSK, inicie sesión en el host del kit de desarrollo.

1. En la máquina virtual protegida que se ejecuta en el host de ciclo de vida de hardware o en la estación de trabajo de acceso con privilegios, abra una sesión de Windows PowerShell. Ejecute los comandos siguientes para establecer una sesión remota en la máquina virtual que hospeda el PEP:
 
   - En un sistema integrado:

      ```powershell  
      $cred = Get-Credential

      $pep = New-PSSession -ComputerName <IP_address_of_ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
      ```
    
      El parámetro `ComputerName` puede ser la dirección IP o el nombre DNS de una de las máquinas virtuales que hospeda el PEP.

      > [!NOTE]  
      > Azure Stack Hub no realiza ninguna llamada remota al validar la credencial del PEP. Se basa en una clave pública RSA almacenada localmente para hacerlo.

   - Si va a ejecutar el ADSK:

      ```powershell  
      $cred = Get-Credential
    
      $pep = New-PSSession -ComputerName azs-ercs01 -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
      ```
    
   Cuando se le pida, utilice las siguientes credenciales:
   
   -  **Nombre de usuario**: especifique la cuenta CloudAdmin con el formato **&lt;*dominio de Azure Stack Hub*&gt;\cloudadmin**. En ASDK, el nombre de usuario es **azurestack\cloudadmin**.
   - **Contraseña**: escriba la misma contraseña que proporcionó durante la instalación de la cuenta del administrador de dominio de AzureStackAdmin.

   > [!NOTE]
   > Si no puede conectarse al punto de conexión de ERCS, vuelva a intentar los pasos uno y dos con otra dirección IP de la máquina virtual de ERCS.

1. Después de conectarse, el símbolo del sistema cambia a **[*dirección IP o nombre de VM de ERCS*]: PS >** o a **[azs-ercs01]: PS >** , en función del entorno. Desde aquí, ejecute `Get-Command` para ver la lista de los cmdlets disponibles.

   Puede encontrar una referencia para los cmdlets en [Referencia del punto de conexión con privilegios elevados de Azure Stack Hub](../reference/pep-2002/index.md)

   Muchos de estos cmdlets están concebidos únicamente para entornos de sistema integrados (por ejemplo, los cmdlets relacionados con la integración de centros de datos). En el ASDK, se han validado los siguientes cmdlets:

   - Clear-Host
   - Close-PrivilegedEndpoint
   - Exit-PSSession
   - Get-AzureStackLog
   - Get-AzureStackStampInformation
   - Get-Command
   - Get-FormatData
   - Get-Help
   - Get-ThirdPartyNotices
   - Measure-Object
   - New-CloudAdminUser
   - Out-Default
   - Remove-CloudAdminUser
   - Select-Object
   - Set-CloudAdminUserPassword
   - Test-AzureStack
   - Stop-AzureStack
   - Get-ClusterLog

## <a name="how-to-use-the-privileged-endpoint"></a>Uso del punto de conexión con privilegios 

Tal y como se mencionó anteriormente, el PEP es un punto de conexión de [PowerShell JEA](/powershell/scripting/learn/remoting/jea/overview). Al proporcionar una capa de seguridad sólida, un punto de conexión de JEA reduce algunas de las funcionalidades básicas de PowerShell, como la finalización con tabulación o de scripting. Si intenta algún tipo de operación de scripts, se producirá el error **ScriptsNotAllowed**. Este error es el comportamiento esperado.

Por ejemplo, para obtener la lista de parámetros de un cmdlet determinado, debe ejecutar el siguiente comando:

```powershell
    Get-Command <cmdlet_name> -Syntax
```

Como alternativa, puede usar el cmdlet [**Import-PSSession**](/powershell/module/microsoft.powershell.utility/import-pssession?view=powershell-5.1) para importar todos los cmdlets de PEP a la sesión actual en la máquina local. Los cmdlets y las funciones del PEP ahora están disponibles en la máquina local, junto con la finalización con tabulación y, más en general, con el scripting. También puede ejecutar el módulo **[Get-Help](/powershell/module/microsoft.powershell.core/get-help)** para revisar las instrucciones del cmdlet.

Lleve a cabo los siguientes pasos para importar la sesión del PEP al equipo local:

1. Establezca la confianza.

   - En un sistema integrado, ejecute el siguiente comando desde una sesión de Windows PowerShell con privilegios elevados para agregar el PEP como host de confianza a la máquina virtual protegida que se ejecuta en el host de ciclo de vida de hardware o en la estación de trabajo de acceso con privilegios.

      ```powershell
      winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```

   - Si va a ejecutar el ADSK, inicie sesión en el host del kit de desarrollo.

1. En la máquina virtual protegida que se ejecuta en el host de ciclo de vida de hardware o en la estación de trabajo de acceso con privilegios, abra una sesión de Windows PowerShell. Ejecute los comandos siguientes para establecer una sesión remota en la máquina virtual que hospeda el PEP:

   - En un sistema integrado:
    
      ```powershell  
      $cred = Get-Credential
      
      $session = New-PSSession -ComputerName <IP_address_of_ERCS> `
        -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```
    
      El parámetro `ComputerName` puede ser la dirección IP o el nombre DNS de una de las máquinas virtuales que hospeda el PEP.

   - Si va a ejecutar el ADSK:
     
      ```powershell  
      $cred = Get-Credential
    
      $session = New-PSSession -ComputerName azs-ercs01 `
        -ConfigurationName PrivilegedEndpoint -Credential $cred
      ```

   Cuando se le pida, utilice las siguientes credenciales:

   - **Nombre de usuario**: especifique la cuenta CloudAdmin con el formato **&lt;*dominio de Azure Stack Hub*&gt;\cloudadmin**. (Para ASDK, el nombre de usuario es **azurestack\cloudadmin**).
      
   - **Contraseña**: escriba la misma contraseña que proporcionó durante la instalación de la cuenta del administrador de dominio de AzureStackAdmin.

1. Importe la sesión PEP a la máquina local:

   ```powershell 
   Import-PSSession $session
   ```

1. Ahora, puede usar la finalización con tabulación y realizar scripting como de costumbre en la sesión de PowerShell local con todas las funciones y cmdlets del PEP sin reducir la posición de seguridad de Azure Stack Hub. ¡Disfrute!


## <a name="close-the-privileged-endpoint-session"></a>Cierre de la sesión del punto de conexión con privilegios

Como se mencionó anteriormente, el PEP registra cada una de las acciones (y su salida correspondiente) que realiza en la sesión de PowerShell. Debe cerrar la sesión mediante el cmdlet `Close-PrivilegedEndpoint`. Este cmdlet cierra correctamente el punto de conexión y transfiere los archivos de registro a un recurso compartido de archivos externo donde se retienen.

Para cerrar la sesión del punto de conexión:

1. Cree un recurso compartido de archivos externo al que se pueda acceder mediante el PEP. En un entorno de kit de desarrollo, solo puede crear un recurso compartido de archivos en el host del kit de desarrollo.
1. Ejecute el siguiente cmdlet:

   ```powershell  
   Close-PrivilegedEndpoint -TranscriptsPathDestination "\\fileshareIP\SharedFolder" -Credential Get-Credential
   ```

   el cmdlet usa los parámetros de la tabla siguiente:

   | Parámetro | Descripción | Tipo | Obligatorio |
   |---------|---------|---------|---------|
   | *TranscriptsPathDestination* | Ruta de acceso al recurso compartido de archivos externo definido como "fileshareIP\sharefoldername". | String | Sí|
   | *Credential:* | Credenciales para acceder al recurso compartido de archivos. | SecureString |   Sí |


Una vez los archivos de registro de transcripción se transfieren correctamente al recurso compartido de archivos, se eliminan automáticamente del PEP. 

> [!NOTE]
> Si cierra la sesión del PEP mediante los cmdlets `Exit-PSSession` o `Exit`, o simplemente cierra la consola de PowerShell, los archivos de transcripción no se transfieren a un recurso compartido de archivos. Permanecen en el PEP. La próxima vez que ejecute `Close-PrivilegedEndpoint` e incluya un recurso compartido de archivos, también se transferirán los registros de transcripción de la sesión anterior. No utilice `Exit-PSSession` o `Exit` para cerrar la sesión en el PEP; use `Close-PrivilegedEndpoint` en su lugar.

## <a name="unlocking-the-privileged-endpoint-for-support-scenarios"></a>Desbloqueo del punto de conexión con privilegios para escenarios de soporte técnico

Durante un escenario de soporte técnico, es posible que el ingeniero de soporte técnico de Microsoft eleve la sesión de PowerShell de punto de conexión con privilegios para acceder a los elementos internos de la infraestructura de Azure Stack Hub. A veces, se hace referencia a este proceso de forma informal como "romper en caso de emergencia" o "desbloquear el PEP". El proceso de elevación de la sesión del PEP es un proceso de autenticación de dos pasos, dos personas y dos organizaciones. El procedimiento de desbloqueo lo inicia el operador de Azure Stack Hub, que conserva el control de su entorno en todo momento. El operador tiene acceso al PEP y ejecuta este cmdlet:
 
 ```powershell  
      Get-SupportSessionToken
 ```

El cmdlet devuelve el token de solicitud de la sesión de soporte, una cadena alfanumérica muy larga. A continuación, el operador pasa el token de solicitud al ingeniero de soporte técnico de Microsoft a través de un medio de su elección (por ejemplo, chat, correo electrónico). El ingeniero de soporte técnico de Microsoft utiliza el token de solicitud para generar, si es válido, un token de autorización de la sesión de soporte y lo devuelve al operador de Azure Stack Hub. En la misma sesión de PowerShell del PEP, el operador pasa el token de autorización como entrada a este cmdlet:

```powershell  
      unlock-supportsession
      cmdlet Unlock-SupportSession at command pipeline position 1
      Supply values for the following parameters:
      ResponseToken:
 ```

Si el token de autorización es válido, se proporcionan capacidades de administración completas y disponibilidad total en la infraestructura para elevar la sesión de PowerShell del PEP. 

> [!NOTE]
> Todas las operaciones y los cmdlets que se ejecutan en una sesión del PEP elevada deben realizarse bajo una supervisión estricta del ingeniero de soporte técnico de Microsoft. Si no lo hace, podría provocar un tiempo de inactividad grave, pérdida de datos y requerir una reimplementación completa del entorno de Azure Stack Hub.

Una vez finalizada la sesión de soporte, es muy importante volver a cerrar la sesión de PEP elevada mediante el cmdlet **Close-PrivilegedEndpoint**, como se explica en la sección anterior. Una vez finalizada la sesión de PEP, el token de desbloqueo ya no es válido y no se puede volver a usar para desbloquear otra vez la sesión de PEP.
Una sesión de PEP elevada tiene un período de validez de 8 horas, después del cual, si no se termina, se bloquea de nuevo automáticamente en una sesión de PEP normal.

## <a name="content-of-the-privileged-endpoint-tokens"></a>Contenido de los tokens de punto de conexión con privilegios

La solicitud y los tokens de autorización de la sesión de soporte de PEP aprovechan la criptografía para proteger el acceso y garantizar que solo los tokens autorizados puedan desbloquear la sesión de PEP. Los tokens están diseñados para garantizar criptográficamente que un token de respuesta solo lo pueda aceptar la sesión de PEP que generó el token de solicitud. Los tokens de PEP no contienen ningún tipo de información que pueda identificar de forma única un entorno de Azure Stack Hub o un cliente. Son completamente anónimos. A continuación se proporcionan los detalles del contenido de cada token.
 
### <a name="support-session-request-token"></a>Token de solicitud de la sesión de soporte

El token de solicitud de la sesión de soporte de PEP se compone de tres objetos:

- Un identificador de sesión generado aleatoriamente.
- Un certificado autofirmado, generado con el fin de tener un par de claves pública y privada único. El certificado no incluye información sobre el entorno.
- Una marca de tiempo que indica la expiración del token de solicitud.

A continuación, el token de solicitud se cifra con la clave pública de la nube de Azure en la que está registrado el entorno de Azure Stack Hub.
 
### <a name="support-session-authorization-response-token"></a>Token de respuesta de autorización de la sesión de soporte

El token de respuesta de autorización de soporte del PEP se compone de dos objetos:

- El identificador de sesión generado aleatoriamente extraído del token de solicitud.
- Una marca de tiempo que indica la expiración del token de respuesta.
      
A continuación, el token de respuesta se cifra con el certificado autofirmado incluido en el token de solicitud. El certificado autofirmado se descifró con la clave privada asociada a la nube de Azure en la que se registró el entorno de Azure Stack Hub.


## <a name="next-steps"></a>Pasos siguientes

- [Herramientas de diagnóstico de Azure Stack Hub](./diagnostic-log-collection.md)
- [Referencia del punto de conexión con privilegios elevados de Azure Stack Hub](../reference/pep-2002/index.md)