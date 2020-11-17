---
title: Emparejamiento de red virtual en Azure Stack Hub
description: Obtenga información sobre cómo usar el emparejamiento de red virtual para conectar redes virtuales en Azure Stack Hub.
author: sethmanheim
ms.author: sethm
ms.date: 11/11/2020
ms.topic: conceptual
ms.reviewer: sranthar
ms.lastreviewed: 10/09/2020
ms.openlocfilehash: 16d7701161b8ec8c16aa3caecf5d5e291d6c0e99
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94548724"
---
# <a name="virtual-network-peering"></a>Emparejamiento de red virtual

El emparejamiento de red virtual permite conectar redes virtuales sin problemas en un entorno de Azure Stack Hub. A efectos de conectividad las redes virtuales aparecen como una sola. El tráfico entre máquinas virtuales usa la infraestructura de SDN subyacente. Al igual que el tráfico entre las máquinas virtuales de la misma red, el tráfico solo se enruta a través de la red privada de Azure Stack Hub.

Azure Stack Hub no admite el emparejamiento global, ya que no se aplica el concepto de "regiones".

Las ventajas del uso del emparejamiento de red virtual son las siguientes:

- Baja latencia, conexión de gran ancho de banda entre los recursos de redes virtuales diferentes.
- La capacidad de los recursos de una red virtual para comunicarse con los de otra red virtual.
- La capacidad de transferir datos entre redes virtuales de distintas suscripciones e inquilinos de Azure Active Directory.
- No tienen tiempo de inactividad ni los recursos de la red virtual al crear el emparejamiento, o después.

El tráfico de red entre redes virtuales emparejadas es privado. El tráfico entre redes virtuales se mantiene en el nivel de infraestructura. No se requiere ninguna red pública de Internet, puertas de enlace ni cifrado en la comunicación entre redes virtuales.

## <a name="connectivity"></a>Conectividad

En el caso de las redes virtuales emparejadas, los recursos de cualquiera de ellas se pueden conectar directamente con los de la red virtual emparejada.

La latencia de red entre las máquinas virtuales de redes virtuales emparejadas en la misma región es la misma que la de una única red virtual. El rendimiento de la red se basa en el ancho de banda que se permite para la máquina virtual, en proporción a su tamaño. No hay restricciones adicionales con respecto al ancho de banda en el emparejamiento.

El tráfico entre las máquinas virtuales en las redes virtuales emparejadas se enruta directamente a través de la capa SDN, no de una puerta de enlace ni en la red Internet pública.

En cualquier red virtual, se pueden aplicar grupos de seguridad de red para bloquear el acceso a otras redes o subredes virtuales. Al configurar el emparejamiento de red virtual, abra o cierre las reglas del grupo de seguridad de red entre las redes virtuales. Si abre la conectividad completa entre redes virtuales emparejadas, puede aplicar grupos de seguridad de red a subredes o máquinas virtuales concretas para bloquear o denegar el acceso específico. La opción predeterminada es la conectividad total. Para obtener más información sobre los grupos de seguridad de red, consulte [Grupos de seguridad](/azure/virtual-network/security-overview).

## <a name="service-chaining"></a>Encadenamiento de servicios

El encadenamiento de servicios permite dirigir el tráfico de una red virtual a una aplicación virtual o puerta de enlace de una red emparejada a través de rutas definidas por el usuario.

Para habilitar el encadenamiento de servicios, configure las rutas definidas por el usuario que apuntan a máquinas virtuales de redes virtuales emparejadas como la dirección IP del *próximo salto*.

Puede implementar redes *radiales*, en las que la red virtual del concentrador hospeda componentes de la infraestructura, como una aplicación virtual de red o una puerta de enlace de VPN. Todas las redes virtuales de radio se pueden emparejar con la red virtual de concentrador. El tráfico fluye por las aplicaciones virtuales de red o las puertas de enlace de VPN de la red virtual del concentrador.

