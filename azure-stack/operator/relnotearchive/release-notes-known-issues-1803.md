---
title: Actualización 1803 de Azure Stack | Microsoft Docs
description: Conozca el contenido de la actualización 1803 de los sistemas integrados de Azure Stack, los problemas conocidos y la ubicación dónde debe descargarse la actualización.
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
ms.date: 07/11/2018
ms.author: brenduns
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 6f15453ce20988929728a9d326ab5f7bf901beca
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248757"
---
# <a name="azure-stack-1803-update"></a>Actualización de Azure Stack 1803

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describen las mejoras y correcciones de esta actualización 1803, los problemas conocidos de esta versión y la ubicación donde debe descargarse la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]        
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación    
El número de compilación de la actualización de Azure Stack 1803 es **20180329.1**.


## <a name="before-you-begin"></a>Antes de empezar    
> [!IMPORTANT]    
> No intente crear máquinas virtuales durante la instalación de esta actualización. Para más información sobre cómo administrar las actualizaciones, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).


### <a name="prerequisites"></a>Requisitos previos
- Antes de aplicar la actualización de Azure Stack 1803, instale la actualización 1802.   

- Antes de aplicar la actualización 1803 de Azure Stack, instale **AzS Hotfix – 1.0.180312.1- Build 20180222.2**. Esta revisión actualiza Windows Defender, y está disponible al descargar actualizaciones para Azure Stack.

  Para instalar la revisión, siga los procedimientos normales para [instalar actualizaciones de Azure Stack](../azure-stack-apply-updates.md). El nombre de la actualización aparece como **AzS Hotfix 1.0.180312.1** e incluye los siguientes archivos: 
    - PUPackageHotFix_20180222.2-1.exe
    - PUPackageHotFix_20180222.2 1.bin
    - Metadata.xml

  Después de cargar estos archivos en una cuenta de almacenamiento y un contenedor, ejecute la instalación desde el icono de actualización en el portal de administración. 
  
  A diferencia de las actualizaciones de Azure Stack, al instalar esta actualización no cambia la versión de Azure Stack. Para confirmar que esta actualización está instalada, vea la lista de **actualizaciones instaladas**.



### <a name="new-features"></a>Nuevas características 
Esta actualización incluye las siguientes correcciones y mejoras para Azure Stack.

- **Actualización de los secretos de Azure Stack**: (cuentas y certificados). Para más información sobre cómo administrar los secretos, consulte [Cambio de secretos en Azure Stack](../azure-stack-rotate-secrets.md). 

