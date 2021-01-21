---
title: Requisitos del sistema para Azure Stack HCI
description: Cómo elegir los servidores, el almacenamiento y los componentes de red para Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 01/14/2021
ms.openlocfilehash: 3b29dfb35bad91ef02feebc22255b89116da1505
ms.sourcegitcommit: 8526f642ef859b0006c3991d966f93608a87288a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/15/2021
ms.locfileid: "98243466"
---
# <a name="system-requirements-for-azure-stack-hci"></a>Requisitos del sistema para Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2

Use este tema para evaluar los requisitos del sistema para servidores, almacenamiento y redes para Azure Stack HCI.

## <a name="server-requirements"></a>Requisitos del servidor

Un clúster estándar de Azure Stack HCI requiere un mínimo de dos servidores y un máximo de 16; sin embargo, los clústeres se pueden combinar mediante conjuntos de clústeres para crear una plataforma HCI de cientos de nodos.

Tenga en cuenta lo siguiente en los distintos tipos de implementaciones de Azure Stack HCI:

- Los clústeres extendidos requieren la implementación de servidores en dos sitios independientes. Los sitios pueden estar en países, ciudades, plantas o salas diferentes. Un clúster extendido requiere un mínimo de cuatro servidores (dos por sitio) y un máximo de 16 (ocho por sitio).

- Se recomienda que todos los servidores sean del mismo fabricante y modelo, usen un procesador Intel Nehalem de 64 bits, tipo AMD EPYC o procesadores posteriores compatibles con traducción de direcciones de segundo nivel (SLAT). Se requiere un procesador escalable Intel Xeon de segunda generación para admitir la memoria persistente de Intel Optane DC. Los procesadores deben ser de al menos 1,4 GHz y admitir el conjunto de instrucciones de x64.

- Asegúrese de que los servidores estén equipados con al menos 32 GB de RAM por nodo para acomodar el sistema operativo del servidor, las máquinas virtuales y otras aplicaciones o cargas de trabajo. Además, reserve 4 GB de RAM por terabyte (TB) de capacidad de la unidad de memoria caché en cada servidor para los metadatos de Espacios de almacenamiento directo.

- Compruebe que la compatibilidad con la virtualización esté activada en el BIOS o UEFI:
    - Virtualización asistida por hardware. Está disponible en procesadores que incluyen una opción de virtualización; concretamente, procesadores con la tecnología de virtualización Intel (Intel VT) o de virtualización AMD (AMD-V).
    - La prevención de ejecución de datos (DEP) mediante hardware tiene que estar disponible y habilitada. En los sistemas Intel, es el bit XD (bit para deshabilitar la ejecución). En los sistemas AMD, es el bit NX (bit de no ejecución).

