---
title: Introducción a Controladora de red en Azure Stack HCI
description: Use este tema para obtener información sobre Controladora de red para redes definidas por software (SDN) en Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: c207b436571d85d0902cd69a2168144e176919b1
ms.sourcegitcommit: 6a0f7f452998c404a80ca9d788dbf3cdf4d78b38
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2020
ms.locfileid: "91893907"
---
# <a name="what-is-network-controller"></a>¿Qué es Controladora de red?

> Se aplica a Azure Stack HCI, versión 20H2; Windows Server 2019

Controladora de red es la piedra angular en la administración de redes definidas por software (SDN). Es un rol del servidor, altamente escalable, que proporciona un punto de automatización programable y centralizado para administrar, configurar, supervisar y solucionar problemas de la infraestructura de red virtual.

Mediante el uso de Controladora de red, puede automatizar la configuración y administración de la infraestructura de red en lugar de realizar la configuración manual de los dispositivos y servicios de red.

## <a name="how-network-controller-works"></a>Funcionamiento de Controladora de red

Controladora de red proporciona una interfaz de programación de aplicaciones (API) que permite a Controladora de red comunicarse con dispositivos, servicios y componentes de red y administrarlos (Southbound API), y una segunda API que permite a las aplicaciones de administración indicar a Controladora de red qué valores de red y servicios son necesarios (Northbound API).

Con Southbound API, Controladora de red puede administrar dispositivos y servicios de red, y recopilar toda la información que necesite sobre la red. Controladora de red supervisa continuamente el estado de los dispositivos y servicios de red, y garantiza la corrección de cualquier desfase de configuración con respecto al estado deseado.

La API Northbound de Controladora de red se implementa como una interfaz REST. Proporciona la posibilidad de administrar la red del centro de datos desde las aplicaciones de administración. Para la administración, los usuarios pueden usar la API REST directamente o usar la instancia de Windows PowerShell basada en ella, o aplicaciones de administración con una interfaz gráfica de usuario como Windows Admin Center o System Center Virtual Machine Manager.

## <a name="network-controller-features"></a>Características de Controladora de red

Controladora de red le permite administrar características de SDN como redes virtuales, firewalls, equilibrador de carga de software y puerta de enlace de RAS. A continuación se muestran algunas de sus numerosas características.

### <a name="virtual-network-management"></a>Administración de red virtual

Esta característica de Controladora de red le permite implementar y configurar la virtualización de red de Hyper-V, configurar adaptadores de red virtuales en máquinas virtuales individuales y almacenar y distribuir directivas de red virtual. Con esta característica, puede crear redes virtuales y subredes, conectar máquinas virtuales a estas redes y habilitar la comunicación entre las máquinas virtuales de la misma red virtual.

Controladora de red admite redes basadas en red de área local virtual (VLAN), virtualización de red para encapsulación de enrutamiento genérico (NVGRE) y red de área local virtual extensible (VXLAN).

## <a name="firewall-management"></a>Administración del firewall

Esta característica de Controladora de red le permite configurar y administrar reglas de control de acceso de permiso o denegación en el firewall para las máquinas virtuales de carga de trabajo, tanto para el tráfico interno (horizontal de derecha a izquierda) como externo (vertical de arriba a abajo) del centro de datos. Las reglas de firewall se asocian en el puerto vSwitch de las máquinas virtuales de carga de trabajo y, por tanto, se distribuyen entre las cargas de trabajo del centro de datos y se mueven junto con las cargas de trabajo.

Puede utilizar Northbound API para definir las reglas de firewall para el tráfico entrante y saliente de las máquinas virtuales de carga de trabajo. También puede configurar cada regla de firewall para que registre el tráfico al que permite o deniega el paso.

## <a name="software-load-balancer-management"></a>Administración del equilibrador de carga de software

El [equilibrador de carga de software](software-load-balancer.md) le permite habilitar varios servidores para que hospeden la misma carga de trabajo, lo que proporciona una alta disponibilidad y escalabilidad. Con el equilibrador de carga de software, puede configurar y administrar el equilibrio de carga, la traducción de direcciones de red de entrada y el acceso de salida a Internet de las cargas de trabajo conectadas a redes VLAN y a redes virtuales tradicionales.

## <a name="gateway-management"></a>Administración de la puerta de enlace

[La puerta de enlace de Servicio de acceso remoto (RAS)](gateway-overview.md) le permite implementar, configurar y administrar máquinas virtuales que pertenecen a un grupo de puertas de enlace, lo que proporciona conectividad de red externa a las cargas de trabajo del cliente. Con las puertas de enlace, se admiten los siguientes tipos de conectividad entre las redes virtuales y remotas:

- Conectividad de puerta de enlace de red privada virtual (VPN) de sitio a sitio mediante IPsec
- Conectividad de puerta de enlace de VPN de sitio a sitio mediante la encapsulación de enrutamiento genérico (GRE)
- Funcionalidad de reenvío de nivel 3
 
Las conexiones de puerta de enlace admiten Protocolo de puerta de enlace de borde (BGP) para la administración de rutas dinámicas.

## <a name="virtual-appliance-chaining"></a>Encadenamiento de aplicaciones virtuales

Esta característica de Controladora de red le permite conectar aplicaciones de red virtual a sus redes virtuales. Estos dispositivos se pueden usar en firewalls avanzados, equilibrio de carga, detección y prevención de intrusiones y muchos otros servicios de red. Puede añadir aplicaciones virtuales que realicen funciones de enrutamiento definido por el usuario y de creación de reflejo del puerto. Con el enrutamiento definido por el usuario, la aplicación virtual se usa como un enrutador entre las subredes virtuales de la red virtual. Con la creación de reflejo del puerto, todo el tráfico de red que entra o sale del puerto supervisado se duplica y se envía a una aplicación virtual para su análisis.

Para más información acerca de las rutas definidas por el usuario, consulte [Uso de aplicaciones virtuales de red en una red virtual](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn).

## <a name="network-controller-deployment-considerations"></a>Consideraciones sobre la implementación de Controladora de red

- No implemente el rol del servidor Controladora de red en hosts físicos. Controladora de red debe implementarse en sus propias máquinas virtuales dedicadas.

- Puede implementar Controladora de red en entornos de dominio y en entornos que no pertenecen a un dominio. En entornos de dominio, Controladora de red autentica los usuarios y dispositivos de red mediante Kerberos. En entornos que no son de dominio, debe implementar certificados para la autenticación.

- Es fundamental que las implementaciones de Controladora de red proporcionen alta disponibilidad y la posibilidad de escalar o reducir verticalmente con facilidad según las necesidades del centro de datos. Use al menos tres máquinas virtuales para proporcionar alta disponibilidad a la aplicación Controladora de red.

- Para lograr una alta disponibilidad y escalabilidad, Controladora de red se basa en Service Fabric. Service Fabric proporciona una plataforma de sistemas distribuidos que se usa para crear aplicaciones escalables, confiables y fáciles de administrar para la nube. [Más información acerca de Controladora de red como una aplicación de Service Fabric](/windows-server/networking/sdn/technologies/network-controller/network-controller-high-availability#network-controller-as-a-service-fabric-application).


## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Plan de implementación de Controladora de red](network-controller.md)
- [Implementación de controladora de red con Windows PowerShell](../deploy/network-controller-powershell.md)
- [SDN en Azure Stack HCI](software-defined-networking.md)