<!-- 1914853 --> 
- **Redireccionamiento automático a HTTPS** cuando usa HTTP para acceder a los portales de usuario y administración. Esta mejora se realizó teniendo en cuenta los comentarios de [UserVoice](https://feedback.azure.com/forums/344565-azure-stack/suggestions/32205385-it-would-be-great-if-there-was-a-automatic-redirec) para Azure Stack. 

<!-- 2202621  --> 
- **Acceso a Marketplace**: ya puede abrir el Marketplace de Azure Stack usando la opción [+Nuevo](https://ms.portal.azure.com/#create/hub) desde los portales de usuario y administración, como lo haría en Azure Portal.
 
<!-- 2202621 --> 
- **Azure Monitor**: Azure Stack agrega [Azure Monitor](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-azure-monitor) a los portales de usuario y administración. Esto incluye nuevos exploradores de métricas y registros de actividad. Para acceder a esta instancia de Azure Monitor desde redes externas, el puerto **13012** debe estar abierto en configuraciones de firewall. Para más información acerca de los puertos que requiere Azure Stack, consulte [Integración del centro de datos de Azure Stack: Publicar puntos de conexión](../azure-stack-integrate-endpoints.md).

   Además, como parte de este cambio, en **Más servicios**, *Registros de auditoría* ahora aparece como *Registros de actividad*. Ahora la funcionalidad es coherente con Azure Portal. 

<!-- 1664791 --> 
- **Archivos dispersos**: cuando agregue una Nueva imagen a Azure Stack, o agregue una imagen a través de la difusión del Marketplace, dicha imagen se convertirá en un archivo disperso. Las imágenes que se agregaron antes de usar la versión 1803 de Azure Stack no se pueden convertir. En su lugar, debe utilizar la difusión del Marketplace para volver a enviar esas imágenes y aprovechar esta característica. 
 
   Los archivos dispersos son un formato de archivo eficaz que se usa para disminuir el uso del espacio de almacenamiento y mejorar la E/S. Para más información, consulte el artículo [Fsutil sparse](https://docs.microsoft.com/windows-server/administration/windows-commands/fsutil-sparse) para Windows Server. 

### <a name="fixed-issues"></a>Problemas corregidos

<!-- 1739988 --> 
-  El equilibrio de carga interno (ILB) ya administra correctamente las direcciones MAC de máquinas virtuales de back-end, lo que hace que descarte paquetes en la red de back-end cuando se usan instancias de Linux en la red de back-end. ILB funciona bien con instancias de Windows en la red de back-end. 

<!-- 1805496 --> 
-  Un problema por el que las conexiones VPN entre Azure Stack se desconectarían debido a que Azure Stack y Azure usan configuraciones diferentes para la política IKE. Los valores de SALifetime (tiempo) y SALiftetime (bytes) no eran compatibles con Azure y se modificaron en la versión 1803 para coincidir con la configuración de Azure. El valor de SALifetime (segundos) era 14,400 y, después de la versión 1803, es 27,000. El valor de SALifetime (bytes) era 819,200 y, después de la versión 1803, es 33,553,408.

<!-- 2209262 --> 
-  El problema de IP por el que las conexiones VPN estaban visibles en el portal; sin embargo, la activación o conmutación de Reenvío IP no tenía ningún efecto. La característica está activada de forma predeterminada y aún no se admite la posibilidad de cambiarla.  El control se ha eliminado del portal. 

<!-- 1766332 --> 
-  Azure Stack no admite VPN Gateways basadas en directivas, aunque la opción aparece en el portal.  La opción se ha eliminado del portal. 

<!-- 1868283 --> 
-  Azure Stack ahora previene el redimensionamiento de una máquina virtual que se crea con discos dinámicos. 

<!-- 1756324 --> 
-  Los datos de uso de las máquinas virtuales se separan ahora a intervalos de una hora. Esto es concuerda con Azure. 

<!--  2253274 --> 
-  El problema por el que, en los portales de administración y de usuario, la hoja Configuración para subredes vNet no se carga. Como solución, use PowerShell y el cmdlet [Get-AzureRmVirtualNetworkSubnetConfig](https://docs.microsoft.com/powershell/module/azurerm.network/get-azurermvirtualnetworksubnetconfig?view=azurermps-5.5.0&preserve-view=true) para ver y administrar esta información.

- Cuando crea una máquina virtual, el mensaje *No se pueden mostrar los precios* ya no aparece cuando se elige un tamaño de máquina virtual.

- Varias revisiones de rendimiento, estabilidad, seguridad y el sistema operativo que se usa en Azure Stack.


### <a name="changes"></a>Cambios
- La manera de cambiar el estado de una oferta recién creada de *privada* a *pública* o *retirada* ha cambiado. Para más información, consulte [Creación de una oferta](../azure-stack-create-offer.md).


### <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización    
<!-- 2328416 -->  Durante la instalación de la actualización 1803, puede haber tiempo de inactividad del servicio BLOB y de los servicios internos que usan el servicio BLOB. Esto incluye algunas operaciones de máquinas virtuales. Este tiempo de inactividad puede causar errores en las operaciones de los inquilinos o alertas de los servicios que no pueden acceder a los datos. Este problema se resuelve solo cuando la actualización completa la instalación. 



### <a name="post-update-steps"></a>Pasos posteriores a la actualización
- Después de la instalación de 1803, instale todas las revisiones aplicables. Para más información, consulte los siguientes artículos de la Knowledge base, así como nuestra [Directiva de mantenimiento](../azure-stack-servicing-policy.md).

  - [KB 4344115: Azure Stack, revisión 1.0.180427.15](https://support.microsoft.com/help/4344115).

- Después de instalar esta actualización, revise la configuración del firewall para asegurarse de que están abiertos los [puertos necesarios](../azure-stack-integrate-endpoints.md). Por ejemplo, esta actualización presenta *Azure Monitor*, que incluye un cambio de los registros de auditoría en los registros de actividad. Con este cambio, el puerto 13012 ahora se usa y también debe estar abierto.  


### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)
Los siguientes son problemas conocidos posteriores a la instalación de la compilación **20180323.2**.

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

- En el portal de administración, puede que vea una alerta crítica del componente *Microsoft.Update.Admin*. El nombre de la alerta, la descripción y la solución se muestran como:  
    - *ERROR - Template for FaultType ResourceProviderTimeout is missing.* (ERROR: falta la plantilla de FaultType ResourceProviderTimeout)

  Esta alerta puede omitirse sin problemas. 


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



- Azure Stack admite una única *puerta de enlace de red local* por dirección IP. Y esto se aplica a las suscripciones de todos los inquilinos. Tras la creación de la primera conexión a la puerta de enlace de red local, los sucesivos intentos para crear un recurso de puerta de enlace de red local con la misma dirección IP se bloquean.

-  En una red virtual que se creó con una configuración de servidor DNS de *Automática*, se produce un error al cambiar a un servidor DNS personalizado. La configuración actualizada no se inserta en las máquinas virtuales de esa red virtual.

- Azure Stack no admite la adición de interfaces de red adicionales a una instancia de máquina virtual una vez implementada la máquina virtual. Si la máquina virtual requiere más de una interfaz de red, se deben definir en el momento de la implementación.

<!-- 2096388 --> 
- No se puede usar el portal de administración para actualizar las reglas de un grupo de seguridad de red. 

    Solución alternativa para App Service: si tiene que establecer una conexión de Escritorio remoto a las instancias del controlador, modifique las reglas de seguridad de los grupos de seguridad de red con PowerShell.  Estos son algunos ejemplos de cómo *permitir* y, luego, restaurar la configuración a *denegar*:  
    
    - *Permitir:*
 
      ```powershell    
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Add-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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
> Después de actualizar a Azure Stack 1803, puede seguir usando los proveedores de recursos SQL y MySQL que implementó anteriormente.  Le recomendamos que actualice SQL y MySQL cuando haya disponible una nueva versión. Al igual que Azure Stack, aplique las actualizaciones a los proveedores de recursos SQL y MySQL de manera secuencial.  Por ejemplo, si usa la versión 1711, aplique primero la versión 1712, luego la versión 1802 y luego actualice a la 1803.      
>   
> La instalación de la actualización 1803 no afecta el uso actual de los proveedores de recursos SQL o MySQL por parte de los usuarios.
> Con independencia de la versión que use de los proveedores de recursos, los datos de los usuarios de sus bases de datos no se tocan, y permanecen accesibles.    



#### <a name="app-service"></a>App Service
- Los usuarios deben registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.

- Para escalar horizontalmente la infraestructura (roles de trabajo, administración y front-end), debe usar PowerShell, tal como se describe en las notas de la versión de proceso.


#### <a name="usage"></a>Uso  
-  El uso de los datos del medidor de uso de la dirección IP pública muestran el mismo valor *EventDateTime* para cada registro en lugar de la marca *TimeDate* que muestra cuándo se creó el registro. Actualmente, no puede usar estos datos para realizar un recuento adecuado del uso de la dirección IP pública.

<!-- #### Identity -->

#### <a name="downloading-azure-stack-tools-from-github"></a>Descarga de las herramientas de Azure Stack desde GitHub
- Al usar el cmdlet *invoke-webrequest* de PowerShell para descargar las herramientas de Azure Stack de Github, recibirá un error:     
    -  *invoke-webrequest : The request was aborted: Could not create SSL/TLS secure channel.* (invoke-webrequest: anulada la solicitud, no se pudo crear un canal seguro SSL/TLS)     

  Este error se produce debido a una degradación reciente de la compatibilidad con GitHub de los estándares criptográficos Tlsv1 y Tlsv1.1 (los predeterminados de PowerShell). Para más información, consulte [Weak cryptographic standards removal notice](https://githubengineering.com/crypto-removal-notice/) (Aviso de eliminación de estándares criptográficos débiles).

  Para resolver este problema, agregue `[Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12` a la parte superior del script para obligar a la consola de PowerShell a usar TLSv1.2 cuando se descargue de los repositorios de GitHub.


## <a name="download-the-update"></a>Descarga de la actualización
Puede descargar la actualización de Azure Stack 1803 [aquí](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Vea también
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](../azure-stack-monitor-update.md).
- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](../azure-stack-apply-updates.md).