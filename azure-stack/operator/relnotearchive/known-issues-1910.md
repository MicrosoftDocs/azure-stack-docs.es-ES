---
title: Problemas conocidos de Azure Stack Hub 1910
description: Obtenga información sobre los problemas conocidos de Azure Stack Hub 1910.
author: sethmanheim
ms.topic: article
ms.date: 11/16/2020
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ROBOTS: NOINDEX
ms.openlocfilehash: 0a23951d53d57b103c442ecf2b5b18f894e0f58b
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248778"
---
# <a name="azure-stack-hub-1910-known-issues"></a>Problemas conocidos de Azure Stack Hub 1910

En este artículo se enumeran los problemas conocidos de las versiones de Azure Stack Hub. La lista se actualiza conforme se identifican nuevos problemas.

> [!IMPORTANT]  
> Si a su instancia le faltan más de dos actualizaciones de Azure Stack Hub, se considera que no cumple los requisitos. [Para recibir soporte técnico, deberá actualizarla al menos a la versión mínima admitida.](../azure-stack-servicing-policy.md#keep-your-system-under-support)

## <a name="update"></a>Actualizar

Para ver los problemas conocidos con las actualizaciones de Azure Stack Hub, consulte [Solución de problemas de actualizaciones en Azure Stack Hub](../azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Suscripciones administrativas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Las dos suscripciones administrativas que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**.
- Corrección: Si tiene recursos que se ejecutan en estas dos suscripciones, vuelva a crearlos en las suscripciones de usuario.
- Repetición: Comunes

### <a name="duplicate-subscription-button-in-lock-blade"></a>Botón Duplicar suscripción en la hoja Bloqueo

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal del administrador, la hoja **Bloqueo** para las suscripciones de usuario tiene dos botones que muestran **Suscripción**.
- Repetición: Comunes

### <a name="subscription-permissions"></a>Permisos de suscripción

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No puede ver los permisos de la suscripción mediante los portales de Azure Stack Hub.
- Corrección: Use [PowerShell para verificar los permisos](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Repetición: Comunes

### <a name="storage-account-settings"></a>Configuración de cuentas de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Configuración** de la cuenta de almacenamiento muestra una opción para cambiar el **tipo de transferencia de seguridad**. La característica no se admite actualmente en Azure Stack Hub.
- Repetición: Comunes

### <a name="upload-blob-with-oauth-error"></a>Carga de BLOB con error en OAuth

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, al intentar cargar un blob mediante la opción **OAuth(preview)** , la tarea genera un mensaje de error.
- Corrección: Cargue el blob mediante la opción SAS.
- Repetición: Comunes

### <a name="upload-blob-option-unsupported"></a>Opción de carga de BLOB no admitida

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, cuando intenta cargar un blob en la hoja de carga, hay una opción para seleccionar **AAD** o **Autenticación de clave**, pero **AAD** no se admite en Azure Stack Hub.
- Repetición: Comunes

### <a name="alert-for-network-interface-disconnected"></a>Alerta de interfaz de red desconectada

- Aplicable a: este problema afecta a la versión 1908 y superiores.
- Causa: cuando se desconecta un cable de un adaptador de red, no se muestra ninguna alerta en el portal de administración. Este problema se debe a que este error está deshabilitado de forma predeterminada en Windows Server 2019.
- Repetición: Comunes

### <a name="incorrect-tooltip-when-creating-vm"></a>Información sobre herramientas incorrecta al crear una máquina virtual

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, cuando se selecciona un disco administrado con el tipo de disco SSD Premium, la lista desplegable muestra **Disco del SO**. La información sobre herramientas situada junto a esa opción indica **Puede que algunos tamaños del disco del SO estén disponibles gratis en la cuenta gratuita de Azure**; sin embargo, esto no es válido para Azure Stack Hub. Además, la lista incluye **Cuenta gratuita válida**, opción que tampoco es válida para Azure Stack Hub.
- Repetición: Comunes

### <a name="delete-a-storage-container"></a>Eliminación de un contenedor de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, cuando un usuario intenta eliminar un contenedor de almacenamiento, se produce un error en la operación si no se activa la opción **Invalidar la configuración de rol RBAC y Azure Policy**.
- Corrección: asegúrese de que se activa la casilla **Invalidar la configuración de rol RBAC y Azure Policy**.
- Repetición: Comunes

### <a name="refresh-button-on-virtual-machines-fails"></a>Error en el botón de actualización de las máquinas virtuales

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, cuando navega a **Máquinas virtuales** e intenta actualizar con el botón de la parte superior, los estados no se actualizan correctamente.
- Corrección: el estado se actualiza automáticamente cada 5 minutos, con independencia de si se hace clic en el botón de actualización o no. Espere 5 minutos y compruebe el estado.
- Repetición: Comunes

### <a name="storage-account-options"></a>Opciones de la cuenta de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, el nombre de las cuentas de almacenamiento se muestra como **Cuenta de almacenamiento: blob, archivo, tabla, cola**; sin embargo, la opción **archivo** no se admite en Azure Stack Hub.
- Repetición: Comunes

### <a name="storage-account-configuration"></a>Configuración de la cuenta de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, cuando se crea una cuenta de almacenamiento y se ve su **Configuración**, no se pueden guardar los cambios de configuración, ya que generarían un error de AJAX.
- Repetición: Comunes

### <a name="capacity-monitoring-in-sql-resource-provider-keeps-loading"></a>La supervisión de la capacidad del proveedor de recursos de SQL continúa cargándose

- Aplicable a: este problema se aplica a la actualización de Azure Stack Hub 1910 o posterior, con la versión 1.1.33.0 o anterior del proveedor de recursos de SQL instalada.
- Causa: la versión actual del proveedor de recursos de SQL no es compatible con algunos de los cambios más recientes del portal en la actualización 1910.
- Corrección: siga el proceso de actualización del proveedor de recursos para aplicar la revisión 1.1.47.0 del proveedor de recursos de SQL después de cambiar Azure Stack Hub a la actualización 1910 ([versión 1.1.47.0 del proveedor de recursos de SQL](https://aka.ms/azurestacksqlrp11470)). Para el proveedor de recursos de MySQL, también se recomienda aplicar la revisión 1.1.47.0 del proveedor de recursos de MySQL después de cambiar Azure Stack Hub a la actualización 1910 ([versión 1.1.47.0 del proveedor de recursos de MySQL](https://aka.ms/azurestackmysqlrp11470)).
- Repetición: Comunes

### <a name="access-control-iam"></a>Control de acceso (IAM)

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La extensión IAM está obsoleta. El portal de Ibiza que se incluye con Azure Stack Hub introduce un nuevo comportamiento que hace que se produzca un error en la extensión RBAC si el usuario está abriendo la hoja **Access Control (IAM)** de una suscripción que no está seleccionada en el selector de suscripción global (**directorio + suscripción** en el portal de usuarios). La hoja muestra el mensaje **Cargando** en bucle y el usuario no puede agregar nuevos roles a la suscripción. La hoja **Agregar** también muestra el mensaje **Cargando** en bucle.
- Corrección: Asegúrese de que la suscripción esté marcada en el menú **Directorio + suscripción**. A este menú se puede acceder desde la parte superior del portal, cerca del botón **Notificaciones**, o bien mediante el acceso directo de la hoja **Todos los recursos** que muestra el mensaje **Don't see a subscription? (¿No ve una suscripción?) Abra la configuración de Directorio + suscripción**. La suscripción se debe seleccionar en este menú.

### <a name="sql-resource-provider"></a>Proveedor de recursos SQL

- Aplicable a: este problema se aplica a los stamps que ejecutan la versión 1908 u otra anterior.
- Causa: al implementar la versión 1.1.47.0 del proveedor de recursos SQL, el portal no muestra ningún recurso que no sea el asociado al proveedor de recursos SQL.
- Corrección: elimine el proveedor de recursos, actualice el stamp y vuelva a implementar el proveedor de recursos

### <a name="activity-log-blade"></a>Hoja Registro de actividades

- Aplicable a: este problema afecta a los stamps que ejecutan la versión 1907 o posterior. <!-- Note: Applies to 2002 as well -->
- Causa: al acceder al registro de actividad, el portal solo muestra la primera página de entradas. La opción **Cargar más resultados** no cargará entradas adicionales.
- Corrección: ajuste el intervalo de tiempo en el filtro para revisar las entradas que se encuentran después de la primera página.

## <a name="networking"></a>Redes

### <a name="load-balancer"></a>Equilibrador de carga

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: al agregar máquinas virtuales del conjunto de disponibilidad al grupo back-end de un equilibrador de carga, se muestra un mensaje de error en el portal que indica **No se pudo guardar el grupo back-end de equilibradores de carga**. Se trata de un problema cosmético en el portal; la funcionalidad existe y las máquinas virtuales se agregan correctamente al grupo back-end de modo interno.
- Repetición: Comunes

### <a name="network-security-groups"></a>Grupos de seguridad de red

- Aplicable a: este problema se aplica a todas las versiones admitidas. 
- Causa: No se puede crear una regla **DenyAllOutbound** explícita en un grupo de seguridad de red, ya que esto impedirá todas las comunicaciones internas con la infraestructura necesarias para que se complete la implementación de la máquina virtual.
- Repetición: Comunes

### <a name="service-endpoints"></a>Puntos de conexión del servicio

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Red virtual** muestra una opción para usar **puntos de conexión de servicio**. Esta característica no se admite actualmente en Azure Stack Hub.
- Repetición: Comunes

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>No se puede eliminar un grupo de seguridad de red si las NIC no están conectadas a la máquina virtual en ejecución

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: al desasociar un grupo de seguridad de red y una NIC que no están conectadas a una máquina virtual en ejecución, se produce un error en la operación de actualización (PUT) de ese objeto en el nivel de la controladora de red. El grupo de seguridad de red se actualizará en el nivel del proveedor de recursos de red, pero no en la controladora de red, por lo que este grupo pasará a un estado de error.
- Corrección: conecte las NIC asociadas al grupo de seguridad de red que se debe quitar con las máquinas virtuales en ejecución y desasocie este grupo o quite todas las NIC asociadas a él.
- Repetición: Comunes

### <a name="network-interface"></a>interfaz de red

#### <a name="addingremoving-network-interface"></a>Incorporación o eliminación de una interfaz de red

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede agregar una nueva interfaz de red a una VM cuyo estado es **En ejecución**.
- Corrección: detenga la máquina virtual antes de agregar o quitar una interfaz de red.
- Repetición: Comunes

#### <a name="primary-network-interface"></a>Interfaz de red principal

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede cambiar la NIC principal de una máquina virtual. La eliminación o desasociación de la NIC principal provoca problemas al iniciar la máquina virtual.
- Repetición: Comunes

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

#### <a name="next-hop-type"></a>Tipo de próximo salto

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, cuando se crea una tabla de rutas, aparece **Puerta de enlace de red virtual** como una de las opciones de tipo de próximo salto; sin embargo, esta opción no se admite en Azure Stack Hub.
- Repetición: Comunes

#### <a name="alerts"></a>Alertas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Puerta de enlace de red virtual** muestra una opción para usar **alertas**. Esta característica no se admite actualmente en Azure Stack Hub.
- Repetición: Comunes

#### <a name="active-active"></a>Activo-activo

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, durante la creación, y en el menú de recursos de **Puerta de enlace de red virtual**, verá una opción para habilitar la configuración **activa-activa**. Esta característica no se admite actualmente en Azure Stack Hub.
- Repetición: Comunes

#### <a name="vpn-troubleshooter"></a>Solucionador de problemas de VPN

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, la hoja **Conexiones** muestra una característica denominada **Solucionador de problemas de VPN**. Esta característica no se admite actualmente en Azure Stack Hub.
- Repetición: Comunes

#### <a name="vpn-troubleshooter"></a>Solucionador de problemas de VPN

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, aparecen la característica **Solución de problemas de VPN** y **Métricas** en un recurso de puerta de enlace de VPN, pero esto no se admite en Azure Stack Hub.
- Repetición: Comunes

#### <a name="documentation"></a>Documentación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Los vínculos de documentación de la página de introducción a la Puerta de enlace de red virtual enlazan a la documentación específica de Azure en lugar de a la de Azure Stack Hub. Use los vínculos siguientes para consultar la documentación de Azure Stack Hub:

  - [SKU de puerta de enlace](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Conexiones de alta disponibilidad](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configuración de BGP en Azure Stack Hub](../../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuitos ExpressRoute](../azure-stack-connect-expressroute.md)
  - [Especificación de directivas de IPsec o IKE personalizadas](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Proceso

### <a name="vm-boot-diagnostics"></a>Diagnósticos de arranque de VM

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: al crear una nueva máquina virtual Windows, puede aparecer el siguiente error: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** (No se pudo iniciar la máquina virtual "vm-name". No se pudo actualizar la configuración de salida de serie de la VM 'vm-name'). El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque.
- Corrección: Vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.
- Repetición: Comunes

### <a name="consumed-compute-quota"></a>Cuota de proceso consumida

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al crear una nueva máquina virtual, es posible que reciba un error como, por ejemplo, **Esta suscripción tiene una capacidad de vCPU regionales totales en esta ubicación y está usando todas las disponibles 50 vCPU regionales totales**. Esto indica que se ha alcanzado la cuota de núcleos totales que tiene disponibles.
- Corrección: pida a su operador un plan complementario con una cuota adicional. La edición de la cuota del plan actual no funcionará ni reflejará la cuota incrementada.
- Repetición: poco frecuente

### <a name="privileged-endpoint"></a>Punto de conexión con privilegios

- Aplicable a: este problema se aplica a la versión 1910 y a las anteriores.
- Causa: no es posible conectar con el punto de conexión con privilegios (máquinas virtuales ERC) desde un equipo cuya versión de Windows no esté en inglés.
- Corrección: este es un problema conocido que se ha solucionado en las versiones posteriores a la 1910. Una posible solución es ejecutar los cmdlets de PowerShell **New-PSSession** y **Enter-PSSession** mediante la referencia cultural **en-US**; para ver distintos ejemplos, establezca la cultura mediante este script: https://resources.oreilly.com/examples/9780596528492/blob/master/Use-Culture.ps1.
- Repetición: poco frecuente

### <a name="virtual-machine-scale-set"></a>Conjunto de escalado de máquina virtual

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Creación de errores durante la revisión y actualización de entornos de Azure Stack Hub de 4 nodos

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La creación de máquinas virtuales en un conjunto de disponibilidad de 3 dominios de error y la creación de una instancia de conjunto de escalado de máquinas virtuales genera un error **FabricVmPlacementErrorUnsupportedFaultDomainSize** durante el proceso de actualización en un entorno de Azure Stack Hub de 4 nodos.
- Corrección: Se pueden crear VM únicas en un conjunto de disponibilidad con 2 dominios de error correctamente. Sin embargo, la creación de instancias del conjunto de escalado todavía no está disponible durante el proceso de actualización en una implementación de Azure Stack Hub de 4 nodos.
