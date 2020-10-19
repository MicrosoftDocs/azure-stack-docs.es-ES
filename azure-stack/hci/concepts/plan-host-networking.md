---
title: Planeamiento de redes de host para Azure Stack HCI
description: Aprenda a planear redes de host para clústeres de Azure Stack HCI
author: v-dasis
ms.topic: how-to
ms.date: 10/13/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 8f18cd223faca91bc490b659e0a25fce1add3fea
ms.sourcegitcommit: 4b1a4ec0ac0283faea9438e6617fcb3cfc6fee6d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2020
ms.locfileid: "92041229"
---
# <a name="plan-host-networking-for-azure-stack-hci"></a>Planeamiento de redes de host para Azure Stack HCI

> Se aplica a Azure Stack HCI, versión 20H2

En este tema se describen los requisitos y consideraciones de planeamiento de redes de host en entornos de clústeres de Azure Stack HCI extendidos y no extendidos.

## <a name="traffic-types-supported"></a>Tipos de tráfico admitidos

Azure Stack HCI usa Bloque de mensajes del servidor (SMB). SMB en Azure Stack HCI admite los siguientes tipos de tráfico:

- Capa de bus de almacenamiento (SBL): es la que Espacios de almacenamiento directo usa. Se emplea para el tráfico de prioridad más alta
- Volúmenes compartidos de clúster (CSV)
- Migración en vivo (LM)
- Réplica de almacenamiento (SR): solo se instala en clústeres extendidos
- Recursos compartidos de archivos (FS): FS tradicional y Servidor de archivos de escalabilidad horizontal (SOFS)
- Latido del clúster (HB)
- Comunicación del clúster (combinaciones de nodos, actualizaciones de clúster, actualizaciones del registro)

El tráfico SMB puede viajar por los siguientes protocolos:

- Protocolo de control de transmisión (TCP): se usa entre sitios
- Acceso directo a memoria remota (RDMA)
- QUIC: futuro

## <a name="traffic-bandwidth-allocation"></a>Asignación de ancho de banda para el tráfico

En la tabla siguiente se muestran las asignaciones de ancho de banda para varios tipos de tráfico, donde:

- Todas las unidades están en Gbps
- Los valores se aplican a los clústeres extendidos y no extendidos
- El tráfico SMB obtiene el 50 % de la asignación de ancho de banda total
- El tráfico de la capa de bus de almacenamiento y de Volumen compartido de clúster (SBL/CSV) obtiene el 70 % del 50 % restante
- El tráfico de Migración en vivo (LM) obtiene el 15 % del 50 % restante
- El tráfico de Réplica de almacenamiento (SR) obtiene el 14 % del 50 % restante
- El tráfico de Latido (HB) obtiene el 1 % del 50 % restante
- \* = debe usar la compresión en lugar de RDMA si la asignación de ancho de banda para el tráfico de LM es < 5 Gbps

|Velocidad de NIC|Velocidad de NIC en equipo|Reserva de Azure del 50 % de SMB|SBL/CSV %|Ancho de banda de SBL/CSV|LM %|Ancho de banda de LM|SR % |Ancho de banda de SR|HB %|Ancho de banda de HB|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|10|20|10|70%|7|14 %*|1,4*|14 %|1.4|2 %|0,2|
|25|50|25|70%|17.5|15 %*|3,75*|14 %|3,5|1 %|0,25|
|40|80| 40|70%|28|15 %|6|14 %|5.6|1 %|0,4|
|50|100|50|70%|35|15 %|7.5|14 %|7|1 %|0.5|
|100|200|100|70%|70|15 %|15|14 %|14|1 %|1|
|200|400|200|70%|140|15 %|30|14 %|28|1 %|2|

## <a name="rdma-considerations"></a>Consideraciones sobre RDMA

El acceso directo a memoria remota (RDMA) es un acceso directo a memoria desde la memoria de un equipo a la de otro sin necesidad de que intervenga ningún sistema operativo del equipo. Esto permite redes de alto rendimiento y baja latencia a la vez que reduce el uso de la CPU, lo que resulta especialmente útil en los clústeres.

Todo el tráfico de RDMA del host aprovecha las ventajas de SMB directo. SMB directo es el tráfico de SMB 3.0 que se envía a través de RDMA y se multiplexa en el puerto 445. Para que el tráfico RDMA siga siendo compatible con la mayoría de los conmutadores físicos actuales y futuros del mercado, se debe usar un mínimo de dos clases de tráfico habilitadas para el control de flujo basado en prioridades.

