---
title: Notas de la versión del Kit de desarrollo de Microsoft Azure Stack | Microsoft Docs
description: Mejoras, correcciones y problemas conocidos del Kit de desarrollo de Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/04/2019
ms.author: sethm
ms.reviewer: misainat
ms.lastreviewed: 06/04/2019
ms.openlocfilehash: 2ca85da5d9fde42fb06eef149e7304ab08bc32ee
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691200"
---
# <a name="asdk-release-notes"></a>Notas de la versión del Kit de desarrollo de Azure Stack

En este artículo se proporciona información sobre los cambios, correcciones y problemas conocidos del Kit de desarrollo de Azure Stack (ASDK). Si no está seguro de qué versión se está ejecutando, puede usar el [portal de administración](../operator/azure-stack-updates.md#determine-the-current-version).

Para estar al día de las novedades del ASDK, suscríbase a la [![RSS](./media/asdk-release-notes/feed-icon-14x14.png)](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#) [fuente RSS](https://docs.microsoft.com/api/search/rss?search=Azure+Stack+Development+Kit+release+notes&locale=en-us#).

## <a name="build-11905040"></a>Compilación 1.1905.0.40

<!-- ### Changes -->

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de las nuevas características de esta versión, consulte [esta sección](../operator/azure-stack-release-notes-1905.md#whats-in-this-update) de las notas de la versión de Azure Stack.

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- Debido a un tiempo de espera de la entidad de servicio mientras se ejecutaba el script de registro, para [registrar el ASDK](asdk-register.md) correctamente, debe editar el script de PowerShell **RegisterWithAzure.psm1**. Haga lo siguiente:

  1. En el equipo host de ASDK, abra el archivo **C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** en un editor con permisos elevados.
  2. En la línea 1249, agregue un parámetro `-TimeoutInSeconds 1800` al final. Esto es necesario debido a un tiempo de espera de entidad de servicio al ejecutar el script de registro. Ahora, la línea 1249 debe tener un aspecto similar al siguiente:

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

- Para ver una lista de otros problemas resueltos de Azure Stack en esta versión, consulte [esta sección](../operator/azure-stack-release-notes-1905.md#fixes) de las notas de la versión de Azure Stack.
- Para ver una lista de problemas conocidos, consulte [este artículo](../operator/azure-stack-release-notes-known-issues-1905.md).
- Tenga en cuenta que [las revisiones disponibles de Azure Stack](../operator/azure-stack-release-notes-1905.md#hotfixes) no son aplicables a Azure Stack ASDK.

## <a name="build-11904036"></a>Compilación 1.1904.0.36

<!-- ### Changes -->

### <a name="new-features"></a>Nuevas características

- Para obtener una lista de las nuevas características de esta versión, consulte [esta sección](../operator/azure-stack-release-notes-1904.md#whats-in-this-update) de las notas de la versión de Azure Stack.

### <a name="fixed-and-known-issues"></a>Problemas conocidos y resueltos

- Debido a un tiempo de espera de la entidad de servicio mientras se ejecutaba el script de registro, para [registrar el ASDK](asdk-register.md) correctamente, debe editar el script de PowerShell **RegisterWithAzure.psm1**. Haga lo siguiente:

  1. En el equipo host de ASDK, abra el archivo **C:\AzureStack-Tools-master\Registration\RegisterWithAzure.psm1** en un editor con permisos elevados.
  2. En la línea 1249, agregue un parámetro `-TimeoutInSeconds 1800` al final. Esto es necesario debido a un tiempo de espera de entidad de servicio al ejecutar el script de registro. Ahora, la línea 1249 debe tener un aspecto similar al siguiente:

     ```powershell
      $servicePrincipal = Invoke-Command -Session $PSSession -ScriptBlock { New-AzureBridgeServicePrincipal -RefreshToken $using:RefreshToken -AzureEnvironment $using:AzureEnvironmentName -TenantId $using:TenantId -TimeoutInSeconds 1800 }
      ```

- Se ha corregido el problema de conexión de VPN identificado [aquí, en la versión 1902](#known-issues).

- Para ver una lista de otros problemas resueltos de Azure Stack en esta versión, consulte [esta sección](../operator/azure-stack-release-notes-1904.md#fixes) de las notas de la versión de Azure Stack.
- Para ver una lista de problemas conocidos, consulte [este artículo](../operator/azure-stack-release-notes-known-issues-1904.md).
- Tenga en cuenta que [las revisiones disponibles de Azure Stack](../operator/azure-stack-release-notes-1904.md#hotfixes) no son aplicables a Azure Stack ASDK.

## <a name="build-1903"></a>Compilación 1903

La carga de 1903 no incluye una versión de ASDK.

## <a name="build-11902069"></a>Compilación 1.1902.0.69

### <a name="new-features"></a>Nuevas características

- La compilación 1902 presenta una nueva interfaz de usuario en el portal de administrador de Azure Stack para crear planes, ofertas, cuotas y planes complementarios. Para obtener más información, incluidas las capturas de pantalla, consulte la documentación sobre [Crear planes, ofertas y cuotas](../operator/azure-stack-create-plan.md).

- Para obtener una lista de otros cambios y mejoras de esta versión, consulte [esta sección](../operator/azure-stack-update-1902.md#improvements) de las notas de la versión de Azure Stack.

<!-- ### New features

- For a list of new features in this release, see [this section](../operator/azure-stack-update-1902.md#new-features) of the Azure Stack release notes.

### Fixed and known issues

- For a list of issues fixed in this release, see [this section](../operator/azure-stack-update-1902.md#fixed-issues) of the Azure Stack release notes. For a list of known issues, see [this section](../operator/azure-stack-update-1902.md#known-issues-post-installation).
- Note that [available Azure Stack hotfixes](../operator/azure-stack-update-1902.md#azure-stack-hotfixes) are not applicable to the Azure Stack ASDK. -->

### <a name="known-issues"></a>Problemas conocidos

- Existe un problema al establecer una conexión VPN desde otro host en el ASDK mediante los pasos descritos en [este artículo](asdk-connect.md). Cuando un cliente VPN intenta conectarse al entorno de ASDK, aparece un error que dice que **el nombre de usuario o la contraseña no son correctos**. Este error se produce incluso si está seguro de haber usado la cuenta correcta y de haber escrito la contraseña correctamente. El problema no tiene que ver con sus credenciales, sino con un cambio en el protocolo de autenticación usado para la conexión VPN en el ASDK. Para resolver este problema, siga estos pasos:

   En primer lugar, realice cambios en el protocolo de autenticación usado en el servidor ASDK:

   1. RDP al host de ASDK.
   2. Abra una sesión de PowerShell con privilegios elevados e inicie sesión con AzureStack\AzureStackAdmin mediante la contraseña que proporcionó en el momento de la implementación.
   3. Ejecute los comandos siguientes:

      ```powershell
      netsh nps set np name = "Connections to Microsoft Routing and Remote Access server" profileid = "0x100a" profiledata = "1A000000000000000000000000000000" profileid = "0x1009" profiledata = "0x5"
      restart-service remoteaccess -force
      ```

   A continuación, modifique el script de conexión del cliente. La manera más sencilla de hacerlo es cambiar directamente el módulo del script C:\AzureStack-Tools-master\connect\azurestack.connect.psm1:

   1. Modifique el cmdlet **Add-AzsVpnConnection** para cambiar el parámetro `AuthenticationMethod` de `MsChapv2` a `EAP`:

      ```powershell
      $connection = Add-VpnConnection -Name $ConnectionName -ServerAddress $ServerAddress -TunnelType L2tp -EncryptionLevel Required -AuthenticationMethod Eap -L2tpPsk $PlainPassword -Force -RememberCredential -PassThru -SplitTunneling
      ```

   2. Cambie el cmdlet **Connect-AzsVpn** para que deje de usar `rasdial @ConnectionName $User $PlainPassword` y use `rasphone`, dado que EAP requiere inicio de sesión interactivo:

      ```powershell
      rasphone $ConnectionName
      ```

   3. Guarde los cambios y, luego, vuelva a importar el módulo **azurestack.connect.psm1**.
   4. Siga las instrucciones de [este artículo](asdk-connect.md#set-up-vpn-connectivity).
   5. Cuando se conecte al ASDK mediante VPN, en Windows, vaya a **Configuración de red e Internet** y, luego, a **VPN**, en lugar de conectarse desde la barra de tareas, para así tener la seguridad de que se le pidan las credenciales.

- Se identificó un problema en el que se descartaron los paquetes de más de 1450 bytes para un equilibrador de carga interno (ILB). El problema se debe a la configuración de MTU en el host, cuyo valor es demasiado bajo para hospedar los paquetes VXLAN encapsulados que atraviesan el rol y que, a partir de la compilación 1901, se movieron al host. Hay al menos dos escenarios que se pueden dar y en los que hemos visto como se manifiesta este problema:

  - Consultas SQL a la solución Always On de SQL que están detrás de un equilibrador de carga interno (ILB) y que tienen más de 660 bytes.
  - Las implementaciones de Kubernetes no se realizan correctamente si intenta habilitar varios maestros.  

  El problema se produce cuando establece comunicación entre una VM y un ILB en la misma red virtual, pero en diferentes subredes. Para solucionar este problema, puede ejecutar los siguientes comandos en un símbolo del sistema con privilegios elevados en el host de ASDK:

  ```shell
  netsh interface ipv4 set sub "hostnic" mtu=1660
  netsh interface ipv4 set sub "management" mtu=1660
  ```
