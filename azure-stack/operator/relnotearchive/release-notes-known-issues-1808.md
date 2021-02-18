---
title: Actualización 1808 de Azure Stack | Microsoft Docs
description: Obtenga información sobre las novedades de la actualización 1808 de los sistemas integrados de Azure Stack, incluidos los problemas conocidos y dónde se puede descargar la actualización.
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
ms.date: 10/02/2018
ms.author: sethm
ms.reviewer: justini
ROBOTS: NOINDEX
ms.openlocfilehash: 3574c55568db86c96d22d43fdf1bb08ac79c061e
ms.sourcegitcommit: f9be5640dd445b3d926c9ce3e2165e96c72ece89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2021
ms.locfileid: "100009203"
---
# <a name="azure-stack-1808-update"></a>Actualización de Azure Stack 1808

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el contenido del paquete de actualización 1808. El paquete de actualización incluye mejoras, correcciones y problemas conocidos de esta versión de Azure Stack. También se incluye un vínculo para que pueda descargar la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 1808 de Azure Stack es **1.1808.0.97**.  

### <a name="new-features"></a>Nuevas características

Esta actualización incluye las siguientes correcciones para Azure Stack.

<!--  2682594   | IS  --> 
- **Ahora en todos los entornos de Azure Stack se usa el formato de zona horaria Hora universal coordinada (UTC).**  Ahora todos los datos de registro y la información relacionada se muestra en formato UTC. Si actualiza desde una versión anterior que no se ha instalado con UTC, el entorno se actualiza para usar la hora UTC. 

<!-- 2437250  | IS  ASDK --> 
- **Compatibilidad con Managed Disks.** Ahora se puede usar Managed Disks en las máquinas virtuales de Azure Stack y conjuntos de escalado de máquinas virtuales. Para más información, vea [Azure Stack Managed Disks: Differences and considerations](../../user/azure-stack-managed-disk-considerations.md) (Managed Disks de Azure Stack: Diferencias y consideraciones).

<!-- 2563799  | IS  ASDK --> 
- **Azure Monitor**. Al igual que ocurre con Azure Monitor en Azure, Azure Monitor en Azure Stack dispone de registros y métricas de infraestructura de nivel básico de la mayoría de los servicios. Para más información, vea [Azure Monitor on Azure Stack](../../user/azure-stack-metrics-azure-data.md) (Azure Monitor en Azure Stack).

<!-- 2487932| IS --> 
- **Preparación del host de extensiones**. Puede usar el host de extensiones para ayudar a proteger Azure Stack reduciendo el número de puertos TCP/IP necesarios. Con la actualización 1808, puede preparar Azure Stack para el host de extensiones. Para más información, vea [Prepare for extension host for Azure Stack](../azure-stack-extension-host-prepare.md) (Preparación de un host de extensiones para Azure Stack).

<!-- IS --> 
- **Los elementos de la galería para Virtual Machine Scale Sets ahora están integrados**. El elemento de la galería Virtual Machine Scale Set ahora está disponible en los portales de usuario y administrador sin necesidad de descargarlo.  Si actualiza a 1808 está disponible tras la finalización de la actualización.  