El protocolo RDMA de área extensa de Internet (iWARP) ejecuta RDMA mediante TCP, mientras que RDMA sobre Ethernet convergente (RoCE) evita el uso de TCP, pero requiere conmutadores de NIC y conmutadores físicos que lo admitan. Para conocer los requisitos de red convergentes para RDMA sobre RoCE, consulte la [guía de implementación de RDMA para Windows Server 2016 y 2019](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

RDMA está habilitado de forma predeterminada para todo el tráfico horizontal de derecha a izquierda entre los nodos de clúster de un sitio de la misma subred. RDMA está deshabilitado y no se admite para el tráfico de clúster extendido vertical de arriba abajo entre sitios de diferentes subredes.

Estos son los requisitos de RDMA para Azure Stack HCI:

- Todo el tráfico entre subredes y entre sitios (clústeres extendidos) debe usar WinSock TCP. Los saltos de red intermedios se encuentran fuera de la vista y el control de Azure Stack HCI.
- No se admite el RDMA entre subredes y entre sitios (clústeres extendidos). El uso de vínculos superiores y varios dispositivos de red conlleva múltiples puntos de error que hacen que este sea inestable y no compatible.
- No se necesitan NIC virtuales adicionales para el tráfico de réplica de almacenamiento de los clústeres extendidos. No obstante, para solucionar problemas, puede ser útil mantener el tráfico entre sitios y entre subredes separado del tráfico RDMA horizontal de derecha a izquierda. Si SMB directo no se puede deshabilitar de forma nativa entre sitios o entre subredes por flujo, entonces:
    - Se deben aprovisionar una o más vNIC adicionales para la réplica de almacenamiento
    - Las vNIC de réplica de almacenamiento deben tener el RDMA deshabilitado mediante el cmdlet [Disable-NetAdapterRDMA](https://docs.microsoft.com/powershell/module/netadapter/disable-netadapterrdma) de PowerShell porque el RDMA es por definición entre sitios y entre subredes.
    - Los adaptadores de RDMA nativos necesitan un vSwitch y varias VNIC para la compatibilidad con réplica de almacenamiento con el fin de satisfacer los requisitos de sitio o subred anteriores
    - Los requisitos de ancho de banda de RDMA internos del sitio requieren conocer los porcentajes de ancho de banda por tipo de tráfico, como se describe en la sección **Asignación de ancho de banda para el tráfico**. Esto garantizará que se puedan aplicar los límites y reservas de ancho de banda adecuados para el tráfico horizontal de derecha a izquierda (nodo a nodo).
- El tráfico de migración en vivo y de réplica de almacenamiento debe estar limitado por el ancho de banda de SMB; de lo contrario, podrían consumir todo el ancho de banda, lo que privaría de este al tráfico de almacenamiento de alta prioridad. Para más información, consulte los cmdlets [Set-SmbBandwidthLimit](https://docs.microsoft.com/powershell/module/smbshare/set-smbbandwidthlimit) y [Set-SRNetworkConstraint](https://docs.microsoft.com/powershell/module/storagereplica/set-srnetworkconstraint) de PowerShell.

> [!NOTE]
> Debe convertir bits en bytes al usar el cmdlet `Set-SmbBandwidthLimit`.

## <a name="node-interconnect-requirements"></a>Requisitos de interconexión entre nodos

En esta sección se describen los requisitos de red específicos entre los nodos de servidor de un sitio, denominados interconexiones. Se pueden usar las interconexiones de nodos con conmutador o sin conmutador y se admiten:

- **Con conmutador:** los nodos de servidor suelen conectarse entre sí a través de redes Ethernet que usan conmutadores de red. Los conmutadores deben estar configurados correctamente para administrar el ancho de banda y el tipo de red. Si usa RDMA que implementa el protocolo RoCE, es importante el dispositivo de red y la configuración del conmutador.
- **Sin conmutador:** los nodos de servidor también se pueden interconectar mediante conexiones Ethernet directas sin un conmutador. En este caso, cada nodo de servidor debe tener una conexión directa con todos los demás nodos del clúster en el mismo sitio.

### <a name="interconnects-for-2-3-node-clusters"></a>Interconexiones para clústeres de dos o tres nodos

Estos son los requisitos *mínimos* de interconexión para los clústeres de sitio único que tienen dos o tres nodos. Se aplican a cada nodo de servidor:

- Una o más tarjetas de adaptador de red de 1 GB que se usarán para las funciones de administración
- Una o más tarjetas de interfaz de red de 10 GB (o superior) para el tráfico de almacenamiento y carga de trabajo
- Se recomiendan dos o más conexiones de red entre cada nodo para la redundancia y el rendimiento

### <a name="interconnects-for-4-node-and-greater-clusters"></a>Interconexiones para clústeres de cuatro o más nodos

Estos son los requisitos *mínimos* de interconexión para los clústeres que tienen cuatro o más nodos, y para los clústeres de alto rendimiento. Se aplican a cada nodo de servidor:

- Una o más tarjetas de adaptador de red de 1 GB que se usarán para las funciones de administración.
- Una o más tarjetas de interfaz de red de 25 GB (o superior) para el tráfico de almacenamiento y carga de trabajo. Se recomiendan dos o más conexiones de red para la redundancia y el rendimiento.
- Tarjetas de red que son compatibles con el acceso directo a memoria remota (RDMA): iWARP (opción recomendada) o RoCE.

### <a name="site-to-site-requirements-stretched-cluster"></a>Requisitos de sitio a sitio (clúster extendido)

Cuando se conectan sitios de clústeres extendidos, siguen siendo aplicables los requisitos de interconexión de cada sitio y tienen requisitos adicionales de tráfico de migración en vivo de la réplica de almacenamiento y de Hyper-V que deben tenerse en cuenta:

- Al menos una conexión RDMA o Ethernet/TCP de 1 GB entre sitios para la replicación sincrónica. Se prefiere una conexión de 25 GB.
- Una red entre sitios con suficiente ancho de banda para contener la carga de trabajo de escritura de E/S y un promedio de latencia de ida y vuelta de 5 ms para la replicación sincrónica. Este tipo de replicación no tiene una recomendación de latencia.
- Si usa una única conexión entre sitios, establezca límites de ancho de banda SMB para la réplica de almacenamiento mediante PowerShell. Para más información, consulte [Set-SmbBandwidthLimit](/powershell/module/smbshare/set-smbbandwidthlimit).
- Si usa varias conexiones entre sitios, separe el tráfico entre las conexiones. Por ejemplo, coloque el tráfico de la réplica de almacenamiento en una red independiente distinta a la del tráfico de migración en vivo de Hyper-V con PowerShell. Para más información, consulte [Set-SRNetworkConstraint](/powershell/module/storagereplica/set-srnetworkconstraint).

## <a name="network-port-requirements"></a>Requisitos de puerto de red

Asegúrese de que los puertos de red adecuados estén abiertos entre todos los nodos de servidor de un sitio y entre sitios (para clústeres extendidos). Necesitará reglas adecuadas de firewall y de enrutador para permitir ICMP, SMB (puerto 445, más el puerto 5445 para SMB directo) y el tráfico bidireccional WS-MAN (puerto 5985) entre todos los servidores del clúster.

Al usar el Asistente para creación de clústeres en Windows Admin Center para crear el clúster, el Asistente abre automáticamente los puertos de firewall adecuados en cada servidor del clúster para los clústeres de conmutación por error, Hyper-V y réplica de almacenamiento. Si usa un firewall diferente en cada servidor, abra los puertos siguientes:

### <a name="failover-clustering-ports"></a>Puertos de clústeres de conmutación por error

- ICMPv4 e ICMPv6
- Puerto TCP 445
- Puertos dinámicos RPC
- Puerto TCP 135
- Puerto TCP 137
- Puerto TCP 3343
- Puerto UDP 3343

### <a name="hyper-v-ports"></a>Puertos de Hyper-V

- Puerto TCP 135
- Puerto TCP 80 (conectividad HTTP)
- Puerto TCP 443 (conectividad HTTPS)
- Puerto TCP 6600
- Puerto TCP 2179
- Puertos dinámicos RPC
- Asignador de puntos de conexión de RPC
- Puerto TCP 445

### <a name="storage-replica-ports-stretched-cluster"></a>Puertos de réplica de almacenamiento (clúster extendido)

- Puerto TCP 445
- TCP 5445 (si se usa iWarp RDMA)
- Puerto TCP 5985
- ICMPv4 e ICMPv6 (si se usa el cmdlet `Test-SRTopology` de PowerShell)

Puede que se requieran puertos adicionales no mencionados antes. Estos son los puertos para la funcionalidad básica de Azure Stack HCI.

## <a name="network-switch-requirements"></a>Requisitos del conmutador de red

En esta sección se definen los requisitos de los conmutadores físicos que se usan con Azure Stack HCI. En estos requisitos se enumeran las especificaciones del sector, los estándares de la organización y los protocolos que son obligatorios para todas las implementaciones de Azure Stack HCI. A menos que se indique lo contrario, se requiere la última versión activa (no reemplazada) del estándar.

Estos requisitos ayudan a garantizar unas comunicaciones confiables entre los nodos de las implementaciones de clúster de Azure Stack HCI. Unas comunicaciones confiables entre los nodos resultan fundamentales. Proporcionar el nivel de confiabilidad necesario para Azure Stack HCI requiere que los conmutadores:

- Cumplan con las especificaciones, los estándares y los protocolos aplicables del sector
- Ofrezcan visibilidad sobre qué especificaciones, estándares y protocolos admite el conmutador
- Proporcionen información sobre qué funcionalidades están habilitadas

Asegúrese de preguntar al proveedor del conmutador si el conmutador admite lo siguiente:

#### <a name="standard-ieee-8021q"></a>Estándar: IEEE 802.1Q

Los conmutadores Ethernet deben cumplir con la especificación IEEE 802.1Q que define las redes VLAN. Las VLAN son necesarias para varios aspectos de Azure Stack HCI y son necesarias en todos los escenarios.

#### <a name="standard-ieee-8021qbb"></a>Estándar: IEEE 802.1Qbb

Los conmutadores Ethernet deben cumplir con la especificación IEEE 802.1Qbb que define el control de flujo basado en prioridades (PFC). Si se usa Data Center Bridging (DCB), se necesita PFC. Como DCB se puede usar en escenarios de RDMA como RoCE e iWARP, se necesita 802.1Qbb en todos los escenarios. Se requiere un mínimo de tres prioridades de clase de servicio (CoS) sin que se degraden las funcionalidades del conmutador o la velocidad del puerto.

#### <a name="standard-ieee-8021qaz"></a>Estándar: IEEE 802.1Qaz

Los conmutadores Ethernet deben cumplir con la especificación IEEE 802.1Qaz que define la selección de transmisiones mejorada (ETS). Se requiere ETS si se usa DCB. Como DCB se puede usar en escenarios de RDMA como RoCE e iWARP, se necesita 802.1Qaz en todos los escenarios. Se requiere un mínimo de tres prioridades de clase de servicio (CoS) sin que se degraden las funcionalidades del conmutador o la velocidad del puerto.

#### <a name="standard-ieee-8021ab"></a>Estándar: IEEE 802.1AB

Los conmutadores Ethernet deben cumplir la especificación IEEE 802.1AB que define el protocolo de detección de niveles de vínculo (LLDP). Se requiere LLDP para que Windows detecte la configuración del conmutador. La configuración del tipo, longitud y valores (TLVs) de LLDP debe estar habilitada dinámicamente. Estos conmutadores no deben requerir configuración adicional.

Por ejemplo, si habilita el subtipo 3 de 802.1, se anunciarán automáticamente todas las redes VLAN disponibles en los puertos del conmutador.

#### <a name="tlv-requirements"></a>Requisitos de TLV

LLDP permite a las organizaciones definir y codificar sus propios TLV personalizados. Estos se denominan TLV específicos de la organización. Todos los TLV específicos de la organización se inician con un valor de tipo TLV de LLDP de 127. En la tabla siguiente se muestra qué subtipos de TLV personalizados específicos de la organización (tipo de TLV 127) son obligatorios y cuáles son opcionales:

|Condición|Organización|Subtipo de TLV|
|-|-|-|
|Obligatorio|IEEE 802.1|Nombre de VLAN (subtipo = 3)|
|Obligatorio|IEEE 802.3|Tamaño máximo del marco (subtipo = 4)|
|Opcional|IEEE 802.1|Identificador de VLAN del puerto (subtipo = 1)|
|Opcional|IEEE 802.1|Identificador de VLAN de puerto y protocolo (subtipo = 2)|
|Opcional|IEEE 802.1|Agregación de vínculos (subtipo = 7)|
|Opcional|IEEE 802.1|Notificación de congestión (subtipo = 8)|
|Opcional|IEEE 802.1|Configuración de ETS (subtipo = 9)|
|Opcional|IEEE 802.1|Recomendación de ETS (subtipo = A)|
|Opcional|IEEE 802.1|Configuración de PFC (subtipo = B)|
|Opcional|IEEE 802.1|EVB (subtipo = D)|
|Opcional|IEEE 802.3|Agregación de vínculos (subtipo = 3)|

> [!NOTE]
> En el futuro, es posible que se requieran algunas de las características opcionales que se enumeran.

## <a name="next-steps"></a>Pasos siguientes

- Repase las aspectos básicos de los clústeres de conmutación por error. Consulte [Aspectos básicos de redes de clústeres de conmutación por error](https://techcommunity.microsoft.com/t5/failover-clustering/failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09)
- Repase el uso de SET. Consulte [Acceso directo a memoria remota (RDMA) y Switch Embedded Teaming (SET)](https://docs.microsoft.com/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).
- Para la implementación, consulte [Creación de un clúster con Windows Admin Center](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster).
- Para la implementación, consulte [Creación de un clúster con Windows PowerShell](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell).