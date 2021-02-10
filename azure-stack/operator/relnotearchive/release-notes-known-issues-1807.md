---
title: Actualización 1807 de Azure Stack | Microsoft Docs
description: Conozca las novedades de la actualización 1807 de los sistemas integrados de Azure Stack, incluidos los problemas conocidos y la ubicación donde debe descargarse la actualización.
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
ms.openlocfilehash: 9dd3647f607dc743c6eff5907ae4a47fa097e764
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248742"
---
# <a name="azure-stack-1807-update"></a>Actualización de Azure Stack 1807

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el contenido de la actualización 1807. Esta actualización incluye mejoras, correcciones y problemas conocidos de esta versión de Azure Stack y dónde debe descargarse la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 1807 de Azure Stack es **1.1807.0.76**.  

### <a name="new-features"></a>Nuevas características

Esta actualización incluye las siguientes correcciones para Azure Stack.

<!-- 1658937 | ASDK, IS --> 
- **Iniciar copias de seguridad según una programación predefinida**: como un dispositivo, Azure Stack ahora puede desencadenar automáticamente las copias de seguridad de infraestructura periódicamente para eliminar la intervención humana. Azure Stack también limpiará automáticamente el recurso compartido externo para las copias de seguridad anteriores al período de retención definido. Para más información, consulte [Habilitación de la copia de seguridad de Azure Stack con PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 2496385 | ASDK, IS -->  
- **Tiempo de transferencia de datos agregado al tiempo total de copia de seguridad.** Para más información, consulte [Habilitación de la copia de seguridad de Azure Stack con PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 1702130 | ASDK, IS -->  
- **La capacidad externa de copia de seguridad ahora muestra la capacidad correcta del recurso compartido externo.** (Anteriormente esto se codificada de forma rígida a 10 GB). Para más información, consulte [Habilitación de la copia de seguridad de Azure Stack con PowerShell](../azure-stack-backup-enable-backup-powershell.md).

<!-- 2508488 |  IS   -->
- **Expanda la capacidad**[mediante la incorporación de nodos de unidad de escalado adicionales](../azure-stack-add-scale-node.md).

<!-- 2753130 |  IS, ASDK   -->  
- **Las plantillas de Azure Resource Manager admiten ahora el elemento de condición**: ahora puede implementar un recurso en una plantilla de Azure Resource Manager con una condición. Puede diseñar la plantilla para implementar un recurso en función de una condición, por ejemplo, evaluando si un valor de un parámetro está presente. Para información acerca de cómo usar una plantilla como una condición, consulte [Implementación condicional de un recurso en una plantilla de Azure Resource Manager](https://docs.microsoft.com/azure/architecture/building-blocks/extending-templates/conditional-deploy) y [sección Variables de plantillas de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-templates-variables) en la documentación de Azure. 

   También puede usar plantillas para [implementar recursos en varias suscripciones o grupos de recursos](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-cross-resource-group-deployment).  

<!--2753073 | IS, ASDK -->  
- **Se ha actualizado la compatibilidad con la versión de recursos de la API Microsoft.Network** para incluir compatibilidad con la versión de la API 2017-10-01 desde 2015-06-15 para los recursos de red de Azure Stack.  La compatibilidad con las versiones de recursos entre 2017-10-01 y 2015-06-15 no se incluye en esta versión.  Consulte en [Consideraciones para las redes de Azure Stack](../../user/azure-stack-network-differences.md) las diferencias de funcionalidad.

<!-- 2272116 | IS, ASDK   -->  
- **Azure Stack ha agregado compatibilidad con las búsquedas inversas de DNS para los puntos de conexión de infraestructura de Azure Stack que se usan externamente** (es decir, para portal, adminportal, management y adminmanagement). Esto permite que los nombres de puntos de conexión externos de Azure Stack se puedan resolver desde una dirección IP.

<!-- 2780899 |  IS, ASDK   --> 
- **Azure Stack ahora admite agregar interfaces de red adicionales a una máquina virtual existente.**  Esta funcionalidad está disponible mediante el portal, PowerShell y la CLI. Para más información, consulte [Incorporación de interfaces de red a máquinas virtuales o su eliminación de ellas](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-vm) en la documentación de Azure. 

<!-- 2222444 | IS, ASDK   -->  
- **Se han realizado mejoras en la precisión y la resistencia en medidores de uso de la red.**  Los medidores de uso de la red ahora son más precisos y tienen en cuenta las suscripciones suspendidas, los períodos de interrupción y las condiciones de carrera.

<!-- 2753080 | IS -->  
- **Actualizar notificación disponible.** Las implementaciones de Azure Stack conectadas ahora comprobarán periódicamente un punto de conexión seguro y determinarán si una actualización está disponible para la nube. Esta notificación aparece en el icono de actualización, como aparecería después de buscar manualmente e importar una nueva actualización. Obtenga más información acerca de la [administración de actualizaciones para Azure Stack](../azure-stack-updates.md).

<!-- 2297790 | IS, ASDK -->  
- **Mejoras en el cliente de Syslog (característica en vista previa) de Azure Stack**. Este cliente permite el reenvío de la auditoría y los registros relacionados con la infraestructura de Azure Stack a un servidor de syslog o a un software de administración de eventos e información de seguridad (SIEM) que es externo a Azure Stack. El cliente de syslog ahora es compatible con el protocolo TCP con texto sin formato o el cifrado de TLS 1.2; el último es la configuración predeterminada. Puede configurar la conexión TLS con autenticación solo de servidor o mutua.

  Para configurar el modo en que el cliente de syslog se comunica (como el protocolo, el cifrado y la autenticación) con el servidor de syslog, use el cmdlet *Set-SyslogServer*. Este cmdlet está disponible desde el punto de conexión con privilegios (PEP).

  Para agregar el certificado de cliente para la autenticación mutua de TLS 1.2 del cliente de syslog, use el cmdlet Set-SyslogClient en el PEP.

  Con esta versión preliminar, puede ver una cantidad mucho mayor de auditorías y alertas. 

  Dado que esta característica está aún en versión preliminar, no la use en entornos de producción.

  Para más información, consulte [Reenvío de syslog de Azure Stack](../azure-stack-integrate-security.md).

<!-- ####### | IS, ASDK | --> 
- **Azure Resource Manager incluye el nombre de la región.** Con esta versión, los objetos recuperados desde Azure Resource Manager ahora incluirá el atributo de nombre de región. Si un script de PowerShell existente pasa directamente el objeto a otro cmdlet, el script puede producir un error. Este es el comportamiento compatible de Azure Resource Manager y requiere que el cliente que realiza la llamada quite el atributo de región. Para más información acerca de Azure Resource Manager, consulte la [documentación de Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/). verify 8-10 mdb -->

<!-- TBD | IS, ASDK -->  
- **Cambios en la funcionalidad Proveedores delegados.** A partir del modelo 1807, el modelo de Proveedores delegados se ha simplificado para alinearse mejor con el modelo de distribuidor de Azure y, para crear otros Proveedores delegados, no bastará con quitar el formato del modelo y hacer que la característica Proveedor delegado esté disponible en un solo nivel. Para habilitar la transición al nuevo modelo y la administración de las suscripciones, las suscripciones de usuario ahora se pueden mover entre las suscripciones de Proveedor delegado nuevas o existentes que pertenezcan al mismo inquilino de directorio. Las suscripciones de usuario que pertenecen a la suscripción del proveedor predeterminado también se pueden pasar a las suscripciones de Proveedor delegado en el mismo inquilino de directorio.  Para más información, consulte [Delegación de ofertas en Azure Stack](../azure-stack-delegated-provider.md).

<!-- 2536808 IS ASDK --> 
- **Mejor tiempo de creación de VM** para las máquinas virtuales que se crean con imágenes que se descargan de Azure Marketplace.

<!-- TBD | IS, ASDK -->  
- **Mejoras en la facilidad de uso de Azure Stack Capacity Planner**. Azure Stack [Capacity Planner](https://aka.ms/azstackcapacityplanner) ahora ofrece una experiencia simplificada para la entrada de caché S2D y la capacidad de S2D al definir las SKU de la solución. Se ha quitado el límite de 1000 máquinas virtuales.


### <a name="fixed-issues"></a>Problemas corregidos

<!-- TBD | ASDK, IS --> 
- Se han realizado varias mejoras en el proceso de actualización para que sea más confiable. Además, las correcciones se llevaron a cabo en la infraestructura subyacente, lo que minimiza el posible tiempo de inactividad para las cargas de trabajo durante la actualización.

<!--2292271 | ASDK, IS --> 
- Se ha corregido un problema por el que un límite de cuota modificado no se aplicaba a las suscripciones existentes. Ahora, si genera un límite de cuota para un recurso de red que forma parte de una oferta o plan asociado a una suscripción de usuario, el nuevo límite se aplica a las suscripciones previamente existentes además de a las nuevas.

<!-- 448955 | IS ASDK --> 
- Ahora puede consultar correctamente los registros de actividad para los sistemas que se implementan en una zona horaria UTC+N.    

<!-- 2319627 |  ASDK, IS --> 
- La comprobación previa de los parámetros de configuración de copia de seguridad (ruta de acceso, nombre de usuario, contraseña y clave de cifrado) ya no establece una configuración incorrecta en la configuración de copia de seguridad. (Anteriormente, se establecía una configuración incorrecta en la copia de seguridad y esta produciría después un error al desencadenarse).

<!-- 2215948 |  ASDK, IS -->
- La lista de copias de seguridad ahora se actualiza cuando elimina manualmente la copia de seguridad desde el recurso compartido externo.

<!-- 2332636 | IS -->  
- La actualización a esta versión ya no restablece el propietario predeterminado de la suscripción del proveedor predeterminado al usuario **CloudAdmin** integrado cuando se implementa con AD FS.

<!-- 2360715 |  ASDK, IS -->  
- Al configurar la integración del centro de datos, ya no se accede al archivo de metadatos de AD FS desde un recurso compartido de archivos de Azure. Para más información, consulte [Configuración de la integración de AD FS mediante el archivo de metadatos de federación](../azure-stack-integrate-identity.md#setting-up-ad-fs-integration-by-providing-federation-metadata-file). 

<!-- 2388980 | ASDK, IS --> 
- Se ha corregido un problema que impedía a los usuarios asignar una dirección IP pública existente, que se había asignado previamente a una interfaz de red o a un equilibrador de carga, a una nueva interfaz de red o equilibrador de carga.  

<!-- 2551834 - IS, ASDK --> 
- Cuando se selecciona *Información general* para una cuenta de almacenamiento en los portales de administrador o de usuario, ahora el panel *Información esencial* muestra correctamente toda la información esperada. 

<!-- 2551834 - IS, ASDK --> 
- Cuando se selecciona *Etiquetas* para una cuenta de almacenamiento en los portales de administrador o de usuario, la información ahora se muestra correctamente.

<!-- TBD - IS ASDK --> 
- Esta versión de Azure Stack corrige el problema que impedía que la aplicación del controlador se actualizara desde los paquetes de extensiones de OEM.

<!-- 2055809- IS ASDK --> 
- Se ha corregido un problema que impedía la eliminación de las máquinas virtuales de la hoja de proceso cuando la máquina virtual no se podía crear.  

<!--  2643962 IS ASDK -->  
- La alerta por *baja capacidad de memoria* ya no aparece de forma incorrecta.

- **Varias revisiones** de rendimiento, estabilidad, seguridad y el sistema operativo que se usa en Azure Stack.


<!-- ### Additional releases timed with this update    -->

### <a name="common-vulnerabilities-and-exposures"></a>Puntos vulnerables y exposiciones comunes
Azure Stack usa las instalaciones Server Core de Windows Server 2016 para hospedar la infraestructura de claves. Esta versión instala las siguientes actualizaciones de Windows Server 2016 en los servidores de infraestructura de Azure Stack: 
- [CVE-2018-8206](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8206)
- [CVE-2018-8222](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8222)
- [CVE-2018-8282](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8282)
- [CVE-2018-8304](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8304)
- [CVE-2018-8307](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8307)
- [CVE-2018-8308](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8308) 
- [CVE-2018-8309](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8309)
- [CVE-2018-8313](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8313)  

Para más información acerca de estos puntos vulnerables, haga clic en los vínculos anteriores o consulte los artículos de Microsoft Knowledge Base [4338814](https://support.microsoft.com/help/4338814) y [4345418](https://support.microsoft.com/help/4345418).



## <a name="before-you-begin"></a>Antes de empezar

### <a name="prerequisites"></a>Requisitos previos

- Antes de aplicar la actualización de Azure Stack 1807, instale la actualización 1805.  No había actualización 1806.  

- Instale la actualización o revisión para la versión 1805 más reciente disponible.  

- Antes de iniciar la instalación de esta actualización, ejecute [Test-AzureStack](../azure-stack-diagnostic-test.md) con los parámetros siguientes para validar el estado de Azure Stack y resolver todos los problemas operativos detectados, incluidas todas las advertencias y errores. Repase también las alertas activas y resuelva las que requieran alguna acción.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ``` 

### <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización

<!-- 2468613 - IS --> 
- Durante la instalación de esta actualización, puede que vea alertas con el título *Error – Template for FaultType UserAccounts.New is missing.* (Error: Falta la plantilla para FaultType UserAccounts.New).  Puede omitir estas alertas con seguridad. Estas alertas se cerrarán automáticamente cuando se complete la instalación de esta actualización.

<!-- 2489559 - IS --> 
- No intente crear máquinas virtuales durante la instalación de esta actualización. Para más información sobre cómo administrar las actualizaciones, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).

<!-- 2830461 - IS --> 
- En determinadas circunstancias cuando una actualización requiere atención, es posible que no se genere la alerta correspondiente. El estado preciso se reflejará en el portal y no se ve afectado.

### <a name="post-update-steps"></a>Pasos posteriores a la actualización
Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte los siguientes artículos de la Knowledge base, así como nuestra [Directiva de mantenimiento](../azure-stack-servicing-policy.md). 
- [KB 4464231: Revisión de Azure Stack 1.1807.1.78](https://support.microsoft.com/help/4464231)

<!-- 2933866 IS -->  Después de la instalación de esta actualización, puede ver el **estado mejorado para las instalaciones de actualización con errores.** Esto puede incluir información sobre errores de instalación de actualización anteriores que se han revisado para reflejar las dos nuevas categorías de estado. Las categorías de estado nuevas son *PreparationFailed* e *InstallationFailed*.  

## <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

Los siguientes son problemas conocidos posteriores a la instalación de esta compilación.

### <a name="portal"></a>Portal

- La documentación técnica de Azure Stack se centra en la versión más reciente. Debido a los cambios del portal entre las versiones, es posible que lo que vea al usar los portales de Azure Stack varíe con respecto a lo que ve en la documentación. 

- No está disponible la posibilidad de [abrir una nueva solicitud de soporte técnico desde la lista desplegable](../azure-stack-manage-portals.md#quick-access-to-help-and-support) en el portal de administración. En su lugar, para los sistemas integrados de Azure Stack, use el vínculo siguiente: [https://aka.ms/newsupportrequest](https://aka.ms/newsupportrequest).

<!-- 2931230 IS  ASDK --> 
- No se pueden eliminar los planes que se agregan a una suscripción de usuario como planes complementarios, aunque se quite el plan de la suscripción de usuario. El plan permanecerá hasta que también se eliminen las suscripciones a las que haga referencia el plan complementario. 

<!--2760466 IS  ASDK --> 
- Cuando se instala un nuevo entorno de Azure Stack que ejecuta esta versión, la alerta que indica *Activación necesaria* podría no mostrarse. La [activación](../azure-stack-registration.md) se requiere para poder usar la redifusión de marketplace.  

<!-- TBD - IS ASDK --> 
- Los dos tipos de suscripción administrativa que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**. Estos tipos de suscripción están visibles en los nuevos entornos de Azure Stack a partir de la versión 1804 pero aún no están listos para su uso. Tendrá que seguir usando el tipo de suscripción del **proveedor predeterminado**.

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
-  Es posible que vea alertas del componente **Controlador de mantenimiento** con los siguientes detalles:  

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


<!-- 2812138 | IS --> 
- Es posible que vea una alerta para el componente **Storage** con los siguientes detalles:

   - NOMBRE: error de comunicación interna del servicio Storage  
   - GRAVEDAD: crítica  
   - COMPONENTE: Storage  
   - DESCRIPCIÓN: se produjo un error de comunicación interna del servicio Storage al enviar solicitudes a los nodos siguientes.  

    La alerta puede omitirse sin riesgos, pero deberá cerrarla manualmente.

<!-- 2368581 - IS. ASDK --> 
- Operador de Azure Stack: si recibe una alerta de memoria insuficiente y no se pueden implementar las máquinas virtuales del inquilino debido a un **error de creación de máquina virtual de Fabric**, es posible que la marca de Azure Stack supere la memoria disponible. Use la [herramienta de planeamiento de capacidad de Azure Stack](https://gallery.technet.microsoft.com/Azure-Stack-Capacity-24ccd822) para comprender mejor la capacidad disponible para las cargas de trabajo.


### <a name="compute"></a>Proceso

<!-- 2724873 - IS --> 
-  Al usar los cmdlets de PowerShell **Start-AzsScaleUnitNode** o **Stop-AzsScaleunitNode** para administrar las unidades de escalado, el primer intento para iniciar o detener la unidad de escalado podría producir un error. Si se produce un error en el cmdlet en la primera ejecución, ejecute el cmdlet una segunda vez. La segunda ejecución debe ser correcta para completar la operación. 

<!-- 2494144 - IS, ASDK --> 
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
- Si aprovisionar una extensión en una implementación de máquina virtual tarda demasiado tiempo, los usuarios deberían dejar que se agote el tiempo de espera de aprovisionamiento en lugar de intentar detener el proceso para desasignar o eliminar la máquina virtual.  

<!-- 1662991 IS ASDK --> 
- No se admite el diagnóstico de máquinas virtuales Linux en Azure Stack. Si implementa una máquina virtual Linux con diagnósticos de máquina virtual habilitado, se producirá un error en la implementación. Tampoco se podrá realizar la implementación si habilita las métricas básicas de máquina virtual Linux a través de la configuración de diagnóstico.  

<!-- 2724961- IS ASDK --> 
- Al registrar el proveedor de recursos **Microsoft.Insight** en la configuración de la suscripción y crear una máquina virtual Windows con el diagnóstico del sistema operativo invitado habilitado, la página de información general de la máquina virtual no muestra los datos de métricas. 

   Para buscar datos de métricas, como el gráfico de porcentaje de CPU para la máquina virtual, vaya a la hoja **Métrica** y muestre todas las métricas de invitado de las máquinas virtuales Windows admitidas.

### <a name="networking"></a>Redes  

<!-- 1766332 - IS ASDK --> 
-  En **Redes**, si hace clic en **Create VPN Gateway** (Crear instancia de VPN Gateway) para configurar una conexión VPN, aparece **Policy Based** (Basada en directivas) como un tipo de VPN. No seleccione esta opción. En Azure Stack solo se admite la opción **Route Based** (Basada en rutas).

<!-- 1902460 - IS ASDK --> 
- Azure Stack admite una única *puerta de enlace de red local* por dirección IP. Y esto se aplica a las suscripciones de todos los inquilinos. Tras la creación de la primera conexión a la puerta de enlace de red local, los sucesivos intentos para crear un recurso de puerta de enlace de red local con la misma dirección IP se bloquean.

<!-- 16309153 - IS ASDK --> 
-  En una red virtual que se creó con una configuración de servidor DNS de *Automática*, se produce un error al cambiar a un servidor DNS personalizado. La configuración actualizada no se inserta en las máquinas virtuales de esa red virtual.

<!-- 2702741 -  IS ASDK --> 
- No se garantiza que las direcciones IP públicas que se implementan mediante el método de asignación dinámica se conserven después de emitirse una detención o desasignación.

<!-- 2529607 - IS ASDK --> 
- Durante la *rotación secreta* de Azure Stack, hay un período en el que las direcciones IP públicas a nivel de instancia no son accesibles entre dos y cinco minutos.

<!-- 2664148 - IS ASDK --> 
- En los escenarios en los que el inquilino tenga acceso a sus máquinas virtuales mediante un túnel VPN S2S, podría encontrarse con que los intentos de conexión producen un error si la subred local se ha agregado a la puerta de enlace de red local una vez que la puerta de enlace ya está creada. 


### <a name="sql-and-mysql"></a>SQL y MySQL



<!-- No Number - IS, ASDK -->  
- Los caracteres especiales, entre los que se incluyen los espacios y los puntos, no se admiten en el nombre de **familia** al crear una SKU para los proveedores de recursos de SQL y MySQL. 

<!-- TBD - IS --> 
- Solo el proveedor de recursos puede crear elementos en servidores que hospedan SQL o MySQL. Los elementos creados en un servidor host que no se crean con el proveedor de recursos podrían dar lugar a un error de coincidencia de estado.  

<!-- TBD - IS -->
> [!NOTE]   
> Después de actualizar a esta versión de Azure Stack, puede seguir usando los proveedores de recursos SQL y MySQL que implementó anteriormente.  Le recomendamos que actualice SQL y MySQL cuando haya disponible una nueva versión. Al igual que Azure Stack, aplique las actualizaciones a los proveedores de recursos SQL y MySQL de manera secuencial. Por ejemplo, si usa la versión 1804, aplique primero la versión 1805 y luego actualice a la 1807.  
>  
> La instalación de esta actualización no afecta al uso actual de los proveedores de recursos SQL o MySQL por parte de los usuarios. 
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
Puede descargar el paquete de actualización de Azure Stack 1807 desde [aquí](https://aka.ms/azurestackupdatedownload).


## <a name="next-steps"></a>Pasos siguientes
- Para revisar la directiva de servicio de los sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido, consulte [Directiva de servicio de Azure Stack](../azure-stack-servicing-policy.md).  
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](../azure-stack-monitor-update.md).  
- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).  
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](../azure-stack-apply-updates.md).  