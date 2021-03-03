---
title: Proxy transparente para sistemas integrados de Azure Stack Hub
description: Información general del proxy transparente en sistemas integrados de Azure Stack Hub.
author: PatAltimore
ms.topic: conceptual
ms.date: 01/25/2021
ms.author: patricka
ms.reviewer: sranthar
ms.lastreviewed: 01/25/2021
ms.openlocfilehash: 974f40364b4eed13bd7440b35596597312c98624
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/05/2021
ms.locfileid: "99577372"
---
# <a name="transparent-proxy-for-azure-stack-hub"></a>Proxy transparente para Azure Stack Hub

Un proxy transparente (también conocido como proxy de intercepción, en línea o forzado) intercepta la comunicación normal en la capa de red sin requerir ninguna configuración especial del cliente. Los clientes no necesitan estar enterados de la existencia del proxy.

Si el centro de datos requiere que todo el tráfico utilice un proxy, debe configurar un proxy transparente para procesar todo el tráfico según la directiva mediante la separación del tráfico entre las zonas de la red.

## <a name="traffic-types"></a>Tipos de tráfico

El tráfico saliente desde Azure Stack Hub se clasifica como tráfico de inquilino o tráfico de infraestructura.

Los inquilinos generan el tráfico de inquilino por medio de máquinas virtuales, equilibradores de carga, puertas de enlace de VPN, servicios de aplicaciones, etc.

