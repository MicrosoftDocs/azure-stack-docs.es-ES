---
title: Diferencias entre los servicios de red de Azure Stack Hub
description: Conozca las diferencias y consideraciones al trabajar con redes en Azure Stack Hub.
author: mattbriggs
ms.date: 5/27/2020
ms.topic: article
ms.author: mabrigg
ms.reviewer: wamota
ms.lastreviewed: 07/10/2019
ms.openlocfilehash: 459d6177015cbca40d2f8f830eba85dc87be18c7
ms.sourcegitcommit: e79aafb05c5fc512a83f0ebc6d76503213ccbc70
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/12/2020
ms.locfileid: "84730686"
---
# <a name="differences-and-considerations-for-azure-stack-hub-networking"></a>Diferencias y consideraciones para las redes de Azure Stack Hub

Las redes de Azure Stack Hub tienen muchas de las características que proporcionan las redes de Azure. Sin embargo, hay algunas diferencias clave que debe conocer antes de implementar una red de Azure Stack Hub.

En este artículo se proporciona información general sobre las consideraciones únicas para las redes de Azure Stack Hub y sus características. Para más información acerca de las diferencias de alto nivel entre Azure Stack Hub y Azure, consulte el artículo [Consideraciones clave](azure-stack-considerations.md).

## <a name="cheat-sheet-networking-differences"></a>Hoja de referencia rápida: diferencias de redes

