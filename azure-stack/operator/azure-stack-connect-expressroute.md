---
title: Conexión de Azure Stack a Azure mediante ExpressRoute | Microsoft Docs
description: Aprenda a conectar redes virtuales de Azure Stack a redes virtuales de Azure mediante ExpressRoute.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 10/22/2018
ms.openlocfilehash: 2fee81a79b58294a9c43f8d550f139157cb64a9b
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283571"
---
# <a name="connect-azure-stack-to-azure-using-azure-expressroute"></a>Conexión de Azure Stack a Azure mediante Azure ExpressRoute

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En este artículo se describe cómo conectar una red virtual de Azure Stack a una red virtual de Azure mediante una conexión directa de [Microsoft Azure ExpressRoute](/azure/expressroute/).

Puede usar este artículo como tutorial y utilizar los ejemplos para configurar el mismo entorno de prueba. O bien, puede usar el artículo como guía para la configuración de su propio entorno de ExpressRoute.

## <a name="overview-assumptions-and-prerequisites"></a>Información general, suposiciones y requisitos previos

Azure ExpressRoute le permite ampliar sus redes locales a la nube de Microsoft mediante una conexión privada que facilita un proveedor de conectividad. ExpressRoute no es una conexión VPN a través de la red pública de Internet.

Para más información sobre Azure ExpressRoute, consulte [Información general sobre ExpressRoute](/azure/expressroute/expressroute-introduction).

### <a name="assumptions"></a>Supuestos

En este artículo se da por hecho que:

* Tiene conocimientos prácticos de Azure.
* Tiene conocimientos básicos de Azure Stack.
* Tiene conocimientos básicos de redes.

### <a name="prerequisites"></a>Requisitos previos

Para conectar Azure Stack y Azure mediante ExpressRoute, debe cumplir los siguientes requisitos:

* Un [circuito ExpressRoute](/azure/expressroute/expressroute-circuit-peerings) aprovisionado a través de un [proveedor de conectividad](/azure/expressroute/expressroute-locations).
* Una suscripción a Azure para crear un circuito ExpressRoute y VNet en Azure.
* Un enrutador que debe:
  * Admitir conexiones VPN de sitio a sitio entre su interfaz LAN y la puerta de enlace multiinquilino de Azure Stack.
  * Admitir la creación de varios VRF (reenvío y enrutamiento virtual) si hay más de un inquilino en la implementación de Azure Stack.
* Un enrutador que tenga:
  * Un puerto WAN conectado al circuito ExpressRoute.
  * Un puerto LAN conectado a la puerta de enlace multiinquilino de Azure Stack.

### <a name="expressroute-network-architecture"></a>Arquitectura de red de ExpressRoute

En la figura siguiente se muestran los entornos de Azure y Azure Stack después de finalizar la configuración de ExpressRoute mediante los ejemplos de este artículo:

![Red de ExpressRoute](media/azure-stack-connect-expressroute/Conceptual.png)

En la figura siguiente se muestra cómo varios inquilinos se conectan desde la infraestructura de Azure Stack a Azure en Microsoft Edge mediante el enrutador de ExpressRoute:

![Conexiones multiinquilino con ExpressRoute](media/azure-stack-connect-expressroute/Architecture.png)

En el ejemplo de este artículo se usa la misma arquitectura multiinquilino mostrada en este diagrama para conectar Azure Stack a Azure mediante el emparejamiento privado de ExpressRoute. La conexión se realiza mediante una conexión VPN de sitio a sitio desde la puerta de enlace de red virtual en Azure Stack hasta un enrutador de ExpressRoute.

En los pasos de este artículo se muestra cómo crear una conexión de extremo a extremo entre dos redes virtuales desde dos inquilinos diferentes de Azure Stack hasta sus correspondientes redes virtuales en Azure. La configuración de dos inquilinos es opcional; también puede usar estos pasos para un solo inquilino.

## <a name="configure-azure-stack"></a>Configurar Azure Stack

Para configurar el entorno de Azure Stack para el primer inquilino, use los pasos siguientes como guía. Si va a configurar más de un inquilino, repita estos pasos:

>[!NOTE]
>Estos pasos muestran cómo crear recursos mediante el portal de Azure Stack, pero también puede usar PowerShell.