El tráfico de infraestructura se genera desde el primer intervalo `/27` del grupo de direcciones IP virtuales públicas hacia servicios de infraestructura, como la identidad, las revisiones y actualizaciones, las métricas de uso, la sindicación con Marketplace, el registro, la recopilación de registros, Windows Defender, etc. El tráfico que procede de estos servicios se enruta a [puntos de conexión de Azure](azure-stack-integrate-endpoints.md#ports-and-urls-outbound). Azure no acepta tráfico modificado por un proxy ni tráfico interceptado de TLS/SSL. Este es el motivo por el que Azure Stack Hub no admite una configuración de proxy nativo.

Al configurar un proxy transparente, puede elegir enviar todo el tráfico saliente o solo el tráfico de infraestructura a través del proxy.

## <a name="partner-integration"></a>Integración de asociados

Microsoft se ha asociado con los principales proveedores de servidores proxy del sector para validar los escenarios de casos de uso de Azure Stack Hub con una configuración de proxy transparente. El siguiente diagrama es un ejemplo de configuración de red de Azure Stack Hub con servidores proxy de alta disponibilidad. Los dispositivos proxy externos deben colocarse más arriba en el tráfico vertical que los dispositivos de borde.

![Diagrama de red con un servidor proxy antes de los dispositivos de borde](./media/azure-stack-transparent-proxy/proxy.svg)

 Además, los dispositivos de borde se deben configurar de una de las siguientes maneras para enrutar el tráfico desde Azure Stack Hub:
- Enrutar todo el tráfico saliente desde Azure Stack Hub a los dispositivos proxy
- Enrutar todo el tráfico saliente desde el primer intervalo `/27` del grupo de direcciones IP virtuales de Azure Stack Hub a los dispositivos proxy mediante el enrutamiento basado en directivas.  

Para ver una configuración de borde de ejemplo, consulte la sección [Configuración de borde de ejemplo](#example-border-configuration) de este artículo.

Revise los siguientes documentos para conocer las configuraciones de proxy transparentes validadas con Azure Stack Hub: 

- [Configuración de un proxy transparente de puerta de enlace de seguridad de Check Point](https://supportcenter.checkpoint.com/supportcenter/portal?eventSubmit_doGoviewsolutiondetails=&solutionid=sk171559)
- [Configuración de un proxy transparente de firewall Sophos XG](https://community.sophos.com/xg-firewall/f/recommended-reads/124106/xg-firewall-integration-with-azure-stack-hub)

En escenarios en los que es necesario que el tráfico de salida de Azure Stack Hub fluya a través de un proxy explícito, los dispositivos de Check Point y Sophos proporcionan una característica de modo dual que permite intervalos específicos de tráfico mediante el modo transparente, mientras que otros intervalos se pueden configurar para pasar mediante un modo explícito. Con esta característica, estos dispositivos proxy se pueden configurar de modo que se envíe a través del proxy transparente solo el tráfico de infraestructura, mientras que todo el tráfico de inquilino se envía mediante el modo explícito.

> [!IMPORTANT]
> No se admite la interceptación de tráfico SSL y puede provocar errores del servicio cuando se accede a los puntos de conexión. El tiempo de expiración máximo admitido para comunicarse con los puntos de conexión necesarios para la identidad es de 60 s con 3 reintentos. Para más información, consulte [Integración del firewall de Azure Stack Hub](azure-stack-firewall.md#ssl-interception).

## <a name="example-border-configuration"></a>Configuración de borde de ejemplo

La solución se basa en el enrutamiento basado en directivas (PBR), que usa un conjunto de criterios definido por el administrador implementados mediante una lista de control de acceso (ACL). La ACL clasifica el tráfico que se dirige a la dirección IP del próximo salto de los dispositivos proxy implementados en un mapa de ruta, en lugar del enrutamiento normal que se basa únicamente en la dirección IP de destino. El tráfico de red de infraestructura específico para los puertos 80 y 443 se enruta desde los dispositivos de borde hasta la implementación de proxy transparente. El proxy transparente realiza filtrado de direcciones URL y no se anula *ningún tráfico permitido*.

El ejemplo de configuración siguiente es para un chasis de Cisco Nexus 9508. 

En este escenario, las redes de infraestructura de origen que requieren acceso a Internet son las siguientes:

- VIP pública: primer intervalo /27
- Red de infraestructura: último intervalo /27
- Red de BMC: último intervalo /27

En este escenario, las siguientes subredes reciben el tratamiento de enrutamiento basado en directivas (PBR):

| Red | Rango de direcciones IP | Subred que recibe el tratamiento de PBR
|---------|----------|-------------------------------
| Grupo de direcciones IP virtuales públicas | Primer intervalo /27 de 172.21.107.0/27 | 172.21.107.0/27 = de 172.21.107.1 a 172.21.107.30
| Red de infraestructura | Último intervalo /27 de 172.21.7.0/24 | 172.21.7.224/27 = de 172.21.7.225 a 172.21.7.254
| Red de BMC | Último intervalo /27 de 10.60.32.128/26 | 10.60.32.160/27 = de 10.60.32.161 a 10.60.32.190

### <a name="configure-border-device"></a>Configuración del dispositivo de borde

Escriba el comando `feature pbr` para habilitar el PBR. 

```
****************************************************************************
PBR Configuration for Cisco Nexus 9508 Chassis
PBR Enivronment configured to use VRF08
The test rack has is a 4-node Azure Stack stamp with 2x TOR switches and 1x BMC switch. Each TOR switch 
has a single uplink to the Nexus 9508 chassis using BGP for routing. In this example the test rack 
is in it's own VRF (VRF08)
****************************************************************************
!
feature pbr
!

<Create VLANs that the proxy devices will use for inside and outside connectivity>

!
VLAN 801
name PBR_Proxy_VRF08_Inside
VLAN 802
name PBR_Proxy_VRF08_Outside
!
interface vlan 801
description PBR_Proxy_VRF08_Inside
no shutdown
mtu 9216
vrf member VRF08
no ip redirects
ip address 10.60.3.1/29
!
interface vlan 802
description PBR_Proxy_VRF08_Outside
no shutdown
mtu 9216
vrf member VRF08
no ip redirects
ip address 10.60.3.33/28
!
!
ip access-list PERMITTED_TO_PROXY_ENV1
100 permit tcp 172.21.107.0/27 any eq www
110 permit tcp 172.21.107.0/27 any eq 443
120 permit tcp 172.21.7.224/27 any eq www
130 permit tcp 172.21.7.224/27 any eq 443
140 permit tcp 10.60.32.160/27 any eq www
150 permit tcp 10.60.32.160/27 any eq 443
!
!
route-map TRAFFIC_TO_PROXY_ENV1 pbr-statistics
route-map TRAFFIC_TO_PROXY_ENV1 permit 10
  match ip address PERMITTED_TO_PROXY_ENV1
  set ip next-hop 10.60.3.34 10.60.3.35
!
!
interface Ethernet1/1
  description DownLink to TOR-1:TeGig1/0/47
  mtu 9100
  logging event port link-status
  vrf member VRF08
  ip address 192.168.32.193/30
  ip policy route-map TRAFFIC_TO_PROXY_ENV1
  no shutdown
!
interface Ethernet2/1
  description DownLink to TOR-2:TeGig1/0/48
  mtu 9100
  logging event port link-status
  vrf member VRF08
  ip address 192.168.32.205/30
  ip policy route-map TRAFFIC_TO_PROXY_ENV1
  no shutdown
!

<Interface configuration for inside/outside connections to proxy devices. In this example there are 2 firewalls>

!
interface Ethernet1/41
  description management interface for Firewall-1
  switchport
  switchport access vlan 801
  no shutdown
!
interface Ethernet1/42
  description Proxy interface for Firewall-1
  switchport
  switchport access vlan 802
  no shutdown
!
interface Ethernet2/41
  description management interface for Firewall-2
  switchport
  switchport access vlan 801
  no shutdown
!
interface Ethernet2/42
  description Proxy interface for Firewall-2
  switchport
  switchport access vlan 802
  no shutdown
!

<BGP network statements for VLAN 801-802 subnets and neighbor statements for R023171A-TOR-1/R023171A-TOR-2> 

!
router bgp 65000
!
vrf VRF08
address-family ipv4 unicast
network 10.60.3.0/29
network 10.60.3.32/28
!
neighbor 192.168.32.194
  remote-as 65001
  description LinkTo 65001:R023171A-TOR-1:TeGig1/0/47
  address-family ipv4 unicast
    maximum-prefix 12000 warning-only
neighbor 192.168.32.206
  remote-as 65001
  description LinkTo 65001:R023171A-TOR-2:TeGig1/0/48
  address-family ipv4 unicast
    maximum-prefix 12000 warning-only
!
!
```

Cree la nueva ACL que se usará para identificar el tráfico que obtendrá el tratamiento de PBR. Ese tráfico es el tráfico web (puerto HTTP 80 y puerto HTTPS 443) de los hosts y subredes del bastidor de prueba que obtiene el servicio de proxy, como se detalla en este ejemplo. Por ejemplo, el nombre de la ACL es **PERMITTED_TO_PROXY_ENV1**.

```
ip access-list PERMITTED_TO_PROXY_ENV1
100 permit tcp 172.21.107.0/27 any eq www <<HTTP traffic from CL04 Public Admin VIPs leaving test rack>>
110 permit tcp 172.21.107.0/27 any eq 443 <<HTTPS traffic from CL04 Public Admin VIPs leaving test rack>>
120 permit tcp 172.21.7.224/27 any eq www <<HTTP traffic from CL04 INF-pub-adm leaving test rack>>
130 permit tcp 172.21.7.224/27 any eq 443 <<HTTPS traffic from CL04 INF-pub-adm leaving test rack>>
140 permit tcp 10.60.32.160/27 any eq www <<HTTP traffic from DVM and HLH leaving test rack>>
150 permit tcp 10.60.32.160/27 any eq 443 <<HTTPS traffic from DVM and HLH leaving test rack>>
```

El núcleo de la funcionalidad de PBR se implementa mediante el mapa de ruta **TRAFFIC_TO_PROXY_ENV1**. Se ha agregado la opción **pbr-statistics** para habilitar la visualización de las estadísticas de coincidencia de directivas con el fin de comprobar el número de paquetes que tienen un reenvío de PBR y los que no. La secuencia 10 del mapa de ruta permite el tratamiento de PBR para el tráfico que cumple los criterios **PERMITTED_TO_PROXY_ENV1** de la ACL. Ese tráfico se reenvía a las direcciones IP del próximo salto de `10.60.3.34` y `10.60.3.35`, que son las direcciones VIP de los dispositivos proxy principal y secundario en nuestra configuración de ejemplo.

```
!
route-map TRAFFIC_TO_PROXY_ENV1 pbr-statistics
route-map TRAFFIC_TO_PROXY_ENV1 permit 10
  match ip address PERMITTED_TO_PROXY_ENV1
  set ip next-hop 10.60.3.34 10.60.3.35
```

Las ACL se usan como criterios de coincidencia para el mapa de ruta **TRAFFIC_TO_PROXY_ENV1**. Cuando el tráfico coincide con la ACL **PERMITTED_TO_PROXY_ENV1**, PBR invalida la tabla de enrutamiento normal y, en su lugar, reenvía el tráfico a los próximos saltos de dirección IP enumerados.
 
La directiva de PBR **TRAFFIC_TO_PROXY_ENV1** se aplica al tráfico que entra al dispositivo de borde desde hosts de CL04 y direcciones VIP públicas, y desde HLH y DVM en el bastidor de prueba.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre la integración del firewall, consulte [Integración del firewall de Azure Stack Hub](azure-stack-firewall.md).
