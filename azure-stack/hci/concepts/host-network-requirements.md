---
title: Requisitos de red de host para Azure Stack HCI
description: Obtenga información sobre los requisitos de red de host para Azure Stack HCI.
author: v-dasis
ms.topic: how-to
ms.date: 11/25/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 3e31852e554c85ffab18aacaa336a007a97874f2
ms.sourcegitcommit: 9b0e1264ef006d2009bb549f21010c672c49b9de
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2021
ms.locfileid: "98255306"
---
# <a name="host-network-requirements-for-azure-stack-hci"></a>Requisitos de red de host para Azure Stack HCI

> Se aplica a Azure Stack HCI, versión 20H2

En este tema se describen las consideraciones y los requisitos de redes de host de Azure Stack HCI. Para obtener información sobre las arquitecturas de los centros de datos y las conexiones físicas entre servidores, consulte [Requisitos de red física para Azure Stack HCI](physical-network-requirements.md).

## <a name="network-traffic-types"></a>Tipos de tráfico de red

El tráfico de red de Azure Stack HCI se puede clasificar por su finalidad prevista:

- **Tráfico de proceso**: tráfico que se origina o se destina a una máquina virtual.
- **Tráfico de almacenamiento**: tráfico para Espacios de almacenamiento directo (S2D) mediante Bloque de mensajes del servidor (SMB).
- **Tráfico de administración**: tráfico importante para un administrador de clústeres, como Active Directory, Escritorio remoto, Windows Admin Center y Windows PowerShell.

## <a name="selecting-a-network-adapter"></a>Selección de un adaptador de red

Para Azure Stack HCI, es necesario elegir un adaptador de red que haya logrado la certificación de centro de datos definido por software (SDDC) de Windows Server con la calificación adicional (AQ) Estándar o Premium. Estos adaptadores admiten las características más avanzadas de la plataforma y han experimentado la mayor parte de las pruebas de nuestros asociados de hardware. Normalmente, este nivel de escrutinio conduce a una reducción en los problemas de calidad relacionados con el hardware y los controladores.

