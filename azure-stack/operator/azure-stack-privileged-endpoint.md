---
title: Uso del punto de conexión con privilegios en Azure Stack Hub | Microsoft Docs
description: Aprenda a usar el punto de conexión con privilegios (PEP) en Azure Stack Hub como operador.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 1/8/2020
ms.author: mabrigg
ms.reviewer: fiseraci
ms.lastreviewed: 1/8/2020
ms.openlocfilehash: 665e0a32abfbdce3953423135600eed98cbc2eef
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2020
ms.locfileid: "75882137"
---
# <a name="use-the-privileged-endpoint-in-azure-stack-hub"></a>Uso del punto de conexión con privilegios en Azure Stack Hub

Como operador de Azure Stack Hub, debe usar el portal del administrador, PowerShell o las API de Azure Resource Manager en la mayor parte de las tareas de administración diarias. Sin embargo, con algunas operaciones menos comunes, deberá usar el *punto de conexión con privilegios* (PEP). El PEP es una consola remota de PowerShell preconfigurada que proporciona las funcionalidades suficientes para ayudarle a realizar una tarea necesaria. El punto de conexión usa [PowerShell JEA (Just Enough Administration)](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview) para exponer únicamente un conjunto restringido de cmdlets. Para acceder al PEP e invocar el conjunto restringido de cmdlets, se usa una cuenta sin privilegios. No se necesita ninguna cuenta de administrador. Para mayor seguridad, no se permite scripting.

Puede usar el PEP para realizar estas tareas:

- Tareas de bajo nivel, como la [recopilación de registros de diagnóstico](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs).
- Muchas tareas de integración de centros de datos posteriores a la implementación para sistemas integrados, como agregar reenviadores de Sistema de nombres de dominio (DNS) después de la implementación, configurar la integración con Microsoft Graph y con Active Directory Federation Services (AD FS), la rotación de certificados, etc.
- Trabaje con el soporte técnico para obtener acceso temporal y de alto nivel para la solución de problemas en profundidad de un sistema integrado.

El PEP registra cada acción (y su salida correspondiente) que se realiza en la sesión de PowerShell. De esta forma las operaciones se realizan con total transparencia y se pueden auditar de forma completa. Puede conservar estos archivos de registro para futuras auditorías.

> [!NOTE]
> En el Kit de desarrollo de Azure Stack (ASDK), puede ejecutar algunos de los comandos disponibles en el PEP directamente desde una sesión de PowerShell en el host del Kit de desarrollo. No obstante, puede que quiera probar algunas operaciones usando el PEP, como la recopilación de registros, dado que es el único método disponible para realizar determinadas operaciones en un entorno de sistemas integrados.

## <a name="access-the-privileged-endpoint"></a>Acceso al punto de conexión con privilegios

El acceso al PEP se realiza mediante una sesión remota de PowerShell en la máquina virtual que lo hospeda. En el ASDK, esta máquina virtual se denomina **AzS-ERCS01**. Si va a usar un sistema integrado, hay tres instancias del PEP, cada una de las cuales se ejecuta en una máquina virtual (*Prefix*-ERCS01, *Prefix*-ERCS02 o *Prefix*-ERCS03) en diferentes hosts para proporcionar resistencia.

Antes de comenzar este procedimiento en un sistema integrado, asegúrese de que puede acceder a un PEP bien mediante la dirección IP o a través de DNS. Después de la implementación inicial de Azure Stack Hub, solo puede acceder al PEP mediante la dirección IP, dado que la integración de DNS no está configurada aún. El proveedor de hardware OEM le proporcionará un archivo JSON denominado **AzureStackStampDeploymentInfo** que contiene las direcciones IP del PEP.

También puede encontrar la dirección IP en el portal de administración de Azure Stack Hub. Abra el portal, por ejemplo, `https://adminportal.local.azurestack.external`. Seleccione **Region Management (Administración de regiones)**  > **Properties (Propiedades)** .

Tendrá que establecer la configuración de la referencia cultural actual en `en-US` al ejecutar el punto de conexión con privilegios; de lo contrario, los cmdlets como Test-AzureStack o Get-AzureStackLog no funcionarán según lo esperado.

