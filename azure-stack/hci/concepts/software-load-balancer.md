---
title: Equilibrador de carga de software para redes definidas por software (SDN) en Azure Stack HCI
description: Use este tema para obtener información sobre el equilibrador de carga de software para redes definidas por software (SDN) en Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/8/2020
ms.openlocfilehash: 79588b25f76540aed1499123f7d39e4d57cf9ecd
ms.sourcegitcommit: 1621f2748b2059fd47ccacd48595a597c44ee63f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/08/2020
ms.locfileid: "91858988"
---
# <a name="what-is-software-load-balancer-slb-for-sdn"></a>¿Qué es el equilibrador de carga de software \(SLB\) para SDN?

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

Los proveedores de servicios en la nube y las empresas que implementan [redes definidas por software (SDN) en Azure Stack HCI](software-defined-networking.md) pueden usar el equilibrador de carga de software para distribuir uniformemente el tráfico de red del inquilino y del cliente del inquilino entre los recursos de la red virtual. SLB permite que varios servidores hospeden la misma carga de trabajo, lo que proporciona alta disponibilidad y escalabilidad.

El equilibrador de carga de software incluye las siguientes funcionalidades:

- Servicios de equilibrio de carga de nivel 4 para el tráfico TCP/UDP vertical de arriba abajo y horizontal de derecha a izquierda.

- Equilibrio de carga del tráfico de redes públicas y redes internas.

- Compatibilidad con direcciones IP dinámicas en redes de área local virtuales y en las redes virtuales que se crean mediante la virtualización de red de Hyper-V.

- Compatibilidad con el sondeo de estado.

- Listo para el escalado en la nube, incluida la funcionalidad de escalabilidad horizontal y escalado vertical para multiplexores y agentes de host.

- Un borde unificado multiinquilino mediante una integración perfecta con tecnologías de SDN, como la puerta de enlace RAS, el firewall del centro de datos y el reflector de rutas.