Puede identificar un adaptador que tenga AQ Estándar o Premium; para ello, revise la entrada de [Catálogo de Windows Server](https://www.windowsservercatalog.com/) correspondiente al adaptador y la versión del sistema operativo aplicable. A continuación se muestra un ejemplo de la notación de AQ Premium:

:::image type="content" source="media/plan-networking/windows-certified.png" alt-text="Certificado para Windows" lightbox="media/plan-networking/windows-certified.png":::

## <a name="overview-of-key-network-adapter-capabilities"></a>Introducción a las funcionalidades principales del adaptador de red

Las principales funcionalidades del adaptador de red que aprovecha Azure Stack HCI incluyen:

- Varias colas dinámicas de máquinas virtuales (VMMQ dinámico o d.VMMQ)
- Acceso directo a memoria remota (RDMA)
- RDMA de invitado
- Formación de equipos insertada en el conmutador (SET)

### <a name="dynamic-vmmq"></a>VMMQ dinámico

Todos los adaptadores de red con AQ Premium admiten VMMQ dinámico. VMMQ dinámico requiere el uso de la funcionalidad Formación de equipos insertada en el conmutador.

**Tipos de tráfico aplicables**: proceso

**Certificaciones necesarias**: Premium

VMMQ dinámico es una tecnología de recepción inteligente que se basa en sus predecesores Cola de máquinas virtuales (VMQ), Ajuste de escala de recepción virtual (vRSS) y VMMQ para proporcionar tres mejoras principales:

- Optimiza la eficiencia del host mediante el uso de núcleos de CPU.
- Ajuste automático del procesamiento del tráfico de red a núcleos de CPU, lo que permite a las máquinas virtuales cumplir y mantener el rendimiento esperado.
- Permite que las cargas de trabajo "en ráfagas" reciban la cantidad esperada de tráfico.

Para más información sobre VMMQ dinámico, consulte la entrada de blog [Aceleraciones sintéticas](https://techcommunity.microsoft.com/t5/networking-blog/synthetic-accelerations-in-a-nutshell-windows-server-2019/ba-p/653976).

### <a name="rdma"></a>RDMA

RDMA es una descarga de la pila de red al adaptador de red que permite que el tráfico de almacenamiento SMB omita el sistema operativo para su procesamiento.

RDMA habilita redes de alto rendimiento y baja latencia mientras usa recursos mínimos de CPU del host. Estos recursos de CPU del host se pueden usar para ejecutar máquinas virtuales o contenedores adicionales.

**Tipos de tráfico aplicables**: almacenamiento del host

**Certificaciones necesarias**: Estándar

Todos los adaptadores con AQ estándar o Premium admiten RDMA (Acceso directo a memoria remota). RDMA es la opción de implementación recomendada para las cargas de trabajo de almacenamiento en Azure Stack HCI y se puede habilitar opcionalmente para las cargas de trabajo de almacenamiento (mediante SMB) de las máquinas virtuales. Consulte la sección **RDMA de invitado** más adelante.

Azure Stack HCI admite RDMA mediante las implementaciones del protocolo iWARP o RoCE.

> [!IMPORTANT]
> Los adaptadores RDMA solo funcionan con otros adaptadores RDMA que implementan el mismo protocolo RDMA (iWARP o RoCE).

No todos los adaptadores de red de los proveedores admiten RDMA. En la tabla siguiente se enumeran los proveedores (en orden alfabético) que ofrecen adaptadores RDMA certificados como Premium. Sin embargo, hay proveedores de hardware no incluidos en esta lista que también admiten RDMA. Consulte [Catálogo de Windows Server](https://www.windowsservercatalog.com/) para comprobar la compatibilidad con RDMA.

> [!NOTE]
> InfiniBand (IB) no es compatible con Azure Stack HCI.

|Proveedor de NIC|iWARP|RoCE|
|----|----|----|
|Broadcom|No|Sí|
|Chelsio|Sí|No|
|Intel|Sí|Sí (algunos modelos)|
|Marvell (QLogic/Cavium)|Sí|Sí|
|Nvidia (Mellanox)|No|Sí|

> [!NOTE]
> No todos los adaptadores de los proveedores admiten RDMA. Compruebe la compatibilidad con RDMA con el proveedor específico de su tarjeta de red.

Para más información sobre la implementación de RDMA, descargue el documento de Word del [Repositorio de GitHub de SDN](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

#### <a name="internet-wide-area-rdma-protocol-iwarp"></a>Protocolo RDMA de área extensa de Internet (iWARP)

iWARP usa el Protocolo de control de transmisión (TCP) y se puede mejorar opcionalmente con el Control de flujo basado en prioridades (PFC) y el Servicio de transmisión mejorado (ETS) de Data Center Bridging (DCB).

Se recomienda usar iWARP si:

- Tiene poca o ninguna experiencia con redes o no está familiarizado con la administración de conmutadores de red.
- No tiene control sobre los conmutadores ToR.
- No va a administrar la solución después de la implementación.
- Ya tiene implementaciones con iWARP.
- No está seguro de qué opción elegir.

#### <a name="rdma-over-converged-ethernet-roce"></a>RDMA sobre Ethernet convergente (RoCE)

RoCE usa el Protocolo de datagramas de usuario (UDP) y requiere PFC y ETS de Data Center Bridging para proporcionar confiabilidad.

Se recomienda usar RoCE si:

- Ya tiene implementaciones con RoCE en el centro de datos.
- Conoce los requisitos de red física.

### <a name="guest-rdma"></a>RDMA de invitado

RDMA de invitado permite que las cargas de trabajo de SMB de las máquinas virtuales disfruten de las mismas ventajas que con el uso de RDMA en los hosts.

**Tipos de tráfico aplicables**: proceso

**Certificaciones necesarias**: Premium

 Las principales ventajas de usar RDMA de invitado son:

- Descarga del procesamiento del tráfico de red de la CPU a la NIC
- Latencia extremadamente baja
- Capacidad de proceso elevada

Para más información, incluida la relativa a la implementación de RDMA de invitado, descargue el documento de Word del [Repositorio de GitHub de SDN](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

### <a name="switch-embedded-teaming-set"></a>Formación de equipos insertada en el conmutador (SET)

Formación de equipos insertada en el conmutador (SET) es una tecnología de formación de equipos basada en software que se ha incluido en el sistema operativo Windows Server desde Windows Server 2016. SET no depende del tipo de adaptadores de red utilizados.

**Tipos de tráfico aplicables**: proceso, almacenamiento y administración

**Certificaciones necesarias:** ninguna (integrada en el sistema operativo)

SET es la única tecnología de formación de equipos admitida por Azure Stack HCI. Equilibrio de carga y conmutación por error (LBFO) es otra tecnología de formación de equipos que se usa habitualmente con Windows Server, pero no se admite con Azure Stack HCI. Consulte la entrada de blog [Formación de equipos en Azure Stack HCI](https://techcommunity.microsoft.com/t5/networking-blog/teaming-in-azure-stack-hci/ba-p/1070642) para más información sobre LBFO en Azure Stack HCI. SET funciona bien con el tráfico de proceso, almacenamiento y administración.

SET es importante para Azure Stack HCI, ya que es la única tecnología de formación de equipos que permite:

- Formación de equipos de adaptadores RDMA (si es necesario)
- RDMA de invitado
- VMMQ dinámico
- Otras características principales de Azure Stack HCI (consulte [Formación de equipos en Azure Stack HCI](https://techcommunity.microsoft.com/t5/networking-blog/teaming-in-azure-stack-hci/ba-p/1070642)).

SET proporciona características adicionales con respecto a LBFO, incluidas las mejoras de calidad y rendimiento. Para ello, SET requiere el uso de adaptadores simétricos (idénticos): no se admite la formación de equipos de adaptadores asimétricos. Los adaptadores de red simétricos son los que tienen los mismos valores de:

- marca (proveedor)
- modelo (versión)
- velocidad (rendimiento)
- configuración

La manera más sencilla de identificar si los adaptadores son simétricos es comprobar si las velocidades son las mismas y las descripciones de la interfaz coinciden. Solo se admite una diferencia en el número que aparece en la descripción. Use el cmdlet [`Get-NetAdapterAdvancedProperty`](/powershell/module/netadapter/get-netadapteradvancedproperty) para asegurarse de que la configuración que se indica muestra los mismos valores de propiedad.

Consulte la tabla siguiente para obtener un ejemplo de las descripciones de interfaz que solo se diferencian el número (#):

|Nombre|Descripción de interfaz|Velocidad del vínculo|
|----|----|----|
|NIC1|Network Adapter #1|25 Gbps|
|NIC2|Network Adapter #2|25 Gbps|
|NIC3|Network Adapter #3|25 Gbps|
|NIC4|Network Adapter #4|25 Gbps|

### <a name="rdma-traffic-considerations"></a>Consideraciones sobre el tráfico RDMA

Si implementa Data Center Bridging (DCB), debe asegurarse de que la configuración de PFC y ETS se implementa correctamente en todos los puertos de red, incluidos los conmutadores de red. DCB es obligatorio para RoCE y opcional para iWARP.

Para obtener información detallada sobre cómo implementar RDMA, descargue el documento de Word del [Repositorio de GitHub de SDN](https://github.com/Microsoft/SDN/blob/master/Diagnostics/S2D%20WS2016_ConvergedNIC_Configuration.docx).

Las implementaciones de Azure Stack HCI basadas en RoCE requieren la configuración de tres clases de tráfico PFC, incluida la clase de tráfico predeterminada, en el tejido y en todos los hosts:

#### <a name="cluster-traffic-class"></a>Clase de tráfico del clúster

Esta clase de tráfico garantiza que haya suficiente ancho de banda reservado para los latidos de clúster:

- Requerido: sí
- PFC habilitado: No
- Prioridad de tráfico recomendada: Prioridad 7
- Reserva de ancho de banda recomendada:
    - Redes RDMA de 10 GbE o menos = 2 %
    - Redes RDMA de 25 GbE o más = 1 %

#### <a name="rdma-traffic-class"></a>Clase de tráfico RDMA

Esta clase de tráfico garantiza que haya suficiente ancho de banda reservado para las comunicaciones de RDA sin pérdida mediante SMB directo:

- Requerido: sí
- PFC habilitado: Sí
- Prioridad de tráfico recomendada: Prioridad 3 o 4
- Reserva de ancho de banda recomendada: 50 %

#### <a name="default-traffic-class"></a>Clase de tráfico predeterminada

Esta clase de tráfico incluye el resto del tráfico no definido en el clúster o las clases de tráfico RDMA, incluido el tráfico de las máquinas virtuales y el tráfico de administración:

- Requerido: predeterminado (no se necesita ninguna configuración en el host)
- Control de flujo (PFC) habilitado: No
- Clase de tráfico recomendada: predeterminada (Prioridad 0)
- Reserva de ancho de banda recomendada: predeterminada (no se requiere configuración del host)

## <a name="storage-traffic-models"></a>Modelos de tráfico de almacenamiento

SMB ofrece muchas ventajas como protocolo de almacenamiento para Azure Stack HCI, incluido SMB multicanal. Aunque SMB multicanal está fuera del ámbito de este tema, es importante comprender que el tráfico se multiplexa en cada vínculo posible que pueda usar SMB multicanal.

> [!NOTE]
>Se recomienda usar varias subredes y VLAN para separar el tráfico de almacenamiento en Azure Stack HCI.

Considere el ejemplo siguiente de un clúster de cuatro nodos. Cada servidor tiene dos puertos de almacenamiento (lado izquierdo y derecho). Dado que cada adaptador se encuentra en la misma subred y VLAN, SMB multicanal distribuirá las conexiones entre todos los vínculos disponibles. Por lo tanto, el puerto del lado izquierdo del primer servidor (192.168.1.1) establecerá una conexión con el puerto del lado izquierdo del segundo servidor (192.168.1.2). El puerto del lado derecho del primer servidor (192.168.1.12) se conectará al puerto del lado derecho del segundo servidor. Se establecen conexiones similares para el tercer y cuarto servidor.

Sin embargo, esto crea conexiones innecesarias y produce congestión en el vínculo interno (grupo de agregación de vínculos de varios chasis o MC-LAG) que conecta los conmutadores de la parte superior del rack (ToR) (marcados con X). Observe el diagrama siguiente:

:::image type="content" source="media/plan-networking/four-node-cluster-1.png" alt-text="Clúster de cuatro nodos en la misma subred" lightbox="media/plan-networking/four-node-cluster-1.png":::

El enfoque recomendado es usar subredes y VLAN independientes para cada conjunto de adaptadores. En el diagrama siguiente, los puertos de la derecha ahora usan la subred 192.168.2.x/24 y la VLAN2. Esto permite que el tráfico de los puertos de la izquierda permanezca en TOR1 y que el tráfico de los puertos del lado derecho permanezca en TOR2. Observe el diagrama siguiente:

:::image type="content" source="media/plan-networking/four-node-cluster-2.png" alt-text="Clúster de cuatro nodos en subredes diferentes" lightbox="media/plan-networking/four-node-cluster-2.png":::

## <a name="traffic-bandwidth-allocation"></a>Asignación de ancho de banda para el tráfico

En la tabla siguiente se muestran asignaciones de ancho de banda de ejemplo de varios tipos de tráfico, con velocidades de adaptador comunes, en Azure Stack HCI. Tenga en cuenta que se trata de un ejemplo de una solución convergente en la que todos los tipos de tráfico (proceso, almacenamiento y administración) se ejecutan en los mismos adaptadores físicos y la formación de equipos se realiza mediante SET.

Dado que este caso de uso supone la mayoría de las restricciones, representa una buena línea de base. Sin embargo, si se tienen en cuenta las permutaciones de número de adaptadores y velocidades, se debe considerar un ejemplo y no un requisito auxiliar.

En este ejemplo se realizan las suposiciones siguientes:

- Hay dos adaptadores por equipo.
- Tráfico de Capa de bus de almacenamiento (SBL), Volumen compartido de clúster (CSV) e Hyper-V (Migración en vivo):

    - Se usan los mismos adaptadores físicos.
    - Se usa SMB.
- SMB recibe una asignación de ancho de banda del 50 % con Data Center Bridging.
    - El tráfico de SBL y CSV es el tráfico de prioridad más alta y recibe el 70 % de la reserva de ancho de banda de SMB y:
    - Migración en vivo (LM) está limitado mediante el cmdlet `Set-SMBBandwidthLimit` y recibe el 29 % del ancho de banda restante.
        - Si el ancho de banda disponible para Migración en vivo es > = 5 Gbps y los adaptadores de red son compatibles, use RDMA. Para ello, use el siguiente cmdlet:

            ```Powershell
            Set-VMHost VirtualMachineMigrationPerformanceOption SMB
            ```

        - Si el ancho de banda disponible para Migración en vivo es < 5 Gbps, use la compresión para reducir los tiempos de cortes. Para ello, use el siguiente cmdlet:

            ```Powershell
            Set-VMHost -VirtualMachineMigrationPerformanceOption Compression
            ```

 - Si usa RDMA con Migración en vivo, asegúrese de que el tráfico de Migración en vivo no puede consumir todo el ancho de banda asignado a la clase de tráfico RDMA mediante un límite de ancho de banda de SMB. Tenga cuidado, ya que este cmdlet toma la entrada en bytes por segundo (Bps), mientras que los adaptadores de red se muestran en bits por segundo (bps). Por ejemplo, use el siguiente cmdlet para establecer un límite de ancho de banda de 6 Gbps:

    ```Powershell
    Set-SMBBandwidthLimit -Category LiveMigration -BytesPerSecond 750MB
    ```

    > [!NOTE]
    >En este ejemplo, 750 MBps equivalen a 6 Gbps.

Esta es la tabla de asignación de ancho de banda de ejemplo:

|Velocidad de NIC|Ancho de banda agrupado|Reserva de ancho de banda para SMB**|SBL/CSV %|Ancho de banda de SBL/CSV|% para Migración en vivo|Ancho de banda máximo para Migración en vivo|% para latidos|Ancho de banda para latidos|
|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|-----|
|10 Gbps|20 Gbps|10 Gbps|70%|7 Gbps|*|*|2 %|200 Mbps|
|25 Gbps|50 Gbps|25 Gbps|70%|17,5 Gbps|29%|7,25 Gbps|1 %|250 MBps|
|40 Gbps|80 Gbps|40 Gbps|70%|28 Gbps|29%|11,6 Gbps|1 %|400 MBps|
|50 Gbps|100 Gbps|50 Gbps|70%|35 Gbps|29%|14,5 Gbps|1 %|500 Mbps|
|100 Gbps|200 Gbps|100 Gbps|70%|70 Gbps|29%|29 Gbps|1 %|1 Gbps|
|200 Gbps|400 Gbps|200 Gbps|70%|140 Gbps|29%|58 Gbps|1 %|2 Gbps|

*: se debe usar compresión en lugar de RDMA, ya que la asignación de ancho de banda para el tráfico de Migración en vivo es < 5 Gbps.

**: el porcentaje del 50 % es un ejemplo de reserva de ancho de banda para este ejemplo.

## <a name="stretched-cluster-considerations"></a>Consideraciones sobre clústeres extendidos

Los clústeres extendidos proporcionan recuperación ante desastres que abarca varios centros de datos. En su forma más sencilla, una red de clústeres extendidos de Azure Stack HCI tiene el siguiente aspecto:

:::image type="content" source="media/plan-networking/stretched-cluster.png" alt-text="Clúster extendido" lightbox="media/plan-networking/stretched-cluster.png":::

Los clústeres extendidos tienen los siguientes requisitos y características:

- RDMA está limitado a un único sitio y no se admite entre diferentes sitios o subredes.
- Los servidores del mismo sitio deben residir en el mismo bastidor y en el mismo límite de capa 2.
- La comunicación entre sitios atraviesa un límite de capa 3; no se admiten las topologías de capa 2 extendidas.

- Si un sitio usa RDMA para sus adaptadores de almacenamiento, estos adaptadores deben estar en una subred y VLAN independientes que no puedan enrutar entre sitios. Esto evita que la réplica de almacenamiento use RDMA entre sitios.
- Los adaptadores usados para la comunicación entre sitios:

    - Pueden ser físicos o virtuales (vNIC de host). Si es virtual, debe aprovisionar una vNIC en su propia subred y VLAN por cada NIC física.
    - Deben estar en su propia subred y VLAN, que pueden enrutar entre sitios.
    - Se debe deshabilitar RDMA mediante el cmdlet `Disable-NetAdapterRDMA`. Se recomienda requerir explícitamente que la réplica de almacenamiento use interfaces específicas con el cmdlet `Set-SRNetworkConstraint`.
    - Debe cumplir los requisitos adicionales para la réplica de almacenamiento.
-   En el caso de una conmutación por error a otro sitio, debe asegurarse de que hay suficiente ancho de banda disponible para ejecutar las cargas de trabajo en el otro sitio. Una opción segura es aprovisionar los sitios con el 50% de su capacidad disponible. Esto no es un requisito difícil si se puede tolerar un rendimiento menor durante una conmutación por error.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga información acerca de los requisitos del conmutador de red y de la red física. Consulte [Requisitos de red física para Azure Stack HCI](physical-network-requirements.md).
- Repase las aspectos básicos de los clústeres de conmutación por error. Consulte [Aspectos básicos de redes de clústeres de conmutación por error](https://techcommunity.microsoft.com/t5/failover-clustering/failover-clustering-networking-basics-and-fundamentals/ba-p/1706005?s=09)
- Repase el uso de SET. Consulte [Acceso directo a memoria remota (RDMA) y Switch Embedded Teaming (SET)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).
- Para la implementación, consulte [Creación de un clúster con Windows Admin Center](../deploy/create-cluster.md).
- Para la implementación, consulte [Creación de un clúster con Windows PowerShell](../deploy/create-cluster-powershell.md).