---
title: Actualización 1802 de Azure Stack | Microsoft Docs
description: Conozca el contenido de la actualización 1802 de los sistemas integrados de Azure Stack, los problemas conocidos y la ubicación dónde debe descargarse la actualización.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/30/2018
ms.author: brenduns
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: b9334a3079a767f723240946af8091508204b8ae
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248766"
---
# <a name="azure-stack-1802-update"></a>Actualización 1802 de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describen las mejoras y correcciones de este paquete de actualización 1802, los problemas conocidos de esta versión y la ubicación donde debe descargarse la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]        
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación    
El número de compilación de la actualización 1802 de Azure Stack es **20180302.1**.  


## <a name="before-you-begin"></a>Antes de empezar    
> [!IMPORTANT]    
> No intente crear máquinas virtuales durante la instalación de esta actualización. Para más información sobre cómo administrar las actualizaciones, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).


### <a name="prerequisites"></a>Requisitos previos
- Antes de aplicar la actualización 1802 de Azure Stack, instale la actualización 1712.    

- Antes de aplicar la actualización 1802 de Azure Stack, instale **AzS Hotfix 1.0.180312.1- Build 20180222.2**. Esta revisión actualiza Windows Defender, y está disponible al descargar actualizaciones para Azure Stack.

  Para instalar la revisión, siga los procedimientos normales para [instalar actualizaciones de Azure Stack](../azure-stack-apply-updates.md). El nombre de la actualización aparece como **AzS Hotfix 1.0.180312.1** e incluye los siguientes archivos: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2 1.bin
    - Metadata.xml

  Después de cargar estos archivos en una cuenta de almacenamiento y un contenedor, ejecute la instalación desde el icono de actualización en el portal de administración. 
  
  A diferencia de las actualizaciones de Azure Stack, al instalar esta actualización no cambia la versión de Azure Stack.  Para confirmar que esta actualización está instalada, vea la lista de **actualizaciones instaladas**.
 


