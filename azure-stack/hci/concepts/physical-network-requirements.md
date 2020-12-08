---
title: Requisitos de red física para Azure Stack HCI
description: Obtenga información sobre los requisitos de red física para Azure Stack HCI.
author: v-dasis
ms.topic: how-to
ms.date: 11/30/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 7452437d7f760a688c2403cb4def735b2daaa105
ms.sourcegitcommit: a7a2ac1b9be926134826dce03e348154fd212bc9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/01/2020
ms.locfileid: "96443290"
---
# <a name="physical-network-requirements-for-azure-stack-hci"></a>Requisitos de red física para Azure Stack HCI

> Se aplica a Azure Stack HCI, versión 20H2

En este tema se describen los requisitos y las consideraciones de red física (tejido) para Azure Stack HCI, especialmente para los conmutadores de red.

> [!NOTE]
> Los requisitos para futuras versiones de Azure Stack HCI pueden cambiar.

## <a name="network-switches-for-azure-stack-hci"></a>Conmutadores de red para Azure Stack HCI

Microsoft prueba Azure Stack HCI con los estándares y protocolos que se identifican en la siguiente sección **Requisitos del conmutador de red** . Aunque Microsoft no certifica los conmutadores de red, trabajamos con los proveedores para identificar los dispositivos que admiten los requisitos de Azure Stack HCI.

