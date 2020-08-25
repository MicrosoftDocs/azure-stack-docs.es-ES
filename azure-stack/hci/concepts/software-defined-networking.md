---
title: Redes definidas por software (SDN) en Azure Stack HCI
description: Una introducción a los temas sobre redes definidas por software (SDN) que se aplican a las características de Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 08/18/2020
ms.openlocfilehash: 5a2820de2c681ad5a49d0e4ee3e4973736e1169b
ms.sourcegitcommit: fb89c3d837e2673b28784fd997099c825deb32f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/19/2020
ms.locfileid: "88591799"
---
# <a name="sdn-in-azure-stack-hci"></a>SDN en Azure Stack HCI

> Se aplica a Azure Stack HCI, versión 20H2; Windows Server 2019

SDN proporciona una manera de configurar y administrar de forma centralizada redes y servicios de red como conmutación, enrutamiento y equilibrio de carga en el centro de datos. Puede usar SDN para crear, proteger y conectar dinámicamente su red para satisfacer las necesidades en constante evolución de las aplicaciones. El funcionamiento de las redes de centros de datos a escala global para servicios como Microsoft Azure, que realiza de forma eficaz decenas de miles de cambios de red al día, solo es posible gracias a SDN.

Los elementos de red virtual, como el [conmutador virtual de Hyper-V](/windows-server/virtualization/hyper-v-virtual-switch/hyper-v-virtual-switch), la [virtualización de red de Hyper-V](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization), el [equilibrio de carga de software](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn) y la [puerta de enlace de RAS](/windows-server/networking/sdn/technologies/network-function-virtualization/ras-gateway-for-sdn) están diseñados para ser elementos integrales de la infraestructura de SDN. También puede usar los dispositivos compatibles con SDN existentes para lograr una integración más profunda entre las cargas de trabajo que se ejecutan en las redes virtuales y la red física.

Hay tres componentes principales de SDN en Azure Stack HCI y puede elegir cuál desea implementar: Controladora de red, Equilibrador de carga de software y Puerta de enlace.

## <a name="network-controller"></a>Controladora de red

[Controladora de red](/windows-server/networking/sdn/technologies/Software-Defined-Networking-Technologies#network-controller) proporciona un punto de automatización programable y centralizado para administrar, configurar, supervisar y solucionar problemas de la infraestructura de red virtual en su centro de datos. Se trata de un rol de servidor muy escalable que usa Service Fabric para proporcionar alta disponibilidad. Controladora de red debe implementarse en sus propias máquinas virtuales dedicadas.

La implementación de Controladora de red permite las siguientes funcionalidades:

- Creación y administración de redes virtuales y subredes. Conecte las máquinas virtuales a subredes virtuales.
- Configuración y administración de la microsegmentación para las máquinas virtuales conectadas a redes virtuales o redes basadas en VLAN tradicionales.
- Conexión de aplicaciones virtuales a las redes virtuales.
- Configuración de directivas de Calidad de servicio (QoS) para las máquinas virtuales conectadas a redes virtuales o redes basadas en VLAN tradicionales.

Se recomienda [implementar Controladora de red mediante PowerShell](../deploy/network-controller-powershell.md) después de crear un clúster de Azure Stack HCI.

## <a name="software-load-balancing"></a>Equilibrio de carga de software

El [Equilibrio de carga de software](/windows-server/networking/sdn/technologies/network-function-virtualization/software-load-balancing-for-sdn) (SLB) se puede usar para distribuir uniformemente el tráfico de red del cliente entre varias máquinas virtuales. Permite que varios servidores hospeden la misma carga de trabajo, lo que proporciona una alta disponibilidad y escalabilidad. SLB usa [Protocolo de puerta de enlace de borde](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) para anunciar las direcciones IP virtuales a la red física.

## <a name="gateway"></a>Puerta de enlace

Las puertas de enlace se usan para enrutar el tráfico de red entre una red virtual y otra red, ya sea local o remota. Las puertas de enlace se pueden usar para:

- Crear conexiones IPsec seguras de sitio a sitio entre redes virtuales de SDN y redes externas de clientes a través de Internet.
- Crear conexiones de encapsulación de enrutamiento genérico (GRE) entre redes virtuales de SDN y redes externas. La diferencia entre las conexiones de sitio a sitio y las conexiones de GRE es que estas últimas no son conexiones cifradas. Para más información acerca de los escenarios de conectividad de GRE, consulte [Tunelización de GRE en Windows Server](/windows-server/remote/remote-access/ras-gateway/gre-tunneling-windows-server).
- Crear conexiones de nivel 3 entre redes virtuales de SDN y redes externas. En este caso, la puerta de enlace de SDN simplemente actúa como enrutador entre la red virtual y la red externa.

Las puertas de enlace usan [Protocolo de puerta de enlace de borde](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp) para anunciar los puntos de conexión de GRE y establecer conexiones punto a punto. La implementación de SDN crea un grupo de puertas de enlace predeterminado que admite todos los tipos de conexión. Dentro de este grupo, puede especificar cuántas puertas de enlace están reservadas en modo de espera en caso de que se produzca un error en una puerta de enlace activa.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Introducción a SDN en Windows Server](/windows-server/networking/sdn/software-defined-networking)
- [Implementación de una infraestructura de red definida por software con scripts](/windows-server/networking/sdn/deploy/deploy-a-software-defined-network-infrastructure-using-scripts)
