---
title: Actualización 1804 de Azure Stack | Microsoft Docs
description: Conozca el contenido de la actualización 1804 de los sistemas integrados de Azure Stack, los problemas conocidos y la ubicación donde debe descargarse la actualización.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2018
ms.author: sethm
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 5d871458a7af4e9233e750b07404bc151274033a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248755"
---
# <a name="azure-stack-1804-update"></a>Actualización de Azure Stack 1804

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describen las mejoras y correcciones de esta actualización 1804, los problemas conocidos de esta versión y la ubicación donde debe descargarse la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]        
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación    
El número de compilación de la actualización 1804 de Azure Stack es **20180513.1**.   

### <a name="new-features"></a>Nuevas características
Esta actualización incluye las siguientes correcciones para Azure Stack.

<!-- 15028744 - IS -->  
- **Soporte técnico de Visual Studio para implementaciones de Azure Stack sin conexión con AD FS**. En Visual Studio, podrás agregar suscripciones y autenticar con las credenciales de usuario federado de AD FS. 
 
<!-- 1779474, 1779458 - IS --> 
- **Use máquinas virtuales de las series Av2 y F**. Azure Stack ya puede usar máquinas virtuales basadas en tamaños de máquinas virtuales de las series Av2 y F. Para más información, consulte los [Tamaños de máquinas virtuales admitidos en Azure Stack](../../user/azure-stack-vm-sizes.md). 

<!-- 1759172 - IS, ASDK --> 
- **Nuevas suscripciones administrativas**. Con 1804 hay dos nuevos tipos de suscripción disponibles en el portal. Estos nuevos tipos de suscripción se suman a la suscripción del proveedor predeterminado y se ven con las nuevas instalaciones de Azure Stack a partir de la versión 1804. *No use estos nuevos tipos de suscripción con esta versión de Azure Stack*. Anunciaremos la disponibilidad para usar estos tipos de suscripción con una actualización futura. 

  Si actualiza Azure Stack a la versión 1804, los dos nuevos tipos de suscripción no serán visibles. Sin embargo, las nuevas implementaciones de sistemas integrados e instalaciones de Azure Stack de la versión 1804 o posterior del Kit de desarrollo de Azure Stack tienen acceso a los tres tipos de suscripción.  

  Estos nuevos tipos de suscripción forman parte de un cambio de mayor envergadura para proteger la suscripción del proveedor predeterminado y para que resulte más fácil implementar recursos compartidos, como los servidores de hospedaje de SQL. A medida que se agreguen más partes de este cambio más grande con actualizaciones futuras a Azure Stack, los recursos implementados bajo estos tipos de suscripción podrían perderse. 

  Los tres tipos de suscripción ahora visibles son:  
  - Suscripción de proveedor predeterminado: continúe usando este tipo de suscripción. 
  - Suscripción de medición: *no use este tipo de suscripción*.
  - Suscripción de consumo: *no use este tipo de suscripción*.

  



## <a name="fixed-issues"></a>Problemas corregidos

<!-- IS, ASDK -->  
-  En el portal de administración, ya no es preciso actualizar el icono de actualización para que muestre información.
 
<!-- 2050709  -->  
-  Ahora puede usar el portal de administración para editar las métricas de almacenamiento de Blob service, Table service y Queue service.
 
<!-- IS, ASDK --> 
-  En **Redes**, al hacer clic en **Conexión** para configurar una conexión VPN, **De sitio a sitio (IPsec)** es ahora la única opción disponible.

- **Varias revisiones** de rendimiento, estabilidad, seguridad y el sistema operativo que se usa en Azure Stack.

