---
title: Problemas conocidos de Azure Stack Hub
description: Obtenga información sobre los problemas conocidos de las versiones de Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 01/28/2021
ms.author: sethm
ms.reviewer: sranthar
ms.lastreviewed: 09/09/2020
ms.openlocfilehash: af4187ecf610543c693aff742b1e74b8d5bef84c
ms.sourcegitcommit: 659114a3fb90c962316eb4cddab53d2d2da35b03
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2021
ms.locfileid: "99065529"
---
# <a name="azure-stack-hub-known-issues"></a>Problemas conocidos de Azure Stack Hub

En este artículo se enumeran los problemas conocidos de las versiones de Azure Stack Hub. La lista se actualiza conforme se identifican nuevos problemas.

Para tener acceso a los problemas conocidos de una versión diferente, use la lista desplegable del selector de versiones encima de la tabla de contenido de la izquierda.

::: moniker range=">=azs-2002"
> [!IMPORTANT]  
> Revise esta sección antes de aplicar la actualización.
::: moniker-end
::: moniker range="<azs-2002"
> [!IMPORTANT]  
> Si a su instancia le faltan más de dos actualizaciones de Azure Stack Hub, se considera que no cumple los requisitos. [Para recibir soporte técnico, deberá actualizarla al menos a la versión mínima admitida.](azure-stack-servicing-policy.md#keep-your-system-under-support) 
::: moniker-end

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->

::: moniker range="azs-2008"
## <a name="update"></a>Actualizar

Para ver los problemas conocidos con las actualizaciones de Azure Stack Hub, consulte [Solución de problemas de actualizaciones en Azure Stack Hub](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Error de la actualización al instalar el paquete Microsoft.AzureStack.Compute.Installer en la máquina virtual de la entidad de certificación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Durante la actualización, un proceso adopta un bloqueo en el contenido nuevo que debe copiarse en la máquina virtual de la entidad de certificación. Si se produce un error en la actualización, se termina el bloqueo.
- Corrección: Reanude la actualización.
- Repetición: poco frecuente

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Suscripciones administrativas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Las dos suscripciones administrativas que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**.
- Corrección: Si tiene recursos que se ejecutan en estas dos suscripciones, vuelva a crearlos en las suscripciones de usuario.
- Repetición: Comunes

## <a name="networking"></a>Redes

### <a name="network-security-groups"></a>Grupos de seguridad de red

#### <a name="denyalloutbound-rule-cannot-be-created"></a>No se puede crear la regla DenyAllOutbound

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede crear una regla **DenyAllOutbound** explícita para Internet en un grupo de seguridad de red durante la creación de una máquina virtual, ya que esto impedirá las comunicaciones necesarias para que se complete la implementación de la máquina virtual. También se denegarán las dos direcciones IP esenciales necesarias para implementar VM: IP DHCP: 169.254.169.254 e IP DNS: 168.63.129.16.

- Corrección: Permita el tráfico saliente a Internet durante la creación de la máquina virtual y modifique el grupo de seguridad de red para bloquear el tráfico necesario una vez completada la creación de la máquina virtual.
- Repetición: Comunes

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

#### <a name="documentation"></a>Documentación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Los vínculos de documentación de la página de introducción a la Puerta de enlace de red virtual enlazan a la documentación específica de Azure en lugar de a la de Azure Stack Hub. Use los vínculos siguientes para consultar la documentación de Azure Stack Hub:

  - [SKU de puerta de enlace](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Conexiones de alta disponibilidad](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configuración de BGP en Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuitos ExpressRoute](azure-stack-connect-expressroute.md)
  - [Especificación de directivas de IPsec o IKE personalizadas](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer dirige el tráfico a una máquina virtual de back-end en escenarios específicos

- Aplicable a: este problema se aplica a todas las versiones admitidas. 
- Causa: Al habilitar la **afinidad de sesión** en un equilibrador de carga, el hash de tupla 2 emplea la dirección IP física en lugar de las IP privadas asignadas a las máquinas virtuales. En escenarios en los que el tráfico dirigido al equilibrador de carga llega a través de una VPN, o si todas las máquinas virtuales cliente (IP de origen) residen en el mismo nodo y la afinidad de sesión está habilitada, todo el tráfico se dirige a una máquina virtual de back-end.
- Repetición: Comunes

#### <a name="ipv6-button-visible-in-frontend-ip-configuration"></a>Botón IPv6 visible en la configuración IP de front-end

- Aplicable a: este problema se aplica a la versión 2008.
- Causa: el botón IPv6 es visible y se habilita al crear la configuración de IP de front-end de un equilibrador de carga público. Se trata de un problema poco importante en el portal. IPv6 no se admite en Azure Stack Hub.
- Repetición: Comunes

#### <a name="backend-port-and-frontend-port-need-to-be-the-same-when-floating-ip-is-enabled"></a>El puerto de back-end y el puerto de front-end deben ser los mismos cuando la IP flotante esté habilitada

- Aplicable a: este problema afecta a todas las versiones. 
- Causa: El puerto de front-end y el puerto de back-end deben ser los mismos en la regla de equilibrio de carga cuando la IP flotante esté habilitada. es así por diseño.
- Repetición: Comunes

<!-- ## Compute -->

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-2005"
## <a name="update"></a>Actualizar

Para ver los problemas conocidos con las actualizaciones de Azure Stack Hub, consulte [Solución de problemas de actualizaciones en Azure Stack Hub](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Error de la actualización al instalar el paquete Microsoft.AzureStack.Compute.Installer en la máquina virtual de la entidad de certificación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Durante la actualización, un proceso adopta un bloqueo en el contenido nuevo que debe copiarse en la máquina virtual de la entidad de certificación. Si se produce un error en la actualización, se termina el bloqueo.
- Corrección: Reanude la actualización.
- Repetición: poco frecuente

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Suscripciones administrativas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Las dos suscripciones administrativas que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**.
- Corrección: Si tiene recursos que se ejecutan en estas dos suscripciones, vuelva a crearlos en las suscripciones de usuario.
- Repetición: Comunes

## <a name="networking"></a>Redes

### <a name="network-security-groups"></a>Grupos de seguridad de red

#### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>No se puede eliminar un grupo de seguridad de red si las NIC no están conectadas a la máquina virtual en ejecución

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: al desasociar un grupo de seguridad de red y una NIC que no están conectadas a una máquina virtual en ejecución, se produce un error en la operación de actualización (PUT) de ese objeto en el nivel de la controladora de red. El grupo de seguridad de red se actualizará en el nivel del proveedor de recursos de red, pero no en la controladora de red, por lo que este grupo pasará a un estado de error.
- Corrección: conecte las NIC asociadas al grupo de seguridad de red que se debe quitar con las máquinas virtuales en ejecución y desasocie este grupo o quite todas las NIC asociadas a él.
- Repetición: Comunes

#### <a name="denyalloutbound-rule-cannot-be-created"></a>No se puede crear la regla DenyAllOutbound

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede crear una regla **DenyAllOutbound** explícita para Internet en un grupo de seguridad de red durante la creación de una máquina virtual, ya que esto impedirá las comunicaciones necesarias para que se complete la implementación de la máquina virtual.
- Corrección: Permita el tráfico saliente a Internet durante la creación de la máquina virtual y modifique el grupo de seguridad de red para bloquear el tráfico necesario una vez completada la creación de la máquina virtual.
- Repetición: Comunes

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

#### <a name="documentation"></a>Documentación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Los vínculos de documentación de la página de introducción a la Puerta de enlace de red virtual enlazan a la documentación específica de Azure en lugar de a la de Azure Stack Hub. Use los vínculos siguientes para consultar la documentación de Azure Stack Hub:

  - [SKU de puerta de enlace](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Conexiones de alta disponibilidad](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configuración de BGP en Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuitos ExpressRoute](azure-stack-connect-expressroute.md)
  - [Especificación de directivas de IPsec o IKE personalizadas](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)
  
### <a name="load-balancer"></a>Load Balancer

#### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer dirige el tráfico a una máquina virtual de back-end en escenarios específicos

- Aplicable a: este problema se aplica a todas las versiones admitidas. 
- Causa: Al habilitar la **afinidad de sesión** en un equilibrador de carga, el hash de tupla 2 emplea la dirección IP física en lugar de las IP privadas asignadas a las máquinas virtuales. En escenarios en los que el tráfico dirigido al equilibrador de carga llega a través de una VPN, o si todas las máquinas virtuales cliente (IP de origen) residen en el mismo nodo y la afinidad de sesión está habilitada, todo el tráfico se dirige a una máquina virtual de back-end.
- Repetición: Comunes

#### <a name="public-ip"></a>Dirección IP pública

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: el valor **IdleTimeoutInMinutes** de una dirección IP pública que está asociada a un equilibrador de carga no se puede cambiar. La operación pone a la dirección IP pública en estado de error.
- Corrección: Para devolverla a un estado correcto, cambie el valor **IdleTimeoutInMinutes** en la regla del equilibrador de carga que hace referencia a la dirección IP pública a su valor original (el valor predeterminado es 4 minutos).
- Repetición: Comunes

## <a name="compute"></a>Proceso

### <a name="issues-deploying-virtual-machine-scale-set-with-standard_ds2_v2-size-using-the-portal"></a>Problemas de implementación del conjunto de escalado de máquinas virtuales con el tamaño Standard_DS2_v2 mediante el portal

- Aplicable a: este problema se aplica a la versión 2005.
- Causa: un error del portal hace que se produzca un error en la creación del conjunto de escalado de máquinas virtuales con el tamaño Standard_DS2_v2.
- Corrección: use PowerShell o la CLI para implementar este tamaño de máquina virtual del conjunto de escalado de máquinas virtuales.

### <a name="issues-using-vm-extensions-in-ubuntu-server-2004"></a>Problemas con las extensiones de máquina virtual de Ubuntu Server 20.04

- Aplicable a: Este problema se aplica a **Ubuntu Server 20 04 LTS**.
- Causa: Algunas distribuciones de Linux han pasado a Python 3.8 y han quitado el punto de entrada de `/usr/bin/python` heredado para Python por completo. Los usuarios de distribución de Linux que han pasado a Python 3.x deben asegurarse de que el punto de entrada `/usr/bin/python` heredado exista antes de intentar implementar esas extensiones en sus máquinas virtuales. De lo contrario, podría producirse un error en la implementación de la extensión.
- Corrección: Siga los pasos de resolución descritos en [Problemas al usar extensiones de VM en los sistemas de Azure Virtual Machines para Linux habilitados para Python 3](/azure/virtual-machines/extensions/issues-using-vm-extensions-python-3) pero omita el paso 2, ya que Azure Stack Hub no tiene la funcionalidad **Ejecutar comando**.

### <a name="nvv4-vm-size-on-portal"></a>Tamaño de máquina virtual NVv4 en el portal

- Aplicable a: Este problema se aplica a la versión 2002 y versiones posteriores.
- Causa: Durante la creación de una máquina virtual, verá el tamaño de máquina virtual: NV4as_v4. Los clientes que tienen el hardware necesario para la versión preliminar de la GPU de Azure Stack Hub basada en el procesador AMD MI25 pueden realizar una implementación correcta de la máquina virtual. Todos los demás clientes recibirán un error de implementación con este tamaño de máquina virtual.
- Corrección: Por diseño como preparación para la versión preliminar de la GPU de Azure Stack Hub.

### <a name="consumed-compute-quota"></a>Cuota de proceso consumida

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al crear una nueva máquina virtual, es posible que reciba un error como, por ejemplo, **Esta suscripción tiene una capacidad de vCPU regionales totales en esta ubicación y está usando todas las disponibles 50 vCPU regionales totales**. Esto indica que se ha alcanzado la cuota de núcleos totales que tiene disponibles.
- Corrección: pida a su operador un plan complementario con una cuota adicional. La edición de la cuota del plan actual no funcionará ni reflejará la cuota incrementada.
- Repetición: poco frecuente

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>La hoja de información general de máquinas virtuales no muestra el nombre de equipo correcto

- Aplicable a: este problema afecta a todas las versiones.
- Causa: Al ver los detalles de una máquina virtual en la hoja de información general, el nombre del equipo se muestra como **(no disponible)** . Por su propio diseño esto es para máquinas virtuales creadas a partir de discos o instantáneas de disco especializados y aparece también para imágenes de Marketplace.
- Corrección: Vea la hoja **Propiedades** en **Configuración**.

### <a name="virtual-machine-scale-set"></a>Conjunto de escalado de máquina virtual

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Creación de errores durante la revisión y actualización de entornos de Azure Stack Hub de 4 nodos

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La creación de máquinas virtuales en un conjunto de disponibilidad de 3 dominios de error y la creación de una instancia de conjunto de escalado de máquinas virtuales genera un error **FabricVmPlacementErrorUnsupportedFaultDomainSize** durante el proceso de actualización en un entorno de Azure Stack Hub de 4 nodos.
- Corrección: Se pueden crear VM únicas en un conjunto de disponibilidad con 2 dominios de error correctamente. Sin embargo, la creación de instancias del conjunto de escalado todavía no está disponible durante el proceso de actualización en una implementación de Azure Stack Hub de 4 nodos.

## <a name="storage"></a>Storage

### <a name="retention-period-reverts-to-0"></a>El período de retención vuelve a 0

- Aplicable a: este problema afecta a las versiones 2002 y 2005.
- Causa: Si especifica un período de tiempo distinto de 0 como valor del período de retención, este vuelve a 0 (el valor predeterminado de esta configuración) en las actualizaciones 2002 o 2005. El valor de 0 días surte efecto inmediatamente después de finalizar la actualización, lo que hace que todas las cuentas de almacenamiento existentes y las cuentas de almacenamiento recién eliminadas queden inmediatamente fuera del período de retención y se marquen para la recolección de elementos no utilizados periódica (que se ejecuta cada hora).
- Corrección: Especifique manualmente un valor correcto para el período de retención. Las cuentas de almacenamiento cuyos elementos no utilizados se hayan recolectado antes de que se especifique el nuevo período de retención, no son recuperables.  

## <a name="resource-providers"></a>Proveedores de recursos

### <a name="sqlmysql"></a>SQL/MySQL

- Aplicable a: este problema afecta a la versión 2002.
- Causa: si el sello contiene la versión 1.1.33.0 o anterior del proveedor de recursos (RP) de SQL, tras su actualización, no se cargarán las hojas de SQL y MySQL.
- Corrección: actualice el proveedor de recursos a la versión 1.1.47.0.

### <a name="app-service"></a>App Service

- Aplicable a: este problema afecta a la versión 2002.
- Causa: si el stamp contiene la versión 1.7 o anterior del proveedor de recursos (RP) de App Service, tras la actualización del stamp no se cargará la hoja de App Service.
- Corrección: actualice el proveedor de recursos a la versión 2002 Q2.

## <a name="powershell"></a>PowerShell

[!Include[Known issue for install - one](../includes/known-issue-az-install-1.md)]

[!Include[Known issue for install - two](../includes/known-issue-az-install-2.md)]

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range="azs-2002"
## <a name="update"></a>Actualizar

Después de aplicar la actualización 2002, puede aparecer incorrectamente una alerta de un "origen de hora no válido" en el portal de administración. Esta alerta es un falso positivo se puede ignorar y se corregirá en una próxima versión. 

Para ver los problemas conocidos con las actualizaciones de Azure Stack Hub, consulte [Solución de problemas de actualizaciones en Azure Stack Hub](azure-stack-troubleshooting.md#troubleshoot-azure-stack-hub-updates).

### <a name="update-failed-to-install-package-microsoftazurestackcomputeinstaller-to-ca-vm"></a>Error de la actualización al instalar el paquete Microsoft.AzureStack.Compute.Installer en la máquina virtual de la entidad de certificación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Durante la actualización, un proceso adopta un bloqueo en el contenido nuevo que debe copiarse en la máquina virtual de la entidad de certificación. Si se produce un error en la actualización, se termina el bloqueo.
- Corrección: Reanude la actualización.
- Repetición: poco frecuente

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Suscripciones administrativas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Las dos suscripciones administrativas que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**.
- Corrección: Si tiene recursos que se ejecutan en estas dos suscripciones, vuelva a crearlos en las suscripciones de usuario.
- Repetición: Comunes

### <a name="subscription-permissions"></a>Permisos de suscripción

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No puede ver los permisos de la suscripción mediante los portales de Azure Stack Hub.
- Corrección: Use [PowerShell para verificar los permisos](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Repetición: Comunes

### <a name="storage-account-options"></a>Opciones de la cuenta de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de usuarios, el nombre de las cuentas de almacenamiento se muestra como **Cuenta de almacenamiento: blob, archivo, tabla, cola**; sin embargo, la opción **archivo** no se admite en Azure Stack Hub.
- Repetición: Comunes

### <a name="create-managed-disk-snapshot"></a>Creación de una instantánea de disco administrado

- Aplicable a: este problema afecta a la versión 2002.
- Causa: en el portal de usuarios, al crear una instantánea de disco administrado, el cuadro de texto **Tipo de cuenta** está vacío. Al seleccionar el botón **Crear** con el tipo de cuenta vacío, se produce un error en la creación de la instantánea.
- Corrección: seleccione un tipo de cuenta en la lista desplegable **Tipo de cuenta** y, a continuación, cree la instantánea.
- Repetición: Comunes

### <a name="alert-for-network-interface-disconnected"></a>Alerta de interfaz de red desconectada

- Aplicable a: Este problema se aplica a la versión 1908 y versiones posteriores.
- Causa: cuando se desconecta un cable de un adaptador de red, no se muestra ninguna alerta en el portal de administración. Este problema se debe a que este error está deshabilitado de forma predeterminada en Windows Server 2019.
- Repetición: Comunes

## <a name="networking"></a>Redes

### <a name="denyalloutbound-rule-cannot-be-created"></a>No se puede crear la regla DenyAllOutbound

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede crear una regla **DenyAllOutbound** explícita para Internet en un grupo de seguridad de red durante la creación de una máquina virtual, ya que esto impedirá las comunicaciones necesarias para que se complete la implementación de la máquina virtual.
- Corrección: Permita el tráfico saliente a Internet durante la creación de la máquina virtual y modifique el grupo de seguridad de red para bloquear el tráfico necesario una vez completada la creación de la máquina virtual.
- Repetición: Comunes

### <a name="icmp-protocol-not-supported-for-nsg-rules"></a>Protocolo ICMP no compatible con las reglas del grupo de seguridad de red

- Aplicable a: este problema se aplica a todas las versiones admitidas. 
- Causa: al crear una regla de seguridad de red de entrada o de salida, la opción **Protocol** (Protocolo) muestra una opción **ICMP**. Esto es algo que no se admite actualmente en Azure Stack Hub. Este problema se ha corregido y no aparecerá en la siguiente versión de Azure Stack Hub.
- Repetición: Comunes

### <a name="cannot-delete-an-nsg-if-nics-not-attached-to-running-vm"></a>No se puede eliminar un grupo de seguridad de red si las NIC no están conectadas a la máquina virtual en ejecución

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: al desasociar un grupo de seguridad de red y una NIC que no están conectadas a una máquina virtual en ejecución, se produce un error en la operación de actualización (PUT) de ese objeto en el nivel de la controladora de red. El grupo de seguridad de red se actualizará en el nivel del proveedor de recursos de red, pero no en la controladora de red, por lo que este grupo pasará a un estado de error.
- Corrección: conecte las NIC asociadas al grupo de seguridad de red que se debe quitar con las máquinas virtuales en ejecución y desasocie este grupo o quite todas las NIC asociadas a él.
- Repetición: Comunes

### <a name="load-balancer-directing-traffic-to-one-backend-vm-in-specific-scenarios"></a>Load Balancer dirige el tráfico a una máquina virtual de back-end en escenarios específicos 

- Aplicable a: este problema se aplica a todas las versiones admitidas. 
- Causa: Al habilitar la **afinidad de sesión** en un equilibrador de carga, el hash de tupla 2 emplea la dirección IP física en lugar de las IP privadas asignadas a las máquinas virtuales. En escenarios en los que el tráfico dirigido al equilibrador de carga llega a través de una VPN, o si todas las máquinas virtuales cliente (IP de origen) residen en el mismo nodo y la afinidad de sesión está habilitada, todo el tráfico se dirige a una máquina virtual de back-end.
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

### <a name="public-ip"></a>Dirección IP pública

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: el valor **IdleTimeoutInMinutes** de una dirección IP pública que está asociada a un equilibrador de carga no se puede cambiar. La operación pone a la dirección IP pública en estado de error.
- Corrección: para devolverla a un estado correcto, cambie el valor **IdleTimeoutInMinutes** en la regla del equilibrador de carga que hace referencia a la dirección IP pública a su valor original (el valor predeterminado es 4 minutos).
- Repetición: Comunes

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

#### <a name="documentation"></a>Documentación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Los vínculos de documentación de la página de introducción a la Puerta de enlace de red virtual enlazan a la documentación específica de Azure en lugar de a la de Azure Stack Hub. Use los vínculos siguientes para consultar la documentación de Azure Stack Hub:

  - [SKU de puerta de enlace](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Conexiones de alta disponibilidad](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configuración de BGP en Azure Stack Hub](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuitos ExpressRoute](azure-stack-connect-expressroute.md)
  - [Especificación de directivas de IPsec o IKE personalizadas](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Proceso

### <a name="cannot-create-a-virtual-machine-scale-set-with-standard_ds2_v2-vm-size-on-portal"></a>No se puede crear un conjunto de escalado de máquinas virtuales con el tamaño de máquina virtual Standard_DS2_v2 en el portal

- Aplicable a: este problema se aplica a la versión 2002.
- Causa: hay un error del portal que impide la creación de un conjunto de escalado de máquinas virtuales con el tamaño de máquina virtual Standard_DS2_v2. Al crear una se producirá el error: "{"code":"DeploymentFailed","message":" se ha producido un error al menos en una operación de implementación de recursos. Enumere las operaciones de implementación para obtener más información. Consulte https://aka.ms/arm-debug para obtener detalles de uso.","details":[{"code":"BadRequest","message":"{\r\n \" error\": {\r\n \" code\": \" NetworkProfileValidationError\" ,\r\n \" message\": \" El tamaño de máquina virtual Standard_DS2_v2 no está en la lista de tamaños permitidos para que las redes aceleradas se habiliten en la máquina virtual en el índice 0 del conjunto de escalado de máquinas virtuales /subscriptions/x/resourceGroups/RGVMSS/providers/Microsoft.Compute/virtualMachineScaleSets/vmss. Tamaños permitidos: .\"\r\n }\r\n}"}]}"
- Corrección: cree un conjunto de escalado de máquinas virtuales con PowerShell o con una plantilla de Azure Resource Manager.

### <a name="vm-overview-blade-does-not-show-correct-computer-name"></a>La hoja de información general de máquinas virtuales no muestra el nombre de equipo correcto

- Aplicable a: este problema afecta a todas las versiones.
- Causa: Al ver los detalles de una máquina virtual en la hoja de información general, el nombre del equipo se muestra como **(no disponible)** . Por su propio diseño esto es para máquinas virtuales creadas a partir de discos o instantáneas de disco especializados y aparece también para imágenes de Marketplace.
- Corrección: Vea la hoja **Propiedades** en **Configuración**.

### <a name="nvv4-vm-size-on-portal"></a>Tamaño de máquina virtual NVv4 en el portal

- Aplicable a: este problema se aplica a la versión 2002 y posteriores.
- Causa: Durante la creación de una máquina virtual, verá el tamaño de máquina virtual: NV4as_v4. Los clientes que tienen el hardware necesario para la versión preliminar de la GPU de Azure Stack Hub basada en el procesador AMD MI25 pueden realizar una implementación correcta de la máquina virtual. Todos los demás clientes recibirán un error de implementación con este tamaño de máquina virtual.
- Corrección: Por diseño como preparación para la versión preliminar de la GPU de Azure Stack Hub.

### <a name="vm-boot-diagnostics"></a>Diagnósticos de arranque de VM

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: al crear una nueva máquina virtual (VM), puede aparecer el siguiente error: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** (No se pudo iniciar la máquina virtual "vm-name". No se pudo actualizar la configuración de salida de serie de la VM 'vm-name'). El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque.
- Corrección: Vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.
- Repetición: Comunes

### <a name="vm-boot-diagnostics"></a>Diagnósticos de arranque de VM

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: al intentar iniciar una máquina virtual desasignada, puede aparecer el siguiente error: **No se encontró la cuenta de almacenamiento de los diagnósticos de la máquina virtual "diagnosticstorageaccount". Asegúrese de que la cuenta de almacenamiento no se ha eliminado**. El error se produce si intenta iniciar una máquina virtual con los diagnósticos de arranque habilitados, pero se ha eliminado la cuenta de almacenamiento de diagnósticos de arranque a la que se hace referencia.
- Corrección: Vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.
- Repetición: Comunes

### <a name="consumed-compute-quota"></a>Cuota de proceso consumida

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al crear una nueva máquina virtual, es posible que reciba un error como, por ejemplo, **Esta suscripción tiene una capacidad de vCPU regionales totales en esta ubicación y está usando todas las disponibles 50 vCPU regionales totales**. Esto indica que se ha alcanzado la cuota de núcleos totales que tiene disponibles.
- Corrección: pida a su operador un plan complementario con una cuota adicional. La edición de la cuota del plan actual no funcionará ni reflejará la cuota incrementada.
- Repetición: poco frecuente

### <a name="virtual-machine-scale-set"></a>Conjunto de escalado de máquina virtual

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-hub-environments"></a>Creación de errores durante la revisión y actualización de entornos de Azure Stack Hub de 4 nodos

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La creación de máquinas virtuales en un conjunto de disponibilidad de 3 dominios de error y la creación de una instancia de conjunto de escalado de máquinas virtuales genera un error **FabricVmPlacementErrorUnsupportedFaultDomainSize** durante el proceso de actualización en un entorno de Azure Stack Hub de 4 nodos.
- Corrección: Se pueden crear VM únicas en un conjunto de disponibilidad con 2 dominios de error correctamente. Sin embargo, la creación de instancias del conjunto de escalado todavía no está disponible durante el proceso de actualización en una implementación de Azure Stack Hub de 4 nodos.

### <a name="sql-vm"></a>VM con SQL

#### <a name="storage-account-creating-failure-when-configuring-auto-backup"></a>Error al crear la cuenta de almacenamiento al configurar la copia de seguridad automática

- Aplicable a: Este problema afecta a la versión 2002.
- Causa: Al configurar la copia de seguridad automatizada de las máquinas virtuales con SQL con una nueva cuenta de almacenamiento, se produce el error **Error de validación de la plantilla de implementación. No se encuentra el parámetro de plantilla para "SqlAutobackupStorageAccountKind".**
- Corrección: Aplique la revisión de la versión 2002 más reciente.

#### <a name="auto-backup-cannot-be-configured-with-tls-12-enabled"></a>No se puede configurar la copia de seguridad automática con TLS 1.2 habilitado

- Aplicable a: Este problema se aplica a las nuevas instalaciones de la versión 2002 y versiones posteriores y a cualquier versión anterior con TLS 1.2 habilitado.
- Causa: Al configurar la copia de seguridad automatizada de las máquinas virtuales con SQL con una cuenta de almacenamiento existente, se produce el error **Agente de IaaS de SQL Server: Se ha cerrado la conexión subyacente: Error inesperado de envío.**
- Repetición: Comunes

## <a name="storage"></a>Storage

### <a name="retention-period-revert-to-0"></a>El período de retención vuelve a 0

- Aplicable a: este problema afecta a las versiones 2002 y 2005.
- Causa: Si previamente especificó un período de tiempo distinto de 0 como valor del período de retención, este volverá a 0 (el valor predeterminado de esta configuración) durante las actualizaciones 2002 y 2005. El valor de 0 días surte efecto inmediatamente después de finalizar la actualización, lo que hace que todas las cuentas de almacenamiento existentes y las cuentas de almacenamiento recién eliminadas queden inmediatamente fuera del período de retención y se marquen para la recolección de elementos no utilizados periódica (que se ejecuta cada hora). 
- Corrección: Especifique manualmente un valor adecuado para el período de retención. Sin embargo, las cuentas de almacenamiento cuyos elementos no utilizados se hayan recolectado antes de que se especifique el nuevo período de retención, no son recuperables.  

## <a name="resource-providers"></a>Proveedores de recursos

### <a name="sqlmysql"></a>SQL/MySQL

- Aplicable a: este problema afecta a la versión 2002. 
- Causa: si el stamp contiene la versión 1.1.33.0 o anterior del proveedor de recursos (RP) de SQL, tras la actualización del stamp no se cargarán las hojas de SQL y MySQL.
- Corrección: actualizar el proveedor de recursos a la versión 1.1.47.0

### <a name="app-service"></a>App Service

- Aplicable a: este problema afecta a la versión 2002.
- Causa: si el stamp contiene la versión 1.7 o anterior del proveedor de recursos (RP) de App Service, tras la actualización del stamp no se cargará la hoja de App Service.
- Corrección: actualice el proveedor de recursos a la versión [2020 Q2](azure-stack-app-service-update.md).

## <a name="powershell"></a>PowerShell

[!Include[Known issue for install - one](../includes/known-issue-az-install-1.md)]

[!Include[Known issue for install - two](../includes/known-issue-az-install-2.md)]

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->
::: moniker-end

::: moniker range=">=azs-2002"
## <a name="archive"></a>Archivar

Para acceder a los problemas conocidos archivados de una versión anterior, use la lista desplegable del selector de versiones encima de la tabla de contenido de la izquierda y seleccione la versión que quiere ver.

## <a name="next-steps"></a>Pasos siguientes

- [Revisión de la lista de comprobación de actividades de actualización](release-notes-checklist.md)
- [Revisión de la lista de actualizaciones de seguridad](release-notes-security-updates.md)
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1910"
## <a name="1910-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1910
::: moniker-end
::: moniker range="azs-1908"
## <a name="1908-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1908
::: moniker-end
::: moniker range="azs-1907"
## <a name="1907-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1907
::: moniker-end
::: moniker range="azs-1906"
## <a name="1906-archived-known-issues"></a>Problemas conocidos archivados de la compilación 1906
::: moniker-end
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

::: moniker range="<azs-2002"
Puede consultar los [problemas conocidos de las versiones anteriores de Azure Stack Hub en la Galería de TechNet](https://aka.ms/azsarchivedrelnotes). Estos documentos archivados se proporcionan únicamente con fines de referencia y no implican que estas versiones sean compatibles. Para obtener información sobre el soporte técnico de Azure Stack Hub, consulte [Directiva de mantenimiento de Azure Stack Hub](azure-stack-servicing-policy.md). Para obtener más ayuda, póngase en contacto con los servicios de asistencia al cliente de Microsoft.
::: moniker-end