![Configuración de red de Azure Stack](media/azure-stack-connect-expressroute/image2.png)

### <a name="before-you-begin"></a>Antes de empezar

Antes de empezar a configurar Azure Stack, necesita:

* Una implementación del sistema integrado de Azure Stack o una implementación del Kit de desarrollo de Azure Stack (ASDK). Para información sobre cómo implementar el ASDK, consulte el [inicio rápido de implementación del Kit de desarrollo de Azure Stack](../asdk/asdk-download.md).
* Una oferta de Azure Stack a la que los usuarios puedan suscribirse. Para más información, consulte [Introducción a los servicios, planes, ofertas y suscripciones](service-plan-offer-subscription-overview.md).

### <a name="create-network-resources-in-azure-stack"></a>Creación de recursos de red en Azure Stack

Use los procedimientos siguientes para crear los recursos de red necesarios en Azure Stack para un inquilino.

#### <a name="create-the-virtual-network-and-vm-subnet"></a>Creación de la red virtual y la subred de máquina virtual

1. Inicie sesión en el portal de usuarios de Azure Stack.

2. En el portal, seleccione **+ Crear un recurso**.

3. En **Azure Marketplace**, seleccione **Networking** (Red).

4. En **Featured** (Destacadas) seleccione **Virtual network** (Red virtual).

5. En **Create virtual network** (Crear red virtual), escriba los valores mostrados en la siguiente tabla en los campos adecuados:

   |Campo  |Valor  |
   |---------|---------|
   |NOMBRE     |Tenant1VNet1         |
   |Espacio de direcciones     |10.1.0.0/16|
   |Nombre de subred     |Tenant1-Sub1|
   |Intervalo de direcciones de subred     |10.1.1.0/24|

6. Verá la suscripción que creó anteriormente rellena en el campo **Suscripción**. Para el resto de campos:

    * En **Resource group** (Grupo de recursos), seleccione **Create new** (Crear) para crear un grupo de recursos o, si ya tiene uno, seleccione **Use existing** (Usar existente).
    * Compruebe la **Ubicación** predeterminada.
    * Haga clic en **Create**(Crear).
    * (Opcional) Haga clic en **Pin to dashboard** (Anclar al panel).

#### <a name="create-the-gateway-subnet"></a>Creación de la subred de la puerta de enlace

1. En **Virtual network** (Red virtual), seleccione **Tenant1VNet1**.
1. En **CONFIGURACIÓN**, seleccione **Subredes**.
1. Seleccione **+ Gateway subnet** (+Subred de puerta de enlace) para agregar una subred de puerta de enlace a la red virtual.
1. El nombre de la subred se establece como **GatewaySubnet** de forma predeterminada. Las subredes de puerta de enlace son un caso especial y deben usar este nombre para que funcionen correctamente.
1. Compruebe que el **intervalo de direcciones** sea **10.1.0.0/24**.
1. Haga clic en **Aceptar** para crear la subred de puerta de enlace.

#### <a name="create-the-virtual-network-gateway"></a>Creación de la puerta de enlace de red virtual

1. En el portal de usuario de Azure Stack, seleccione **+ Create a resource** (+ Crear un recurso).
1. En **Azure Marketplace**, seleccione **Networking** (Red).
1. Seleccione **Puerta de enlace de red virtual** en la lista de recursos de red.
1. En el campo **Name** (Nombre), escriba **GW1**.
1. Seleccione **Red virtual**.
1. Seleccione **Tenant1VNet1** en la lista desplegable.
1. Seleccione **Public IP address** (Dirección IP pública), luego **Choose public IP address** (Elegir dirección IP pública) y haga clic en **Create new** (Crear).
1. En el campo **Name** (Name), escriba **GW1-PiP** y, luego, haga clic en **OK** (Aceptar).
1. El **Tipo de VPN** debe tener seleccionada la opción **Route-based** (Basada en enrutamiento) de forma predeterminada. Mantenga este ajuste.
1. Compruebe que la **Suscripción** y la **Ubicación** son correctas. Haga clic en **Create**(Crear).

#### <a name="create-the-local-network-gateway"></a>Creación de la puerta de enlace de red local

