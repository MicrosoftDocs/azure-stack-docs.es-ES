---
title: Antes de implementar Azure Stack HCI
description: Preparación de la implementación de Azure Stack HCI
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/03/2020
ms.openlocfilehash: b780dad569f1f2bdb2488505ecb2e12b0aaf2b3f
ms.sourcegitcommit: 4af79f4fa2598d57c81e994192c10f8c6be5a445
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/10/2020
ms.locfileid: "89742191"
---
# <a name="before-you-deploy-azure-stack-hci"></a>Antes de implementar Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2

En esta guía paso a paso, aprenderá a:

- Determinar si el hardware cumple los requisitos básicos de los clústeres estándar (un sitio) o extendidos (dos sitios) de Azure Stack HCI
- Asegurarse de no superar las especificaciones de hardware máximas admitidas
- Recopilar la información necesaria para una implementación correcta
- Instalación de Windows Admin Center en un equipo o un servidor de administración

## <a name="determine-hardware-requirements"></a>Determinar los requisitos de hardware

Microsoft recomienda adquirir una solución de hardware o software de Azure Stack HCI validada de nuestros asociados. Estas soluciones se diseñan, se ensamblan y se validan con nuestra arquitectura de referencia para garantizar la compatibilidad y la confiabilidad, de modo que pueda empezar a utilizarlas rápidamente. Compruebe que los sistemas, componentes, dispositivos y controladores que usa estén certificados para Windows Server 2019 según el catálogo de Windows Server. Visite el sitio web de [soluciones de Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) para obtener soluciones validadas.

### <a name="server-requirements"></a>Requisitos del servidor

- Un clúster estándar de Azure Stack HCI requiere un mínimo de dos servidores y un máximo de 16; sin embargo, los clústeres se pueden combinar mediante conjuntos de clústeres para crear una plataforma HCI de cientos de nodos.
- Los clústeres extendidos requieren la implementación de servidores en dos sitios independientes. Los sitios pueden estar en países, ciudades, plantas o salas diferentes. Un clúster extendido requiere un mínimo de cuatro servidores (dos por sitio) y un máximo de 16 (ocho por sitio).
- Se recomienda que todos los servidores sean del mismo fabricante y modelo, usen un procesador Intel Nehalem de 64 bits, tipo AMD EPYC o procesadores posteriores compatibles con traducción de direcciones de segundo nivel (SLAT). Se requiere un procesador escalable Intel Xeon de segunda generación para admitir la memoria persistente de Intel Optane DC. Los procesadores deben ser de al menos 1,4 GHz y admitir el conjunto de instrucciones de x64.
- Asegúrese de que los servidores estén equipados con al menos 32 GB de RAM por nodo para acomodar el sistema operativo del servidor, las máquinas virtuales y otras aplicaciones o cargas de trabajo. Además, reserve 4 GB de RAM por terabyte (TB) de capacidad de la unidad de memoria caché en cada servidor para los metadatos de Espacios de almacenamiento directo.
- Compruebe que la compatibilidad con la virtualización esté activada en el BIOS o UEFI:
    - Virtualización asistida por hardware. Está disponible en procesadores que incluyen una opción de virtualización; concretamente, procesadores con la tecnología de virtualización Intel (Intel VT) o de virtualización AMD (AMD-V).
    - La prevención de ejecución de datos (DEP) mediante hardware tiene que estar disponible y habilitada. En los sistemas Intel, es el bit XD (bit para deshabilitar la ejecución). En los sistemas AMD, es el bit NX (bit de no ejecución).