Estos requisitos también se publican en [Directivas y especificaciones del programa de compatibilidad de hardware de Windows](https://docs.microsoft.com/windows-hardware/design/compatibility/whcp-specifications-policies).  Seleccione **Descargar especificaciones y directivas, versión 1809**, abra el archivo ZIP, abra **WHCP-Components-Peripherals-Specification-1809.pdf** y, a continuación, consulte la sección **Device.Network.Switch.SDDC**.

> [!IMPORTANT]
> Aunque los demás conmutadores de red que usan tecnologías y protocolos que no se enumeran aquí pueden funcionar, Microsoft no puede garantizar que funcionarán con Azure Stack HCI y es posible que no pueda ayudar a solucionar los problemas que se produzcan.

Al adquirir conmutadores de red, póngase en contacto con el proveedor del conmutador y asegúrese de que los dispositivos cumplen todos los requisitos de Azure Stack HCI. Los siguientes proveedores (en orden alfabético) han confirmado que sus conmutadores admiten los requisitos de Azure Stack HCI:

|Vendor|10 GbE|25 GbE|100 GbE|
|-----|-----|-----|-----|
|Dell|[Serie S41xx](https://www.dell.com/learn/us/en/45/shared-content~data-sheets~en/documents~dell-emc-networking-s4100-series-spec-sheet.pdf)|[Serie S52xx](https://www.delltechnologies.com/resources/en-us/asset/data-sheets/products/networking/dell_emc_networking-s5200_on_spec_sheet.pdf)|[Serie S52xx](https://www.delltechnologies.com/resources/en-us/asset/data-sheets/products/networking/dell_emc_networking-s5200_on_spec_sheet.pdf)|
|Lenovo|[G8272](https://lenovopress.com/tips1267-lenovo-rackswitch-g8272), [NE1032](https://lenovopress.com/lp0605-thinksystem-ne1032-rackswitch)|[NE2572](https://lenovopress.com/lp0608-lenovo-thinksystem-ne2572-rackswitch) |[NE10032](https://lenovopress.com/lp0609-lenovo-thinksystem-ne10032-rackswitch) |

> [!IMPORTANT]
> Microsoft actualiza esta lista cuando recibe información de los cambios realizados por los proveedores de conmutadores de red.

Si su conmutador no está incluido, póngase en contacto con el proveedor del conmutador para asegurarse de que el modelo de conmutador y la versión del sistema operativo del conmutador admiten los requisitos de la sección siguiente.

## <a name="network-switch-requirements"></a>Requisitos del conmutador de red

En esta sección se enumeran los estándares del sector que son obligatorios para los conmutadores de red que se usan en todas las implementaciones de Azure Stack HCI. Estos estándares ayudan a garantizar unas comunicaciones confiables entre los nodos de las implementaciones de clústeres de Azure Stack HCI.

> [!NOTE]
> Los adaptadores de red que se usan para el tráfico de proceso, almacenamiento y administración requieren Ethernet. Para más información, consulte [Requisitos de red de host para Azure Stack HCI](host-network-requirements.md).

Estos son los estándares y especificaciones de IEEE obligatorios:

### <a name="standard-ieee-8021q"></a>Estándar: IEEE 802.1Q

Los conmutadores Ethernet deben cumplir con la especificación IEEE 802.1Q que define las redes VLAN. Las VLAN son necesarias para varios aspectos de Azure Stack HCI y son necesarias en todos los escenarios.

### <a name="standard-ieee-8021qbb"></a>Estándar: IEEE 802.1Qbb

Los conmutadores Ethernet deben cumplir con la especificación IEEE 802.1Qbb que define el control de flujo basado en prioridades (PFC). Si se usa Data Center Bridging (DCB), se necesita PFC. Como DCB se puede usar en escenarios de RDMA como RoCE e iWARP, se necesita 802.1Qbb en todos los escenarios. Se requiere un mínimo de tres prioridades de clase de servicio (CoS) sin que se degraden las funcionalidades del conmutador o la velocidad del puerto. Al menos una de estas clases de tráfico debe proporcionar comunicación sin pérdida.

### <a name="standard-ieee-8021qaz"></a>Estándar: IEEE 802.1Qaz

Los conmutadores Ethernet deben cumplir con la especificación IEEE 802.1Qaz que define la selección de transmisiones mejorada (ETS). Se requiere ETS si se usa DCB. Como DCB se puede usar en escenarios de RDMA como RoCE e iWARP, se necesita 802.1Qaz en todos los escenarios. Se requiere un mínimo de tres prioridades de clase de servicio (CoS) sin que se degraden las funcionalidades del conmutador o la velocidad del puerto.

> [!NOTE]
> La infraestructura hiperconvergida tiene una dependencia elevada de la comunicación de capa 2 horizontal de derecha a izquierda en el mismo bastidor y, por tanto, requiere ETS. Microsoft no prueba Azure Stack HCI con Punto de código de servicios diferenciados (DSCP). 

### <a name="standard-ieee-8021ab"></a>Estándar: IEEE 802.1AB

Los conmutadores Ethernet deben cumplir la especificación IEEE 802.1AB que define el protocolo de detección de niveles de vínculo (LLDP). Se requiere LLDP para Azure Stack HCI y se admite la formación de equipos para detectar la configuración del conmutador.

La configuración del tipo, longitud y valores (TLVs) de LLDP debe estar habilitada dinámicamente. Estos conmutadores no deben requerir configuración adicional más allá de la habilitación de un TLV específico. Por ejemplo, si habilita el subtipo 3 de 802.1, se anunciarán automáticamente todas las redes VLAN disponibles en los puertos del conmutador.

### <a name="custom-tlv-requirements"></a>Requisitos de los TLV personalizados

LLDP permite a las organizaciones definir y codificar sus propios TLV personalizados. Estos se denominan TLV específicos de la organización. Todos los TLV específicos de la organización se inician con un valor de tipo TLV de LLDP de 127. En la tabla siguiente se muestra qué subtipos de TLV personalizados específicos de la organización (tipo de TLV 127) son obligatorios:

|Versión requerida|Organización|Subtipo de TLV|
|-----|-----|-----|-----|
|20H2 y versiones posteriores|IEEE 802.1|Nombre de VLAN (subtipo = 3)|
|20H2 y versiones posteriores|IEEE 802.3|Tamaño máximo del marco (subtipo = 4)|

## <a name="network-traffic-and-architecture"></a>Arquitectura y tráfico de red

Esta sección es principalmente para los administradores de red.

Azure Stack HCI puede funcionar en diversas arquitecturas de centros de datos, entre las que se incluyen las de dos niveles (columna-hoja) y las de tres niveles (principal-agregación-acceso). En esta sección se hace referencia a los conceptos de la topología columna-hoja que se suelen usar con las cargas de trabajo en una infraestructura hiperconvergida, como Azure Stack HCI.

## <a name="network-models"></a>Modelos de red

El tráfico de red se puede clasificar por su dirección. Los entornos de red de área de almacenamiento (SAN) tradicionales son intensivos en tráfico vertical de arriba abajo, donde el tráfico fluye desde una capa de proceso a una capa de almacenamiento a través de un límite de capa 3 (IP). La infraestructura hiperconvergida es más intensa en tráfico horizontal de derecha a izquierda, donde una parte importante del tráfico permanece dentro de un límite de capa 2 (VLAN).

> [!IMPORTANT]
>Se recomienda que todos los nodos de clúster de un sitio se encuentren físicamente en el mismo bastidor y estén conectados a los mismos conmutadores de la parte superior del rack (ToR).

### <a name="north-south-traffic-for-azure-stack-hci"></a>Tráfico vertical de arriba abajo para Azure Stack HCI

El tráfico vertical de arriba abajo tiene las siguientes características:

- El tráfico fluye desde un conmutador ToR hacia la columna o desde la columna hacia un conmutador ToR.
- El tráfico sale del bastidor físico o cruza un límite de capa 3 (IP).
- Incluye tráfico de administración (PowerShell, Windows Admin Center), tráfico de proceso (VM) y tráfico de clúster extendido entre sitios.
- Usa un conmutador Ethernet para la conectividad a la red física.

### <a name="east-west-traffic-for-azure-stack-hci"></a>Tráfico horizontal de derecha a izquierda para Azure Stack HCI

El tráfico horizontal de derecha a izquierda tiene las siguientes características:

- El tráfico permanece dentro de los conmutadores ToR y el límite de capa 2 (VLAN).
- Incluye tráfico de almacenamiento o tráfico de Migración en vivo entre los nodos del mismo clúster y (si se usa un clúster extendido) dentro del mismo sitio.
- Puede usar un conmutador Ethernet (conmutado) o una conexión directa (sin conmutador), como se describe en las dos secciones siguientes.

## <a name="using-switches"></a>Con conmutadores

El tráfico vertical de arriba abajo requiere el uso de conmutadores. Además de usar un conmutador Ethernet que admita los protocolos necesarios para Azure Stack HCI, el aspecto más importante es el tamaño adecuado del tejido de red.

Es imperativo conocer el ancho de banda de tejido "sin bloqueo" que pueden admitir los conmutadores Ethernet y que se minimice (o, preferiblemente, se elimine) la suscripción en exceso de la red.

Los puntos de congestión comunes y la suscripción en exceso, como el [grupo de agregación de vínculos de varios chasis](https://en.wikipedia.org/wiki/Multi-chassis_link_aggregation_group) que se usan para la redundancia de rutas de acceso, se pueden eliminar mediante el uso correcto de subredes y VLAN. Consulte también [Requisitos de red de host para Azure Stack HCI](host-network-requirements.md).

Colabore con su proveedor de red o con el equipo de soporte técnico de red para asegurarse de que los conmutadores de red tienen el tamaño adecuado para la carga de trabajo que intenta ejecutar.

## <a name="using-switchless"></a>Sin conmutadores

Azure Stack HCI admite conexiones sin conmutadores (directas) para el tráfico horizontal de derecha a izquierda para todos los tamaños de clúster, siempre que cada nodo del clúster tenga una conexión redundante a cada nodo del clúster. Esto se llama conexión de "malla completa".

> [!NOTE]
>Las ventajas de las implementaciones sin conmutadores disminuyen con clústeres de más de tres nodos debido al número de adaptadores de red necesarios.

### <a name="advantages-of-switchless-connections"></a>Ventajas de las conexiones sin conmutador

- No es necesaria la adquisición de conmutadores para el tráfico horizontal de derecha a izquierda. Se requiere un conmutador para el tráfico vertical de arriba abajo. Esto puede dar lugar a costos de capital reducidos (gastos de capital), pero depende del número de nodos del clúster.
- Dado que no hay ningún conmutador, la configuración se limita al host, lo que puede reducir el número potencial de pasos de configuración necesarios. Este valor disminuye a medida que aumenta el tamaño del clúster.

### <a name="disadvantages-of-switchless-connections"></a>Desventajas de las conexiones sin conmutador

- A medida que crece el número de nodos del clúster, el costo de los adaptadores de red podría superar el costo del uso de conmutadores de red.
- Se requiere más planeación para los esquemas de direcciones IP y de subred.
- Proporciona solo acceso de almacenamiento local. El tráfico de las máquinas virtuales, el tráfico de administración y cualquier otro tráfico que requiera acceso vertical de arriba abajo no pueden usar estos adaptadores.
- Por lo general, no se escala bien con clústeres de más de tres nodos.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de los requisitos de host y del adaptador de red. Consulte [Requisitos de red de host para Azure Stack HCI](host-network-requirements.md).
- Repase las aspectos básicos de los clústeres de conmutación por error. Consulte [Aspectos básicos de redes de clústeres de conmutación por error](https://techcommunity.microsoft.com/t5/failover-clustering/.failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09).
- Repase el uso de SET. Consulte [Acceso directo a memoria remota (RDMA) y Formación de equipos insertada en el conmutador (SET)](https://docs.microsoft.com/windows-server/virtualization/.hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).
- Para la implementación, consulte [Creación de un clúster de Azure Stack HCI mediante Windows Admin Center](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster).
- Para la implementación, consulte [Creación de un clúster de Azure Stack HCI mediante Windows PowerShell](https://docs.microsoft.com/azure-stack/hci/deploy/create-cluster-powershell).