El recurso de la puerta de enlace de red local identifica la puerta de enlace remota en el otro extremo de la conexión VPN. En este ejemplo, el extremo remoto de la conexión es la subinterfaz LAN del enrutador de ExpressRoute. Para el inquilino 1 del diagrama anterior, la dirección remota es 10.60.3.255.

1. Inicie sesión en el portal de usuario de Azure Stack y seleccione **+ Crear un recurso**.
1. En **Azure Marketplace**, seleccione **Networking** (Red).
1. Seleccione **local network gateway** (puerta de enlace de red local) en la lista de recursos de red.
1. En el campo **Name** (Nombre), escriba **ER-Router-GW**.
1. Para el campo **IP address** (Dirección IP), consulte la figura anterior. La dirección IP de la subinterfaz LAN del enrutador de ExpressRoute para el inquilino 1 es 10.60.3.255. Para su propio entorno, escriba la dirección IP de la interfaz correspondiente de su enrutador.
1. En el campo **Address Space** (Espacio de direcciones), escriba el espacio de direcciones de las redes virtuales a las que quiere conectarse en Azure. Las subredes del inquilino 1 son las siguientes:

   * 192.168.2.0/24 es la red virtual central de Azure.
   * 10.100.0.0/16 es la red virtual radial de Azure.

   > [!IMPORTANT]
   > En este ejemplo se da por hecho que usa rutas estáticas para la conexión VPN de sitio a sitio entre la puerta de enlace de Azure Stack y el enrutador de ExpressRoute.

1. Compruebe que la **suscripción**, el **grupo de recursos** y la **ubicación** son correctos. Seleccione **Crear**.

#### <a name="create-the-connection"></a>Creación de la conexión

1. En el portal de usuario de Azure Stack, seleccione **+ Crear un recurso**.
1. En **Azure Marketplace**, seleccione **Networking** (Red).
1. Seleccione **Conexión** en la lista de recursos.
1. En **Basics** (Datos básicos), elija **Site-to-site (IPSec)** (Sitio a sitio [IPSec]) como **tipo de conexión**.
1. Seleccione la **suscripción**, el **grupo de recursos** y la **ubicación**. Haga clic en **OK**.
1. En **Settings** (Configuración), seleccione **Virtual network gateway** (Puerta de enlace de red virtual) y elija **GW1**.
1. Seleccione **Local network gateway** (Puerta de enlace de red virtual) y luego elija **ER Router GW**.
1. En el campo **Connection name** (Nombre de la conexión), escriba **ConnectToAzure**.
1. En el campo **Shared key (PSK)** (Clave compartida [PSK]), escriba **abc123** y luego seleccione **OK** (Aceptar).
1. En **Summary** (Resumen), seleccione **OK** (Aceptar).

#### <a name="get-the-virtual-network-gateway-public-ip-address"></a>Obtención de la dirección IP pública de puerta de enlace de red virtual

Después de crear la puerta de enlace de red virtual, puede obtener la dirección IP pública de la puerta de enlace. Anote esta dirección en caso de que la necesite más adelante en la implementación. En función de su implementación, esta dirección se usa como **dirección IP interna**.

1. En el portal de usuario de Azure Stack, seleccione **All resources** (Todos los recursos).
1. En **All resources** (Todos los recursos), seleccione la puerta de enlace de red virtual, que es **GW1** en el ejemplo.
1. En **Virtual network gateway** (Puerta de enlace de red virtual), seleccione **Overview** (Información general) de la lista de recursos. Como alternativa, puede seleccionar **Properties** (Propiedades).
1. La dirección IP en que debe fijarse se muestra en **Public IP address** (Dirección IP pública). En la configuración de ejemplo, esta dirección es 192.68.102.1.

#### <a name="create-a-virtual-machine-vm"></a>Creación de una máquina virtual (VM)

Para comprobar el tráfico de datos mediante la conexión VPN, necesita máquinas virtuales para enviar y recibir datos en la red virtual de Azure Stack. Cree una máquina virtual e impleméntela en la subred de máquina virtual de la red virtual.

1. En el portal de usuario de Azure Stack, seleccione **+ Crear un recurso**.
1. En **Azure Marketplace**, seleccione **Compute** (Proceso).
1. En la lista de imágenes de máquina virtual, seleccione la imagen **Windows Server 2016 Datacenter Eval**.

   >[!NOTE]
   >Si la imagen usada en este artículo no está disponible, pida a su operador de Azure Stack que proporcione una imagen de Windows Server diferente.

