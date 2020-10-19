---
title: Introducción sobre la puerta de enlace de RAS en Azure Stack HCI
description: Use este tema para obtener información sobre la puerta de enlace de RAS para redes definidas por software (SDN) en Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: a7f05f7e07960e83681a13c92f4653b0993668de
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858993"
---
# <a name="what-is-ras-gateway-for-software-defined-networking"></a>¿Qué es la puerta de enlace de RAS para las redes definidas por software?

> Se aplica a Azure Stack HCI, versión 20H2; Windows Server 2019

La puerta de enlace de Servicio de acceso remoto (RAS) es un enrutador compatible con el Protocolo de puerta de enlace de borde (BGP), basado en software y diseñado para los proveedores de servicios en la nube (CSP) y las empresas que hospedan redes virtuales multiinquilino mediante virtualización de red de Hyper-V (HNV). Puede usar la puerta de enlace de RAS para enrutar el tráfico de red entre una red virtual y otra red, ya sea local o remota.

La puerta de enlace de RAS requiere [Controladora de red](network-controller-overview.md), ya que este rol realiza la implementación de grupos de puertas de enlace, configura las conexiones de inquilino en cada puerta de enlace y cambia los flujos de tráfico de red a una puerta de enlace en espera en caso de error de una puerta de enlace.

  > [!NOTE]
  > La arquitectura multiempresa es la capacidad que tiene una infraestructura en la nube de admitir las cargas de trabajo de las máquinas virtuales de varios inquilinos aislándolas unas de otras y permitiendo que todas las cargas de trabajo se ejecuten en la misma infraestructura. Las distintas cargas de trabajo de un inquilino individual pueden interconectarse y administrarse de manera remota, pero estos sistemas no se interconectan con las cargas de trabajo de los demás inquilinos, ni tampoco pueden los demás inquilinos administrarlas de manera remota.

## <a name="ras-gateway-features"></a>Características de la puerta de enlace de RAS

La puerta de enlace de RAS ofrece una serie de características para redes privadas virtuales (VPN), tunelización, reenvío y enrutamiento dinámico.

### <a name="site-to-site-ipsec-vpn"></a>VPN de sitio a sitio (IPsec)

Esta característica de puerta de enlace de RAS le permite conectar dos redes en diferentes ubicaciones físicas a través de Internet mediante una conexión de red privada virtual de sitio a sitio (S2S). Se trata de una conexión cifrada que usa el protocolo VPN IKEv2.

Para los proveedores de servicios en la nube que hospedan muchos inquilinos en su centro de datos, la puerta de enlace de RAS proporciona una solución de puerta de enlace multiinquilino que permite a los inquilinos acceder a sus recursos y administrarlos mediante conexiones VPN de sitio a sitio desde sitios remotos. La puerta de enlace de RAS permite el flujo de tráfico de red entre los recursos virtuales del centro de datos y la red física.

### <a name="site-to-site-gre-tunnels"></a>Túneles de sitio a sitio basados en la encapsulación de enrutamiento genérico (GRE)

Los túneles basados en la encapsulación de enrutamiento genérico (GRE) permiten la conectividad entre redes virtuales de inquilino y redes externas. Dado que el protocolo GRE es ligero y la compatibilidad con GRE está disponible para la mayoría de los dispositivos de red, es una opción ideal para la tunelización en aquellos casos donde no se requiere el cifrado de datos.

La compatibilidad con GRE en túneles de sitio a sitio resuelve el problema del reenvío entre redes virtuales de inquilinos y redes externas de inquilinos mediante una puerta de enlace multiinquilino.

### <a name="layer-3-forwarding"></a>Reenvío de capa 3

El reenvío de capa 3 (L3) permite la conectividad entre la infraestructura física del centro de datos y la infraestructura virtualizada de la nube de virtualización de red de Hyper-V. Mediante una conexión de reenvío de nivel 3, las máquinas virtuales de red de inquilinos pueden conectarse a una red física a través de la puerta de enlace de SDN, la cual ya está configurada en un entorno de redes definidas por software. En este caso, la puerta de enlace de SDN actúa como un enrutador entre la red virtualizada y la red física.

### <a name="dynamic-routing-with-bgp"></a>Enrutamiento dinámico con BGP

BGP reduce la necesidad de configuración de enrutamiento manual en los enrutadores, ya que es un protocolo de enrutamiento dinámico y aprende automáticamente las rutas entre sitios que están conectados mediante conexiones VPN de sitio a sitio. Si su organización tiene varios sitios que están conectados mediante enrutadores habilitados para BGP, como la puerta de enlace de RAS, el BGP permitirá a los enrutadores calcular y usar rutas válidas entre sí automáticamente en caso de que se produzcan interrupciones o errores en la red.

El reflector de rutas BGP incluido con la puerta de enlace de RAS proporciona una alternativa a la topología de malla completa BGP necesaria para la sincronización de rutas entre los enrutadores. Para más información, consulte [¿Qué es el reflector de rutas?](route-reflector-overview.md)

## <a name="how-ras-gateway-works"></a>Funcionamiento de la puerta de enlace de RAS

La puerta de enlace de RAS enruta el tráfico de red entre la red física y los recursos de la red de VM, independientemente de la ubicación. Puede enrutar el tráfico de red en la misma ubicación física o en muchas ubicaciones diferentes.

Puede implementar la puerta de enlace de RAS en grupos de alta disponibilidad que usan varias características a la vez. Los grupos de puertas de enlace contienen varias instancias de puerta de enlace de RAS para la alta disponibilidad y la conmutación por error.

Puede escalar o reducir verticalmente un grupo de puertas de enlace fácilmente agregando o quitando máquinas virtuales de puerta de enlace del grupo. La eliminación o adición de puertas de enlace no interrumpe los servicios que proporciona un grupo. También puede agregar y quitar grupos de puertas de enlace completos. Para más información, consulte [Alta disponibilidad de la puerta de enlace de RAS](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-high-availability).

Cada grupo de puertas de enlace proporciona redundancia M + N. Esto significa que se realiza una copia de seguridad de "M" máquinas virtuales de puertas de enlace activas mediante "N" máquinas virtuales de puerta de enlace en espera. La redundancia M + N proporciona más flexibilidad a la hora de determinar el nivel de confiabilidad que necesita al implementar la puerta de enlace de RAS.

Puede asignar una sola dirección IP pública a todos los grupos o a un subconjunto de grupos. Al hacerlo, se reduce en gran medida el número de direcciones IP públicas que se deben usar, ya que es posible que todos los inquilinos se conecten a la nube en una única dirección IP.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Arquitectura de implementación de la puerta de enlace de RAS](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-deployment-architecture)
- [Introducción a la controladora de red](network-controller-overview.md)
- [Plan de implementación de Controladora de red](network-controller.md)
- [SDN en Azure Stack HCI](software-defined-networking.md)