| Servicio | Característica | Azure (global) | Azure Stack Hub |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------|------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------|
| DNS | DNS multiinquilino | Compatible | Todavía no se admite |
|  | Registros AAAA de DNS | Compatible | No compatible |
|  | Zonas DNS por suscripción | 100 (valor predeterminado)<br>Puede aumentarse a petición. | 100 |
|  | Conjuntos de registros DNS por zona | 5000 (valor predeterminado)<br>Puede aumentarse a petición. | 5000 |
|  | Servidores de nombres para la delegación de zona | Azure proporciona cuatro servidores de nombres para cada zona de usuario (inquilino) que se crea. | Azure Stack Hub proporciona dos servidores de nombres para cada zona de usuario (inquilino) que se crea. |
| Azure Firewall | Servicio de seguridad de red | Azure Firewall es un servicio de seguridad de red administrado y basado en la nube que protege los recursos de Azure Virtual Network. | Todavía no se admite. |
| Virtual Network | Emparejamiento de redes virtuales de Azure | Conecte dos redes virtuales de la misma región mediante la red troncal de Azure. | Todavía no se admite |
|  | Direcciones IPv6 | Puede asignar una dirección IPv6 como parte de la [configuración de la interfaz de red](https://docs.microsoft.com/azure/virtual-network/virtual-network-network-interface-addresses#ip-address-versions). | Se admite solo IPv4. |
|  | Plan de DDoS Protection | Compatible | Todavía no se admite. |
|  | Configuraciones de direcciones IP del conjunto de escalado | Compatible | Todavía no se admite. |
|  | Servicios de acceso privado (subred) | Compatible | Todavía no se admite. |
|  | Puntos de conexión de servicio | Admitido para la conexión interna (sin conexión a Internet) a los servicios de Azure. | Todavía no se admite. |
|  | Directivas de puntos de conexión de servicio | Compatible | Todavía no se admite. |
|  | Túneles de servicio | Compatible | Todavía no se admite.  |
| Grupos de seguridad de red | Reglas de seguridad aumentada | Compatible | Compatible. |
|  | Reglas de seguridad eficaz | Compatible | Todavía no se admite. |
|  | Grupos de seguridad de la aplicación | Compatible | Todavía no se admite. |
|  | Protocolos de reglas | TCP, UDP, ICMP, Cualquiera | Solo TCP, UDP o Cualquiera |
| Puertas de enlace de red virtual | Puerta de enlace de VPN de punto a sitio | Compatible | Todavía no se admite. |
|  | Puerta de enlace de VNET a VNET | Compatible | Todavía no se admite. |
|  | Tipo de puerta de enlace de red virtual | Azure admite VPN<br> ExpressRoute <br> Red de Hyper. | Actualmente, Azure Stack Hub solo admite el tipo VPN. |
|  | SKU de puerta de enlace de VPN | Compatibilidad con SKU básicas, GW1, GW2, GW3, alto rendimiento estándar, ultra alto rendimiento. | Compatibilidad con SKU básicas, estándar y de alto rendimiento. |
|  | Tipo VPN | Azure admite tanto la basada en directivas como la basada en rutas. | Azure Stack Hub solo admite la basada en rutas. |
|  | Configuración de BGP | Azure admite la configuración de dirección de emparejamiento BGP y de peso del mismo nivel. | La dirección de emparejamiento y el peso de par de BGP se configuran automáticamente en Azure Stack Hub. No hay ninguna manera de que el usuario configure estas opciones con sus propios valores. |
|  | Sitio de puerta de enlace predeterminado | Azure admite la configuración de un sitio predeterminado para la tunelización forzada. | Todavía no se admite. |
|  | Cambio del tamaño de la puerta de enlace | Azure permite cambiar el tamaño de la puerta de enlace después de la implementación. | No se admite el cambio de tamaño. |
|  | Configuración de disponibilidad | activa/activa | activa/pasiva |
|  | UsePolicyBasedTrafficSelectors | Azure admite el uso de selectores de tráfico en función de directivas con las conexiones de puerta de enlace basadas en rutas. | Todavía no se admite. |
|  | Supervisión y alertas | Azure utiliza Azure Monitor para proporcionar la capacidad de configurar alertas para los recursos de VPN. | Todavía no se admite.|
| Equilibrador de carga | SKU | Se admiten los equilibradores de carga Estándar y Básico. | Solo se admite el equilibrador de carga Básico.<br>La propiedad SKU no se admite.<br>El equilibrador de carga de SKU básico /path/ no puede tener más de cinco configuraciones de IP de front-end.  |
|  | Zones | Availability Zones se admite. | Todavía no se admite |
|  | Las reglas NAT de entrada admiten los puntos de conexión de servicio | Azure permite especificar puntos de conexión de servicio para las reglas NAT de entrada. | Azure Stack Hub todavía no admite puntos de conexión de servicio, por lo que estos no se pueden especificar. |
|  | Protocolo | Azure permite especificar GRE o ESP. | En Azure Stack Hub no se admite la clase de protocolo. |
| Dirección IP pública | Version de dirección IP pública | Azure admite tanto IPv6 como IPv4. | Se admite solo IPv4. |
| | SKU | Azure admite Básico y Estándar. | Solo se admite Básico. |
| Interfaz de red | Obtención de tabla de rutas efectivas | Compatible | Todavía no se admite. |
|  | Obtención de ACL efectivas | Compatible | Todavía no se admite. |
|  | Habilitar Accelerated Networking | Compatible | Todavía no se admite. |
|  | reenvío de IP | Deshabilitado de forma predeterminada.  Se puede habilitar. | No se puede alternar esta opción.  Está activada de forma predeterminada. |
|  | Grupos de seguridad de la aplicación | Compatible | Todavía no se admite. |
|  | Etiqueta de nombre DNS interno | Compatible | Todavía no se admite. |
|  | Versión de dirección IP privada | Se admiten tanto IPv6 como IPv4. | Se admite solo IPv4. |
|  | Dirección MAC estática | No compatible | No compatible. Cada sistema de Azure Stack Hub usa el mismo grupo de direcciones MAC. |
| Network Watcher | Funciones de supervisión de red de inquilinos de Network Watcher | Compatible | Todavía no se admite. |
| CDN | Perfiles de Content Delivery Network | Compatible | Todavía no se admite. |
| puerta de enlace de aplicaciones | Equilibrio de carga de nivel 7 | Compatible | Todavía no se admite. |
| Traffic Manager | Dirija el tráfico entrante para obtener un rendimiento y una confiabilidad óptimos de las aplicaciones. | Compatible | Todavía no se admite. |
| ExpressRoute | Configure una conexión privada rápida con los servicios en la nube de Microsoft desde su infraestructura local o una instalación de colocalización. | Compatible | Compatibilidad para conectar Azure Stack Hub a un circuito de ExpressRoute. |

## <a name="api-versions"></a>Versiones de API 

Las redes de Azure Stack Hub admiten las siguientes versiones de API: 

- 2018-11-01
- 2018-10-01
- 2018-08-01
- 2018-07-01
- 2018-06-01
- 2018-05-01
- 2018-04-01
- 2018-03-01
- 2018-02-01
- 2018-01-01
- 01-11-2017
- 2017-10-01

## <a name="next-steps"></a>Pasos siguientes

[DNS en Azure Stack Hub](azure-stack-dns.md)