## <a name="additional-releases-timed-with-this-update"></a>Versiones adicionales en el momento de esta actualización  
Las siguientes están disponibles ahora, pero no requieren la actualización de Azure Stack 1804.
- **Actualización del pack de supervisión de System Center Operations Manager para Microsoft Azure Stack**. Una nueva versión (1.0.3.0) del paquete de supervisión de System Center Operations Manager para Microsoft Azure Stack está disponible para [descargar](https://www.microsoft.com/download/details.aspx?id=55184). Con esta versión, puede usar a las entidades de servicio al agregar una implementación de Azure Stack conectada. Esta versión también incluye una experiencia de Update Management que le permite realizar una acción de corrección directamente desde Operations Manager. También hay nuevos paneles que muestran los proveedores de recursos, las unidades de escalado y los nodos de las unidades de escalado.

- **Nueva versión 1.3.0 de PowerShell de administración para Azure Stack**.  Azure Stack PowerShell 1.3.0 ahora está disponible para instalar. Esta versión proporciona comandos para que todos los proveedores de recursos de administración administren Azure Stack.  Con esta versión, parte del contenido del [repositorio](https://github.com/Azure/AzureStack-Tools) de GitHub para herramientas de Azure Stack entrará en desuso. 

   Para obtener información de instalación, siga las [instrucciones](../azure-stack-powershell-install.md) o el contenido de [ayuda](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0&preserve-view=true) para el módulo 1.3.0 de Azure Stack. 

- **Versión inicial de la referencia de la API de REST para Azure Stack**. Se ha publicado la [referencia de la API para todos los proveedores de recursos de administración de Azure Stack](https://docs.microsoft.com/rest/api/azure-stack/). 


## <a name="before-you-begin"></a>Antes de empezar    

### <a name="prerequisites"></a>Requisitos previos
- Antes de aplicar la actualización de Azure Stack 1804, instale la actualización 1803.  
  
- Instale la actualización o revisión más reciente disponible para la versión 1803. 


### <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización   
- Durante la instalación de la actualización 1804, puede que vea alertas con el título *Error – Template for FaultType UserAccounts.New is missing* (Error: Falta la plantilla para FaultType UserAccounts.New).  Puede omitir estas alertas con seguridad. Estas alertas se cerrarán automáticamente cuando se complete la actualización 1804.   
 
<!-- TBD - IS --> 
- No intente crear máquinas virtuales durante la instalación de esta actualización. Para más información sobre cómo administrar las actualizaciones, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).


### <a name="post-update-steps"></a>Pasos posteriores a la actualización
Después de la instalación de 1804, instale todas las revisiones aplicables. Para más información, consulte los siguientes artículos de la Knowledge base, así como nuestra [Directiva de mantenimiento](../azure-stack-servicing-policy.md).  
 - [KB 4344114: Azure Stack, revisión 1.0.180527.15](https://support.microsoft.com/help/4344114).




### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)
Los siguientes son problemas conocidos posteriores a la instalación de la compilación **20180513.1**.

#### <a name="portal"></a>Portal

- La documentación técnica de Azure Stack se centra en la versión más reciente. Debido a los cambios del portal entre las versiones, es posible que lo que vea al usar los portales de Azure Stack varíe con respecto a lo que ve en la documentación. 

<!-- TBD - IS ASDK --> 
- Con esta versión de Azure Stack, no se pueden aplicar actualizaciones de controladores mediante un paquete de extensiones OEM.  No hay ninguna solución alternativa para este problema.

<!-- 1272111 - IS --> 
-  Después de instalar o actualizar a esta versión de Azure Stack, es posible que no pueda ver las unidades de escalado de Azure Stack en el portal de administración.  
  Solución alternativa: use PowerShell para ver información acerca de las unidades de escalado. Para más información, consulte el contenido de [ayuda](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.3.0&preserve-view=true) del módulo de Azure Stack 1.3.0. 

<!-- 2332636 - IS -->  
-  Cuando usa AD FS para el sistema de identidad de Azure Stack y actualiza a esta versión de Azure Stack, el propietario predeterminado de la suscripción del proveedor predeterminado se restablece al usuario **CloudAdmin** integrado.  
  Solución alternativa: para resolver este problema después de instalar esta actualización, use el paso 3 del procedimiento [Automatización de desencadenador para configurar la relación de confianza de proveedor de notificaciones en Azure Stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) para restablecer el propietario de la suscripción de proveedor predeterminado.   

<!-- TBD - IS ASDK --> 
- Algunos tipos de suscripción administrativa no están disponibles.  Cuando actualiza Azure Stack a esta versión, los dos tipos de suscripciones que se [introdujeron con la versión 1804](#new-features) no aparecen en la consola. Se espera que esto sea así. Los tipos de suscripción no disponibles son *suscripción de medición* y *suscripción de consumo*. Estos tipos de suscripción están visibles en los nuevos entornos de Azure Stack a partir de la versión 1804 pero aún no están listos para su uso. Tendrá que seguir usando el tipo de suscripción del *proveedor predeterminado*.  


<!-- TBD -  IS ASDK -->
- No está disponible la posibilidad de [abrir una nueva solicitud de soporte técnico desde la lista desplegable](../azure-stack-manage-portals.md#quick-access-to-help-and-support) del portal de administración. En su lugar, use el siguiente vínculo:     
    - Para sistemas integrados de Azure Stack. use https://aka.ms/newsupportrequest.

<!-- 2403291 - IS ASDK --> 
- No puede usar la barra de desplazamiento horizontal a lo largo de la parte inferior de los portales de administrador y de usuario. Si no puede acceder a la barra de desplazamiento horizontal, use las rutas de navegación para ir a una hoja anterior del portal seleccionando la hoja que desea ver en la lista de rutas que se encuentra en la parte superior izquierda del portal.

<!-- TBD - IS --> 
- No es posible ver los recursos de proceso o almacenamiento en el portal de administración. La causa de este problema es un error durante la instalación de la actualización que hace que se notifique como correcta erróneamente. Si se produce este problema, póngase en contacto con los servicios de soporte técnico de Microsoft para obtener ayuda.

<!-- TBD - IS --> 
- Puede que vea un panel en blanco en el portal. Para recuperar el panel, seleccione el icono de engranaje en la esquina superior derecha del portal y, a continuación, seleccione **Restaurar configuración predeterminada**.

<!-- TBD - IS ASDK --> 
- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y, a continuación, elimine las suscripciones del usuario.

<!-- TBD - IS ASDK --> 
- No puede ver los permisos de la suscripción mediante los portales de Azure Stack. Como alternativa, use PowerShell para comprobar los permisos.

<!-- TBD - IS ASDK --> 
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
 

#### <a name="compute"></a>Proceso
<!-- TBD - IS --> 
- Al seleccionar un tamaño de máquina virtual para una implementación, algunos tamaños de la serie F no aparecen en el selector de tamaño al crear una máquina virtual. Los siguientes tamaños de máquinas virtuales no aparecen en el selector: *F8s_v2*, *F16s_v2*, *F32s_v2* y *F64s_v2*.  
  Como alternativa, utilice uno de los métodos siguientes para implementar una máquina virtual. En cada método, debe especificar el tamaño de máquina virtual que desea utilizar.

  - **Plantilla de Azure Resource Manager:** cuando utilice una plantilla, establezca *vmSize* en la plantilla de modo que sea igual al tamaño deseado de la máquina virtual. Por ejemplo, se utiliza el siguiente para implementar una máquina virtual que utiliza el tamaño *F32s_v2*:  

    ```
        "properties": {
        "hardwareProfile": {
                "vmSize": "Standard_F32s_v2"
        },
    ```  
  - **CLI de Azure:** puede utilizar el comando [az vm create](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest#az-vm-create&preserve-view=true) y especificar el tamaño de máquina virtual como parámetro, de forma similar a `--size "Standard_F32s_v2"`.

  - **PowerShell:** con PowerShell, puede usar [New-AzureRMVMConfig](https://docs.microsoft.com/powershell/module/azurerm.compute/new-azurermvmconfig?view=azurermps-6.0.0&preserve-view=true) con el parámetro que especifica el tamaño de la máquina virtual, de forma similar a `-VMSize "Standard_F32s_v2"`.


<!-- TBD - IS ASDK --> 
- Los valores de escalado para conjuntos de escalas de máquina virtual no están disponibles en el portal. Como alternativa, puede usar [Azure PowerShell](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-manage-powershell#change-the-capacity-of-a-scale-set). Debido a diferencias en la versión de PowerShell, debe usar el parámetro `-Name` en lugar de `-VMScaleSetName`.

<!-- TBD - IS --> 
-  Cuando crea un conjunto de disponibilidad en el portal en **Nuevo** > **Compute** > **Conjunto de disponibilidad**, solo puede crear uno con un dominio de error y un dominio de actualización de 1. Como alternativa, al crear una nueva máquina virtual, cree el conjunto de disponibilidad mediante PowerShell, la CLI o el portal.

<!-- TBD - IS ASDK --> 
- Al crear máquinas virtuales en el portal de usuario de Azure Stack, el portal muestra un número incorrecto de discos de datos que se pueden asociar a una máquina virtual de la serie D. Todas las máquinas virtuales de la serie D pueden albergar tantos discos de datos como la configuración de Azure.

<!-- TBD - IS ASDK --> 
- Cuando no es posible crear una imagen de máquina virtual, los elementos con error que no se pueden eliminar se podrían agregar a la hoja de proceso de imágenes de máquina virtual.

  Como alternativa, cree una nueva imagen de máquina virtual con un disco duro virtual ficticio que se pueda crear mediante Hyper-V (nuevo VHD, ruta de acceso C:\dummy.vhd, fijo, bytes de tamaño 1 GB). Este proceso debería corregir el problema que impide que se elimine el elemento con error. A continuación, 15 minutos después de crear la imagen ficticia, puede eliminarlo correctamente.

  Luego puede volver a intentar cargar la imagen de máquina virtual que anteriormente produjo un error.

<!-- TBD - IS ASDK --> 
- Si aprovisionar una extensión en una implementación de máquina virtual tarda demasiado tiempo, los usuarios deberían dejar que se agote el tiempo de espera de aprovisionamiento en lugar de intentar detener el proceso para desasignar o eliminar la máquina virtual.  

<!-- 1662991 IS ASDK --> 
- No se admite el diagnóstico de máquinas virtuales Linux en Azure Stack. Si implementa una máquina virtual Linux con diagnósticos de máquina virtual habilitado, se producirá un error en la implementación. Tampoco se podrá realizar la implementación si habilita las métricas básicas de máquina virtual Linux a través de la configuración de diagnóstico.  


#### <a name="networking"></a>Redes
<!-- 1766332 - IS ASDK --> 
-  En **Redes**, si hace clic en **Create VPN Gateway** (Crear instancia de VPN Gateway) para configurar una conexión VPN, aparece **Policy Based** (Basada en directivas) como un tipo de VPN. No seleccione esta opción. En Azure Stack solo se admite la opción **Route Based** (Basada en rutas).

<!-- 2388980 - IS ASDK --> 
- Después de crear una máquina virtual y asociarla a una dirección IP pública, no puede desasociar esa máquina virtual de esa dirección IP. Puede que parezca que se ha desasociado, pero la dirección IP pública anteriormente asignada permanece asociada a la máquina virtual original.

  Actualmente, solo debe usar nuevas direcciones IP públicas para las nuevas máquinas virtuales creadas.

  Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual asociada originalmente, y no a la nueva.

<!-- 2292271 - IS ASDK --> 
- Si genera un límite de cuota para un recurso de red que forma parte de una oferta o plan que está asociado a una suscripción de inquilino, el nuevo límite no se aplicará a esa suscripción. Sin embargo, el nuevo límite se aplicará a las nuevas suscripciones que se creen después de que se aumente la cuota. 

  Para solucionar este problema, use un plan complementario para aumentar una cuota de red cuando el plan ya esté asociado a una suscripción. Para obtener más información, consulte cómo [disponer de un plan complementario](../azure-stack-subscribe-plan-provision-vm.md#to-make-an-add-on-plan-available).

<!-- 2304134 IS ASDK --> 
- No se puede eliminar una suscripción que disponga de recursos de zona DNS o recursos de tabla de rutas asociados a ella. Para eliminar correctamente la suscripción, primero debe eliminar los recursos de la zona DNS y de la tabla de rutas de la suscripción de inquilino. 
  

<!-- 1902460 - IS ASDK --> 
- Azure Stack admite una única *puerta de enlace de red local* por dirección IP. Y esto se aplica a las suscripciones de todos los inquilinos. Tras la creación de la primera conexión a la puerta de enlace de red local, los sucesivos intentos para crear un recurso de puerta de enlace de red local con la misma dirección IP se bloquean.

<!-- 16309153 - IS ASDK --> 
-  En una red virtual que se creó con una configuración de servidor DNS de *Automática*, se produce un error al cambiar a un servidor DNS personalizado. La configuración actualizada no se inserta en las máquinas virtuales de esa red virtual.

<!-- TBD - IS ASDK --> 
- Azure Stack no admite la adición de interfaces de red adicionales a una instancia de máquina virtual una vez implementada la máquina virtual. Si la máquina virtual requiere más de una interfaz de red, se deben definir en el momento de la implementación.

<!-- 2096388 IS --> 
- No se puede usar el portal de administración para actualizar las reglas de un grupo de seguridad de red. 

    Solución alternativa para App Service: si tiene que establecer una conexión de Escritorio remoto a las instancias del controlador, modifique las reglas de seguridad de los grupos de seguridad de red con PowerShell.  Estos son algunos ejemplos de cómo *permitir* y, luego, restaurar la configuración a *denegar*:  
    
    - *Permitir:*
 
      ```powershell    
      Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
      
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
        
        Connect-AzureRmAccount -EnvironmentName AzureStackAdmin
        
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

<!-- TBD - IS --> 
- Solo el proveedor de recursos puede crear elementos en servidores que hospedan SQL o MySQL. Los elementos creados en un servidor host que no se crean con el proveedor de recursos podrían dar lugar a un error de coincidencia de estado.  

<!-- IS, ASDK --> 
- No se admiten caracteres especiales, espacios y puntos incluidos, en los nombres de **familia** o **capa** al crear una SKU para los proveedores de recursos SQL y MySQL.

<!-- TBD - IS -->
> [!NOTE]  
>  Después de actualizar a Azure Stack 1804, puede seguir usando los proveedores de recursos SQL y MySQL que implementó anteriormente.  Le recomendamos que actualice SQL y MySQL cuando haya disponible una nueva versión. Al igual que Azure Stack, aplique las actualizaciones a los proveedores de recursos SQL y MySQL de manera secuencial.  Por ejemplo, si usa la versión 1802, aplique primero la versión 1803 y luego actualice a la 1804.      
>   
> La instalación de la actualización 1804 no afecta el uso actual de los proveedores de recursos SQL o MySQL por parte de los usuarios.
> Con independencia de la versión que use de los proveedores de recursos, los datos de los usuarios de sus bases de datos no se tocan, y permanecen accesibles.    



#### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Los usuarios deben registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.

<!-- TBD - IS ASDK --> 
- Para escalar horizontalmente la infraestructura (roles de trabajo, administración y front-end), debe usar PowerShell, tal como se describe en las notas de la versión de proceso.

<!-- TBD - IS ASDK --> 
- App Service solo puede implementarse en la **suscripción de proveedor predeterminada** en este momento.  En una futura actualización, App Service se implementará en la nueva suscripción de medición introducida en Azure Stack 1804 y todas las implementaciones existentes se migrarán también a esta nueva suscripción.

#### <a name="usage"></a>Uso  
<!-- TBD - IS ASDK --> 
-  El uso de los datos del medidor de uso de la dirección IP pública muestran el mismo valor *EventDateTime* para cada registro en lugar de la marca *TimeDate* que muestra cuándo se creó el registro. Actualmente, no puede usar estos datos para realizar un recuento adecuado del uso de la dirección IP pública.


<!-- #### Identity -->
<!-- #### Marketplace --> 


## <a name="download-the-update"></a>Descarga de la actualización
Puede descargar el paquete de actualización de Azure Stack 1804 [aquí](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Vea también
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](../azure-stack-monitor-update.md).
- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](../azure-stack-apply-updates.md).