<!-- IS, ASDK --> 
- **Escalado de conjuntos de escalado de máquinas virtuales**. Puede usar el portal para [escalar un conjunto de escalado de máquinas virtuales](../azure-stack-compute-add-scalesets.md#scale-a-virtual-machine-scale-set) (VMSS).    

<!-- 2489570 | IS ASDK--> 
- **Compatibilidad con configuraciones de directiva IPSec/IKE personalizadas** para [puertas de enlace de VPN en Azure Stack](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md).

<!-- | IS ASDK--> 
- **Elemento de Marketplace de Kubernetes**. Ahora puede implementar clústeres de Kubernetes con el [Elemento de Marketplace de Kubernetes](../azure-stack-solution-template-kubernetes-cluster-add.md). Los usuarios pueden seleccionar el elemento de Kubernetes y rellenar varios parámetros para implementar un clúster de Kubernetes en Azure Stack. El propósito de las plantillas es facilitar a los usuarios la configuración de implementaciones de Kubernetes de desarrollo y pruebas en unos pocos pasos.

<!-- | IS ASDK--> 
- **Plantillas de cadena de bloques**. Ahora puede ejecutar [las implementaciones del consorcio Ethereum](../../user/azure-stack-ethereum.md) en Azure Stack. Puede encontrar tres nuevas plantillas en las [plantillas de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates). Permiten al usuario implementar y configurar una red del consorcio Ethereum de varios miembros con conocimientos mínimos de Azure y Ethereum. El propósito de las plantillas es facilitar a los usuarios la configuración de implementaciones de cadena de bloques de desarrollo y pruebas en unos pocos pasos.

<!-- | IS ASDK--> 
- **El perfil de versión de API 2017-03-09-profile se ha actualizado a 2018-03-01-hybrid**. Los perfiles de la API especifican el proveedor de recursos de Azure y la versión de la API para los puntos de conexión REST de Azure. Para más información sobre los perfiles, vea [Administración de perfiles de versión de API en Azure Stack](../../user/azure-stack-version-profiles.md).

 ### <a name="fixed-issues"></a>Problemas corregidos
<!-- IS ASDK--> 
- Se ha corregido el problema de la creación de un conjunto de disponibilidad en el portal que provocaba que el conjunto tuviera un dominio de error y un dominio de actualización de 1. 

<!-- IS ASDK --> 
- Ahora en el portal hay valores disponibles para escalar conjuntos de escalado de máquinas virtuales.  

<!-- 2494144- IS, ASDK --> 
- Se ha corregido el problema que impedía que aparecieran algunos tamaños de máquina virtual de la serie F al seleccionar un tamaño de máquina virtual para la implementación. 

<!-- IS, ASDK --> 
- Mejoras de rendimiento al crear máquinas virtuales y uso más optimizado del almacenamiento subyacente.

- **Varias revisiones** de rendimiento, estabilidad, seguridad y el sistema operativo que se usa en Azure Stack.


### <a name="changes"></a>Cambios
<!-- 1697698  | IS, ASDK --> 
-  Ahora los *tutoriales de inicio rápido* del panel del portal de usuario están vinculados a artículos pertinentes en la documentación en línea de Azure Stack.

<!-- 2515955   | IS ,ASDK--> 
- *Todos los servicios* reemplaza a *Más servicios* en los portales de administración y del usuario de Azure Stack. Ahora se puede usar *Todos los servicios* como alternativa al desplazamiento en los portales de Azure Stack de la misma manera que en los portales de Azure.

<!-- TBD | IS, ASDK --> 
- *+ Crear un recurso* reemplaza a *+ Nuevo* en los portales de administración y del usuario de Azure Stack.  Ahora se puede usar *+ Crear un recurso* como alternativa al desplazamiento en los portales de Azure Stack de la misma manera que en los portales de Azure.  

<!--  TBD | IS, ASDK --> 
-  Se han retirado los tamaños de máquina virtual *A básico* para la [creación de conjuntos de escalado de máquinas virtuales](../azure-stack-compute-add-scalesets.md) (VMSS) a través del portal. Para crear un VMSS con este tamaño, use PowerShell o una plantilla.  

### <a name="common-vulnerabilities-and-exposures"></a>Puntos vulnerables y exposiciones comunes

Esta actualización instala las actualizaciones siguientes:  

- [CVE-2018-0952](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-0952)
- [CVE-2018-8200](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8200)
- [CVE-2018-8204](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8204)
- [CVE-2018-8253](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8253)
- [CVE-2018-8339](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8339)
- [CVE-2018-8340](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8340)
- [CVE-2018-8341](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8341)
- [CVE-2018-8343](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8343)
- [CVE-2018-8344](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8344)
- [CVE-2018-8345](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8345)
- [CVE-2018-8347](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8347)
- [CVE-2018-8348](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8348)
- [CVE-2018-8349](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8349)
- [CVE-2018-8394](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8394)
- [CVE-2018-8398](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8398)
- [CVE-2018-8401](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8401)
- [CVE-2018-8404](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8404)
- [CVE-2018-8405](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8405)
- [CVE-2018-8406](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8406)  

Para obtener más información sobre estas vulnerabilidades, haga clic en los vínculos anteriores o vea el artículo de Microsoft Knowledge Base [4343887](https://support.microsoft.com/help/4343887).

Esta actualización también contiene la mitigación de la vulnerabilidad de canal del lado de ejecución especulativa conocida como Error de terminal L1 (L1TF), que se describe en [Microsoft Security Advisory ADV180018](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/ADV180018).  

### <a name="prerequisites"></a>Requisitos previos

- Antes de aplicar la actualización de Azure Stack 1808, instale la actualización 1807. 
- Antes de iniciar la instalación de esta actualización, ejecute [Test-AzureStack](../azure-stack-diagnostic-test.md) con los parámetros siguientes para validar el estado de Azure Stack y resolver todos los problemas operativos detectados, incluidas todas las advertencias y errores. Repase también las alertas activas y resuelva las que requieran alguna acción.  

  ```PowerShell
  Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary
  ```   

### <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización

- Al ejecutar [Test-AzureStack](../azure-stack-diagnostic-test.md) después de la actualización 1808, se muestra un mensaje de advertencia desde el Controlador de administración de placa base (BMC). Puede omitir esta advertencia sin problemas.

<!-- 2468613 - IS --> 
- Durante la instalación de esta actualización, puede que vea alertas con el título *Error – Template for FaultType UserAccounts.New is missing.* (Error: Falta la plantilla para FaultType UserAccounts.New).  Puede omitir estas alertas con seguridad. Estas alertas se cerrarán automáticamente cuando se complete la instalación de esta actualización.

<!-- 2489559 - IS --> 
- No intente crear máquinas virtuales durante la instalación de esta actualización. Para más información sobre cómo administrar las actualizaciones, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).

<!-- 2830461 - IS --> 
- En determinadas circunstancias cuando una actualización requiere atención, es posible que no se genere la alerta correspondiente. El estado preciso se reflejará en el portal y no se ve afectado.

### <a name="post-update-steps"></a>Pasos posteriores a la actualización
Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte los siguientes artículos de la Knowledge base, así como nuestra [Directiva de mantenimiento](../azure-stack-servicing-policy.md). 
- [KB 4465859: Revisión de Azure Stack 1.1808.2.104](https://support.microsoft.com/help/4465859/)

## <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

Los siguientes son problemas conocidos posteriores a la instalación de esta compilación.

### <a name="portal"></a>Portal

- La documentación técnica de Azure Stack se centra en la versión más reciente. Debido a los cambios del portal entre las versiones, es posible que lo que vea al usar los portales de Azure Stack varíe con respecto a lo que ve en la documentación. 

<!-- TBD - IS ASDK --> 
- Puede que vea un panel en blanco en el portal. Para recuperar el panel, haga clic en **Editar panel** y, después, haga clic con el botón derecho y seleccione **Restaurar al estado predeterminado**.

<!-- 2930718 - IS ASDK --> 
- En el portal de administrador, al acceder a los detalles de cualquier suscripción de usuario después de cerrar la hoja y hacer clic en **Recientes**, no aparece el nombre de la suscripción de usuario.

<!-- 3060156 - IS ASDK --> 
-  En los portales de administrador y usuario, hacer clic en la configuración del portal y seleccionar **Eliminar todas las opciones de configuración y los paneles privados** no funciona según lo previsto. Se muestra una notificación de error. 

<!-- 2930799 - IS ASDK --> 
- En los portales de administrador y de usuario, en **Todos los servicios**, el recurso **Plan de DDoS Protection** se muestra de forma incorrecta. No está disponible realmente en Azure Stack. Si se intenta crearlo, se muestra un error que indica que el portal no pudo crear el elemento de Marketplace. 

<!-- 2930820 - IS ASDK --> 
- En los portales de administrador y de usuario, si se busca "Docker", el elemento se devuelve de forma incorrecta. No está disponible realmente en Azure Stack. Si se intenta crearlo, se muestra una hoja con una indicación de error. 

<!-- 2967387 IS, ASDK --> 
- La cuenta que se usa para iniciar sesión en el portal de administrador o usuario de Azure Stack se muestra como **Usuario no identificado**. Esto se produce cuando en la cuenta no se ha especificado un *Nombre* o un *Apellido*. Para solucionar este problema, modifique la cuenta de usuario para proporcionar el nombre o el apellido. Después, tendrá que cerrar sesión y volver a iniciar sesión en el portal. 

<!--  2873083 - IS ASDK --> 
-  Cuando se usa el portal para crear un conjunto de escalado de máquinas virtuales (VMSS), la lista desplegable *Tamaño de instancia* no se carga correctamente cuando se usa Internet Explorer. Para solucionar este problema, use otro explorador cuando utilice el portal para crear un VMSS.  

<!-- 2931230 IS  ASDK --> 
- No se pueden eliminar los planes que se agregan a una suscripción de usuario como planes complementarios, aunque se quite el plan de la suscripción de usuario. El plan permanecerá hasta que también se eliminen las suscripciones a las que haga referencia el plan complementario. 

<!--2760466 IS  ASDK --> 
- Cuando se instala un nuevo entorno de Azure Stack que ejecuta esta versión, la alerta que indica *Activación necesaria* podría no mostrarse. La [activación](../azure-stack-registration.md) se requiere para poder usar la redifusión de marketplace.  

<!-- TBD - IS ASDK --> 
- Los dos tipos de suscripción administrativa que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**. Estos tipos de suscripción están visibles en los nuevos entornos de Azure Stack a partir de la versión 1804 pero aún no están listos para su uso. Tendrá que seguir usando el tipo de suscripción del **proveedor predeterminado**.

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

<!-- 3099544 IS, ASDK --> 
- Cuando cree una máquina virtual mediante el portal de Azure Stack y seleccione el tamaño de la máquina, se mostrará la columna USD/mes con el mensaje **No disponible**. Esta columna no debería aparecer, ya que en Azure Stack no se permite mostrar la columna de precios de la máquina virtual.

<!-- 3090289 IS, ASDK --> 
- Después de aplicar la actualización 1808, se pueden producir los problemas siguientes al implementar máquinas virtuales con Managed Disks:

   1. Si la suscripción se creó antes de la actualización 1808, se puede producir un error en la implementación de máquinas virtuales con Managed Disks con un mensaje de error interno. Para resolver el error, siga estos pasos en cada suscripción:
      1. En el portal del inquilino, vaya a **Suscripciones** y busque la suscripción. Haga clic en **Proveedores de recursos**, después en **Microsoft.Compute** y luego en **Volver a registrar**.
      2. En la misma suscripción, vaya a **Control de acceso (IAM)** y verifique que **Azure Stack – Managed Disk** (Azure Stack - Disco administrado) aparece en la lista.
   2. Si ha configurado un entorno de varios inquilinos, se puede producir un error con un mensaje de error interno en la implementación de máquinas virtuales en una suscripción asociada con un directorio de invitados. Para solucionar el error, siga estos pasos:
      1. Aplique la [revisión 1808 de Azure Stack](https://support.microsoft.com/help/4465859).
      2. Siga los pasos de [este artículo](../azure-stack-enable-multitenancy.md#register-a-guest-directory) para volver a configurar cada uno de los directorios de invitado.

<!-- 2869209 IS, ASDK --> 
- Cuando se usa el [cmdlet **Add-AzsPlatformImage**](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage?view=azurestackps-1.4.0&preserve-view=true), se debe usar el parámetro **-OsUri** como el URI de la cuenta de almacenamiento donde se ha cargado el disco. Si usa la ruta de acceso local del disco, se produce el siguiente error en el cmdlet: *Se produjo un problema en la operación de larga ejecución con el estado "Error"* . 

<!--  2966665 IS, ASDK --> 
- Al conectar discos de datos SSD a máquinas virtuales de discos administrados de tamaño prémium (DS, DSv2, Fs y Fs_V2), se produce el siguiente error:  *Error al actualizar discos para la máquina virtual "vmName". Error: No se puede realizar la operación solicitada porque el tipo de cuenta de almacenamiento "Premium_LRS" no es compatible con el tamaño de VM "Standard_DS/Ds_V2/FS/Fs_v2)*

   Para solucionar este problema, use discos de datos *Standard_LRS* en lugar de *discos Premium_LRS*. El uso de discos de datos *Standard_LRS* no cambia el costo de E/S por segundo ni de facturación. 

<!--  2795678 IS, ASDK --> 
- Cuando usa el portal para crear máquinas virtuales (VM) en un tamaño de máquina virtual prémium (DS, Ds_v2, FS, FSv2), la máquina virtual se crea en una cuenta de almacenamiento estándar. La creación en una cuenta de almacenamiento estándar no afecta al funcionamiento, E/S por segundo ni la facturación. 

   Puede omitir la siguiente advertencia sin problemas: *Ha elegido usar un disco estándar en un tamaño que admite discos prémium. Esto no se recomienda ya que podría afectar al rendimiento del sistema operativo. Considere la opción de usar Premium Storage (SSD).*

<!-- 2967447 - IS, ASDK --> 
- La experiencia de creación de conjuntos de escalado de máquinas virtuales (VMSS) proporciona la versión 7.2 basada en CentOS como una opción para la implementación. Como esa imagen no está disponible en Azure Stack, seleccione otro sistema operativo para la implementación o use una plantilla de Azure Resource Manager en la que se especifique otra imagen de CentOS que el operador haya descargado de Marketplace antes de la implementación.  

<!-- 2724873 - IS --> 
-  Al usar los cmdlets de PowerShell **Start-AzsScaleUnitNode** o **Stop-AzsScaleunitNode** para administrar las unidades de escalado, el primer intento para iniciar o detener la unidad de escalado podría producir un error. Si se produce un error en el cmdlet en la primera ejecución, ejecute el cmdlet una segunda vez. La segunda ejecución debe ser correcta para completar la operación. 

<!-- TBD - IS ASDK --> 
- Al crear máquinas virtuales en el portal de usuario de Azure Stack, el portal muestra un número incorrecto de discos de datos que se pueden asociar a una máquina virtual de la serie DS. Las máquinas virtuales de la serie DS pueden albergar tantos discos de datos como la configuración de Azure.

<!-- TBD - IS ASDK --> 
- Si aprovisionar una extensión en una implementación de máquina virtual tarda demasiado tiempo, los usuarios deberían dejar que se agote el tiempo de espera de aprovisionamiento en lugar de intentar detener el proceso para desasignar o eliminar la máquina virtual.  

<!-- 1662991 IS ASDK --> 
- No se admite el diagnóstico de máquinas virtuales Linux en Azure Stack. Si implementa una máquina virtual Linux con diagnósticos de máquina virtual habilitado, se producirá un error en la implementación. Tampoco se podrá realizar la implementación si habilita las métricas básicas de máquina virtual Linux a través de la configuración de diagnóstico.  

<!-- 2724961- IS ASDK --> 
- Al registrar el proveedor de recursos **Microsoft.Insight** en la configuración de la suscripción y crear una máquina virtual Windows con el diagnóstico del SO invitado habilitado, el gráfico Porcentaje de CPU en la página de información general de la máquina virtual no podrá mostrar los datos de métricas.

   Para buscar el gráfico Porcentaje de CPU para la máquina virtual, vaya a la hoja **Métrica** y muestre todas las métricas de invitado de las máquinas virtuales de Windows admitidas.



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


<!-- ### SQL and MySQL-->


### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Los usuarios deben registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.

<!-- 2489178 - IS ASDK --> 
- Para escalar horizontalmente la infraestructura (roles de trabajo, administración y front-end), debe usar PowerShell, tal como se describe en las notas de la versión de proceso.



### <a name="usage"></a>Uso  
<!-- TBD - IS ASDK --> 
-  El uso de los datos del medidor de uso de la dirección IP pública muestran el mismo valor *EventDateTime* para cada registro en lugar de la marca *TimeDate* que muestra cuándo se creó el registro. Actualmente, no puede usar estos datos para realizar un recuento adecuado del uso de la dirección IP pública.


<!-- #### Identity -->
<!-- #### Marketplace -->


## <a name="download-the-update"></a>Descarga de la actualización
Puede descargar el paquete de actualización de Azure Stack 1808 desde [aquí](https://aka.ms/azurestackupdatedownload).
  

## <a name="next-steps"></a>Pasos siguientes
- Para revisar la directiva de servicio de los sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido, consulte [Directiva de servicio de Azure Stack](../azure-stack-servicing-policy.md).  
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](../azure-stack-monitor-update.md).  
- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).  
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](../azure-stack-apply-updates.md).  