> [!NOTE]
> Por motivos de seguridad, es necesario conectarse al PEP solo desde una máquina virtual protegida que se ejecute en el host de ciclo de vida de hardware, o desde un equipo seguro dedicado, como una [estación de trabajo de acceso con privilegios](https://docs.microsoft.com/windows-server/identity/securing-privileged-access/privileged-access-workstations). No se debe modificar la configuración original del host de ciclo de vida de hardware, (incluida la instalación de software nuevo) ni usarse para la conexión al PEP.

1. Establezca la confianza.

      - En un sistema integrado, ejecute el siguiente comando desde una sesión de Windows PowerShell con privilegios elevados para agregar el PEP como host de confianza a la máquina virtual protegida que se ejecuta en el host de ciclo de vida de hardware o en la estación de trabajo de acceso con privilegios.

      ```powershell  
        winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
      ```

      - Si va a ejecutar el ADSK, inicie sesión en el host del kit de desarrollo.

2. En la máquina virtual protegida que se ejecuta en el host de ciclo de vida de hardware o en la estación de trabajo de acceso con privilegios, abra una sesión de Windows PowerShell. Ejecute los comandos siguientes para establecer una sesión remota en la máquina virtual que hospeda el PEP:
 
  - En un sistema integrado:

    ```powershell  
    $cred = Get-Credential

    $pep = New-PSSession -ComputerName <IP_address_of_ERCS> -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
    Enter-PSSession $pep
    ```
    
    El parámetro `ComputerName` puede ser la dirección IP o el nombre DNS de una de las máquinas virtuales que hospeda el PEP.

    > [!NOTE]  
    >Azure Stack Hub no realiza ninguna llamada remota al validar la credencial del PEP. Se basa en una clave pública RSA almacenada localmente para hacerlo.

   - Si va a ejecutar el ADSK:

     ```powershell  
      $cred = Get-Credential
    
      $pep = New-PSSession -ComputerName azs-ercs01 -ConfigurationName PrivilegedEndpoint -Credential $cred -SessionOption (New-PSSessionOption -Culture en-US -UICulture en-US)
      Enter-PSSession $pep
     ```
    
   - Cuando se le pida, utilice las siguientes credenciales:
   
       - **Nombre de usuario**: especifique la cuenta CloudAdmin con el formato **&lt;*dominio de Azure Stack Hub*&gt;\cloudadmin**. (Para ASDK, el nombre de usuario es **azurestack\cloudadmin**).
  
        - **Contraseña**: escriba la misma contraseña que proporcionó durante la instalación de la cuenta del administrador de dominio de AzureStackAdmin.

      > [!NOTE]
      > Si no puede conectarse al punto de conexión de ERCS, vuelva a intentar los pasos uno y dos con otra dirección IP de la máquina virtual de ERCS.

3. Después de conectarse, el símbolo del sistema cambia a **[*dirección IP o nombre de VM de ERCS*]: PS >** o a **[azs-ercs01]: PS >** , en función del entorno. Desde aquí, ejecute `Get-Command` para ver la lista de los cmdlets disponibles.

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

## <a name="tips-for-using-the-privileged-endpoint"></a>Sugerencias para el uso del punto de conexión con privilegios 

Tal y como se mencionó anteriormente, el PEP es un punto de conexión de [PowerShell JEA](https://docs.microsoft.com/powershell/scripting/learn/remoting/jea/overview). Al proporcionar una capa de seguridad sólida, un punto de conexión de JEA reduce algunas de las funcionalidades básicas de PowerShell, como la finalización con tabulación o de scripting. Si intenta algún tipo de operación de scripts, se producirá el error **ScriptsNotAllowed**. Este error es el comportamiento esperado.

Por ejemplo, para obtener la lista de parámetros de un cmdlet determinado, debe ejecutar el siguiente comando:

```powershell
    Get-Command <cmdlet_name> -Syntax
```

Como alternativa, puede usar el cmdlet [Import-PSSession](https://docs.microsoft.com/powershell/module/Microsoft.PowerShell.Utility/Import-PSSession?view=powershell-5.1) para importar todos los cmdlets de PEP a la sesión actual en al equipo local. De esta forma, todos los cmdlets y las funciones del PEP ahora están disponibles en el equipo local, junto con la finalización con tabulación y, más en general, de scripting.

Lleve a cabo los siguientes pasos para importar la sesión del PEP al equipo local:

1. Establezca la confianza.

    - En un sistema integrado, ejecute el siguiente comando desde una sesión de Windows PowerShell con privilegios elevados para agregar el PEP como host de confianza a la máquina virtual protegida que se ejecuta en el host de ciclo de vida de hardware o en la estación de trabajo de acceso con privilegios.

    ```powershell
    winrm s winrm/config/client '@{TrustedHosts="<IP Address of Privileged Endpoint>"}'
    ```

    - Si va a ejecutar el ADSK, inicie sesión en el host del kit de desarrollo.

2. En la máquina virtual protegida que se ejecuta en el host de ciclo de vida de hardware o en la estación de trabajo de acceso con privilegios, abra una sesión de Windows PowerShell. Ejecute los comandos siguientes para establecer una sesión remota en la máquina virtual que hospeda el PEP:

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

3. Importe la sesión PEP a la máquina local:

    ```powershell 
      Import-PSSession $session
    ```

4. Ahora, puede usar la finalización con tabulación y realizar scripting como de costumbre en la sesión de PowerShell local con todas las funciones y cmdlets del PEP sin reducir la posición de seguridad de Azure Stack Hub. ¡Disfrute!

## <a name="close-the-privileged-endpoint-session"></a>Cierre de la sesión del punto de conexión con privilegios

 Como se mencionó anteriormente, el PEP registra cada una de las acciones (y su salida correspondiente) que realiza en la sesión de PowerShell. Debe cerrar la sesión mediante el cmdlet `Close-PrivilegedEndpoint`. Este cmdlet cierra correctamente el punto de conexión y transfiere los archivos de registro a un recurso compartido de archivos externo donde se retienen.

Para cerrar la sesión del punto de conexión:

1. Cree un recurso compartido de archivos externo al que se pueda acceder mediante el PEP. En un entorno de kit de desarrollo, solo puede crear un recurso compartido de archivos en el host del kit de desarrollo.
2. Ejecute el siguiente cmdlet:

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


## <a name="next-steps"></a>Pasos siguientes

[Herramientas de diagnóstico de Azure Stack Hub](azure-stack-configure-on-demand-diagnostic-log-collection.md#use-the-privileged-endpoint-pep-to-collect-diagnostic-logs)
