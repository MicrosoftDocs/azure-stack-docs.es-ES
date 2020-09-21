---
title: Planeamiento de una infraestructura de red definida por software
description: En este tema se proporciona información sobre cómo planear la implementación de una infraestructura de red definida por software.
manager: grcusanz
ms.topic: conceptual
ms.assetid: ea7e53c8-11ec-410b-b287-897c7aaafb13
ms.author: anpaul
author: AnirbanPaul
ms.date: 09/11/2020
ms.openlocfilehash: 7d7eeaec5f82e08cf33a307f429389f03e712987
ms.sourcegitcommit: a845ae0d3794b5d845b2ae712baa7e38f3011a7b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/12/2020
ms.locfileid: "90045548"
---
# <a name="plan-a-software-defined-network-infrastructure"></a>Planeamiento de una infraestructura de red definida por software

>Se aplica a: Azure Stack HCI versión 20H2, Windows Server 2019, Windows Server (Canal semestral), Windows Server 2016

Obtenga información sobre cómo planear la implementación de una infraestructura de red definida por software en la que se incluyan los requisitos previos de hardware y software. En este tema se incluyen los requisitos de planeamiento de la configuración de red física y lógica, el enrutamiento, las puertas de enlace, el hardware de red, etc. También se incluyen consideraciones sobre la extensión de una infraestructura de red definida por software y el uso de una implementación por fases.

## <a name="prerequisites"></a>Requisitos previos
Hay varios requisitos previos de hardware y software para una infraestructura de red definida por software, entre los que se incluyen:
- **Grupos de seguridad y registro DNS dinámico**. Debe preparar el centro de datos para la implementación del rol Controladora de red, para lo cual se necesita un conjunto de máquinas virtuales. Para poder implementar Controladora de red, debe configurar los grupos de seguridad y el registro DNS dinámico.

    Para más información acerca de la implementación de Controladora de red para el centro de datos, consulte [Requisitos de implementación de Controladora de red](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller).

- **Red física**. Necesita acceso a los dispositivos de red física para configurar las redes de área local virtual, el enrutamiento y el Protocolo de puerta de enlace de borde (BGP). En este tema se proporcionan instrucciones para la configuración manual de conmutadores, así como opciones para usar el emparejamiento BGP en conmutadores y enrutadores de nivel 3, o en una máquina virtual de servidor de enrutamiento y acceso remoto (RRAS).