### <a name="post-update-steps"></a>Pasos posteriores a la actualización
Después de la instalación de 1802, instale todas las revisiones aplicables. Para más información, consulte los siguientes artículos de la Knowledge base, así como nuestra [Directiva de mantenimiento](../azure-stack-servicing-policy.md). 
- Azure Stack, revisión **1.0.180302.4**. [KB 4131152: No se pueden escalar conjuntos de escalado de máquinas virtuales existentes después de instalar la actualización 1802 de Azure Stack]( https://support.microsoft.com/help/4131152). 

  Esta corrección también resuelve los problemas que se detallan en [KB 4103348: Errores del servicio de API de controlador de red cuando intenta instalar una actualización de Azure Stack](https://support.microsoft.com/help/4103348).


### <a name="new-features-and-fixes"></a>Nuevas características y correcciones
Esta actualización incluye las siguientes correcciones y mejoras para Azure Stack.

- **Se ha agregado compatibilidad con las siguientes versiones de API de servicio de Azure Storage**:
    - 17-04-2017 
    - 31-05-2016 
    - 11-12-2015 
    - 08-07-2015 
    
    Para más información, consulte [Azure Stack Storage: Diferencias y consideraciones](../../user/azure-stack-acs-differences.md).

- **Compatibilidad con [blobs en bloques](../../user/azure-stack-acs-differences.md)** más grandes:
    - El tamaño máximo de bloque permitido ha aumentado de 4 MB a 100 MB.
    - El tamaño máximo de blob ha aumentado de 195 GB a 4,75 TB.  

- La **copia de seguridad de infraestructura** aparece ahora en el icono de proveedores de recursos y están habilitadas alertas de copia de seguridad. Para más información sobre el servicio Copia de seguridad de infraestructura, consulte [Copia de seguridad y recuperación de datos para Azure Stack con el servicio Copia de seguridad de infraestructura](../azure-stack-backup-infrastructure-backup.md).

- **Actualice al cmdlet *Test-AzureStack* para** mejorar el diagnóstico de almacenamiento. Para más información sobre este cmdlet, consulte [Validación de Azure Stack](../azure-stack-diagnostic-test.md).

- **Mejoras de control de acceso (RBAC) basado en roles**: ahora puede usar RBAC para delegar permisos en grupos de usuarios universales cuando se implementa Azure Stack con AD FS. Para más información sobre RBAC, consulte [Administración de RBAC](../azure-stack-manage-permissions.md).

- **Se agregó compatibilidad con varios dominios de error**.  Para más información, consulte High availability for Azure Stack (Alta disponibilidad para Azure Stack).

- **Compatibilidad con las actualizaciones de la memoria física**: ahora puede ampliar la capacidad de memoria del sistema integrado de Azure Stack después de la implementación inicial. Para más información, consulte [Administración de la capacidad de memoria física para Azure Stack](../azure-stack-manage-storage-physical-memory-capacity.md).

- **Varias revisiones** de rendimiento, estabilidad, seguridad y el sistema operativo que se usa en Azure Stack.

<!-- #### New features -->

<!-- #### Fixes -->

### <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización    
*No hay ningún problema conocido con la instalación de la actualización 1802.*


### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)
Los siguientes son problemas conocidos posteriores a la instalación de la compilación **20180302.1**

#### <a name="portal"></a>Portal
<!-- 2332636 - IS -->  
-  Cuando usa AD FS para el sistema de identidad de Azure Stack y actualiza a esta versión de Azure Stack, el propietario predeterminado de la suscripción del proveedor predeterminado se restablece al usuario **CloudAdmin** integrado.  
  Solución alternativa: para resolver este problema después de instalar esta actualización, use el paso 3 del procedimiento [Automatización de desencadenador para configurar la relación de confianza de proveedor de notificaciones en Azure Stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) para restablecer el propietario de la suscripción de proveedor predeterminado.   

- No está disponible la posibilidad de [abrir una nueva solicitud de soporte técnico desde la lista desplegable](../azure-stack-manage-portals.md#quick-access-to-help-and-support) del portal de administración. En su lugar, use el siguiente vínculo:     
    - Para sistemas integrados de Azure Stack. use https://aka.ms/newsupportrequest.

<!-- 2050709 --> 
-  En el portal de administración no se pueden editar las métricas de almacenamiento de Blob service, Table service o Queue service. Si va a Storage y selecciona el icono de Blob service, Table service o Queue service, se abre una hoja nueva que muestra una tabla de métricas de dicho servicio. Si luego selecciona Editar en la parte superior del icono del gráfico de métricas, se abre la hoja Editar gráfico, pero no muestra las opciones para editar las métricas.

- No es posible ver los recursos de proceso o almacenamiento en el portal de administración. La causa de este problema es un error durante la instalación de la actualización que hace que se notifique como correcta erróneamente. Si se produce este problema, póngase en contacto con los servicios de soporte técnico de Microsoft para obtener ayuda.

- Puede que vea un panel en blanco en el portal. Para recuperar el panel, seleccione el icono de engranaje en la esquina superior derecha del portal y, a continuación, seleccione **Restaurar configuración predeterminada**.

- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y, a continuación, elimine las suscripciones del usuario.

- No puede ver los permisos de la suscripción mediante los portales de Azure Stack. Como alternativa, use PowerShell para comprobar los permisos.

- En el panel del portal de administración, el icono de actualización no muestra información sobre las actualizaciones. Para resolver este problema, haga clic en el icono para actualizarlo.

-   En el portal de administración, puede que vea una alerta crítica del componente Microsoft.Update.Admin. El nombre de la alerta, la descripción y la solución se muestran como:  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERROR: falta la plantilla de FaultType ResourceProviderTimeout)

    Esta alerta puede omitirse sin problemas. 

<!-- 2253274 --> 
- En los portales de administración y de usuario, la hoja Configuración para subredes vNet no se carga. Como solución, use PowerShell y el cmdlet [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0&preserve-view=true) para ver y administrar esta información.

- En el portal de administración y el portal de usuarios, la hoja de información general no puede cargarse al seleccionarla para las cuentas de almacenamiento que se crearon con una versión anterior de la API (ejemplo: 15-06-2015). Esto incluye las cuentas de almacenamiento del sistema como **updateadminaccount** que se usan durante la revisión y actualización. 

  Como alternativa, use PowerShell para ejecutar el script **Start-ResourceSynchronization.ps1** para restaurar el acceso a los detalles de la cuenta de almacenamiento. [El script está disponible desde GitHub](https://github.com/Azure/AzureStack-Tools/tree/master/Support/scripts) y debe ejecutarse con las credenciales de administrador del servicio en el punto de conexión privilegiado. 

- La hoja de **Service Health** no se carga. Cuando se abre la hoja de Service Health en el portal del administrador o de usuario, Azure Stack muestra un error y no carga información. Este comportamiento es normal. Si bien es posible seleccionar y abrir Service Health, esta característica no está disponible aún, aunque se implementará en una versión futura de Azure Stack.


#### <a name="health-and-monitoring"></a>Estado y supervisión
<!-- 1264761 - IS ASDK -->  
-  Es posible que vea alertas del componente *Controlador de mantenimiento* con los siguientes detalles:  

  Alerta 1:
   - NOMBRE: rol de infraestructura incorrecto
   - GRAVEDAD: advertencia
   - COMPONENTE: controlador de mantenimiento
   - DESCRIPCIÓN: el controlador de mantenimiento Heartbeat Scanner no está disponible. Esto puede afectar a los informes y a las métricas de mantenimiento.  

  Alerta 2:
   - NOMBRE: rol de infraestructura incorrecto
   - GRAVEDAD: advertencia
   - COMPONENTE: controlador de mantenimiento
   - DESCRIPCIÓN: el controlador de mantenimiento Fault Scanner no está disponible. Esto puede afectar a los informes y a las métricas de mantenimiento.

  Ambas alertas pueden omitirse sin problemas. Se cerrarán automáticamente pasado un tiempo.  


#### <a name="marketplace"></a>Marketplace
- Los usuarios pueden examinar toda la plataforma Marketplace sin una suscripción y ver elementos administrativos, como planes y ofertas. Estos elementos no funcionan para los usuarios.

#### <a name="compute"></a>Proceso
- Los valores de escalado para conjuntos de escalas de máquina virtual no están disponibles en el portal. Como alternativa, puede usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Debido a diferencias en la versión de PowerShell, debe usar el parámetro `-Name` en lugar de `-VMScaleSetName`.

<!-- 2290877  --> 
- No se puede escalar verticalmente un conjunto de escalado de máquinas virtuales (VMSS) que se creó al usar Azure Stack antes de la versión 1802. Esto se debe al cambio en la compatibilidad con el uso de conjuntos de disponibilidad con conjuntos de escalado de máquinas virtuales. Esta compatibilidad se ha agregado con la versión 1802.  Al intentar agregar más instancias para escalar un VMSS creado antes de que se agregara esta compatibilidad, se produce un error en la acción con el mensaje *Error en el estado de aprovisionamiento*. 

  Este problema se ha corregido en la versión 1803. Para resolver este problema para la versión 1802, instale la revisión de Azure Stack **1.0.180302.4**. Para más información, consulte el artículo [KB 4131152: No se pueden escalar conjuntos de escalado de máquinas virtuales existentes después de instalar la actualización 1802 de Azure Stack](https://support.microsoft.com/help/4131152). 

- Azure Stack solo admite el uso de discos duros virtuales de tipo fijo. Algunas imágenes ofrecidas mediante Marketplace en Azure Stack usan discos duros virtuales dinámicos, pero estas se han quitado. El hecho de cambiar el tamaño de una máquina virtual (VM) que lleva asociado un disco dinámico deja a la máquina en estado de error.

  Para mitigar este problema, elimine la máquina virtual sin eliminar el disco de la máquina virtual, un blob de disco duro virtual de una cuenta de almacenamiento. A continuación, convierta el disco duro virtual dinámico a fijo y vuelva a crear la máquina virtual.

-  Cuando crea un conjunto de disponibilidad en el portal en **Nuevo** > **Compute** > **Conjunto de disponibilidad**, solo puede crear uno con un dominio de error y un dominio de actualización de 1. Como alternativa, al crear una nueva máquina virtual, cree el conjunto de disponibilidad mediante PowerShell, la CLI o el portal.

- Al crear máquinas virtuales en el portal de usuario de Azure Stack, el portal muestra un número incorrecto de discos de datos que se pueden asociar a una máquina virtual de la serie D. Todas las máquinas virtuales de la serie D pueden albergar tantos discos de datos como la configuración de Azure.

- Cuando no es posible crear una imagen de máquina virtual, los elementos con error que no se pueden eliminar se podrían agregar a la hoja de proceso de imágenes de máquina virtual.

  Como alternativa, cree una nueva imagen de máquina virtual con un disco duro virtual ficticio que se pueda crear mediante Hyper-V (nuevo VHD, ruta de acceso C:\dummy.vhd, fijo, bytes de tamaño 1 GB). Este proceso debería corregir el problema que impide que se elimine el elemento con error. A continuación, 15 minutos después de crear la imagen ficticia, puede eliminarlo correctamente.

  Luego puede volver a intentar cargar la imagen de máquina virtual que anteriormente produjo un error.

-  Si aprovisionar una extensión en una implementación de máquina virtual tarda demasiado tiempo, los usuarios deberían dejar que se agote el tiempo de espera de aprovisionamiento en lugar de intentar detener el proceso para desasignar o eliminar la máquina virtual.  

<!-- 1662991 --> 
- No se admite el diagnóstico de máquinas virtuales Linux en Azure Stack. Si implementa una máquina virtual Linux con diagnósticos de máquina virtual habilitado, se producirá un error en la implementación. Tampoco se podrá realizar la implementación si habilita las métricas básicas de máquina virtual Linux a través de la configuración de diagnóstico.  




#### <a name="networking"></a>Redes
- Después de crear una máquina virtual y asociarla a una dirección IP pública, no puede desasociar esa máquina virtual de esa dirección IP. Puede que parezca que se ha desasociado, pero la dirección IP pública anteriormente asignada permanece asociada a la máquina virtual original.

  Actualmente, solo debe usar nuevas direcciones IP públicas para las nuevas máquinas virtuales creadas.

  Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva.

- El equilibrio de carga interno (ILB) administra incorrectamente las direcciones MAC de máquinas virtuales de back-end, lo que hace que se interrumpa cuando se usan instancias de Linux en la red de back-end.  ILB funciona bien con instancias de Windows en la red de back-end.

-   La característica de reenvío IP está visible en el portal; sin embargo, no ocurre nada al habilitar esta característica. El motivo es que aún no se admite.

- Azure Stack admite una única *puerta de enlace de red local* por dirección IP. Y esto se aplica a las suscripciones de todos los inquilinos. Tras la creación de la primera conexión a la puerta de enlace de red local, los sucesivos intentos para crear un recurso de puerta de enlace de red local con la misma dirección IP se bloquean.

-  En una red virtual que se creó con una configuración de servidor DNS de *Automática*, se produce un error al cambiar a un servidor DNS personalizado. La configuración actualizada no se inserta en las máquinas virtuales de esa red virtual.

- Azure Stack no admite la adición de interfaces de red adicionales a una instancia de máquina virtual una vez implementada la máquina virtual. Si la máquina virtual requiere más de una interfaz de red, se deben definir en el momento de la implementación.

<!-- 2096388 --> 
- No se puede usar el portal de administración para actualizar las reglas de un grupo de seguridad de red. 

    Solución alternativa para App Service: si tiene que establecer una conexión de Escritorio remoto a las instancias del controlador, modifique las reglas de seguridad de los grupos de seguridad de red con PowerShell.  Estos son algunos ejemplos de cómo *permitir* y, luego, restaurar la configuración a *denegar*:  
    
    - *Permitir:*
 
      ```powershell    
      Login-AzureRMAccount -EnvironmentName AzureStackAdmin
      
      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
      
      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
      
      ##This doesn�t work. Need to set properties again even in case of edit
      
      #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
      
      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
      
      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
      ```

    - *Denegar:*

        ```powershell
        
        Login-AzureRMAccount -EnvironmentName AzureStackAdmin
        
        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"
        
        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"
        
        ##This doesn�t work. Need to set properties again even in case of edit
    
        #Set-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389" -NetworkSecurityGroup $nsg -Access Allow  
    
        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange
          
        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg 
        ```





#### <a name="sql-and-mysql"></a>SQL y MySQL
 - Antes de continuar, revise la nota importante en [Antes de empezar](#before-you-begin) cerca del comienzo de estas notas de la versión.
- Puede pasar una hora antes de que los usuarios puedan crear bases de datos en una nueva implementación de SQL o MySQL.

- Solo el proveedor de recursos puede crear elementos en servidores que hospedan SQL o MySQL. Los elementos creados en un servidor host que no se crean con el proveedor de recursos podrían dar lugar a un error de coincidencia de estado.  

<!-- IS, ASDK --> 
- Los caracteres especiales, entre los que se incluyen los espacios y los puntos, no se admiten en el nombre de **familia** al crear una SKU para los proveedores de recursos de SQL y MySQL.

> [!NOTE]  
> Después de actualizar a Azure Stack 1802, puede seguir usando los proveedores de recursos SQL y MySQL que implementó anteriormente.  Le recomendamos que actualice SQL y MySQL cuando haya disponible una nueva versión. Al igual que Azure Stack, aplique las actualizaciones a los proveedores de recursos SQL y MySQL de manera secuencial.  Por ejemplo, si usa la versión 1710, aplique primero la versión 1711, luego la versión 1712 y luego actualice a la 1802.      
>   
> La instalación de la actualización 1802 no afecta el uso actual de los proveedores de recursos SQL o MySQL por parte de los usuarios.
> Con independencia de la versión que use de los proveedores de recursos, los datos de los usuarios de sus bases de datos no se tocan, y permanecen accesibles.    


#### <a name="app-service"></a>App Service
- Los usuarios deben registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.

- Para escalar horizontalmente la infraestructura (roles de trabajo, administración y front-end), debe usar PowerShell, tal como se describe en las notas de la versión de proceso.

<!-- #### Identity -->

#### <a name="downloading-azure-stack-tools-from-github"></a>Descarga de las herramientas de Azure Stack desde GitHub
- Al usar el cmdlet *invoke-webrequest* de PowerShell para descargar las herramientas de Azure Stack de Github, recibirá un error:     
    -  *invoke-webrequest : The request was aborted: Could not create SSL/TLS secure channel.* (invoke-webrequest: anulada la solicitud, no se pudo crear un canal seguro SSL/TLS)     

  Este error se produce debido a una degradación reciente de la compatibilidad con GitHub de los estándares criptográficos Tlsv1 y Tlsv1.1 (los predeterminados de PowerShell). Para más información, consulte [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/) (Aviso de eliminación de estándares criptográficos débiles).

  Para resolver este problema, agregue `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` a la parte superior del script para obligar a la consola de PowerShell a usar TLSv1.2 cuando se descargue de los repositorios de GitHub.


## <a name="download-the-update"></a>Descarga de la actualización
Puede descargar la actualización 1802 de Azure Stack [aquí](https://aka.ms/azurestackupdatedownload).


## <a name="more-information"></a>Más información
Microsoft proporcionó una manera de supervisar y reanudar las actualizaciones mediante el punto de conexión con privilegios (PEP) que se instaló con la actualización 1710.

- Consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](https://docs.microsoft.com/azure/azure-stack/azure-stack-monitor-update).

## <a name="see-also"></a>Vea también

- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](../azure-stack-apply-updates.md).