- Puede usar cualquier dispositivo de arranque compatible con Windows Server, que [incluye ahora SATADOM](https://cloudblogs.microsoft.com/windowsserver/2017/08/30/announcing-support-for-satadom-boot-drives-in-windows-server-2016/). El reflejo RAID 1 **no** es necesario, pero se admite para el arranque. Se recomienda un tamaño mínimo de 200 GB.
- Para conocer los requisitos adicionales específicos de las características de Hyper-V, consulte [requisitos del sistema para Hyper-V en Windows Server](/windows-server/virtualization/hyper-v/system-requirements-for-hyper-v-on-windows).

### <a name="networking-requirements"></a>Requisitos de red

Un clúster de Azure Stack HCI requiere una conexión de red confiable de baja latencia y gran ancho de banda entre cada nodo de servidor. Debe comprobar lo siguiente:

- Compruebe que, al menos, un adaptador de red está disponible y dedicado para la administración del clúster.
- Compruebe que los conmutadores físicos de la red estén configurados para permitir el tráfico en cualquier VLAN que utilice.


Hay varios tipos de comunicación entre los nodos de servidor:

- Comunicación del clúster (combinaciones de nodos, actualizaciones de clúster, actualizaciones del registro)
- Latidos de clúster
- Tráfico redirigido del volumen compartido de clúster (CSV)
- Tráfico de migración en vivo para máquinas virtuales

Con Espacios de almacenamiento directo, se debe tener en cuenta el tráfico de red adicional:

- Capa de bus de almacenamiento (SBL): extensiones, o datos, que se distribuyen entre los nodos
- Estado: supervisión y administración de objetos (nodos, unidades, tarjetas de red, servicio de clúster)

En el caso de los clústeres extendidos, también hay tráfico adicional de réplica de almacenamiento que fluye entre los sitios. El tráfico de la capa de bus de almacenamiento (SBL) y el del volumen compartido de clúster (CSV) no se dirige entre sitios, solo entre los nodos de servidor dentro de cada sitio.

### <a name="domain-requirements"></a>Requisitos del dominio

No hay ningún requisito especial de nivel funcional de dominio para Azure Stack HCI, solo una versión del sistema operativo para el controlador de dominio que todavía se admite. Se recomienda activar la característica de papelera de reciclaje de Active Directory como procedimiento recomendado general, si no lo ha hecho ya.

### <a name="interconnect-requirements-between-nodes"></a>Requisitos de interconexión entre nodos

En esta sección se describen los requisitos de red específicos entre los nodos de servidor de un sitio, denominados interconexiones. Se pueden usar las interconexiones de nodos con conmutador o sin conmutador y se admiten:

- **Con conmutador:** los nodos de servidor suelen conectarse entre sí a través de redes Ethernet que usan conmutadores de red. Los conmutadores deben estar configurados correctamente para administrar el ancho de banda y el tipo de red. Si usa RDMA que implementa el protocolo RoCE, es importante el dispositivo de red y la configuración del conmutador.
- **Sin conmutador:** los nodos de servidor también se pueden interconectar mediante conexiones Ethernet directas sin un conmutador. En este caso, cada nodo de servidor debe tener una conexión directa con todos los demás nodos del clúster en el mismo sitio.

#### <a name="interconnects-for-2-3-node-clusters"></a>Interconexiones para clústeres de dos o tres nodos

Estos son los requisitos *mínimos* de interconexión para los clústeres de sitio único que tienen dos o tres nodos. Se aplican a cada nodo de servidor:

- Una o más tarjetas de adaptador de red de 1 GB que se usarán para las funciones de administración
- Una o más tarjetas de interfaz de red de 10 GB (o superior) para el tráfico de almacenamiento y carga de trabajo
- Se recomiendan dos o más conexiones de red entre cada nodo para la redundancia y el rendimiento

#### <a name="interconnects-for-4-node-and-greater-clusters"></a>Interconexiones para clústeres de cuatro o más nodos

Estos son los requisitos *mínimos* de interconexión para los clústeres que tienen cuatro o más nodos, y para los clústeres de alto rendimiento. Se aplican a cada nodo de servidor:

- Una o más tarjetas de adaptador de red de 1 GB que se usarán para las funciones de administración.
- Una o más tarjetas de interfaz de red de 25 GB (o superior) para el tráfico de almacenamiento y carga de trabajo. Se recomiendan dos o más conexiones de red para la redundancia y el rendimiento.
- Tarjetas de red que son compatibles con el acceso directo a memoria remota (RDMA): iWARP (opción recomendada) o RoCE.

### <a name="site-to-site-requirements-stretched-cluster"></a>Requisitos de sitio a sitio (clúster extendido)

Cuando se conectan sitios de clústeres extendidos, siguen siendo aplicables los requisitos de interconexión de cada sitio y tienen requisitos adicionales de tráfico de migración en vivo de la réplica de almacenamiento y de Hyper-V que deben tenerse en cuenta:

- Al menos una conexión RDMA o Ethernet/TCP de 1 GB entre sitios para la replicación sincrónica. Se prefiere una conexión RDMA de 25 GB.
- Una red entre sitios con suficiente ancho de banda para contener la carga de trabajo de escritura de E/S y un promedio de latencia de ida y vuelta de 5 ms para la replicación sincrónica. Este tipo de replicación no tiene una recomendación de latencia.
- Si usa una única conexión entre sitios, establezca límites de ancho de banda SMB para la réplica de almacenamiento mediante PowerShell. Para más información, consulte [Set-SmbBandwidthLimit](/powershell/module/smbshare/set-smbbandwidthlimit?view=win10-ps).
- Si usa varias conexiones entre sitios, separe el tráfico entre las conexiones. Por ejemplo, coloque el tráfico de la réplica de almacenamiento en una red independiente distinta a la del tráfico de migración en vivo de Hyper-V con PowerShell. Para más información, consulte [Set-SRNetworkConstraint](/powershell/module/storagereplica/set-srnetworkconstraint?view=win10-ps).

### <a name="network-port-requirements"></a>Requisitos de puerto de red

Asegúrese de que los puertos de red adecuados estén abiertos entre todos los nodos de servidor de un sitio y entre sitios (para clústeres extendidos). Necesitará reglas adecuadas de firewall y de enrutador para permitir ICMP, SMB (puerto 445, más el puerto 5445 para SMB directo) y el tráfico bidireccional WS-MAN (puerto 5985) entre todos los servidores del clúster.

Al usar el Asistente para creación de clústeres en Windows Admin Center para crear el clúster, el Asistente abre automáticamente los puertos de firewall adecuados en cada servidor del clúster para los clústeres de conmutación por error, Hyper-V y réplica de almacenamiento. Si usa un firewall de software diferente en cada servidor, abra los puertos siguientes:

#### <a name="failover-clustering-ports"></a>Puertos de clústeres de conmutación por error

- ICMPv4 e ICMPv6
- Puerto TCP 445
- Puertos dinámicos RPC
- Puerto TCP 135
- Puerto TCP 137
- Puerto TCP 3343
- Puerto UDP 3343

#### <a name="hyper-v-ports"></a>Puertos de Hyper-V

- Puerto TCP 135
- Puerto TCP 80 (conectividad HTTP)
- Puerto TCP 443 (conectividad HTTPS)
- Puerto TCP 6600
- Puerto TCP 2179
- Puertos dinámicos RPC
- Asignador de puntos de conexión de RPC
- Puerto TCP 445

#### <a name="storage-replica-ports-stretched-cluster"></a>Puertos de réplica de almacenamiento (clúster extendido)

- Puerto TCP 445
- TCP 5445 (si se usa iWarp RDMA)
- Puerto TCP 5985
- ICMPv4 e ICMPv6 (si se usa Test-SRTopology)

Puede que se requieran puertos adicionales no mencionados antes. Estos son los puertos para la funcionalidad básica de Azure Stack HCI.

### <a name="storage-requirements"></a>Requisitos de almacenamiento

- Azure Stack HCI funciona con unidades SATA, SAS o NVMe de conexión directa o unidades de memoria persistente, cada una de ellas conectada directamente a un solo servidor.
- Cada servidor del clúster debe tener los mismos tipos de unidades y el mismo número de cada tipo. También se recomienda (pero no es obligatorio) que las unidades tengan el mismo tamaño y modelo. Las unidades pueden ser internas en el servidor o en un contenedor externo que esté conectado a un solo servidor. Para más información, consulte [Consideraciones sobre la simetría de unidades](../concepts/drive-symmetry-considerations.md).
- Cada servidor del clúster debe tener volúmenes dedicados para los registros, con un almacenamiento de registros al menos tan rápido como el almacenamiento de datos. Los clústeres extendidos requieren al menos dos volúmenes: uno para los datos replicados y otro para los datos de registro.
- Se requiere SCSI Enclosure Services (SES) para la asignación e identificación de ranuras. Cada contenedor externo debe presentar un identificador único. **NO SE ADMITEN:** Tarjetas controladoras RAID o almacenamiento SAN (Canal de fibra, iSCSI, FCoE), alojamientos compartidos SAS conectados a varios servidores o cualquier forma de E/S de múltiples rutas (MPIO) donde se pueda acceder a las unidades mediante varias rutas de acceso. Las tarjetas adaptadoras de bus host (HBA) deben implementar el modo de paso a través simple.
- Para más ayuda, consulte el tema [Elección de unidades para Espacios de almacenamiento directo](../concepts/choose-drives.md) o [Requisitos de hardware de Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements).
- Para ver las opciones disponibles para volúmenes y resistencia, consulte el tema [Planeación de volúmenes en Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type).
- Si también va a implementar máquinas virtuales y almacenamiento virtualizado, consulte [Uso de Espacios de almacenamiento directo en clústeres de máquinas virtuales invitadas](/windows-server/storage/storage-spaces/storage-spaces-direct-in-vm).

## <a name="review-maximum-supported-hardware-specifications"></a>Revisión de las especificaciones de hardware máximas admitidas

No se admiten las implementaciones de Azure Stack HCI que no cumplan las siguientes especificaciones:

| Recurso                     | Máxima |
| ---------------------------- | --------|
| Servidores físicos por clúster | 16      |
| Máquinas virtuales por host                 | 1024   |
| Discos por máquina virtual (SCSI)          | 256     |
| Almacenamiento por clúster          | 4 PB    |
| Almacenamiento por servidor           | 400 TB  |
| Procesadores lógicos por host  | 512     |
| RAM por host                 | 24 TB   |
| RAM por máquina virtual                   | 12 TB (máquinas virtuales de segunda generación) o 1 TB (primera generación)|
| Procesadores virtuales por host  | 2 048   |
| Procesadores virtuales por máquina virtual    | 240 (máquinas virtuales de segunda generación) o 64 (primera generación)|

## <a name="gather-information"></a>Recopilación de información

Para prepararse para la implementación, recopile los detalles siguientes sobre su entorno:

- **Nombres del servidor:** Familiarícese con las directivas de nomenclatura de la organización para equipos, archivos, rutas de acceso y otros recursos. Deberá aprovisionar varios servidores, cada uno con nombres únicos.
- **Nombre de dominio:** Familiarícese con las directivas de la organización para la nomenclatura de dominios y de unión a un dominio. Unirá los servidores al dominio y tendrá que especificar el nombre de dominio.
- **Direcciones IP estáticas:** Azure Stack HCI requiere direcciones IP estáticas para el tráfico de almacenamiento y de carga de trabajo (máquina virtual) y no admite la asignación de direcciones IP dinámicas a través de DHCP para esta red de alta velocidad. Puede usar DHCP para el adaptador de red de administración a menos que esté usando dos en un equipo, en cuyo caso volverá a necesitar el uso de direcciones IP estáticas. Consulte con el administrador de red la dirección IP que debe usar para cada servidor del clúster.
- **Redes RDMA:** Hay dos tipos de protocolos RDMA: iWarp y RoCE. Observe cuál usa su adaptador de red y, si es RoCE, tenga en cuenta también la versión (v1 o v2). En el caso de RoCE, tenga en cuenta también el modelo del conmutador de la parte superior del rack.
- **IDENTIFICADOR DE VLAN:** Tenga en cuenta el identificador de VLAN que se usará para los adaptadores de red en los servidores, si los hay. Debe poder obtenerlo desde el administrador de red.
- **Nombres de sitio:** En el caso de los clústeres extendidos, se usan dos sitios para la recuperación ante desastres. Puede configurar los sitios mediante Active Directory Domain Services o puede dejar que el asistente para crear clústeres los configure automáticamente. Consulte al administrador del dominio sobre la configuración de los sitios. Para más información, consulte [Introducción a Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="install-windows-admin-center"></a>Instalación de Windows Admin Center

Windows Admin Center es una aplicación implementada localmente y basada en un explorador para la administración de Azure Stack HCI. La manera más sencilla de [instalar Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) es en un equipo de administración local (modo de escritorio), aunque también puede instalarlo en un servidor (modo de servicio).

Si instala Windows Admin Center en un servidor, las tareas que requieren CredSSP, como la creación de clústeres y la instalación de actualizaciones y extensiones, requieren también el uso de una cuenta que sea miembro del grupo de administradores de puerta de enlace en el servidor de Windows Admin Center. Para más información, consulte las dos primeras secciones de [Configuración de los permisos y el control de acceso de usuarios](/windows-server/manage/windows-admin-center/configure/user-access-control#gateway-access-role-definitions).

## <a name="next-steps"></a>Pasos siguientes

Continúe con el siguiente artículo para obtener información sobre cómo implementar el sistema operativo Azure Stack HCI.
> [!div class="nextstepaction"]
> [Implementación del sistema operativo Azure Stack HCI](operating-system.md)