El emparejamiento de red virtual permite que el próximo salto de una ruta definida por el usuario sea la dirección IP de una máquina virtual de la red virtual emparejada. Para obtener más información sobre las rutas definidas por el usuario, consulte [Introducción a las rutas definidas por el usuario](/azure/virtual-network/virtual-networks-udr-overview#user-defined). Para aprender a crear una topología de red en estrella tipo hub-and-spoke, consulte [Topología en estrella tipo hub-and-spoke en Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json).

## <a name="gateways-and-on-premises-connectivity"></a>Puertas de enlace y conectividad local

Cada red virtual, incluidas las redes virtuales emparejadas, puede tener su propia puerta de enlace. Las redes virtuales pueden usar su puerta de enlace para conectarse a una red local. Revise la [documentación de la puerta de enlace de red virtual](/azure/vpn-gateway/).

La puerta de enlace de la red virtual emparejada también se puede configurar como un punto de tránsito a una red local. En ese caso, la red virtual que usa una puerta de enlace remota no puede tener su propia puerta de enlace. Una red virtual no puede tener más de una puerta de enlace. En la red virtual emparejada, la puerta de enlace puede ser local o remota, como se muestra en la figura siguiente:

:::image type="content" source="media/virtual-network-peering/virtual-network-gateway.png" alt-text="Topología de VPN Gateway":::

Tenga en cuenta que se debe crear un objeto **Conexión** en VPN Gateway antes de habilitar las opciones de **UseRemoteGateways** en el emparejamiento.

## <a name="virtual-network-peering-configuration"></a>Configuración del emparejamiento de red virtual

**Permitir acceso a red virtual**: Al habilitar la comunicación entre las redes virtuales, permite que los recursos conectados a cualquier red virtual se comuniquen entre sí con el mismo ancho de banda y latencia que si estuvieran conectados a la misma red virtual. Todas las comunicaciones entre los recursos de las dos redes virtuales se realizan a través de la capa de SDN interna.  

Un motivo de no habilitar el acceso de red puede ser un escenario en el que ha emparejado una red virtual con otra red virtual pero quiere deshabilitar en ocasiones el flujo de tráfico entre ambas redes. Le puede resultar más cómoda la opción de habilitar o deshabilitar en lugar de eliminar y volver a crear emparejamientos. Si esta opción está deshabilitada, el tráfico no fluye entre las redes virtuales emparejadas.

**Permitir tráfico reenviado:** seleccione esta casilla para permitir que el tráfico que *reenvió* una aplicación virtual de red desde una red virtual (este tráfico no se origina en la red virtual) fluya a esta red virtual mediante un emparejamiento. Por ejemplo, supongamos que hay tres redes virtuales llamadas Radio1, Radio2 y Concentrador. Existe un emparejamiento entre cada red virtual de radio y la red virtual de concentrador, pero no existe ninguno entre las redes virtuales de radio. Se implementa una aplicación virtual de red en la red virtual de concentrador, y las rutas definidas por el usuario se aplican a cada red virtual de radio que redirige el tráfico entre las subredes a través de la aplicación virtual de red. Si esta casilla no está seleccionada para realizar el emparejamiento entre cada red virtual de radio y la red virtual de concentrador, el tráfico no fluye entre las redes virtuales de radio porque el concentrador no reenvía el tráfico entre las redes virtuales. Aunque el hecho de habilitar esta funcionalidad permite el tráfico reenviado a través del emparejamiento, no se crean rutas definidas por el usuario ni aplicaciones virtuales de red. Las rutas definidas por el usuario y las aplicaciones virtuales de red se crean por separado. Obtenga información sobre las [rutas definidas por el usuario](/azure/virtual-network/virtual-networks-udr-overview#user-defined). No es necesario comprobar esta configuración si se reenvía el tráfico entre redes virtuales a través de VPN Gateway.

**Permitir tránsito de puerta de enlace:** active esta casilla si tiene una puerta de enlace de red virtual asociada a esta red virtual y quiere permitir que el tráfico procedente de la red virtual emparejada fluya a través de la puerta de enlace. Por ejemplo, esta red virtual puede asociarse a una red local a través de una puerta de enlace de red virtual. La selección de esta casilla permite que el tráfico procedente de la red virtual emparejada fluya a través de la puerta de enlace asociada a esta red virtual hacia la red local. Si activa esta casilla, la red virtual emparejada no puede tener una puerta de enlace configurada. La red virtual emparejada debe tener seleccionada la casilla **Usar puertas de enlace remotas** cuando se configura el emparejamiento de la otra red virtual con esta red virtual. Si deja esta casilla sin activar (valor predeterminado), el tráfico procedente de la red virtual emparejada seguirá fluyendo a esta red virtual, pero no podrá fluir a través de una puerta de enlace de red virtual asociada a esta red virtual.

**Usar puertas de enlace remotas:** active esta casilla para permitir que el tráfico procedente de esta red virtual fluya a través de la puerta de enlace de red virtual asociada a la red virtual con la que está realizando el emparejamiento. Por ejemplo, la red virtual con la que está realizando el emparejamiento tiene una puerta de enlace de VPN asociada que permite la comunicación a una red local. La activación de esta casilla permite que el tráfico procedente de esta red virtual fluya a través de la puerta de enlace de VPN asociada a la red virtual emparejada. Si activa esta casilla, la red virtual emparejada debe tener asociada una puerta de enlace de red virtual y debe tener activada la casilla **Permitir tránsito de puerta de enlace**. Si deja esta casilla sin activar (valor predeterminado), el tráfico procedente de la red virtual emparejada todavía puede fluir a esta red virtual, pero no podrá fluir a través de una puerta de enlace de red virtual asociada a esta red virtual.

No puede usar las puertas de enlace remotas si ya tiene una puerta de enlace configurada en la red virtual.

### <a name="permissions"></a>Permisos

Asegúrese de que, al crear emparejamientos con redes virtuales de diferentes suscripciones e inquilinos de Azure AD, las cuentas tengan asignado el rol **Colaborador**. Además, no hay ninguna funcionalidad de interfaz de usuario para emparejar entre distintos inquilinos de Azure AD. Puede usar la CLI de Azure y PowerShell para crear los emparejamientos.

## <a name="virtual-network-peering-frequently-asked-questions-faq"></a>Preguntas más frecuentes (P+F) sobre el emparejamiento de red virtual

### <a name="what-is-virtual-network-peering"></a>¿Qué es el emparejamiento de red virtual?

El emparejamiento de red virtual permite conectar redes virtuales. Una conexión de emparejamiento de VNet entre redes virtuales permite enrutar el tráfico entre ellas de manera privada a través de direcciones IPv4. Las máquinas virtuales de las VNet emparejadas pueden comunicarse entre sí como si estuvieran dentro de la misma red. También se pueden crear conexiones de emparejamiento de VNet a través de varias suscripciones.

### <a name="does-azure-stack-hub-support-global-vnet-peering"></a>¿Admite Azure Stack Hub el emparejamiento de VNET global?

Azure Stack Hub no admite el emparejamiento global, ya que no se aplica el concepto de "regiones".

### <a name="on-which-azure-stack-hub-update-will-virtual-network-peering-be-available"></a>¿En qué actualización de Azure Stack Hub estará disponible el emparejamiento de red virtual?

El emparejamiento de red virtual está disponible en Azure Stack Hub a partir de la actualización 2008.

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub-to-a-virtual-network-in-azure"></a>¿Puedo emparejar mi red virtual de Azure Stack Hub con una red virtual de Azure?

No, en este momento no se admite el emparejamiento entre Azure y Azure Stack Hub.

### <a name="can-i-peer-my-virtual-network-in-azure-stack-hub1-to-a-virtual-network-in-azure-stack-hub2"></a>¿Puedo emparejar mi red virtual de Azure Stack Hub1 con una red virtual de Azure Stack Hub2?

No, el emparejamiento solo se puede crear entre redes virtuales de un sistema de Azure Stack Hub. Para obtener más información sobre cómo conectar dos redes virtuales de diferentes marcas, consulte [Establecimiento de una conexión de VNET a VNET en Azure Stack Hub](azure-stack-network-howto-vnet-to-vnet-stacks.md).

### <a name="can-i-enable-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>¿Puedo habilitar el emparejamiento si mis redes virtuales pertenecen a suscripciones de diferentes inquilinos de Azure Active Directory?

Sí. No es posible establecer el emparejamiento de VNet si las suscripciones pertenecen a diferentes inquilinos de Azure Active Directory. Puede hacerlo a través de PowerShell o CLI. Aún no se admite el portal.

### <a name="can-i-peer-my-virtual-network-with-a-virtual-network-in-a-different-subscription"></a>¿Puedo emparejar mi red virtual con una red virtual de una suscripción diferente?

Sí. Puede emparejar redes virtuales entre suscripciones.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>¿Hay alguna limitación de ancho de banda para las conexiones de emparejamiento?

No. El emparejamiento de redes virtuales no impone restricciones de ancho de banda. El ancho de banda solo está limitado por el recurso de proceso o de máquina virtual.

### <a name="my-virtual-network-peering-connection-is-in-an-initiated-state-why-cant-i-connect"></a>Mi conexión de emparejamiento de red virtual se encuentra en estado *Iniciado*, ¿por qué no puedo conectarme?

Si la conexión de emparejamiento está en estado **Iniciado**, significa que ha creado un solo vínculo. Se debe crear un vínculo bidireccional con el fin de establecer una conexión correcta. Por ejemplo, para emparejar VNet A con VNet B, se debe crear un vínculo de VNet A con VNet B y de VNet B con VNet A. La creación de ambos vínculos cambia el estado a **Conectado**.

### <a name="my-virtual-network-peering-connection-is-in-a-disconnected-state-why-cant-i-create-a-peering-connection"></a>Mi conexión de emparejamiento de red virtual se encuentra en estado *Desconectado*, ¿por qué no puedo crear una conexión de emparejamiento?

Si la conexión de emparejamiento de red virtual está en estado **Desconectado**, significa que se ha eliminado uno de los vínculos creados. Para volver a establecer una conexión de emparejamiento, elimine el vínculo y vuelva a crearlo.

### <a name="is-virtual-network-peering-traffic-encrypted"></a>¿Está cifrado el tráfico de emparejamiento de red virtual?

No. El tráfico entre recursos de redes virtuales emparejadas es privado y aislado. Permanece por completo en la capa de SDN del sistema de Azure Stack Hub.

### <a name="if-i-peer-vnet-a-to-vnet-b-and-i-peer-vnet-b-to-vnet-c-does-that-mean-vnet-a-and-vnet-c-are-peered"></a>Si emparejo VNet A con VNet B y emparejo VNet B con VNet C, ¿significa que VNet A y VNet C están emparejadas?

No. No se admite el emparejamiento transitivo. Debe emparejar VNet A y VNet C.

## <a name="next-steps"></a>Pasos siguientes

- [Sobre Azure Virtual Network](/azure/virtual-network/virtual-networks-overview)
- [Introducción a las rutas definidas por el usuario](/azure/virtual-network/virtual-networks-udr-overview#user-defined)
- [Topología de red en estrella tipo hub-and-spoke en Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)
