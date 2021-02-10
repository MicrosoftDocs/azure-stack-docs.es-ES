---
title: Actualización 1805 de Azure Stack | Microsoft Docs
description: Conozca las novedades de la actualización 1805 de los sistemas integrados de Azure Stack, incluidos los problemas conocidos y la ubicación donde debe descargarse la actualización.
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
ms.openlocfilehash: 16e11fcbc46065f512cc6b887b9c7159f6b0ac65
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248739"
---
# <a name="azure-stack-1805-update"></a>Actualización de Azure Stack 1805

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describen las mejoras y correcciones de esta actualización 1805, los problemas conocidos de esta versión y la ubicación donde debe descargarse la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]        
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación    
El número de compilación de la actualización 1805 de Azure Stack es **1.1805.1.47**.  

> [!TIP]  
> Según los comentarios recibidos, hay una actualización al esquema de versiones en uso para Microsoft Azure Stack.  A partir de esta actualización, la 1805, el nuevo esquema representa mejor la versión actual de la nube.  
> 
> El esquema de la versión es ahora *Version.YearYearMonthMonth.MinorVersion.BuildNumber* donde en el segundo y tercer conjunto se indica la versión y la versión de lanzamiento. Por ejemplo, 1805.1 representa la *versión que se distribuye a los fabricantes* (RTM) de la actualización 1805.  


### <a name="new-features"></a>Nuevas características
Esta actualización incluye las siguientes correcciones para Azure Stack.
<!-- 2297790 - IS, ASDK --> 
- **Azure Stack incluye ahora un cliente de *Syslog* como** una *característica de la versión preliminar*. Este cliente permite el reenvío de los registros de auditoría y seguridad relacionados con la infraestructura de Azure Stack a un servidor de Syslog o a un software de administración de eventos e información de seguridad (SIEM) que es externo a Azure Stack. Actualmente, el cliente de Syslog solo admite conexiones no autenticadas de UDP en el puerto predeterminado 514. La carga de cada mensaje de Syslog tiene el formato Common Event Format (CEF). 

  Para configurar el cliente de Syslog, use el cmdlet **Set-SyslogServer** que se expone en el punto de conexión con privilegios. 

  Con esta versión preliminar, puede que vea las siguientes tres alertas. Cuando las presenta Azure Stack, estas alertas incluyen *descripciones* e instrucciones de *corrección*. 
  - TITLE: Code Integrity Off (TÍTULO: se ha desactivado la integridad del código)  
  - TITLE: Code Integrity in Audit Mode (TÍTULO: integridad del código en modo auditoría) 
  - TITLE: User Account Created (TÍTULO: se ha creado la cuenta de usuario)

  Aunque esta característica está en su versión preliminar, no se debería usar en entornos de producción.   




### <a name="fixed-issues"></a>Problemas corregidos

<!-- # - applicability -->
- Hemos corregido el problema que bloqueaba [la apertura de una nueva solicitud de soporte técnico desde la lista desplegable](../azure-stack-manage-portals.md#quick-access-to-help-and-support) en el portal de administración. Esta opción funciona ahora según lo previsto. 

- **Varias revisiones** de rendimiento, estabilidad, seguridad y el sistema operativo que se usa en Azure Stack.


<!-- # Additional releases timed with this update    -->



## <a name="before-you-begin"></a>Antes de empezar    

### <a name="prerequisites"></a>Requisitos previos
- Antes de aplicar la actualización de Azure Stack 1805, instale la actualización 1804.  
- Instale la actualización o revisión para la versión 1804 más reciente disponible.   
- Antes de iniciar la instalación de la actualización 1805, ejecute [Test-AzureStack](../azure-stack-diagnostic-test.md) para validar el estado de Azure Stack y resolver todos los problemas operativos que se encuentren. Repase también las alertas activas y resuelva las que requieran alguna acción. 

### <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización   
- Durante la instalación de la actualización 1805, puede que vea alertas con el título *Error – Template for FaultType UserAccounts.New is missing* (Error: Falta la plantilla para FaultType UserAccounts.New).  Puede omitir estas alertas con seguridad. Estas alertas se cerrarán automáticamente cuando se complete la actualización 1805.   

<!-- 2489559 - IS --> 
- No intente crear máquinas virtuales durante la instalación de esta actualización. Para más información sobre cómo administrar las actualizaciones, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).