Para más información, consulte [Características del equilibrador de carga de software](#bkmk_features) en este tema.

> [!NOTE]
> Controladora de red no admite el multiinquilinato para las redes VLAN. Sin embargo, puede usar redes VLAN con equilibrador de carga de software para cargas de trabajo administradas por el proveedor de servicios como, por ejemplo, la infraestructura del centro de datos y los servidores web de alta densidad.

Mediante el equilibrador de carga de software, puede escalar horizontalmente las funcionalidades de equilibrio de carga mediante máquinas virtuales que actúan como equilibradores de carga de software en los mismos servidores de proceso de Hyper-V que usa para las otras cargas de trabajo de las máquinas virtuales. Por este motivo, el equilibrio de carga de software admite la creación y eliminación rápida de los puntos de conexión de equilibrio de carga necesarios para las operaciones de CSP. Además, el equilibrio de carga de software admite decenas de gigabytes por clúster, proporciona un modelo de aprovisionamiento simple y es fácil de escalar y reducir horizontalmente.

## <a name="how-software-load-balancer-works"></a>Funcionamiento del equilibrador de carga de software

El equilibrador de carga de software funciona mediante la asignación de direcciones IP virtuales a las DIP que forman parte de un conjunto de recursos del servicio en la nube del centro de datos.

Las direcciones IP virtuales son direcciones IP únicas que proporcionan acceso público a un grupo de máquinas virtuales con equilibrio de carga. Por ejemplo, las direcciones IP virtuales son direcciones IP que se exponen en Internet para que los inquilinos y los clientes de inquilinos puedan conectarse a los recursos de inquilino del centro de datos en la nube.

Las DIP son las direcciones IP de las máquinas virtuales que forman parte de un grupo con equilibrio de carga situado detrás de la dirección IP virtual. Las DIP se asignan dentro de la infraestructura en la nube a los recursos del inquilino.

Las direcciones IP virtuales se encuentran en el multiplexor que actúa como equilibrador de carga de software (MUX).  El MUX consta de una o varias máquinas virtuales.  Controladora de red proporciona cada MUX con cada dirección IP virtual y, a su vez, cada MUX usa Protocolo de puerta de enlace de borde (BGP) para anunciar cada dirección IP virtual a los enrutadores de la red física como una ruta /32.  BGP permite que los enrutadores de la red física:

- Sepan que hay una dirección IP virtual disponible en cada uno de los MUX, incluso si estos se encuentran en distintas subredes de una red de nivel 3.

- Distribuyan la carga de cada dirección IP virtual en todos los MUX disponibles mediante el enrutamiento multidireccional de igual costo (ECMP).

- Detecten automáticamente un error o eliminación del MUX y dejen de enviar tráfico al MUX con errores.

- Distribuyan la carga del MUX con errores o eliminado entre los MUX que funcionen correctamente.

Cuando el tráfico público llega desde Internet, el MUX que actúa como equilibrador de carga de software examina el tráfico, que contiene la dirección IP virtual como destino, y asigna y reescribe el tráfico para que llegue a una DIP individual. En el tráfico de red de entrada, esta transacción se realiza mediante un proceso de dos pasos que se divide entre las máquinas virtuales del MUX y el host de Hyper-V donde se encuentra la DIP de destino:

1. Equilibrio de carga: el MUX usa la dirección IP virtual para seleccionar una DIP, encapsula el paquete y reenvía el tráfico al host de Hyper-V donde se encuentra la DIP.

2. Traducción de direcciones de red: el host de Hyper-V quita la encapsulación del paquete, traduce la dirección IP virtual en una DIP, vuelve a asignar los puertos y reenvía el paquete a la máquina virtual de la DIP.

El MUX sabe cómo asignar las direcciones IP virtuales a las DIP correctas gracias a las directivas de equilibrio de carga que se definen mediante Controladora de red. Estas reglas incluyen el protocolo, el puerto de front-end, el puerto de back-end y el algoritmo de distribución (5, 3 o 2 tuplas).

Cuando las máquinas virtuales del inquilino responden y envían el tráfico de red saliente de nuevo a Internet o a las ubicaciones de los inquilinos remotos, y dado que el host de Hyper-V realiza la traducción de direcciones de red, el tráfico ignora el MUX y va directamente al enrutador perimetral desde el host de Hyper-V. A este proceso en el que se ignora el MUX se le denomina Direct Server Return (DSR).

Una vez establecido el flujo de tráfico de red inicial, el tráfico de red entrante ignorará completamente el MUX del equilibrador de carga de software.

En la ilustración siguiente, un equipo cliente realiza una consulta DNS para la dirección IP de un sitio de SharePoint de una empresa, en este caso, una empresa ficticia denominada Contoso. Se produce el siguiente proceso:

1. El servidor DNS devuelve la dirección IP virtual 107.105.47.60 al cliente.

2. El cliente envía una solicitud HTTP a la dirección IP virtual.

3. La red física tiene varias rutas de acceso disponibles para llegar a la dirección IP virtual ubicada en cualquier MUX.  Cada enrutador a lo largo del proceso usa ECMP para elegir el siguiente segmento de la ruta hasta que la solicitud llega a un MUX.

4. El MUX que recibe la solicitud comprueba las directivas configuradas y observa que hay dos DIP disponibles, 10.10.10.5 y 10.10.20.5, en una red virtual para enviar la solicitud a la dirección IP virtual 107.105.47.60

5. El MUX selecciona la DIP 10.10.10.5 y encapsula los paquetes con VXLAN para poder enviarlos al host que contiene la DIP mediante la dirección de red física del host.

6. El host recibe el paquete encapsulado y lo inspecciona. Quita la encapsulación y vuelve a escribir el paquete para que el destino sea ahora la DIP 10.10.10.5 en lugar de la dirección IP virtual y, a continuación, envía el tráfico a la máquina virtual de la DIP.

7. La solicitud llega al sitio de SharePoint de Contoso en la granja de servidores 2. El servidor genera una respuesta y la envía al cliente mediante su propia dirección IP como origen.

8. El host intercepta el paquete saliente en el conmutador virtual que recuerda que el cliente, ahora el destino, realizó la solicitud original a la dirección IP virtual.  El host reescribe el origen del paquete para que sea la dirección IP virtual, de modo que el cliente no vea la dirección DIP.

9. El host reenvía el paquete directamente a la puerta de enlace predeterminada de la red física la cual usa su tabla de enrutamiento estándar para reenviar el paquete al cliente, que finalmente recibe la respuesta.

:::image type="content" source="media/software-load-balancing/slb-process.jpg" alt-text="Proceso de equilibrio de carga de software" border="false":::

### <a name="load-balancing-internal-datacenter-traffic"></a>Equilibrio de carga del tráfico interno del centro de datos

Cuando se equilibra la carga del tráfico de red interno al centro de datos como, por ejemplo, entre recursos de inquilino que se ejecutan en servidores diferentes y que pertenecen a la misma red virtual, el conmutador virtual de Hyper-V al que se conectan las máquinas virtuales realiza la traducción de direcciones de red.

Con el equilibrio de carga del tráfico interno, el MUX envía y procesa la primera solicitud, y selecciona la dirección DIP adecuada y, a continuación, enruta el tráfico hacia esta. A partir de ese momento, el flujo de tráfico establecido ignora al MUX y va directamente desde una máquina virtual a otra.

### <a name="health-probes"></a>Sondeos de estado

El equilibrador de carga de software incluye sondeos de estado para validar el estado de la infraestructura de red entre los cuales se incluyen los siguientes:

- Sondeo TCP a puerto

- Sondeo HTTP a puerto y dirección URL

A diferencia de un dispositivo de equilibrio de carga tradicional en el que el sondeo se origina en el dispositivo y se desplaza por la conexión a la dirección DIP, el sondeo del equilibrador de carga de software se origina en el host donde se encuentra la dirección DIP y va directamente desde el agente de host del equilibrador de carga de software a la dirección DIP, con lo que se distribuye aún más el trabajo entre los hosts.

## <a name="software-load-balancer-infrastructure"></a><a name="bkmk_infrastructure"></a>Infraestructura del equilibrador de carga de software
Antes de poder configurar el equilibrador de carga de software, primero debe implementar Controladora de red y una o varias máquinas virtuales del multiplexor que actúe como equilibrador de carga de software.

Además, debe configurar los hosts de Azure Stack HCI con el conmutador virtual de Hyper-V habilitado para redes definidas por software y asegurarse de que se está ejecutando el agente de host del equilibrador de carga de software. Los enrutadores que atienden a los hosts deben admitir el enrutamiento ECMP y el Protocolo de puerta de enlace de borde, y se deben configurar para que acepten las solicitudes de emparejamiento BGP de los MUX del equilibrador de carga de software.

En la ilustración siguiente se proporciona información general de la infraestructura del equilibrador de carga de software.

:::image type="content" source="media/software-load-balancing/slb-overview.png" alt-text="Proceso de equilibrio de carga de software" border="false":::

Las secciones siguientes proporcionan más información sobre estos elementos de la infraestructura del equilibrador de carga.

### <a name="network-controller"></a>Controladora de red
Controladora de red hospeda el administrador del equilibrador de carga de software y realiza las siguientes acciones para lograr ese equilibrio:

- Procesa los comandos del equilibrador de carga de software que se incorporan mediante Northbound API desde Windows Admin Center, System Center, Windows PowerShell o cualquier otra aplicación de administración de redes.

- Calcula la directiva para la distribución a los hosts de Azure Stack HCI y los MUX que actúan como equilibrador de carga de software.

- Proporciona el estado de mantenimiento de la infraestructura del equilibrador de carga de software.

Puede usar Windows Admin Center o Windows PowerShell para instalar y configurar Controladora de red y el resto de la infraestructura del equilibrador de carga de software.

### <a name="slb-mux"></a>Multiplexor (MUX) que actúa como equilibrador de carga de software
Este MUX procesa el tráfico de red entrante y asigna las direcciones IP virtuales a las direcciones DIP y, a continuación, reenvía el tráfico a la dirección DIP correcta. Cada MUX usa también BGP para publicar rutas de VIP a los enrutadores perimetrales. La conexión persistente de BGP notifica a los MUX cuando se produce un error en uno de ellos, lo que permite al resto de MUX activos redistribuir la carga. Esto proporciona principalmente equilibrio de carga para los equilibradores de carga.

### <a name="slb-host-agent"></a>Agente de host del equilibrador de carga de software
Cuando implemente el equilibrador de carga de software, debe usar Windows Admin Center, System Center, Windows PowerShell o cualquier otra aplicación de administración para implementar el agente de host del equilibrador de carga de software en cada servidor host.

El agente de host del equilibrador de carga de software escucha las actualizaciones de directivas del equilibrador que proceden de Controladora de red. Además, programa las reglas para el equilibrador de carga de software en los conmutadores virtuales de Hyper-V habilitados para SDN que están configurados en el equipo local.

### <a name="sdn-enabled-hyper-v-virtual-switch"></a>Conmutador virtual de Hyper-V habilitado para SDN
Para que un conmutador virtual sea compatible con el equilibrador de carga de software, la extensión de la plataforma de filtrado virtual (VFP) debe estar habilitada en el conmutador virtual. Esto se realiza automáticamente mediante los scripts de PowerShell de implementación de SDN, el asistente de implementación de Windows Admin Center y la implementación de System Center Virtual Machine Manager (SCVMM).

Para más información sobre cómo habilitar VFP en conmutadores virtuales, consulte los comandos de Windows PowerShell [Get-VMSystemSwitchExtension](/powershell/module/hyper-v/get-vmsystemswitchextension?view=win10-ps) y [Enable-VMSwitchExtension](/powershell/module/hyper-v/enable-vmswitchextension?f=255&MSPPError=-2147217396&view=win10-ps).

El conmutador virtual de Hyper-V habilitado para SDN realiza las siguientes acciones para el equilibrador de carga de software:

- Procesa la ruta de acceso de datos para el equilibrador de carga de software.

- Recibe el tráfico de red entrante del MUX.

- Ignora al MUX para el tráfico de red saliente y lo envía al enrutador mediante Direct Server Return.

### <a name="bgp-router"></a>Enrutador BGP
El enrutador BGP realiza las siguientes acciones para el equilibrador de carga de software:

- Enruta el tráfico entrante al MUX mediante ECMP.

- En el caso del tráfico de red saliente, usa la ruta que el host proporciona.

- Escucha las actualizaciones de ruta para las direcciones VIP del MUX que actúa como equilibrador de carga de software.

- Elimina estos MUX de la rotación de equilibradores de carga de software si se produce un error en la conexión persistente.

## <a name="software-load-balancer-features"></a><a name="bkmk_features"></a>Características del equilibrador de carga de software
En las secciones siguientes se describen algunas de las características y funcionalidades del equilibrador de carga de software.

### <a name="core-functionality"></a>Funcionalidad básica

- Los equilibradores de carga de software proporcionan servicios de equilibrio de carga de nivel 4 para el tráfico TCP/UDP vertical de arriba abajo y horizontal de derecha a izquierda.

- Puede usar el equilibrador de carga de software en una red basada en virtualización de red de Hyper-V.

- Puede usar el equilibrador de carga de software con una red basada en VLAN para las máquinas virtuales DIP conectadas a un conmutador virtual de Hyper-V habilitado para SDN.

- Una instancia del equilibrador de carga de software puede administrar varios inquilinos.

- El equilibrador de carga de software y las direcciones DIP admiten una ruta de acceso de devolución de baja latencia y escalable, tal y como la implementa Direct Server Return.

- El equilibrador de carga de software funciona también cuando se usa Switch Embedded Teaming (SET) o la virtualización de entrada/salida de raíz única (SR-IOV).

- El equilibrador de carga de software incluye compatibilidad con el protocolo de Internet versión 6 (IPv6) y la versión 4 (IPv4).

- En escenarios de puerta de enlace de sitio a sitio, el equilibrador de carga de software proporciona funcionalidad de traducción de direcciones de red para permitir que todas las conexiones de sitio a sitio utilicen una única dirección IP pública.

### <a name="scale-and-performance"></a>Escala y rendimiento

- Listo para el escalado en la nube, incluida la funcionalidad de escalado horizontal y escalado vertical para multiplexores y agentes de host.

- Un módulo de Controladora de red del administrador del equilibrador de carga de software activo puede admitir ocho instancias de MUX.

### <a name="high-availability"></a>Alta disponibilidad

- Puede implementar el equilibrador de carga de software en más de dos nodos en una configuración activa/activa.

- Los MUX se pueden agregar y quitar del grupo de MUX sin que ello afecte al servicio del equilibrador de carga de software. Esto mantiene la disponibilidad del equilibrador de carga de software cuando se están revisando MUX individuales.

- Las instancias individuales de MUX tienen un tiempo de actividad del 99 por ciento.

- Los datos de seguimiento de estado están disponibles para las entidades de administración.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Introducción a la controladora de red](network-controller-overview.md)
- [Implementación de controladora de red con Windows PowerShell](../deploy/network-controller-powershell.md)
- [SDN en Azure Stack HCI](software-defined-networking.md)