- **Hosts de proceso físico**. Estos hosts ejecutan Hyper-V y son necesarios para hospedar una infraestructura de red definida por software y máquinas virtuales de inquilino. Se necesita un hardware de red específico en estos hosts para obtener el mejor rendimiento, como se indica en la sección [Hardware de red](#network-hardware).

## <a name="physical-and-logical-network-configuration"></a>Configuración de la red física y lógica
Cada host de proceso físico requiere conectividad de red a través de uno o varios adaptadores de red conectados a un puerto del conmutador físico. Una [VLAN](https://en.wikipedia.org/wiki/Virtual_LAN) de nivel 2 admite redes divididas en varios segmentos de red lógica.

>[!TIP]
>Use VLAN 0 para redes lógicas en modo de acceso o sin etiquetar.

>[!IMPORTANT]
>Las redes definidas por software de Windows Server 2016 admiten el direccionamiento IPv4 para las redes subyacentes y superpuestas. No se admite IPv6. Windows Server 2019 admite el direccionamiento IPv4 e IPv6.

### <a name="logical-networks"></a>Redes lógicas
En esta sección se describen los requisitos de planeamiento de la infraestructura de red definida por software para la red lógica de administración y la red lógica del proveedor de virtualización de red de Hyper-V (HNV). Incluye detalles sobre el aprovisionamiento de redes lógicas adicionales para que usen puertas de enlace y el equilibrador de carga de software, así como una topología de red de ejemplo.

#### <a name="management-and-hnv-provider"></a>Proveedor de administración y de virtualización de red de Hyper-V (HNV)
Todos los hosts de proceso físicos deben tener acceso a la red lógica de administración y a la red lógica del proveedor de HNV. Por lo que respecta al planeamiento de direcciones IP, cada host de proceso físico debe tener al menos una dirección IP asignada desde la red lógica de administración. Controladora de red requiere una dirección IP reservada desde esta red para que sirva como dirección IP de Transferencia de estado representacional (REST).

La red del proveedor de HNV sirve como la red física subyacente para el tráfico de inquilino este-oeste (interno-interno), el tráfico de inquilino norte-sur (externo-interno) y para intercambiar información sobre el emparejamiento BGP con la red física.

Un servidor DHCP puede asignar automáticamente direcciones IP para la red de administración, o el usuario puede asignar direcciones IP estáticas de forma manual. La pila de la red definida por software asigna automáticamente direcciones IP para la red lógica del proveedor de HNV para los hosts de Hyper-V individuales de un grupo de direcciones IP. Controladora de red especifica y administra el grupo de direcciones IP.

>[!NOTE]
>Controladora de red asigna una dirección IP del proveedor de HNV a un host de proceso físico solo después de que el agente de host de Controladora de red reciba la directiva de red de una máquina virtual de inquilino específica.

| Si...                                                    | En ese caso...                                               |
| :------------------------------------------------------- | :---------------------------------------------------- |
| Las redes lógicas usan VLAN.                          | El host de proceso físico debe conectarse a un puerto del conmutador troncal que tenga acceso a las VLAN. Es importante tener en cuenta que los adaptadores de red físicos del host del equipo no deben tener activado ningún filtrado de VLAN.|
| Está usando Switched-Embedded Teaming (SET) y tiene varios miembros de un equipo de tarjeta de interfaz de red como, por ejemplo, adaptadores de red.| Debe conectar todos los miembros del equipo de tarjeta de interfaz de red de ese host concreto al mismo dominio de difusión de nivel 2.|
| El host de proceso físico ejecuta máquinas virtuales de infraestructura adicionales como Controladora de red, SLB/Multiplexor (MUX) o puerta de enlace. | Asegúrese de que la red lógica de administración tenga suficientes direcciones IP para cada máquina virtual hospedada. Además, asegúrese de que la red lógica del proveedor de HNV tiene suficientes direcciones IP para asignarlas a cada máquina virtual de infraestructura de SLB/MUX y de puerta de enlace. Aunque Controladora de red administra la reserva de IP, si se produce un error al reservar una nueva dirección IP debido a una falta de disponibilidad, se pueden generar direcciones IP duplicadas en la red.|

Para más información acerca de la virtualización de red de Hyper-V (HNV) que puede usar para virtualizar redes en una implementación de Microsoft SDN, consulte [Virtualización de red de Hyper-V](/windows-server/networking/sdn/technologies/hyper-v-network-virtualization/hyper-v-network-virtualization).

#### <a name="gateways-and-the-software-load-balancer-slb"></a>Puertas de enlace y equilibrador de carga de software (SLB)
Debe crear y aprovisionar redes lógicas adicionales para usar las puertas de enlace y el equilibrador de carga de software. Asegúrese de obtener los prefijos IP, identificadores de VLAN y direcciones IP de puerta de enlace correctos para estas redes.

|                                |                     |
| :----------------------------- | :------------------ |
| **Red lógica de VIP pública** | La red lógica de IP virtual (VIP) pública debe usar prefijos de subred IP que sean enrutables fuera del entorno de nube (normalmente enrutable a Internet). Estas son las direcciones IP de front-end que usan los clientes externos para acceder a los recursos de las redes virtuales, incluida la red lógica de IP virtual de front-end para la puerta de enlace de sitio a sitio. No es necesario que asigne una VLAN a esta red. |
| **Red lógica de VIP privada** | No es necesario que la red lógica de IP virtual privada sea enrutable fuera de la nube. Esto se debe a que solo la usan las redes lógicas de IP virtual a las que se puede acceder desde clientes de la nube internos como, por ejemplo, los servicios privados. No es necesario que asigne una VLAN a esta red. |
| **Red lógica de IP virtual de encapsulación de enrutamiento genérico** | La red VIP de encapsulación de enrutamiento genérico (GRE) es una subred que existe únicamente para definir redes VIP. Las VIP se asignan a las máquinas virtuales de puerta de enlace que se ejecutan en el tejido de SDN para un tipo de conexión GRE de sitio a sitio (S2S). No es necesario que configure previamente esta red en los conmutadores físicos o el enrutador, ni que le asigne una VLAN. |

#### <a name="sample-network-topology"></a>Ejemplo de topología de red
Cambie los prefijos de subred IP de ejemplo y los identificadores de VLAN para su entorno.

| Nombre de red | Subnet | Máscara | Id. de VLAN en tronco | Puerta de enlace | Reserva (ejemplos) |
| :----------------------- | :------------ | :------- | :---------------------------- | :-------------- | :------------------------------------------- |
| Administración              | 10.184.108.0 |    24   |          7                   | 10.184.108.1   | 10.184.108.1: enrutador<br> 10.184.108.4: Controladora de red<br> 10.184.108.10: host de proceso 1<br> 10.184.108.11: host de proceso 2<br> 10.184.108.X: host de proceso X |
| Proveedor de HNV             |  10.10.56.0  |    23    |          11                |  10.10.56.1    | 10.10.56.1: enrutador<br> 10.10.56.2: SLB/MUX1<br> 10.10.56.5: Gateway1 |
| VIP pública               |  41.40.40.0  |    27    |          NA                |  41.40.40.1    | 41.40.40.1: enrutador<br> 41.40.40.3: IP virtual de VPN de sitio a sitio de IPSec |
| VIP privadas              |  20.20.20.0  |    27    |          NA                |  20.20.20.1    | 20.20.20.1: puerta de enlace predeterminada (enrutador) |
| VIP GRE                  |  31.30.30.0  |    24    |          NA                |  31.30.30.1    | 31.30.30.1: puerta de enlace predeterminada |

## <a name="routing-infrastructure"></a>Infraestructura de enrutamiento
La información de enrutamiento, \(como el próximo salto\), de las subredes VIP se anuncia mediante las puertas de enlace de SLB/MUX y del servidor de acceso remoto (RAS) en la red física mediante el emparejamiento BGP interno. Las redes lógicas de IP virtual no tienen una VLAN asignada y no están preconfiguradas en el conmutador de nivel 2 (como, por ejemplo, el conmutador de la parte superior del rack).

Debe crear un par BGP en el enrutador que la infraestructura de SDN use para recibir las rutas de las redes lógicas de IP virtual anunciadas por las puertas de enlace de SLB/MUX y RAS. El emparejamiento BGP solo tiene que producirse en una dirección (desde la puerta de enlace SLB/MUX o RAS hasta el par BGP externo). Por encima del primer nivel de enrutamiento, puede utilizar rutas estáticas u otro protocolo de enrutamiento dinámico como Abrir primero la ruta de acceso más corta (OSPF). Sin embargo, como se indicó anteriormente, el prefijo de subred IP para las redes lógicas de IP virtual debe ser enrutable desde la red física al par BGP externo.

El emparejamiento BGP normalmente se configura en un conmutador o enrutador administrado como parte de la infraestructura de red. El par BGP también puede configurarse en un servidor de Windows Server con el rol RAS instalado en un modo de solo enrutamiento. El enrutador BGP del mismo nivel de la infraestructura de red debe configurarse para usar sus propios números de sistema autónomos (ASN) y permitir el emparejamiento de un ASN que esté asignado a los componentes \(puertas de enlace de SLB/MUX y RAS\) de SDN.

Debe obtener la siguiente información del enrutador físico o del administrador de red que controla ese enrutador:
- ASN del enrutador
- Dirección IP del enrutador

>[!NOTE]
>El SLB o MUX no admite ASN de cuatro bytes. Debe asignar ASN de dos bytes al SLB o MUX, y al enrutador al que se conecta. Puede usar ASN de cuatro bytes en otros lugares de su entorno.

El usuario o el administrador de red deben configurar el enrutador BGP del mismo nivel para que acepte conexiones desde el ASN y la dirección IP, o desde la dirección de subred de la red lógica del proveedor de HNV que esté usando la puerta de enlace de RAS y SLB o MUX.

Para más información, consulte [Protocolo de puerta de enlace de borde (BGP)](/windows-server/remote/remote-access/bgp/border-gateway-protocol-bgp).

## <a name="default-gateways"></a>Puertas de enlace predeterminadas
Las máquinas configuradas para conectarse a varias redes como, por ejemplo, las máquinas virtuales de hosts físicos, SLB/MUX y puertas de enlace solo deben tener una puerta de enlace predeterminada configurada. Use las siguientes puertas de enlace predeterminadas para los hosts y las máquinas virtuales de la infraestructura:
- En el caso de los hosts de Hyper-V, use la red de administración como la puerta de enlace predeterminada.
- En el caso de las máquinas virtuales de Controladora de red, use la red de administración como puerta de enlace predeterminada.
- En el caso de las máquinas virtuales de SLB/MUX, use la red de administración como la puerta de enlace predeterminada.
- En el caso de las máquinas virtuales de puerta de enlace, use la red del proveedor de HNV como puerta de enlace predeterminada. Esto se debe establecer en la NIC de front-end de las máquinas virtuales de puerta de enlace.

## <a name="network-hardware"></a>Hardware de red
En esta sección se proporcionan los requisitos de implementación de hardware de red para las NIC y los conmutadores físicos.

### <a name="network-interface-cards-nics"></a>Tarjetas de interfaz de red (NIC)
Las NIC que se usan en los hosts de Hyper-V y los hosts de almacenamiento requieren funcionalidades específicas para lograr el mejor rendimiento.

El acceso directo a memoria remota (RDMA) es una técnica de omisión de kernel que permite transferir grandes cantidades de datos sin usar la CPU del host, lo cual libera la CPU para realizar otros trabajos. Switch Embedded Teaming (SET) es una solución alternativa de formación de equipos de NIC que puede usar en entornos que incluyen Hyper-V y la pila de SDN. SET integra la funcionalidad de formación de equipos de NIC en el conmutador virtual de Hyper-V.

Para más información, consulte [Acceso directo a memoria remota (RDMA) y Switch Embedded Teaming (SET)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).

Para tener en cuenta la sobrecarga en el tráfico de red virtual del inquilino causada por los encabezados de encapsulación VXLAN o NVGRE, la unidad de transmisión máxima (MTU) del tejido de red del nivel 2 (conmutadores y hosts) debe establecerse en un valor mayor o igual a 1674 bytes \(incluidos los encabezados Ethernet de nivel 2\).

Las NIC que admiten la nueva palabra clave *EncapOverhead* de adaptador avanzado establecen la unidad de transmisión máxima automáticamente a través del agente de host de Controladora de red. Las NIC que no admiten la nueva palabra clave *EncapOverhead* deben establecer el tamaño de la unidad de transmisión máxima de forma manual en cada host físico mediante la palabra clave *JumboPacket* u otra palabra clave \(equivalente\).

### <a name="switches"></a>Conmutadores
Al seleccionar un conmutador físico y un enrutador para su entorno, asegúrese de que admite el siguiente conjunto de funcionalidades:
- La configuración de la unidad de transmisión máxima del switchport es \(obligatoria\)
- Se debe establecer la unidad de transmisión máxima en 1674 bytes o más \(incluido el encabezado L2-Ethernet\)
- Se \(requieren\) protocolos L3
- Enrutamiento multidireccional de igual costo (ECMP)
- ECMP basado en BGP \(IETF RFC 4271\)\-

Las implementaciones deben admitir las instrucciones obligatorias de los siguientes estándares IETF:
- RFC 2545: [extensiones multiprotocolo BGP-4 para el enrutamiento entre dominios IPv6](https://tools.ietf.org/html/rfc2545)
- RFC 4760: [extensiones multiprotocolo para BGP-4](https://tools.ietf.org/html/rfc4760)
- RFC 4893: [compatibilidad con BGP para el espacio de números del sistema autónomo de cuatro octetos](https://tools.ietf.org/html/rfc4893)
- RFC 4456: [Reflejo de rutas BGP: una alternativa al protocolo BGP interno de malla completa (IBGP)](https://tools.ietf.org/html/rfc4456)
- RFC 4724: [mecanismo de reinicio estable para BGP](https://tools.ietf.org/html/rfc4724)

Se requieren los siguientes protocolos de etiquetado:
- VLAN: aislamiento de varios tipos de tráfico
- Tronco 802.1q

Los elementos siguientes proporcionan control de vínculos:
- Calidad de servicio \(QoS\) \(PFC solo es necesario si se usa RoCE\)
- Selección de tráfico mejorada \(802.1Qaz\)
- Control de flujo basado en prioridades (PFC) \(802.1p/Q y 802.1Qbb\)

Los siguientes elementos proporcionan disponibilidad y redundancia:
- Disponibilidad del conmutador (obligatorio)
- Se requiere un enrutador de alta disponibilidad para realizar funciones de puerta de enlace. Puede proporcionarlo mediante el uso de un conmutador o enrutador de varios chasis, o tecnologías como el protocolo de redundancia de enrutador virtual (VRRP).

### <a name="switch-configuration-examples"></a>Ejemplos de configuración de conmutadores
Para ayudar a configurar el conmutador físico o enrutador, hay disponible un conjunto de archivos de configuración de ejemplo para diversos modelos y proveedores de conmutadores en el [repositorio de GitHub de Microsoft SDN](https://github.com/microsoft/SDN/tree/master/SwitchConfigExamples). Se proporciona un archivo Léame detallado y comandos comprobados de la interfaz de la línea de comandos (CLI) para conmutadores específicos.

## <a name="compute"></a>Compute
Todos los hosts de Hyper-V deben tener instalado el sistema operativo adecuado, estar habilitados para Hyper-V y usar un conmutador virtual externo de Hyper-V con al menos un adaptador físico conectado a la red lógica de administración. El host debe ser accesible a través de una dirección IP de administración asignada a la NIC virtual del host de administración.

Puede usar cualquier tipo de almacenamiento que sea compatible con Hyper-V, compartido o local.

> [!TIP]
> Es conveniente usar el mismo nombre para todos los conmutadores virtuales, pero no es obligatorio. Si tiene previsto usar scripts para la implementación, consulte el comentario asociado a la variable `vSwitchName` en el archivo config.psd1.

### <a name="host-compute-requirements"></a>Requisitos de proceso del host
A continuación se muestran los requisitos mínimos de hardware y software para los cuatro hosts físicos usados en la implementación de ejemplo.

administrador de flujos de trabajo|Requisitos de hardware|Requisitos de software|
--------|-------------------------|-------------------------
|Host físico de Hyper-V|CPU de 4 núcleos a 2,66 GHz<br> 32 GB de RAM<br> 300 GB de espacio en disco<br> Un adaptador de red físico de 1 Gb/s (o más rápido)|Sistema operativo: Como se define en la sección<br> "Se aplica a" que aparece al principio de este tema.<br> Rol de Hyper-V instalado|

### <a name="sdn-infrastructure-vm-role-requirements"></a>Requisitos del rol de máquina virtual de infraestructura de SDN
En la tabla siguiente se enumeran los requisitos de los roles de máquina virtual.

Rol|Requisitos de CPU virtual|Requisitos de memoria|Requisitos de disco|
--------|-----------------------------|-----------------------|--------------------------
|Controladora de red (tres nodos)|4 vCPU|4 GB como mínimo<br> (se recomiendan 8 GB)|75 GB para la unidad de sistema operativo
|SLB/MUX (tres nodos)|8 vCPU|Se recomiendan 8 GB|75 GB para la unidad de sistema operativo
|Puerta de enlace RAS<br> (grupo único de puerta de enlace<br> de tres nodos, dos activos, uno pasivo)|8 vCPU|Se recomiendan 8 GB|75 GB para la unidad de sistema operativo
|Enrutador BGP de puerta de enlace de RAS<br> para el emparejamiento de SLB/MUX<br> (como alternativa, use el conmutador para la parte superior del rack<br> como enrutador BGP)|2 CPU virtuales|2 GB|75 GB para la unidad de sistema operativo|

Si usa System Center Virtual Machine Manager (VMM) para la implementación, se necesitarán recursos de máquinas virtuales de infraestructura adicionales para VMM y otras infraestructuras que no sean de SDN. Para más información, consulte [Requisitos del sistema para System Center Virtual Machine Manager](https://docs.microsoft.com/system-center/vmm/system-requirements?view=sc-vmm-2019&preserve-view=true).

## <a name="extending-your-infrastructure"></a>Ampliación de la infraestructura
Los requisitos de tamaño y recursos de la infraestructura dependen de las máquinas virtuales de carga de trabajo de inquilino que planea hospedar. Los requisitos de CPU, memoria y disco de las máquinas virtuales de infraestructura (por ejemplo: Controladora de red, SLB, puerta de enlace, etc.) se definen en la tabla anterior. Puede agregar más máquinas virtuales de infraestructura para realizar un escalado según sea necesario. Sin embargo, las máquinas virtuales de los inquilinos que se ejecutan en los hosts de Hyper-V tienen sus propios requisitos de CPU, memoria y disco que se deben tener en cuenta.

Si las máquinas virtuales de la carga de trabajo de los inquilinos empiezan a consumir demasiados recursos en los hosts de Hyper-V físicos, puede ampliar la infraestructura mediante la incorporación de hosts físicos adicionales. Puede usar Windows Admin Center, VMM o scripts de PowerShell para crear nuevos recursos de servidor a través de Controladora de red. El método que se utilice depende de la implementación inicial de la infraestructura. Si necesita agregar más direcciones IP para la red del proveedor de HNV, puede crear nuevas subredes lógicas (con los grupos de direcciones IP correspondientes) que los hosts puedan usar.

## <a name="phased-deployment"></a>Implementación por fases
En función de sus requisitos, puede que necesite implementar un subconjunto de la infraestructura de SDN. Por ejemplo, si solo quiere hospedar cargas de trabajo de clientes en su centro de datos y no se requiere comunicación externa, puede implementar Controladora de red y omitir la implementación de máquinas virtuales de SLB/MUX y puerta de enlace. A continuación se describen los requisitos de infraestructura de la característica de red para una implementación por fases de la infraestructura de SDN.

Característica|Requisitos para la implementación|Requisitos de red|
--------|-------------------------|-------------------------
|Administración de red lógica<br> Listas de control de acceso (para redes basadas en VLAN)<br> Calidad de servicio (para redes basadas en VLAN)<br>|Controladora de red|Ninguno|
|Redes virtuales<br> Enrutamiento definido por el usuario<br> Listas de control de acceso (para red virtual)<br> Subredes cifradas<br> Calidad de servicio (para redes virtuales)<br> Emparejamiento de redes virtuales de Azure|Controladora de red|VLAN, subred y enrutador de PA de HNV|
|Traducción de direcciones de red entrantes o salientes<br> Equilibrio de carga|Controladora de red<br> SLB/MUX|BGP en la red de PA de HNV<br> Subredes de IP virtual privadas y públicas|
|Conexiones de puerta de enlace de GRE|Controladora de red<br> Puerta de enlace|BGP en la red de PA de HNV<br> Subred de IP virtual de GRE|
|Conexiones de puerta de enlace de IPSec|Controladora de red<br> SLB/MUX<br> Puerta de enlace|BGP en la red de PA de HNV<br> Subred de IP virtual pública|
|Conexiones de puerta de enlace L3|Controladora de red<br> Puerta de enlace|VLAN, subred y enrutador de inquilino<br> BGP en la VLAN de inquilino opcional.|

## <a name="next-steps"></a>Pasos siguientes
Para obtener información relacionada, consulte:
- [Requisitos de implementación de Controladora de red](/windows-server/networking/sdn/plan/installation-and-preparation-requirements-for-deploying-network-controller)
- [SDN en Azure Stack HCI](https://docs.microsoft.com/azure-stack/hci/concepts/software-defined-networking)