### <a name="post-update-steps"></a>Pasos posteriores a la actualización
Después de la instalación de 1805, instale todas las revisiones aplicables. Para más información, consulte los siguientes artículos de la Knowledge base, así como nuestra [Directiva de mantenimiento](../azure-stack-servicing-policy.md).  
 - [KB 4344102: Azure Stack, revisión 1.1805.7.57](https://support.microsoft.com/help/4344102).


## <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)
Los siguientes son problemas conocidos posteriores a la instalación de esta compilación.

### <a name="portal"></a>Portal  

- La documentación técnica de Azure Stack se centra en la versión más reciente. Debido a los cambios del portal entre las versiones, es posible que lo que vea al usar los portales de Azure Stack varíe con respecto a lo que ve en la documentación. 

<!-- 2931230 IS  ASDK --> 
- No se pueden eliminar los planes que se agregan a una suscripción de usuario como planes complementarios, aunque se quite el plan de la suscripción de usuario. El plan permanecerá hasta que también se eliminen las suscripciones a las que haga referencia el plan complementario. 

<!-- TBD - IS ASDK --> 
- Con esta versión de Azure Stack, no se pueden aplicar actualizaciones de controladores mediante un paquete de extensiones OEM.  No hay ninguna solución alternativa para este problema.

<!-- 2551834 - IS, ASDK --> 
-  Cuando se selecciona **Información general** para una cuenta de almacenamiento en los portales de administrador o de usuario, la información del panel *Información esencial* no aparece.  Este panel muestra información sobre la cuenta como, por ejemplo, su *grupo de recursos*, *ubicación* y el *identificador de la suscripción*.  Se puede acceder a otras opciones de Información general como *Servicios* y *Supervisión*, así como a opciones para *Abrir en el Explorador* o para *Eliminar cuenta de almacenamiento*. 

  Para ver la información no disponible, use el cmdlet de PowerShell [Get-azureRMstorageaccount](https://docs.microsoft.com/powershell/module/azurerm.storage/get-azurermstorageaccount?view=azurermps-6.2.0&preserve-view=true). 

<!-- 2551834 - IS, ASDK --> 
-  Cuando se selecciona **Etiquetas** para una cuenta de almacenamiento en los portales de administrador o de usuario, la información no se puede cargar y no aparece.  

  Para ver la información no disponible, use el cmdlet de PowerShell [Get-AzureRmTag](https://docs.microsoft.com/powershell/module/azurerm.tags/get-azurermtag?view=azurermps-6.2.0&preserve-view=true).


<!-- 2332636 - IS -->  
-  Cuando usa AD FS para el sistema de identidad de Azure Stack y actualiza a esta versión de Azure Stack, el propietario predeterminado de la suscripción del proveedor predeterminado se restablece al usuario **CloudAdmin** integrado.  
  Solución alternativa: para resolver este problema después de instalar esta actualización, use el paso 3 del procedimiento [Automatización de desencadenador para configurar la relación de confianza de proveedor de notificaciones en Azure Stack](../azure-stack-integrate-identity.md#trigger-automation-to-configure-claims-provider-trust-in-azure-stack-hub) para restablecer el propietario de la suscripción de proveedor predeterminado.   

<!-- TBD - IS ASDK --> 
- Algunos tipos de suscripción administrativa no están disponibles. Cuando actualiza Azure Stack a esta versión, los dos tipos de suscripciones que se han introducido con la versión 1804 no aparecen en la consola. Se espera que esto sea así. Los tipos de suscripción no disponibles son *suscripción de medición* y *suscripción de consumo*. Estos tipos de suscripción están visibles en los nuevos entornos de Azure Stack a partir de la versión 1804 pero aún no están listos para su uso. Tendrá que seguir usando el tipo de suscripción del *proveedor predeterminado*.  

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


### <a name="health-and-monitoring"></a>Estado y supervisión
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

  Se pueden omitir ambas alertas con seguridad y se cerrarán automáticamente con el tiempo. 

  También es posible que vea la siguiente alerta para *Capacidad*. Para esta alerta, el porcentaje de memoria disponible que se identifica en la descripción puede variar:  

  Alerta 3:
   - NOMBRE: baja capacidad de memoria
   - GRAVEDAD: crítica
   - COMPONENTE: capacidad
   - DESCRIPCIÓN: la región ha consumido más del 80,00 % de la memoria disponible. La creación de máquinas virtuales con grandes cantidades de memoria puede producir un error.  

  En esta versión de Azure Stack, esta alerta puede desencadenarse incorrectamente. Si las máquinas virtuales del inquilino siguen implementándose correctamente, puede ignorar esta alerta. 
  
  La Alerta 3 no se cierra automáticamente. Si cierra esta alerta, Azure Stack creará la misma alerta en 15 minutos.  

<!-- 2368581 - IS. ASDK --> 
-  Como operador de Azure Stack: si recibe una alerta de memoria insuficiente y no se pueden implementar las máquinas virtuales del inquilino debido a un *error de creación de máquina virtual de Fabric*, es posible que la marca de Azure Stack supere la memoria disponible. Use la [herramienta de planeamiento de capacidad de Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para comprender mejor la capacidad disponible para las cargas de trabajo. 


### <a name="compute"></a>Proceso
<!-- TBD - IS, ASDK --> 
- Al seleccionar un tamaño de máquina virtual para una implementación, algunos tamaños de la serie F no aparecen en el selector de tamaño al crear una máquina virtual. Los siguientes tamaños de máquinas virtuales no aparecen en el selector: *F8s_v2*, *F16s_v2*, *F32s_v2* y *F64s_v2*.  
  Como alternativa, utilice uno de los métodos siguientes para implementar una máquina virtual. En cada método, debe especificar el tamaño de máquina virtual que desea utilizar.

  - **Plantilla de Azure Resource Manager:** cuando utilice una plantilla, establezca *vmSize* en la plantilla de modo que sea igual al tamaño deseado de la máquina virtual. Por ejemplo, se utiliza la siguiente entrada para implementar una máquina virtual que utiliza el tamaño *F32s_v2*:  

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
- Al crear máquinas virtuales en el portal de usuario de Azure Stack, el portal muestra un número incorrecto de discos de datos que se pueden asociar a una máquina virtual de la serie DS. Las máquinas virtuales de la serie DS pueden albergar tantos discos de datos como la configuración de Azure.

<!-- TBD - IS ASDK --> 
- Cuando no es posible crear una imagen de máquina virtual, los elementos con error que no se pueden eliminar se podrían agregar a la hoja de proceso de imágenes de máquina virtual.

  Como alternativa, cree una nueva imagen de máquina virtual con un disco duro virtual ficticio que se pueda crear mediante Hyper-V (nuevo VHD, ruta de acceso C:\dummy.vhd, fijo, bytes de tamaño 1 GB). Este proceso debería corregir el problema que impide que se elimine el elemento con error. A continuación, 15 minutos después de crear la imagen ficticia, puede eliminarlo correctamente.

  Luego puede volver a intentar cargar la imagen de máquina virtual que anteriormente produjo un error.

<!-- TBD - IS ASDK --> 
- Si aprovisionar una extensión en una implementación de máquina virtual tarda demasiado tiempo, los usuarios deberían dejar que se agote el tiempo de espera de aprovisionamiento en lugar de intentar detener el proceso para desasignar o eliminar la máquina virtual.  

<!-- 1662991 IS ASDK --> 
- No se admite el diagnóstico de máquinas virtuales Linux en Azure Stack. Si implementa una máquina virtual Linux con diagnósticos de máquina virtual habilitado, se producirá un error en la implementación. Tampoco se podrá realizar la implementación si habilita las métricas básicas de máquina virtual Linux a través de la configuración de diagnóstico.  


### <a name="networking"></a>Redes
<!-- TBD - IS ASDK --> 
- No se pueden crear rutas definidas por el usuario en el portal de administrador ni en el de usuario. Como alternativa, use [Azure PowerShell](https://docs.microsoft.com/azure/virtual-network/tutorial-create-route-table-powershell).

<!-- 1766332 - IS ASDK --> 
-  En **Redes**, si hace clic en **Create VPN Gateway** (Crear instancia de VPN Gateway) para configurar una conexión VPN, aparece **Policy Based** (Basada en directivas) como un tipo de VPN. No seleccione esta opción. En Azure Stack solo se admite la opción **Route Based** (Basada en rutas).

<!-- 2388980 - IS ASDK --> 
- Después de crear una máquina virtual y asociarla a una dirección IP pública, no puede desasociar esa máquina virtual de esa dirección IP. Puede que parezca que se ha desasociado, pero la dirección IP pública anteriormente asignada permanece asociada a la máquina virtual original.

  Actualmente, solo debe usar nuevas direcciones IP públicas para las nuevas máquinas virtuales creadas.

  Este comportamiento se producirá incluso si vuelve a asignar la dirección IP a una nueva máquina virtual (lo que normalmente se conoce como *intercambio de VIP*). Todos los intentos futuros de conectarse a través de esta dirección IP tendrán como resultado una conexión a la máquina virtual original, y no a la nueva.

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

      ##This doesn't work. Need to set properties again even in case of edit

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

        ##This doesn't work. Need to set properties again even in case of edit

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


### <a name="sql-and-mysql"></a>SQL y MySQL

<!-- TBD - IS --> 
- Solo el proveedor de recursos puede crear elementos en servidores que hospedan SQL o MySQL. Los elementos creados en un servidor host que no se crean con el proveedor de recursos podrían dar lugar a un error de coincidencia de estado.  

<!-- IS, ASDK --> 
- No se admiten caracteres especiales, espacios y puntos incluidos, en los nombres de **familia** o **capa** al crear una SKU para los proveedores de recursos SQL y MySQL.

<!-- TBD - IS --> 
> [!NOTE]  
> Después de actualizar a Azure Stack 1805, puede seguir usando los proveedores de recursos SQL y MySQL que ha implementado anteriormente.  Le recomendamos que actualice SQL y MySQL cuando haya disponible una nueva versión. Al igual que Azure Stack, aplique las actualizaciones a los proveedores de recursos SQL y MySQL de manera secuencial. Por ejemplo, si usa la versión 1803, aplique primero la versión 1804 y luego actualice a la 1805.      
>   
> La instalación de la actualización 1805 no afecta al uso actual de los proveedores de recursos SQL o MySQL por parte de los usuarios.
> Con independencia de la versión que use de los proveedores de recursos, los datos de los usuarios de sus bases de datos no se tocan, y permanecen accesibles.    



### <a name="app-service"></a>App Service
<!-- 2352906 - IS ASDK --> 
- Los usuarios deben registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.

<!-- 2489178 - IS ASDK --> 
- Para escalar horizontalmente la infraestructura (roles de trabajo, administración y front-end), debe usar PowerShell, tal como se describe en las notas de la versión de proceso.

<!-- TBD - IS ASDK --> 
- App Service solo puede implementarse en la *suscripción del proveedor predeterminado* en este momento. 


### <a name="usage"></a>Uso  
<!-- TBD - IS ASDK --> 
-  El uso de los datos del medidor de uso de la dirección IP pública muestran el mismo valor *EventDateTime* para cada registro en lugar de la marca *TimeDate* que muestra cuándo se creó el registro. Actualmente, no puede usar estos datos para realizar un recuento adecuado del uso de la dirección IP pública.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Descarga de la actualización
Puede descargar el paquete de actualización de Azure Stack 1805 [aquí](https://aka.ms/azurestackupdatedownload).


## <a name="see-also"></a>Vea también
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](../azure-stack-monitor-update.md).
- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](../azure-stack-apply-updates.md).