---
title: Problemas conocidos de Azure Stack
description: Obtenga información sobre los problemas conocidos de las versiones de Azure Stack.
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
ms.date: 11/21/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 11/21/2019
ms.openlocfilehash: 684f6925c32eabca51fa89776ec95b57a852a987
ms.sourcegitcommit: d9430072dd96ae305101da6d8a47d6c23a0a64c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995593"
---
# <a name="azure-stack-known-issues"></a>Problemas conocidos de Azure Stack

En este artículo se enumeran los problemas conocidos de las versiones de Azure Stack. La lista se actualiza conforme se identifican nuevos problemas.

Para tener acceso a los problemas conocidos de una versión diferente, use la lista desplegable del selector de versiones encima de la tabla de contenido de la izquierda.

::: moniker range=">=azs-1906"
> [!IMPORTANT]  
> Revise esta sección antes de aplicar la actualización.
::: moniker-end
::: moniker range="<azs-1906"
> [!IMPORTANT]  
> Si a su instancia le faltan más de dos actualizaciones de Azure Stack, se considera que no cumple los requisitos. [Para recibir soporte técnico, deberá actualizarla al menos a la versión mínima admitida.](azure-stack-servicing-policy.md#keep-your-system-under-support) 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-1910"
## <a name="update"></a>Actualizar

Para ver los problemas conocidos con las actualizaciones de Azure Stack Hub, consulte [Solución de problemas de actualizaciones en Azure Stack Hub](azure-stack-updates-troubleshoot.md).


## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Suscripciones administrativas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Las dos suscripciones administrativas que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**.
- Corrección: Si tiene recursos que se ejecutan en estas dos suscripciones, vuelva a crearlos en las suscripciones de usuario.
- Repetición: Común

### <a name="subscriptions-lock-blade"></a>Hoja Bloqueo en las suscripciones

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal del administrador, la hoja **Bloqueo** para las suscripciones de usuario tiene dos botones que muestran **Suscripción**.
- Repetición: Común

### <a name="subscription-permissions"></a>Permisos de suscripción

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No puede ver los permisos de la suscripción mediante los portales de Azure Stack.
- Corrección: Use [PowerShell para verificar los permisos](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Repetición: Común

### <a name="storage-account-settings"></a>Configuración de cuentas de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Configuración** de la cuenta de almacenamiento muestra una opción para cambiar el **tipo de transferencia de seguridad**. La característica no se admite actualmente en Azure Stack.
- Repetición: Común

### <a name="upload-blob-with-oauth-error"></a>Carga de BLOB con error en OAuth

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, al intentar cargar un blob mediante la opción **OAuth(preview)** , la tarea genera un mensaje de error.
- Corrección: Cargue el blob mediante la opción SAS.
- Repetición: Común

### <a name="upload-blob-option-unsupported"></a>Opción de carga de BLOB no admitida

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, cuando intenta cargar un blob en la hoja de carga, hay una opción para seleccionar **AAD** o **Autenticación de clave**, pero **AAD** no se admite en Azure Stack.
- Repetición: Común

### <a name="load-balancer-backend-pool"></a>Grupo back-end de equilibradores de carga

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, al agregar un grupo back-end de **equilibradores de carga**, aparece el mensaje de error **No se pudo guardar el grupo back-end de equilibradores de carga**; sin embargo, la operación se realiza correctamente.
- Repetición: Común

### <a name="incorrect-tooltip-when-creating-vm"></a>Información sobre herramientas incorrecta al crear una máquina virtual

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, cuando se selecciona un disco administrado con el tipo de disco SSD Premium, la lista desplegable muestra **Disco del SO**. La información sobre herramientas situada junto a esa opción indica **Puede que algunos tamaños del disco del SO estén disponibles gratis en la cuenta gratuita de Azure**; sin embargo, esto no es válido para Azure Stack. Además, la lista incluye **Cuenta gratuita válida**, opción que tampoco es válida para Azure Stack.
- Repetición: Común

### <a name="vpn-troubleshoot-and-metrics"></a>Solución de problemas y métricas de VPN

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, aparecen la característica **Solución de problemas de VPN** y **Métricas** en un recurso de puerta de enlace de VPN, pero esto no se admite en Azure Stack.
- Repetición: Común

### <a name="adding-extension-to-vm-scale-set"></a>Incorporación de una extensión al conjunto de escalado de máquinas virtuales

- Aplicable a: este problema se aplica a las versiones 1907 y posteriores.
- Causa: en el portal de usuarios, una vez que se crea un conjunto de escalado de máquinas virtuales, la interfaz de usuario no permite al usuario agregar una extensión.
- Repetición: Común

### <a name="delete-a-storage-container"></a>Eliminación de un contenedor de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, cuando un usuario intenta eliminar un contenedor de almacenamiento, se produce un error en la operación si no se activa la opción **Invalidar la configuración de rol RBAC y Azure Policy**.
- Corrección: asegúrese de que se activa la casilla **Invalidar la configuración de rol RBAC y Azure Policy**.
- Repetición: Común

### <a name="refresh-button-on-virtual-machines-fails"></a>Error en el botón de actualización de las máquinas virtuales

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, cuando navega a **Máquinas virtuales** e intenta actualizar con el botón de la parte superior, los estados no se actualizan correctamente.
- Corrección: el estado se actualiza automáticamente cada 5 minutos, con independencia de si se hace clic en el botón de actualización o no. Espere 5 minutos y compruebe el estado.
- Repetición: Común

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, cuando se crea una tabla de rutas, aparece **Puerta de enlace de red virtual** como una de las opciones de Tipo de próximo salto; sin embargo, esta opción no se admite en Azure Stack.
- Repetición: Común

### <a name="storage-account-options"></a>Opciones de la cuenta de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, el nombre de las cuentas de almacenamiento se muestra como **Cuenta de almacenamiento: blob, archivo, tabla, cola**; sin embargo, la opción **archivo** no se admite en Azure Stack.
- Repetición: Común

### <a name="storage-account-configuration"></a>Configuración de la cuenta de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, cuando se crea una cuenta de almacenamiento y se ve su **Configuración**, no se pueden guardar los cambios de configuración, ya que generarían un error de AJAX.
- Repetición: Común

### <a name="capacity-monitoring-in-sql-resource-provider-keeps-loading"></a>La supervisión de la capacidad del proveedor de recursos de SQL continúa cargándose

- Aplicable a: este problema se aplica a la actualización de Azure Stack 1910 o posterior, con la versión 1.1.33.0 o anterior del proveedor de recursos de SQL instalada.
- Causa: la versión actual del proveedor de recursos de SQL no es compatible con algunos de los cambios más recientes del portal en la actualización 1910.
- Corrección: siga el proceso de actualización del proveedor de recursos para aplicar la revisión 1.1.47.0 del proveedor de recursos de SQL después de cambiar Azure Stack a la actualización 1910 ([versión 1.1.47.0 del proveedor de recursos de SQL](https://aka.ms/azurestacksqlrp11470)). Para el proveedor de recursos de MySQL, también se recomienda aplicar la revisión 1.1.47.0 del proveedor de recursos de MySQL después de cambiar Azure Stack a la actualización 1910 ([versión 1.1.47.0 del proveedor de recursos de MySQL](https://aka.ms/azurestackmysqlrp11470)).
- Repetición: Común

### <a name="access-control-iam"></a>Control de acceso (IAM)

- Aplicable a: este problema se aplica a la versión 1903 y posteriores.
- Causa: La extensión IAM no está actualizada. Ibiza se incluyó con Azure Stack e introdujo un nuevo comportamiento que hace que la extensión RBAC deje de funcionar si el usuario abre la extensión RBAC desde una suscripción que no está seleccionada en el selector de suscripciones globales.
- Corrección: Asegúrese de que la suscripción esté activada en el selector de suscripciones globales. 

## <a name="networking"></a>Redes

### <a name="load-balancer"></a>Equilibrador de carga

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: al agregar máquinas virtuales del conjunto de disponibilidad al grupo back-end de un equilibrador de carga, se muestra un mensaje de error en el portal que indica **No se pudo guardar el grupo back-end de equilibradores de carga**. Se trata de un problema cosmético en el portal; la funcionalidad existe y las máquinas virtuales se agregan correctamente al grupo back-end de modo interno.
- Repetición: Común

### <a name="network-security-groups"></a>Grupos de seguridad de red

- Aplicable a: este problema se aplica a todas las versiones admitidas. 
- Causa: No se puede crear una regla **DenyAllOutbound** explícita en un grupo de seguridad de red, ya que esto impedirá todas las comunicaciones internas con la infraestructura necesarias para que se complete la implementación de la máquina virtual.
- Repetición: Común

### <a name="service-endpoints"></a>Puntos de conexión de servicio

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Red virtual** muestra una opción para usar **puntos de conexión de servicio**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

### <a name="network-interface"></a>interfaz de red

#### <a name="addingremoving-network-interface"></a>Incorporación o eliminación de una interfaz de red

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede agregar una nueva interfaz de red a una VM cuyo estado es **En ejecución**.
- Corrección: detenga la máquina virtual antes de agregar o quitar una interfaz de red.
- Repetición: Común

#### <a name="primary-network-interface"></a>Interfaz de red principal

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede cambiar la NIC principal de una máquina virtual. La eliminación o desasociación de la NIC principal provoca problemas al iniciar la máquina virtual.
- Repetición: Común

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

#### <a name="alerts"></a>Alertas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Puerta de enlace de red virtual** muestra una opción para usar **alertas**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="active-active"></a>Activo-activo

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, durante la creación, y en el menú de recursos de **Puerta de enlace de red virtual**, verá una opción para habilitar la configuración **activa-activa**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="vpn-troubleshooter"></a>Solucionador de problemas de VPN

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, la hoja **Conexiones** muestra una característica denominada **Solucionador de problemas de VPN**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="documentation"></a>Documentación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Los vínculos de la documentación de la página de información general de Puerta de enlace de red virtual enlazan a la documentación específica de Azure en lugar de a la de Azure Stack. Use los vínculos siguientes para la documentación de Azure Stack:

  - [SKU de puerta de enlace](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Conexiones de alta disponibilidad](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configuración de BGP en Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuitos ExpressRoute](azure-stack-connect-expressroute.md)
  - [Especificación de directivas de IPsec o IKE personalizadas](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Proceso

### <a name="vm-boot-diagnostics"></a>Diagnósticos de arranque de VM

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: al crear una nueva máquina virtual Windows, puede aparecer el siguiente error: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** (No se pudo iniciar la máquina virtual "vm-name". No se pudo actualizar la configuración de salida de serie de la VM 'vm-name'). El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque.
- Corrección: Vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.
- Repetición: Común

### <a name="consumed-compute-quota"></a>Cuota de proceso consumida

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Al crear una nueva máquina virtual, es posible que reciba un error como, por ejemplo, **Esta suscripción tiene una capacidad de vCPU regionales totales en esta ubicación y está usando todas las disponibles 50 vCPU regionales totales**. Esto indica que se ha alcanzado la cuota de núcleos totales que tiene disponibles.
- Corrección: pida a su operador un plan complementario con una cuota adicional. La edición de la cuota del plan actual no funcionará ni reflejará la cuota incrementada.
- Repetición: poco frecuente

### <a name="virtual-machine-scale-set"></a>Conjunto de escalado de máquina virtual

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Crear errores durante la revisión y actualización de entornos de Azure Stack de 4 nodos

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La creación de VM en un conjunto de disponibilidad de 3 dominios de error y la creación de una instancia de conjunto de escalado de máquinas virtuales genera un error **FabricVmPlacementErrorUnsupportedFaultDomainSize** durante el proceso de actualización en un entorno de Azure Stack de 4 nodos.
- Corrección: Se pueden crear VM únicas en un conjunto de disponibilidad con 2 dominios de error correctamente. Sin embargo, la creación de instancias del conjunto de escalado todavía no está disponible durante el proceso de actualización en una implementación de Azure Stack de 4 nodos.

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1908"
## <a name="1908-update-process"></a>Proceso de actualización de 1908

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al intentar instalar la actualización de Azure Stack, es posible que se produzca un error en el estado de esa actualización y dicho estado cambie a **PreparationFailed**. La causa es que el proveedor de recursos de actualización (URP) no se puede transferir correctamente desde el contenedor de almacenamiento a un recurso compartido de infraestructura interno para su procesamiento.
- Corrección: A partir de la versión 1901 (1.1901.0.95), para solucionar este problema, puede hacer clic en **Actualizar ahora** de nuevo (en lugar de en **Reanudar**). A continuación, URP limpia los archivos del intento anterior y vuelve a iniciar la descarga. Si el problema persiste, recomendamos cargar manualmente la actualización siguiendo la [sección de instalación de actualizaciones](azure-stack-apply-updates.md#install-updates-and-monitor-progress).
- Repetición: Común

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Suscripciones administrativas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Las dos suscripciones administrativas que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**.
- Corrección: Si tiene recursos que se ejecutan en estas dos suscripciones, vuelva a crearlos en las suscripciones de usuario.
- Repetición: Común

### <a name="subscriptions-properties-blade"></a>Hoja de propiedades de suscripciones

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de administrador, la hoja **Propiedades** de las suscripciones no se carga correctamente.
- Corrección: puede ver estas propiedades de suscripciones en el panel **Essentials** (Información esencial) de la hoja **Introducción a las suscripciones**.
- Repetición: Común

### <a name="subscriptions-lock-blade"></a>Hoja Bloqueo en las suscripciones

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal del administrador, la hoja **Bloqueo** para las suscripciones de usuario tiene dos botones que muestran **Suscripción**.
- Repetición: Común

### <a name="subscription-permissions"></a>Permisos de suscripción

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No puede ver los permisos de la suscripción mediante los portales de Azure Stack.
- Corrección: Use [PowerShell para verificar los permisos](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Repetición: Común

### <a name="storage-account-settings"></a>Configuración de cuentas de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Configuración** de la cuenta de almacenamiento muestra una opción para cambiar el **tipo de transferencia de seguridad**. La característica no se admite actualmente en Azure Stack.
- Repetición: Común

### <a name="upload-blob"></a>Carga de blob

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, al intentar cargar un blob mediante la opción **OAuth(preview)** , la tarea genera un mensaje de error.
- Corrección: Cargue el blob mediante la opción SAS.
- Repetición: Común

## <a name="networking"></a>Redes

### <a name="load-balancer"></a>Load Balancer

- Aplicable a: este problema se aplica a todas las versiones admitidas. 
- Causa: al agregar máquinas virtuales del conjunto de disponibilidad al grupo back-end de un equilibrador de carga, se muestra un mensaje de error en el portal que indica **No se pudo guardar el grupo back-end de equilibradores de carga**. Se trata de un problema cosmético en el portal; la funcionalidad existe y las máquinas virtuales se agregan correctamente al grupo back-end de modo interno. 
- Repetición: Común

### <a name="network-security-groups"></a>Grupos de seguridad de red

- Aplicable a: este problema se aplica a todas las versiones admitidas. 
- Causa: No se puede crear una regla **DenyAllOutbound** explícita en un grupo de seguridad de red, ya que esto impedirá todas las comunicaciones internas con la infraestructura necesarias para que se complete la implementación de la máquina virtual.
- Repetición: Común

### <a name="service-endpoints"></a>Puntos de conexión de servicio

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Red virtual** muestra una opción para usar **puntos de conexión de servicio**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

### <a name="network-interface"></a>interfaz de red

#### <a name="addingremoving-network-interface"></a>Incorporación o eliminación de una interfaz de red

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede agregar una nueva interfaz de red a una VM cuyo estado es **En ejecución**.
- Corrección: Detenga la máquina virtual antes de agregar o quitar una interfaz de red.
- Repetición: Común

#### <a name="primary-network-interface"></a>Interfaz de red principal

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede agregar una nueva interfaz de red a una VM cuyo estado es **En ejecución**.
- Corrección: Detenga la máquina virtual antes de agregar o quitar una interfaz de red.
- Repetición: Común

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

#### <a name="alerts"></a>Alertas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Puerta de enlace de red virtual** muestra una opción para usar **alertas**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="active-active"></a>Activo-activo

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, durante la creación, y en el menú de recursos de **Puerta de enlace de red virtual**, verá una opción para habilitar la configuración **activa-activa**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="vpn-troubleshooter"></a>Solucionador de problemas de VPN

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Conexiones** muestra una característica denominada **Solucionador de problemas de VPN**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="documentation"></a>Documentación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Los vínculos de la documentación de la página de información general de Puerta de enlace de red virtual enlazan a la documentación específica de Azure en lugar de a la de Azure Stack. Use los vínculos siguientes para la documentación de Azure Stack:

  - [SKU de puerta de enlace](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Conexiones de alta disponibilidad](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configuración de BGP en Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuitos ExpressRoute](azure-stack-connect-expressroute.md)
  - [Especificación de directivas de IPsec o IKE personalizadas](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Proceso

### <a name="vm-boot-diagnostics"></a>Diagnósticos de arranque de VM

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al crear una nueva máquina virtual Windows, puede aparecer el siguiente error: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** (No se pudo iniciar la máquina virtual "vm-name". No se pudo actualizar la configuración de salida de serie de la VM 'vm-name'). El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque.
- Corrección: Vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.
- Repetición: Común

### <a name="virtual-machine-scale-set"></a>Conjunto de escalado de máquina virtual

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Crear errores durante la revisión y actualización de entornos de Azure Stack de 4 nodos

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La creación de VM en un conjunto de disponibilidad de 3 dominios de error y la creación de una instancia de conjunto de escalado de máquinas virtuales genera un error **FabricVmPlacementErrorUnsupportedFaultDomainSize** durante el proceso de actualización en un entorno de Azure Stack de 4 nodos.
- Corrección: Se pueden crear VM únicas en un conjunto de disponibilidad con 2 dominios de error correctamente. Sin embargo, la creación de instancias del conjunto de escalado todavía no está disponible durante el proceso de actualización en una instancia de Azure Stack de 4 nodos.

### <a name="ubuntu-ssh-access"></a>Acceso a SSH en Ubuntu

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Una VM de Ubuntu 18.04 creada con la autorización de SSH habilitada no le permite usar las claves SSH para iniciar sesión.
- Corrección: Utilice el acceso a la VM para la extensión de Linux a fin de implementar las claves SSH después del aprovisionamiento o utilice la autenticación basada en contraseña.
- Repetición: Común

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>La contraseña de restablecimiento del conjunto de escalado de máquinas virtuales no funciona

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Aparece una nueva hoja para restablecer la contraseña en la interfaz de usuario del conjunto de escalado, pero Azure Stack todavía no admite el restablecimiento de contraseña en un conjunto de escalado.
- Corrección: Ninguno.
- Repetición: Común

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Nube con lluvia en el diagnóstico del conjunto de escalado

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La página de información general del conjunto de escalado de máquinas virtuales muestra un gráfico vacío. Al hacer clic en el gráfico vacío, se abre una hoja de "nube de lluvia". Este es el gráfico de información de diagnóstico del conjunto de escalado, como el porcentaje de CPU, y no es una característica admitida en la compilación actual de Azure Stack.
- Corrección: Ninguno.
- Repetición: Común

### <a name="virtual-machine-diagnostic-settings-blade"></a>Hoja de configuración de diagnóstico de la máquina virtual

- Aplicable a: este problema se aplica a todas las versiones admitidas.    
- Causa: La hoja de configuración de diagnóstico de la máquina virtual tiene una pestaña **Receptor**, que solicita una **Cuenta de Application Insights**. Este es el resultado de una nueva hoja y aún no se admite en Azure Stack.
- Corrección: Ninguno.
- Repetición: Común

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-update-process"></a>Proceso de actualización de 1907

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al intentar instalar la actualización 1907 de Azure Stack, es posible que se produzca un error en el estado de esa actualización y dicho estado cambie a **PreparationFailed**. La causa es que el proveedor de recursos de actualización (URP) no se puede transferir correctamente desde el contenedor de almacenamiento a un recurso compartido de infraestructura interno para su procesamiento.
- Corrección: A partir de la versión 1901 (1.1901.0.95), para solucionar este problema, puede hacer clic en **Actualizar ahora** de nuevo (en lugar de en **Reanudar**). A continuación, URP limpia los archivos del intento anterior y vuelve a iniciar la descarga. Si el problema persiste, recomendamos cargar manualmente el paquete de actualización siguiendo la sección [Importación e instalación de actualizaciones](azure-stack-apply-updates.md).
- Repetición: Común

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Suscripciones administrativas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Las dos suscripciones administrativas que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**.
- Corrección: Si tiene recursos que se ejecutan en estas dos suscripciones, vuelva a crearlos en las suscripciones de usuario.
- Repetición: Común

### <a name="subscriptions-properties-blade"></a>Hoja de propiedades de suscripciones

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de administrador, la hoja **Propiedades** de las suscripciones no se carga correctamente.
- Corrección: puede ver estas propiedades de suscripciones en el panel **Essentials** (Información esencial) de la hoja **Introducción a las suscripciones**.
- Repetición: Común

### <a name="subscription-permissions"></a>Permisos de suscripción

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No puede ver los permisos de la suscripción mediante los portales de Azure Stack.
- Corrección: Use [PowerShell para verificar los permisos](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Repetición: Común

### <a name="storage-account-settings"></a>Configuración de cuentas de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Configuración** de la cuenta de almacenamiento muestra una opción para cambiar el **tipo de transferencia de seguridad**. La característica no se admite actualmente en Azure Stack.
- Repetición: Común

### <a name="upload-blob"></a>Carga de blob

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, al intentar cargar un blob mediante la opción **OAuth(preview)** , la tarea genera un mensaje de error.
- Corrección: Cargue el blob mediante la opción SAS.
- Repetición: Común

## <a name="networking"></a>Redes

### <a name="load-balancer"></a>Load Balancer

- Aplicable a: este problema se aplica a todas las versiones admitidas. 
- Causa: al agregar máquinas virtuales del conjunto de disponibilidad al grupo back-end de un equilibrador de carga, se muestra un mensaje de error en el portal que indica **No se pudo guardar el grupo back-end de equilibradores de carga**. Se trata de un problema cosmético en el portal; la funcionalidad existe y las máquinas virtuales se agregan correctamente al grupo back-end de modo interno. 
- Repetición: Común

### <a name="network-security-groups"></a>Grupos de seguridad de red

- Aplicable a: este problema se aplica a todas las versiones admitidas. 
- Causa: No se puede crear una regla **DenyAllOutbound** explícita en un grupo de seguridad de red, ya que esto impedirá todas las comunicaciones internas con la infraestructura necesarias para que se complete la implementación de la máquina virtual.
- Repetición: Común

### <a name="service-endpoints"></a>Puntos de conexión de servicio

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Red virtual** muestra una opción para usar **puntos de conexión de servicio**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

### <a name="network-interface"></a>interfaz de red

#### <a name="addingremoving-network-interface"></a>Incorporación o eliminación de una interfaz de red

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede agregar una nueva interfaz de red a una VM cuyo estado es **En ejecución**.
- Corrección: Detenga la máquina virtual antes de agregar o quitar una interfaz de red.
- Repetición: Común

#### <a name="primary-network-interface"></a>Interfaz de red principal

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede agregar una nueva interfaz de red a una VM cuyo estado es **En ejecución**.
- Corrección: Detenga la máquina virtual antes de agregar o quitar una interfaz de red.
- Repetición: Común

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

#### <a name="alerts"></a>Alertas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Puerta de enlace de red virtual** muestra una opción para usar **alertas**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="active-active"></a>Activo-activo

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, durante la creación, y en el menú de recursos de **Puerta de enlace de red virtual**, verá una opción para habilitar la configuración **activa-activa**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="vpn-troubleshooter"></a>Solucionador de problemas de VPN

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Conexiones** muestra una característica denominada **Solucionador de problemas de VPN**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

### <a name="network-connection-type"></a>Tipo de conexión de red

- Aplicable a: Este problema se aplica a cualquier entorno de 1906 o 1907. 
- Causa: En el portal de usuarios, la hoja **AddConnection** muestra una opción para usar la opción **De red virtual a red virtual**. Esta característica no se admite actualmente en Azure Stack. 
- Repetición: Común 

#### <a name="documentation"></a>Documentación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Los vínculos de la documentación de la página de información general de Puerta de enlace de red virtual enlazan a la documentación específica de Azure en lugar de a la de Azure Stack. Use los vínculos siguientes para la documentación de Azure Stack:

  - [SKU de puerta de enlace](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Conexiones de alta disponibilidad](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configuración de BGP en Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuitos ExpressRoute](azure-stack-connect-expressroute.md)
  - [Especificación de directivas de IPsec o IKE personalizadas](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Proceso

### <a name="vm-boot-diagnostics"></a>Diagnósticos de arranque de VM

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al crear una nueva máquina virtual Windows, puede aparecer el siguiente error: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** (No se pudo iniciar la máquina virtual "vm-name". No se pudo actualizar la configuración de salida de serie de la VM 'vm-name'). El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque.
- Corrección: Vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.
- Repetición: Común

### <a name="virtual-machine-scale-set"></a>Conjunto de escalado de máquina virtual

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Crear errores durante la revisión y actualización de entornos de Azure Stack de 4 nodos

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La creación de VM en un conjunto de disponibilidad de 3 dominios de error y la creación de una instancia de conjunto de escalado de máquinas virtuales genera un error **FabricVmPlacementErrorUnsupportedFaultDomainSize** durante el proceso de actualización en un entorno de Azure Stack de 4 nodos.
- Corrección: Se pueden crear VM únicas en un conjunto de disponibilidad con 2 dominios de error correctamente. Sin embargo, la creación de instancias del conjunto de escalado todavía no está disponible durante el proceso de actualización en una instancia de Azure Stack de 4 nodos.

### <a name="ubuntu-ssh-access"></a>Acceso a SSH en Ubuntu

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Una VM de Ubuntu 18.04 creada con la autorización de SSH habilitada no le permite usar las claves SSH para iniciar sesión.
- Corrección: Utilice el acceso a la VM para la extensión de Linux a fin de implementar las claves SSH después del aprovisionamiento o utilice la autenticación basada en contraseña.
- Repetición: Común

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>La contraseña de restablecimiento del conjunto de escalado de máquinas virtuales no funciona

- Aplicable a: Este problema se aplica a las versiones 1906 y 1907.
- Causa: Aparece una nueva hoja para restablecer la contraseña en la interfaz de usuario del conjunto de escalado, pero Azure Stack todavía no admite el restablecimiento de contraseña en un conjunto de escalado.
- Corrección: Ninguno.
- Repetición: Común

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Nube con lluvia en el diagnóstico del conjunto de escalado

- Aplicable a: Este problema se aplica a las versiones 1906 y 1907.
- Causa: La página de información general del conjunto de escalado de máquinas virtuales muestra un gráfico vacío. Al hacer clic en el gráfico vacío, se abre una hoja de "nube de lluvia". Este es el gráfico de información de diagnóstico del conjunto de escalado, como el porcentaje de CPU, y no es una característica admitida en la compilación actual de Azure Stack.
- Corrección: Ninguno.
- Repetición: Común

### <a name="virtual-machine-diagnostic-settings-blade"></a>Hoja de configuración de diagnóstico de la máquina virtual

- Aplicable a: Este problema se aplica a las versiones 1906 y 1907.    
- Causa: La hoja de configuración de diagnóstico de la máquina virtual tiene una pestaña **Receptor**, que solicita una **Cuenta de Application Insights**. Este es el resultado de una nueva hoja y aún no se admite en Azure Stack.
- Corrección: Ninguno.
- Repetición: Común

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-update-process"></a>Proceso de actualización de 1906

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al intentar instalar la actualización 1906 de Azure Stack, es posible que se produzca un error en el estado de esa actualización y dicho estado cambie a **PreparationFailed**. La causa es que el proveedor de recursos de actualización (URP) no se puede transferir correctamente desde el contenedor de almacenamiento a un recurso compartido de infraestructura interno para su procesamiento. 
- Corrección: A partir de la versión 1901 (1.1901.0.95), para solucionar este problema, puede hacer clic en **Actualizar ahora** de nuevo (en lugar de en **Reanudar**). A continuación, URP limpia los archivos del intento anterior y vuelve a iniciar la descarga. Si el problema persiste, recomendamos cargar manualmente el paquete de actualización siguiendo la sección [Importación e instalación de actualizaciones](azure-stack-apply-updates.md).
- Repetición: Común

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Suscripciones administrativas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Las dos suscripciones administrativas que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**.
- Corrección: Si tiene recursos que se ejecutan en estas dos suscripciones, vuelva a crearlos en las suscripciones de usuario.
- Repetición: Común

### <a name="subscription-resources"></a>Recursos de suscripción

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La eliminación de las suscripciones del usuario da como resultado recursos huérfanos.
- Corrección: Elimine primero los recursos del usuario o todo el grupo de recursos y, después, elimine las suscripciones del usuario.
- Repetición: Común

### <a name="subscription-permissions"></a>Permisos de suscripción

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No puede ver los permisos de la suscripción mediante los portales de Azure Stack.
- Corrección: Use [PowerShell para verificar los permisos](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Repetición: Común

### <a name="subscriptions-properties-blade"></a>Hoja de propiedades de suscripciones
- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de administrador, la hoja **Propiedades** de las suscripciones no se carga correctamente.
- Corrección: Puede ver estas propiedades de suscripciones en el panel Essentials de la hoja Introducción a las suscripciones.
- Repetición: Común

### <a name="storage-account-settings"></a>Configuración de cuentas de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Configuración** de la cuenta de almacenamiento muestra una opción para cambiar el **tipo de transferencia de seguridad**. La característica no se admite actualmente en Azure Stack.
- Repetición: Común

### <a name="upload-blob"></a>Carga de blob

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, al intentar cargar un blob mediante la opción **OAuth(preview)** , la tarea genera un mensaje de error.
- Corrección: Cargue el blob mediante la opción SAS.
- Repetición: Común

### <a name="update"></a>Actualizar

- Aplicable a: Este problema se aplica a la versión 1906.
- Causa: En el portal de operador, el estado de actualización de la revisión muestra un estado incorrecto de la actualización. El estado inicial indica que no se pudo instalar la actualización, aunque todavía está en curso.
- Corrección: Actualice el portal y el estado se actualizará a "en curso".
- Repetición: Intermitente

## <a name="networking"></a>Redes

### <a name="service-endpoints"></a>Puntos de conexión de servicio

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Red virtual** muestra una opción para usar **puntos de conexión de servicio**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

### <a name="network-interface"></a>interfaz de red

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede agregar una nueva interfaz de red a una VM cuyo estado es **En ejecución**.
- Corrección: Detenga la máquina virtual antes de agregar o quitar una interfaz de red.
- Repetición: Común

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

#### <a name="alerts"></a>Alertas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Puerta de enlace de red virtual** muestra una opción para usar **alertas**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="active-active"></a>Activo-activo

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, durante la creación, y en el menú de recursos de **Puerta de enlace de red virtual**, verá una opción para habilitar la configuración **activa-activa**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="vpn-troubleshooter"></a>Solucionador de problemas de VPN

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Conexiones** muestra una característica denominada **Solucionador de problemas de VPN**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="documentation"></a>Documentación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Los vínculos de la documentación de la página de información general de Puerta de enlace de red virtual enlazan a la documentación específica de Azure en lugar de a la de Azure Stack. Use los vínculos siguientes para la documentación de Azure Stack:

  - [SKU de puerta de enlace](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Conexiones de alta disponibilidad](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configuración de BGP en Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuitos ExpressRoute](azure-stack-connect-expressroute.md)
  - [Especificación de directivas de IPsec o IKE personalizadas](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Equilibrador de carga

#### <a name="add-backend-pool"></a>Agregar grupo de back-end

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, si intenta agregar un **grupo de back-end** a una **instancia de Load Balancer**, se produce un error en la operación con el mensaje **Failed to update Load Balancer...** (No se pudo actualizar Load Balancer...).
- Corrección: Use PowerShell, la CLI o una plantilla de Resource Manager para asociar el grupo de back-end a un recurso de equilibrador de carga.
- Repetición: Común

#### <a name="create-inbound-nat"></a>Crear una NAT de entrada

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, si intenta crear una **regla NAT de entrada** para una instancia de **Load Balancer**, se produce un error en la operación con el mensaje **Failed to update Load Balancer...** (No se pudo actualizar Load Balancer...).
- Corrección: Use PowerShell, la CLI o una plantilla de Resource Manager para asociar el grupo de back-end a un recurso de equilibrador de carga.
- Repetición: Común

## <a name="compute"></a>Proceso

### <a name="vm-boot-diagnostics"></a>Diagnósticos de arranque de VM

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al crear una nueva máquina virtual Windows, puede aparecer el siguiente error: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** (No se pudo iniciar la máquina virtual "vm-name". No se pudo actualizar la configuración de salida de serie de la VM 'vm-name'). El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque.
- Corrección: Vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.
- Repetición: Común

### <a name="virtual-machine-scale-set"></a>Conjunto de escalado de máquina virtual


#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Crear errores durante la revisión y actualización de entornos de Azure Stack de 4 nodos

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La creación de VM en un conjunto de disponibilidad de 3 dominios de error y la creación de una instancia de conjunto de escalado de máquinas virtuales genera un error **FabricVmPlacementErrorUnsupportedFaultDomainSize** durante el proceso de actualización en un entorno de Azure Stack de 4 nodos.
- Corrección: Se pueden crear VM únicas en un conjunto de disponibilidad con 2 dominios de error correctamente. Sin embargo, la creación de instancias del conjunto de escalado todavía no está disponible durante el proceso de actualización en una instancia de Azure Stack de 4 nodos.

### <a name="ubuntu-ssh-access"></a>Acceso a SSH en Ubuntu

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Una VM de Ubuntu 18.04 creada con la autorización de SSH habilitada no le permite usar las claves SSH para iniciar sesión.
- Corrección: Utilice el acceso a la VM para la extensión de Linux a fin de implementar las claves SSH después del aprovisionamiento o utilice la autenticación basada en contraseña.
- Repetición: Común

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>La contraseña de restablecimiento del conjunto de escalado de máquinas virtuales no funciona

- Aplicable a: Este problema se aplica a la versión 1906.
- Causa: Aparece una nueva hoja para restablecer la contraseña en la interfaz de usuario del conjunto de escalado, pero Azure Stack todavía no admite el restablecimiento de contraseña en un conjunto de escalado.
- Corrección: Ninguno.
- Repetición: Común

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Nube con lluvia en el diagnóstico del conjunto de escalado

- Aplicable a: Este problema se aplica a la versión 1906.
- Causa: La página de información general del conjunto de escalado de máquinas virtuales muestra un gráfico vacío. Al hacer clic en el gráfico vacío, se abre una hoja de "nube de lluvia". Este es el gráfico de información de diagnóstico del conjunto de escalado, como el porcentaje de CPU, y no es una característica admitida en la compilación actual de Azure Stack.
- Corrección: Ninguno.
- Repetición: Común

### <a name="virtual-machine-diagnostic-settings-blade"></a>Hoja de configuración de diagnóstico de la máquina virtual

- Aplicable a: Este problema se aplica a la versión 1906.
- Causa: La hoja de configuración de diagnóstico de la máquina virtual tiene una pestaña **Receptor**, que solicita una **Cuenta de Application Insights**. Este es el resultado de una nueva hoja y aún no se admite en Azure Stack.
- Corrección: Ninguno.
- Repetición: Común

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-1906"
## <a name="archive"></a>Archivar

Para acceder a los problemas conocidos archivados de una versión anterior, use la lista desplegable del selector de versiones encima de la tabla de contenido de la izquierda y seleccione la versión que quiere ver.

## <a name="next-steps"></a>Pasos siguientes

- [Revisión de la lista de comprobación de actividades de actualización](release-notes-checklist.md)
- [Revisión de la lista de actualizaciones de seguridad](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1905"
## <a name="1905-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1905
::: moniker-end
::: moniker range="azs-1904"
## <a name="1904-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1904
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1903
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1902
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1901
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1811
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1809
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1808
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1807
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1805
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1804
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1803
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1802
::: moniker-end

::: moniker range="<azs-1906"
Puede consultar los [problemas conocidos de las versiones anteriores de Azure Stack en la Galería de TechNet](https://aka.ms/azsarchivedrelnotes). Estos documentos archivados se proporcionan únicamente con fines de referencia y no implican que estas versiones sean compatibles. Para obtener información sobre la compatibilidad con Azure Stack, consulte [Directiva de mantenimiento de Azure Stack](azure-stack-servicing-policy.md). Para obtener más ayuda, póngase en contacto con los servicios de asistencia al cliente de Microsoft.
::: moniker-end