- Puede usar cualquier dispositivo de arranque compatible con Windows Server, que [incluye ahora SATADOM](https://cloudblogs.microsoft.com/windowsserver/2017/08/30/announcing-support-for-satadom-boot-drives-in-windows-server-2016/). El reflejo RAID 1 **no** es necesario, pero se admite para el arranque. Se recomienda un tamaño mínimo de 200 GB.

- Para conocer los requisitos adicionales específicos de las características de Hyper-V, consulte [requisitos del sistema para Hyper-V en Windows Server](/windows-server/virtualization/hyper-v/system-requirements-for-hyper-v-on-windows).

## <a name="storage-requirements"></a>Requisitos de almacenamiento

Azure Stack HCI funciona con unidades SATA, SAS o NVMe de conexión directa o unidades de memoria persistente, cada una de ellas conectada directamente a un solo servidor.

Para obtener los mejores resultados, siga estas indicaciones:

- Cada servidor del clúster debe tener los mismos tipos de unidades y el mismo número de cada tipo. También se recomienda (pero no es obligatorio) que las unidades tengan el mismo tamaño y modelo. Las unidades pueden ser internas en el servidor o en un contenedor externo que esté conectado a un solo servidor. Para más información, consulte [Consideraciones sobre la simetría de unidades](drive-symmetry-considerations.md).

- Cada servidor del clúster debe tener volúmenes dedicados para los registros, con un almacenamiento de registros al menos tan rápido como el almacenamiento de datos. Los clústeres extendidos requieren al menos dos volúmenes: uno para los datos replicados y otro para los datos de registro.

- Se requiere SCSI Enclosure Services (SES) para la asignación e identificación de ranuras. Cada contenedor externo debe presentar un identificador único. 

   > [!IMPORTANT]
   > **NO SE ADMITEN:** Tarjetas controladoras RAID o almacenamiento SAN (Canal de fibra, iSCSI, FCoE), alojamientos compartidos SAS conectados a varios servidores o cualquier forma de E/S de múltiples rutas (MPIO) donde se pueda acceder a las unidades mediante varias rutas de acceso. Las tarjetas adaptadoras de bus host (HBA) deben implementar el modo de paso a través simple.

## <a name="networking-requirements"></a>Requisitos de red

Un clúster de Azure Stack HCI requiere una conexión de red confiable de baja latencia y gran ancho de banda entre cada nodo de servidor.

- Compruebe que, al menos, un adaptador de red está disponible y dedicado para la administración del clúster.
- Compruebe que los conmutadores físicos de la red estén configurados para permitir el tráfico en cualquier VLAN que utilice.

Para conocer los requisitos y las consideraciones sobre redes de host, consulte [Requisitos de red de host para Azure Stack HCI](host-network-requirements.md).

## <a name="software-defined-networking-sdn-requirements"></a>Requisitos de redes definidas por software (SDN)

Cuando crea un clúster de Azure Stack HCI mediante Windows Admin Center, tiene la opción de implementar Controlador de red para permitir las redes definidas por software. Si tiene previsto usar redes definidas por software en Azure Stack HCI:

- Asegúrese de que los servidores host tengan al menos 50-100 GB de espacio disponible para crear las máquinas virtuales de Controladora de red.

- Debe copiar un disco duro virtual del sistema operativo Azure Stack HCI en el primer nodo del clúster para crear las máquinas virtuales de Controladora de red. Puede preparar el disco duro virtual mediante [Sysprep](/windows-hardware/manufacture/desktop/sysprep-process-overview) o ejecutando el script [Convert-WindowsImage](https://gallery.technet.microsoft.com/scriptcenter/Convert-WindowsImageps1-0fe23a8f) para convertir un archivo .iso en un disco duro virtual.

Para más información acerca de cómo preparar el uso de redes definidas por software en Azure Stack HCI, consulte [Planeamiento de una infraestructura de red definida por software](plan-software-defined-networking-infrastructure.md) y [Planeamiento de la implementación de Controladora de red](../concepts/network-controller.md).

   > [!NOTE]
   > SDN no se admite en clústeres extendidos (multisitio).

### <a name="sdn-hardware-requirements"></a>Requisitos de hardware de SDN

En esta sección se proporcionan los requisitos de hardware de red para las NIC y los conmutadores físicos al planear un entorno de SDN.

#### <a name="network-interface-cards-nics"></a>Tarjetas de interfaz de red (NIC)

Las NIC que se usan en los hosts de Hyper-V y los hosts de almacenamiento requieren funcionalidades específicas para lograr el mejor rendimiento.

El acceso directo a memoria remota (RDMA) es una técnica de omisión de kernel que permite transferir grandes cantidades de datos sin usar la CPU del host, lo cual libera la CPU para realizar otros trabajos. Switch Embedded Teaming (SET) es una solución alternativa de formación de equipos de NIC que puede usar en entornos que incluyen Hyper-V y la pila de SDN. SET integra la funcionalidad de formación de equipos de NIC en el conmutador virtual de Hyper-V.

Para más información, consulte [Acceso directo a memoria remota (RDMA) y Switch Embedded Teaming (SET)](/windows-server/virtualization/hyper-v-virtual-switch/rdma-and-switch-embedded-teaming).

Para tener en cuenta la sobrecarga en el tráfico de red virtual del inquilino causada por los encabezados de encapsulación VXLAN o NVGRE, la unidad de transmisión máxima (MTU) del tejido de red del nivel 2 (conmutadores y hosts) debe establecerse en un valor mayor o igual a 1674 bytes \(incluidos los encabezados Ethernet de nivel 2\).

Las NIC que admiten la nueva palabra clave *EncapOverhead* de adaptador avanzado establecen la unidad de transmisión máxima automáticamente a través del agente de host de Controladora de red. Las NIC que no admiten la nueva palabra clave *EncapOverhead* deben establecer el tamaño de la unidad de transmisión máxima de forma manual en cada host físico mediante la palabra clave *JumboPacket* u otra palabra clave \(equivalente\).

#### <a name="switches-and-routers"></a>Conmutadores y enrutadores

Al seleccionar un conmutador físico y un enrutador para su entorno de SDN, asegúrese de que admite el siguiente conjunto de funcionalidades:
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

### <a name="domain-requirements"></a>Requisitos del dominio

No hay ningún requisito especial de nivel funcional de dominio para Azure Stack HCI, solo una versión del sistema operativo para el controlador de dominio que todavía se admite. Se recomienda activar la característica de papelera de reciclaje de Active Directory como procedimiento recomendado general, si no lo ha hecho ya. Para más información, consulte [Introducción a Active Directory Domain Services](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview).

## <a name="maximum-supported-hardware-specifications"></a>Especificaciones de hardware máximas admitidas

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

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Elegir las unidades](choose-drives.md)
- [Requisitos de hardware de Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/storage-spaces-direct-hardware-requirements)