1. En **Create virtual machine** (Crear una máquina virtual), seleccione **Basics** (Aspectos básicos) y, luego, en **Name** (Nombre), escriba **VM01**.
1. Escriba un nombre de usuario y una contraseña válidos. Usará esta cuenta para iniciar sesión en la máquina virtual una vez creada.
1. Indique los valores de **Subscription**, **Resource group** y **Location** (Suscripción, Grupo de recursos y Ubicación). Seleccione **Aceptar**.
1. En **Elegir un tamaño**, seleccione un tamaño de máquina virtual para esta instancia y, a continuación, elija **Seleccionar**.
1. En **Settings** (Configuración), confirme que:

   * La red virtual es **Tenant1VNet1**.
   * La subred está establecida en **10.1.1.0/24**.

   Use los valores predeterminados y haga clic en **OK** (Aceptar).

1. En **Summary** (Resumen), revise la configuración de máquina virtual y haga clic en **OK** (Aceptar).

Para agregar más inquilinos, repita los pasos realizados en estas secciones:

* [Creación de la red virtual y la subred de máquina virtual](#create-the-virtual-network-and-vm-subnet)
* [Creación de la subred de la puerta de enlace](#create-the-gateway-subnet)
* [Creación de la puerta de enlace de red virtual](#create-the-virtual-network-gateway)
* [Creación de la puerta de enlace de red local](#create-the-local-network-gateway)
* [Creación de la conexión](#create-the-connection)
* [Creación de una máquina virtual](#create-a-virtual-machine)

Si va a usar el inquilino 2 como ejemplo, no olvide cambiar las direcciones IP para evitar superposiciones.

### <a name="configure-the-nat-vm-for-gateway-traversal"></a>Configuración de la máquina virtual de NAT para el cruce seguro de puerta de enlace

> [!IMPORTANT]
> Esta sección es solo para implementaciones de ASDK. La NAT no es necesaria para las implementaciones de varios nodos.

El Kit de desarrollo de Azure Stack está autocontenido y aislado de la red en la que está implementado el host físico. La red IP virtual a la que están conectadas las puertas de enlace no es externa, está oculta detrás de un enrutador que realiza la traducción de direcciones de red (NAT).

El enrutador es un host de ASDK que ejecuta el rol de enrutamiento y servicios de acceso remoto (RRAS). Para permitir la conexión VPN de sitio a sitio en ambos extremos, es necesario configurar NAT en el host de ASDK.

#### <a name="configure-the-nat"></a>Configuración de la NAT

1. Inicie sesión en el equipo host de Azure Stack con su cuenta de administrador.
1. Ejecute el script en un ISE de PowerShell con privilegios elevados. Este script devuelve la **dirección de BGPNAT externa**.

   ```powershell
   Get-NetNatExternalAddress
   ```

1. Para configurar NAT, copie y edite el siguiente script de PowerShell. Edite el script para reemplazar `External BGPNAT address` y `Internal IP address` por los valores de ejemplo siguientes:

   * Como *dirección BGPNAT externa* use 10.10.0.62
   * Como *dirección IP interna* use 192.168.102.1

   Ejecute el script siguiente desde PowerShell ISE con privilegios elevados:

   ```powershell
   $ExtBgpNat = 'External BGPNAT address'
   $IntBgpNat = 'Internal IP address'

   # Designate the external NAT address for the ports that use the IKE authentication.
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 499 `
      -PortEnd 501
   Add-NetNatExternalAddress `
      -NatName BGPNAT `
      -IPAddress $Using:ExtBgpNat `
      -PortStart 4499 `
      -PortEnd 4501
   # Create a static NAT mapping to map the external address to the Gateway public IP address to map the ISAKMP port 500 for PHASE 1 of the IPSEC tunnel.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 500 `
      -InternalPort 500
   # Configure NAT traversal which uses port 4500 to  establish the complete IPSEC tunnel over NAT devices.
   Add-NetNatStaticMapping `
      -NatName BGPNAT `
      -Protocol UDP `
      -ExternalIPAddress $Using:ExtBgpNat `
      -InternalIPAddress $Using:IntBgpNat `
      -ExternalPort 4500 `
      -InternalPort 4500
   ```

## <a name="configure-azure"></a>Configuración de Azure

Cuando termine de configurar Azure Stack, puede implementar los recursos de Azure. En el siguiente diagrama se muestra un ejemplo de una red virtual de inquilino en Azure. Puede usar cualquier esquema de nombres y de direccionamiento para VNet en Azure. Sin embargo, el intervalo de direcciones de las redes virtuales en Azure y Azure Stack debe ser único y no superponerse:

![Redes virtuales de Azure](media/azure-stack-connect-expressroute/AzureArchitecture.png)

Los recursos que se implementan en Azure son similares a los recursos que se implementar en Azure Stack. Implementará los siguientes componentes:

* Redes virtuales y subredes
* Una subred de puerta de enlace
* Una puerta de enlace de red virtual
* Una conexión
* Un circuito ExpressRoute

La infraestructura de red de Azure de ejemplo está configurada de la manera siguiente:

* Un modelo de red virtual estándar de concentrador (192.168.2.0/24) y radio (10.100.0.0./16). Para más información sobre la topología de red en estrella tipo hub-and-spoke, consulte [Implementación de una topología de red en estrella tipo hub-and-spoke en Azure](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke).
* Las cargas de trabajo se implementan en la red virtual radial y el circuito ExpressRoute se conecta a la red virtual central.
* Las dos redes virtuales están conectadas mediante emparejamiento de VNet.

### <a name="configure-the-azure-vnets"></a>Configuración de las redes virtuales de Azure

1. Inicie sesión en Azure Portal con las credenciales de Azure.
1. Cree la red virtual central mediante el intervalo de direcciones 192.168.2.0/24.
1. Cree una subred con el intervalo de direcciones 192.168.2.0/25 y agregue una subred de puerta de enlace con el intervalo de direcciones 192.168.2.128/27.
1. Cree la VNet de spoke con el intervalo de direcciones 10.100.0.0/16.

Para más información sobre cómo crear redes virtuales en Azure, consulte [Creación de una red virtual](/azure/virtual-network/manage-virtual-network#create-a-virtual-network).

### <a name="configure-an-expressroute-circuit"></a>Configuración de un circuito ExpressRoute

1. Revise los requisitos previos de ExpressRoute en [Requisitos previos y lista de comprobación de ExpressRoute](/azure/expressroute/expressroute-prerequisites).

1. Siga los pasos de [Creación y modificación de un circuito ExpressRoute](/azure/expressroute/expressroute-howto-circuit-portal-resource-manager) para crear un circuito ExpressRoute con su suscripción a Azure.

   >[!NOTE]
   >Proporcione la clave de servicio del circuito a su servicio para que puedan configurar el circuito de ExpressRoute en su extremo.

1. Siga los pasos de [Creación y modificación del emparejamiento de un circuito ExpressRoute](/azure/expressroute/expressroute-howto-routing-portal-resource-manager) para configurar el emparejamiento privado en el circuito ExpressRoute.

### <a name="create-the-virtual-network-gateway"></a>Creación de la puerta de enlace de red virtual

Siga los pasos de [Configuración de una puerta de enlace de red virtual para ExpressRoute con PowerShell](/azure/expressroute/expressroute-howto-add-gateway-resource-manager) para crear una puerta de enlace de red virtual para ExpressRoute en la VNet de hub.

### <a name="create-the-connection"></a>Creación de la conexión

Para vincular el circuito ExpressRoute con la VNet de hub, siga los pasos de [Conexión de una red virtual a un circuito ExpressRoute](/azure/expressroute/expressroute-howto-linkvnet-portal-resource-manager).

### <a name="peer-the-vnets"></a>Emparejamiento de las redes virtuales

Empareje las redes virtuales concentrador y radio siguiendo los pasos que se indican en [Creación de un emparejamiento de red virtual mediante Azure Portal](/azure/virtual-network/virtual-networks-create-vnetpeering-arm-portal). Al configurar el emparejamiento de VNet, asegúrese de usar las opciones siguientes:

* Entre el concentrador y el radio, **permita el tránsito de puerta de enlace**.
* Desde los radios al concentrador, **use la puerta de enlace remota**.

### <a name="create-a-virtual-machine"></a>Creación de una máquina virtual

Implemente las máquinas virtuales de carga de trabajo en la red virtual de tipo spoke.

Repita estos pasos para cualquier VNet de inquilino adicional que desee conectar en Azure a través de sus circuitos ExpressRoute correspondientes.

## <a name="configure-the-router"></a>Configuración del enrutador

Puede usar el siguiente diagrama de configuración del enrutador de ExpressRoute como guía para configurar el enrutador de ExpressRoute. Esta figura muestra dos inquilinos (Tenant 1 y Tenant 2) con sus circuitos ExpressRoute correspondientes. Cada inquilino está vinculado a su propio VRF (enrutamiento virtual y reenvío) en el lado LAN y WAN del enrutador de ExpressRoute. Esta configuración garantiza el aislamiento de un extremo a otro entre los dos inquilinos. Anote las direcciones IP usadas en las interfaces del enrutador a medida que siga la configuración de ejemplo.

![Configuración del enrutador de ExpressRoute](media/azure-stack-connect-expressroute/EndToEnd.png)

Puede usar cualquier enrutador que admita VPN de IKEv2 y BGP para finalizar la conexión VPN de sitio a sitio desde Azure Stack. El mismo enrutador se utiliza para conectarse a Azure con un circuito ExpressRoute.

El siguiente ejemplo de configuración del enrutador de los servicios de agregación de Cisco ASR 1000 Series admite la infraestructura de red que se muestra en el diagrama de *configuración del enrutador de ExpressRoute*.

```shell
ip vrf Tenant 1
 description Routing Domain for PRIVATE peering to Azure for Tenant 1
 rd 1:1
!
ip vrf Tenant 2
 description Routing Domain for PRIVATE peering to Azure for Tenant 2
 rd 1:5
!
crypto ikev2 proposal V2-PROPOSAL2
description IKEv2 proposal for Tenant 1
encryption aes-cbc-256
 integrity sha256
 group 2
crypto ikev2 proposal V4-PROPOSAL2
description IKEv2 proposal for Tenant 2
encryption aes-cbc-256
 integrity sha256
 group 2
!
crypto ikev2 policy V2-POLICY2
description IKEv2 Policy for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 proposal V2-PROPOSAL2
description IKEv2 Policy for Tenant 2
crypto ikev2 policy V4-POLICY2
 match fvrf Tenant 2
 match address local 10.60.3.251
 proposal V4-PROPOSAL2
!
crypto ikev2 profile V2-PROFILE
description IKEv2 profile for Tenant 1
match fvrf Tenant 1
 match address local 10.60.3.255
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 1
!
crypto ikev2 profile V4-PROFILE
description IKEv2 profile for Tenant 2
 match fvrf Tenant 2
 match address local 10.60.3.251
 match identity remote any
 authentication remote pre-share key abc123
 authentication local pre-share key abc123
 ivrf Tenant 2
!
crypto ipsec transform-set V2-TRANSFORM2 esp-gcm 256
 mode tunnel
crypto ipsec transform-set V4-TRANSFORM2 esp-gcm 256
 mode tunnel
!
crypto ipsec profile V2-PROFILE
 set transform-set V2-TRANSFORM2
 set ikev2-profile V2-PROFILE
!
crypto ipsec profile V4-PROFILE
 set transform-set V4-TRANSFORM2
 set ikev2-profile V4-PROFILE
!
interface Tunnel10
description S2S VPN Tunnel for Tenant 1
 ip vrf forwarding Tenant 1
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.211
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf Tenant 1
 tunnel protection ipsec profile V2-PROFILE
!
interface Tunnel20
description S2S VPN Tunnel for Tenant 2
 ip vrf forwarding Tenant 2
 ip address 11.0.0.2 255.255.255.252
 ip tcp adjust-mss 1350
 tunnel source TenGigabitEthernet0/1/0.213
 tunnel mode ipsec ipv4
 tunnel destination 10.10.0.62
 tunnel vrf VNET3
 tunnel protection ipsec profile V4-PROFILE
!
interface GigabitEthernet0/0/1
 description PRIMARY ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/1.100
description Primary WAN interface of Tenant 1
 description PRIMARY ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.1 255.255.255.252
!
interface GigabitEthernet0/0/1.102
description Primary WAN interface of Tenant 2
 description PRIMARY ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.17 255.255.255.252
!
interface GigabitEthernet0/0/2
 description BACKUP ExpressRoute Link to AZURE over Equinix
 no ip address
 negotiation auto
!
interface GigabitEthernet0/0/2.100
description Secondary WAN interface of Tenant 1
 description BACKUP ER link supporting Tenant 1 to Azure
 encapsulation dot1Q 101
 ip vrf forwarding Tenant 1
 ip address 192.168.1.5 255.255.255.252
!
interface GigabitEthernet0/0/2.102
description Secondary WAN interface of Tenant 2
description BACKUP ER link supporting Tenant 2 to Azure
 encapsulation dot1Q 102
 ip vrf forwarding Tenant 2
 ip address 192.168.1.21 255.255.255.252
!
interface TenGigabitEthernet0/1/0
 description Downlink to ---Port 1/47
 no ip address
!
interface TenGigabitEthernet0/1/0.211
 description LAN interface of Tenant 1
description Downlink to --- Port 1/47.211
 encapsulation dot1Q 211
 ip vrf forwarding Tenant 1
 ip address 10.60.3.255 255.255.255.254
!
interface TenGigabitEthernet0/1/0.213
description LAN interface of Tenant 2
 description Downlink to --- Port 1/47.213
 encapsulation dot1Q 213
 ip vrf forwarding Tenant 2
 ip address 10.60.3.251 255.255.255.254
!
router bgp 65530
 bgp router-id <removed>
 bgp log-neighbor-changes
 description BGP neighbor config and route advertisement for Tenant 1 VRF
 address-family ipv4 vrf Tenant 1
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.254 mask 255.255.255.254
  network 192.168.1.0 mask 255.255.255.252
  network 192.168.1.4 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65100
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 1
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.254 remote-as 4232570301
  neighbor 10.60.3.254 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.254 activate
  neighbor 10.60.3.254 route-map BLOCK-ALL out
  neighbor 192.168.1.2 remote-as 12076
  neighbor 192.168.1.2 description PRIMARY ER peer for Tenant 1 to Azure
  neighbor 192.168.1.2 ebgp-multihop 5
  neighbor 192.168.1.2 activate
  neighbor 192.168.1.2 soft-reconfiguration inbound
  neighbor 192.168.1.2 route-map Tenant 1-ONLY out
  neighbor 192.168.1.6 remote-as 12076
  neighbor 192.168.1.6 description BACKUP ER peer for Tenant 1 to Azure
  neighbor 192.168.1.6 ebgp-multihop 5
  neighbor 192.168.1.6 activate
  neighbor 192.168.1.6 soft-reconfiguration inbound
  neighbor 192.168.1.6 route-map Tenant 1-ONLY out
  maximum-paths 8
 exit-address-family
 !
description BGP neighbor config and route advertisement for Tenant 2 VRF
address-family ipv4 vrf Tenant 2
  network 10.1.0.0 mask 255.255.0.0
  network 10.60.3.250 mask 255.255.255.254
  network 192.168.1.16 mask 255.255.255.252
  network 192.168.1.20 mask 255.255.255.252
  neighbor 10.10.0.62 remote-as 65300
  neighbor 10.10.0.62 description VPN-BGP-PEER-for-Tenant 2
  neighbor 10.10.0.62 ebgp-multihop 5
  neighbor 10.10.0.62 activate
  neighbor 10.60.3.250 remote-as 4232570301
  neighbor 10.60.3.250 description LAN peer for CPEC:INET:2112 VRF
  neighbor 10.60.3.250 activate
  neighbor 10.60.3.250 route-map BLOCK-ALL out
  neighbor 192.168.1.18 remote-as 12076
  neighbor 192.168.1.18 description PRIMARY ER peer for Tenant 2 to Azure
  neighbor 192.168.1.18 ebgp-multihop 5
  neighbor 192.168.1.18 activate
  neighbor 192.168.1.18 soft-reconfiguration inbound
  neighbor 192.168.1.18 route-map VNET-ONLY out
  neighbor 192.168.1.22 remote-as 12076
  neighbor 192.168.1.22 description BACKUP ER peer for Tenant 2 to Azure
  neighbor 192.168.1.22 ebgp-multihop 5
  neighbor 192.168.1.22 activate
  neighbor 192.168.1.22 soft-reconfiguration inbound
  neighbor 192.168.1.22 route-map VNET-ONLY out
  maximum-paths 8
 exit-address-family
!
ip forward-protocol nd
!
ip as-path access-list 1 permit ^$
ip route vrf Tenant 1 10.1.0.0 255.255.0.0 Tunnel10
ip route vrf Tenant 2 10.1.0.0 255.255.0.0 Tunnel20
!
ip prefix-list BLOCK-ALL seq 5 deny 0.0.0.0/0 le 32
!
route-map BLOCK-ALL permit 10
 match ip address prefix-list BLOCK-ALL
!
route-map VNET-ONLY permit 10
 match as-path 1
!
```

## <a name="test-the-connection"></a>Comprobación de la conexión

Pruebe la conexión después de establecer la conexión de sitio a sitio y el circuito ExpressRoute.

Realice las siguientes pruebas de ping:

* Inicie sesión en una de las máquinas virtuales de la red virtual de Azure y haga ping a la máquina virtual que creó en Azure Stack.
* Inicie sesión en una de las máquinas virtuales que creó en Azure Stack y haga ping a la máquina virtual que creó en la red virtual de Azure.

>[!NOTE]
>Para asegurarse de enviar el tráfico mediante las conexiones de sitio a sitio y ExpressRoute, debe hacer ping a la dirección IP dedicada (DIP) de la máquina virtual en ambos extremos y no a la dirección de IP virtual de la máquina virtual.

### <a name="allow-icmp-in-through-the-firewall"></a>Permitir que ICMP pase por el firewall

De forma predeterminada, Windows Server 2016 no permite paquetes ICMP entrantes a través del firewall. Para cada máquina virtual que use para pruebas de ping, debe permitir paquetes ICMP entrantes. Para crear una regla de firewall de ICMP, ejecute el siguiente cmdlet en una ventana de PowerShell con privilegios elevados:

```powershell
# Create ICMP firewall rule.
New-NetFirewallRule `
  -DisplayName "Allow ICMPv4-In" `
  -Protocol ICMPv4
```

### <a name="ping-the-azure-stack-vm"></a>Hacer ping a la máquina virtual de Azure Stack

1. Inicie sesión en el portal de usuarios de Azure Stack.

1. Busque la máquina virtual que creó y selecciónela.

1. Seleccione **Conectar**.

1. Desde un símbolo del sistema de Windows o PowerShell con privilegios elevados, escriba **ipconfig/all**. Anote la dirección IPv4 que se devuelve en la salida.

1. Haga ping a la dirección IPv4 desde la máquina virtual de la red virtual de Azure.

   En el entorno de ejemplo, la dirección IPv4 proviene de la subred 10.1.1.x/24. En su entorno, la dirección puede ser diferente, pero debe estar en la subred que creó para la subred de la red virtual de inquilino.

### <a name="view-data-transfer-statistics"></a>Visualización de estadísticas de transferencia de datos

Si quiere saber la cantidad de tráfico que pasa por la conexión, puede encontrar esta información en el portal de usuario de Azure Stack. La visualización de las estadísticas de transferencia de datos también es conveniente para saber si los datos de prueba de ping pasaron por las conexiones VPN y ExpressRoute:

1. Inicie sesión en el portal de usuarios de Azure Stack y seleccione **All resources** (Todos los recursos).
1. Vaya al grupo de recursos de VPN Gateway y seleccione el tipo de objeto **Connection** (Conexión).
1. Seleccione la conexión **ConnectToAzure** en la lista.
1. En **Connections** > **Overview** (Conexiones > Información general), puede ver las estadísticas de **Data in** (Entrada de datos) y **Data out** (Salida de datos). Verá algunos valores distintos de cero.

   ![Entrada de datos y salida de datos](media/azure-stack-connect-expressroute/DataInDataOut.png)

## <a name="next-steps"></a>Pasos siguientes

[Implementar aplicaciones en Azure y Azure Stack](../user/azure-stack-solution-pipeline.md )
