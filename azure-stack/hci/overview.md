---
title: Introducción a la solución Azure Stack HCI
description: Azure Stack HCI es una solución de clúster de infraestructura hiperconvergida (HCI) que hospeda cargas de trabajo de Windows y Linux y su almacenamiento en un entorno local híbrido. Los servicios híbridos de Azure mejoran el clúster con funcionalidades como la supervisión basada en la nube, Site Recovery y las copias de seguridad de máquinas virtuales, así como con una vista central de todas las implementaciones de Azure Stack HCI en Azure Portal.
ms.topic: overview
author: khdownie
ms.author: v-kedow
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 12/15/2020
ms.openlocfilehash: 6eef8388367bb1bba3f419fba5b61c54fd729743
ms.sourcegitcommit: 32d77de1a554315f53473407279e464a72aa9aa1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/15/2020
ms.locfileid: "97515010"
---
# <a name="azure-stack-hci-solution-overview"></a>Introducción a la solución Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2

Azure Stack HCI es una solución de clúster de infraestructura hiperconvergida (HCI) que hospeda cargas de trabajo de Windows y Linux y su almacenamiento en un entorno local híbrido. Los servicios híbridos de Azure mejoran el clúster con funcionalidades como la supervisión basada en la nube, Site Recovery y las copias de seguridad de máquinas virtuales, así como con una vista central de todas las implementaciones de Azure Stack HCI en Azure Portal. Puede administrar el clúster con sus herramientas ya existentes, como Windows Admin Center y PowerShell.

Azure Stack HCl, versión 20H2 ahora está [disponible para su descarga](https://azure.microsoft.com/products/azure-stack/hci/hci-download/). Está destinado a los clústeres locales que ejecutan cargas de trabajo virtualizadas, con conexiones de nube híbrida integradas. Como tal, Azure Stack HCI se ofrece como un servicio de Azure y se factura en una suscripción de Azure. Azure Stack HCI también incluye la posibilidad de hospedar Azure Kubernetes Service. Para más información, consulte [Azure Kubernetes Service en Azure Stack HCI](../aks-hci/overview.md).

Dedique unos minutos a ver el vídeo sobre las características de alto nivel de Azure Stack HCI:

> [!VIDEO https://www.youtube.com/embed/fw8RVqo9dcs]

En realidad, Azure Stack HCI es una solución que combina lo siguiente:

- Hardware validado de un fabricante de equipos originales asociado
- Sistema operativo de Azure Stack HCI
- Windows Admin Center
- Servicios de Azure

:::image type="content" source="media/overview/azure-stack-hci-solution.png" alt-text="El sistema operativo de Azure Stack HCI se ejecuta sobre el hardware validado, se administra mediante Windows Admin Center y se conecta a Azure" border="false":::.

Azure Stack HCI, versión 20H2, proporciona nuevas funcionalidades que no están presentes en Windows Server, como la posibilidad de usar Windows Admin Center para crear un clúster hiperconvergido que usa Espacios de almacenamiento directo para una mejor relación rendimiento/precio del almacenamiento. Esto incluye la opción de expandir el clúster para la conmutación automática por error entre sitios. Consulte [Novedades de Azure Stack HCI](#whats-new-in-azure-stack-hci) para más información.

## <a name="use-cases-for-azure-stack-hci"></a>Casos de uso de Azure Stack HCI

Hay muchos casos de uso de Azure Stack HCI, aunque no está diseñado para cargas de trabajo no virtualizadas. Los clientes suelen elegir Azure Stack HCI en los escenarios siguientes:

### <a name="data-center-consolidation-and-modernization"></a>Consolidación y modernización de centros de datos

La actualización y consolidación de los hosts de virtualización antiguos con Azure Stack HCI puede mejorar la escalabilidad y facilitar la administración y la protección del entorno. También es una oportunidad para retirar el almacenamiento de SAN heredado para reducir la superficie y el costo total de propiedad. La administración de operaciones y sistemas se simplifica con herramientas e interfaces unificadas y un único punto de soporte técnico.

### <a name="remote-and-branch-offices"></a>Oficinas remotas y sucursales

Con las soluciones de clúster de dos servidores a partir de 20 000 USD por ubicación, Azure Stack HCI es una excelente manera de modernizar las oficinas remotas y las sucursales, las tiendas minoristas y las ubicaciones del trabajo de campo. La innovadora resistencia anidada permite que los volúmenes sigan en línea y sean accesibles incluso si se producen varios errores de hardware al mismo tiempo. La tecnología de testigos en la nube le permite usar Azure como el pequeño factor que permite el desempate del cuórum del clúster, lo que evita situaciones de cerebro dividido sin el costo de un tercer host. Los clientes también pueden ver de forma centralizada las implementaciones de Azure Stack HCI remotas en Azure Portal.

### <a name="virtual-desktops"></a>Escritorios virtuales

Muchas organizaciones quieren hospedar escritorios virtuales locales para lograr una latencia baja y la soberanía sobre los datos. Azure Stack HCI puede proporcionar un rendimiento similar al de un entorno local.

### <a name="high-performance-virtualized-workloads"></a>Cargas de trabajo virtualizadas de alto rendimiento

Azure Stack HCI puede proporcionar el mejor rendimiento del sector para bases de datos de SQL Server y otras cargas de trabajo virtualizadas con distinción de rendimiento que requieren millones de IOPS de almacenamiento o transacciones de base de datos por segundo, y que ofrece una latencia baja constante con SSD de NVMe, una pila de RDMA sólida y una memoria persistente.

## <a name="azure-integration-benefits"></a>Ventajas de la integración de Azure

Azure Stack HCI está en una posición inmejorable para la infraestructura híbrida, lo cual le permite aprovechar los recursos en la nube y los locales para que trabajen juntos y supervisen, protejan y realicen copias de seguridad en la nube de forma nativa.

Después de registrar el clúster de Azure Stack HCI con Azure, puede usar Azure Portal inicialmente para:

- **Supervisión:** vea todos los clústeres de Azure Stack HCI en una sola vista global en la que puede agruparlos por grupos de recursos y etiquetarlos.
- **Facturación:** Pague por Azure Stack HCl mediante su suscripción a Azure.

Nos estamos esforzando para crear funcionalidades adicionales, así que permanezca atento.

También puede suscribirse a otros servicios híbridos de Azure:

- **Azure Site Recovery** para obtener alta disponibilidad y recuperación ante desastres como servicio (DRaaS).
- **Azure Monitor**, un centro de conectividad centralizado para realizar un seguimiento de lo que sucede en sus aplicaciones, red e infraestructura, con análisis avanzados con tecnología de inteligencia artificial.
- **Testigo en la nube**, para usar Azure como pequeño factor de desempate del cuórum de clúster.
- **Azure Backup** para la protección de datos sin conexión y para proteger frente al ransomware.
- **Azure Update Management** para la evaluación e implementación de actualizaciones de máquinas virtuales Windows que se ejecutan en Azure y en el entorno local.
- **Adaptador de red de Azure** para conectar recursos en el entorno local con sus máquinas virtuales en Azure a través de una VPN de punto a sitio.
- **Azure File Sync** para sincronizar el servidor de archivos con la nube mediante Azure File Sync.

Para más información, consulte [Connecting Windows Server to Azure hybrid services](/windows-server/manage/windows-admin-center/azure/index) (Conexión de Windows Server a los servicios híbridos de Azure).

## <a name="why-azure-stack-hci"></a>¿Por qué usar Azure Stack HCI?

Azure Stack HCI es una pila de virtualización integrada de primera clase basada en tecnologías probadas que ya se han implementado a escala, como Hyper-V, Espacios de almacenamiento directo y redes definidas por software inspiradas en Azure. Hay muchas razones por las que los clientes eligen Azure Stack HCI, entre las que cabe destacar:

- Resulta familiar para los administradores de Hyper-V y de servidores, lo que les permite aprovechar la virtualización existente y almacenar conceptos y aptitudes.
- Funciona con los procesos y herramientas de los centros de datos existentes como Microsoft System Center, Active Directory, directiva de grupo y scripting de PowerShell.
- Funciona con conocidas herramientas de copia de seguridad, seguridad y supervisión de otros fabricantes.
- Las opciones de hardware flexibles permiten a los clientes elegir el proveedor con el mejor servicio y soporte técnico de su zona.
- El soporte técnico conjunto entre Microsoft y el proveedor de hardware mejora la experiencia del cliente.
- Las actualizaciones sin problemas de toda la pila facilitan estar siempre actualizado.
- Un ecosistema flexible y amplio proporciona a los profesionales de TI la flexibilidad necesaria para crear una solución que se adapte mejor a sus necesidades.

## <a name="what-you-need-for-azure-stack-hci"></a>¿Qué necesita para usar Azure Stack HCI?

Para empezar, esto es lo que necesita:

- Un clúster de dos o más servidores del [catálogo de Azure Stack HCI](https://hcicatalog.azurewebsites.net) adquirido a su asociado de hardware de Microsoft preferido
- [Una suscripción de Azure](https://azure.microsoft.com/)
- Una conexión a Internet para cada servidor del clúster que pueda conectarse mediante el tráfico HTTPS saliente a puntos de conexión de Azure conocidos al menos cada 30 días
- En el caso de los clústeres extendidos entre dos sitios, necesita al menos una conexión de 1 GB entre esos sitios (se prefiere una conexión RDMA de 25 GB), con un recorrido de ida y vuelta con una latencia media de 5 ms si desea realizar una replicación sincrónica en la que las escrituras puedan producirse simultáneamente en ambos sitios.
- Si tiene previsto usar redes definidas por software (SDN), necesitará un disco duro virtual (VHD) para que el sistema operativo Azure Stack HCI cree máquinas virtuales de Controladora de red (consulte el [plan de implementación de Controladora de red](concepts/network-controller.md)).

Para más información, consulte [Requisitos del sistema](concepts/system-requirements.md). Para más información, consulte [Requisitos de Azure Kubernetes Service en Azure Stack HCI](../aks-hci/overview.md#what-you-need-to-get-started).

## <a name="hardware-partners"></a>Asociados de hardware

Puede adquirir soluciones de Azure Stack HCI validadas a su asociado de Microsoft preferido para empezar a trabajar sin tener que esperar un tiempo prolongado de diseño y compilación. Los asociados de Microsoft también ofrecen un único punto de contacto para los servicios de implementación y soporte técnico. Puede adquirir nodos validados o un sistema integrado, entre los que se incluyen el sistema operativo de Azure Stack HCI preinstalado, así como las extensiones de asociados para las actualizaciones de firmware y de controladores.

Visite la página de [soluciones de Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) o examine el [catálogo de Azure Stack HCI](https://hcicatalog.azurewebsites.net) para ver las más de 70 soluciones de Azure Stack HCI que están disponibles actualmente de asociados de Microsoft como ASUS, Axellio, Blue Chip, DataOn, Dell EMC, Fujitsu, HPE, Hitachi, Huawei, Lenovo, NEC, primeLine Solutions, QCT, SecureGUARD y Supermicro.

## <a name="software-partners"></a>Asociados de software

Hay una gran variedad de asociados de Microsoft que trabajan en software que permite ampliar las capacidades de Azure Stack HCI, a la vez que permite a los administradores de TI usar herramientas conocidas. Para obtener más información, vea [Aplicaciones de utilidades para Azure Stack HCI](concepts/utility-applications.md).

## <a name="licensing-billing-and-pricing"></a>Licencias, facturación y precios

La facturación de Azure Stack HCI se basa en un precio de suscripción mensual por núcleo de procesador físico, no en una licencia perpetua. Cuando los clientes se conectan a Azure, se carga automáticamente el número de núcleos utilizado y se evalúa con fines de facturación. El costo no varía con el uso que va más allá de los núcleos de procesadores físicos, lo que significa que más máquinas virtuales no cuestan más y que se compensa a los clientes que pueden ejecutar entornos virtuales más densos.

Los clientes pueden adquirir servidores validados de un asociado de hardware con el sistema operativo Azure Stack HCI preinstalado, o pueden comprar servidores validados sin sistema operativo a un fabricante de equipos originales y, después, suscribirse al servicio Azure Stack HCI y [descargar el sistema operativo Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/).

## <a name="the-azure-stack-family"></a>La familia Azure Stack

Azure Stack HCI forma parte de Azure y la familia de Azure Stack, ya que usa los mismos procesos definidos por software, almacenamiento y software de red que Azure Stack Hub. A continuación se incluye un breve resumen de las distintas soluciones. Para más información, consulte [Diferencias entre Azure global, Azure Stack y Azure Stack HCI](../operator/compare-azure-azure-stack.md).

- [Azure](https://azure.microsoft.com): use los servicios en la nube pública para que los recursos informáticos a petición y de autoservicio migren y modernicen las aplicaciones existentes y compilen nuevas aplicaciones nativas en la nube.
- [Azure Stack Edge](/azure/databox-online/data-box-edge-overview): acelere las cargas de trabajo de aprendizaje automático y ejecute aplicaciones en contenedores o cargas de trabajo virtualizadas en el entorno local, en un dispositivo administrado en la nube.
- [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci): ejecute aplicaciones virtualizadas en el entorno local, reemplace y consolide la infraestructura de servidor anticuada y conéctese a Azure para acceder a servicios en la nube.
- [Azure Stack Hub](../operator/azure-stack-overview.md): ejecute aplicaciones en la nube en el entorno local sin conexión o para cumplir los requisitos normativos mediante servicios de Azure coherentes.

:::image type="content" source="media/overview/azure-family-updated.png" alt-text="Diagrama de soluciones de la familia de Azure Stack" border="false":::

## <a name="whats-new-in-azure-stack-hci"></a>Novedades de Azure Stack HCI

La versión 2009 de Windows Admin Center agrega una serie de características a Azure Stack HCI, entre las que cabe destacar las siguientes:

- **Funcionalidades de hospedaje de Azure Kubernetes Service**: ahora puede instalar una versión preliminar de [Azure Kubernetes Service en Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/).
- **Inclusión de redes definidas por software en el asistente de creación de clústeres**: El asistente de creación de clústeres incluye ahora la opción de implementar la característica de Controladora de red denominada [redes definidas por software](concepts/software-defined-networking.md) durante la [creación del clúster](deploy/create-cluster.md#step-5-sdn-optional).
- **Mejoras de acceso directo a memoria remota (RDMA) del Asistente para crear clústeres**: El Asistente para crear clústeres ahora puede configurar RDMA para los adaptadores de red iWARP y RoCE, incluido Data Center Bridging (DCB).

Para más información acerca de las nuevas características, consulte [Anuncio de la disponibilidad general de la extensión de creación de clústeres en Windows Admin Center](https://techcommunity.microsoft.com/t5/windows-admin-center-blog/announcing-general-availability-of-the-cluster-creation/ba-p/1978332).

Los clústeres que ejecutan Azure Stack HCI, versión 20H2, tienen las siguientes características nuevas en comparación con las soluciones basadas en Windows Server 2019:

- **Nuevas funcionalidades de Windows Admin Center**: con la posibilidad de crear y actualizar clústeres hiperconvergidos mediante una interfaz de usuario intuitiva, Azure Stack HCI es más fácil de usar que nunca.
- **Clústeres extendidos para la conmutación automática por error**: la agrupación en clústeres multisitio con replicación de réplicas de almacenamiento y conmutación automática por error de VM proporciona recuperación ante desastres y continuidad empresarial nativas.
- **Reglas de afinidad y de falta de afinidad**: se pueden usar de manera parecida a la forma en que Azure usa Availability Zones para mantener juntas las máquinas virtuales o de manera independiente en clústeres con varios dominios de error como, por ejemplo, los clústeres extendidos.
- **Integración de Azure Portal**: la experiencia de Azure Portal para Azure Stack HCI está diseñada para ver todos los clústeres de Azure Stack HCI de todo el mundo, con nuevas características en el desarrollo.
- **Aceleración de GPU para cargas de trabajo de alto rendimiento**: las aplicaciones de inteligencia artificial o de aprendizaje automático pueden beneficiarse del aumento del rendimiento con los procesadores GPU.
- **Cifrado de BitLocker**: ahora puede usar BitLocker para cifrar el contenido de los volúmenes de datos en Azure Stack HCI, lo cual ayuda a los gobiernos y a otros clientes a cumplir con estándares como FIPS 140-2 e HIPAA.
- **Mayor velocidad de reparación de los volúmenes de Espacios de almacenamiento directo**: repare los volúmenes de forma rápida y sencilla.

Para más información sobre las novedades de Azure Stack HCI 20H2, [vea este vídeo](https://www.youtube.com/watch?v=DPG7wGhh3sAa) de Microsoft Inspire.

## <a name="roles-you-can-run-without-virtualizing"></a>Roles que se pueden ejecutar sin virtualización

Dado que Azure Stack HCI está pensado como host de virtualización en el que se ejecutan todas las cargas de trabajo de las máquinas virtuales, los términos de Azure Stack HCI le permiten ejecutar solo lo necesario para hospedar máquinas virtuales.

Esto significa que puede ejecutar los siguientes roles de servidor:

- Hyper-V
- Controlador de red y otros componentes necesarios de las redes definidas por software (SDN)

Sin embargo, los demás roles y aplicaciones deben ejecutarse dentro de las máquinas virtuales. Tenga en cuenta que puede ejecutar las utilidades, aplicaciones y servicios necesarios para la administración y el estado de las máquinas virtuales hospedadas.

## <a name="video-based-learning"></a>Aprendizaje basado en vídeos

Este es el vídeo de la red extendida de Azure:

- [Seamless connectivity to Azure with Windows Server and hybrid networking](https://www.youtube.com/watch?v=do2_4Y2p9dk) (Conectividad sin problemas en Azure con Windows Server y redes híbridas)

Vídeos acerca de la versión original, basada en Windows Server 2019 de Azure Stack HCI:

- [Vista introductoria sobre Azure Stack y Azure Stack HCI](https://aka.ms/AzureStackOverviewVideo)
- [Microsoft Ignite Live 2019: Introducción a Azure Stack HCI](https://www.youtube.com/watch?v=vueHIBqNIEU)
- [Conozca Azure Stack HCI](https://www.youtube.com/watch?v=4aGZK0Ndmh8&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=13&t=0s)
- [Modernización de tiendas o sucursales con Azure Stack HCI](https://www.youtube.com/watch?v=-JzLhjfkhmM&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=9&t=0s)
- [Novedades de Azure Stack HCI: 45 datos en 45 minutos](https://www.youtube.com/watch?v=C5J4IEnlS_E&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=12&t=0s)
- [Empiece a trabajar con su implementación de Azure Stack HCI](https://www.youtube.com/watch?v=gxaPJLrWy5w&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=11&t=0s)
- [El caso de la reducción de datos: Desduplicación de datos en Azure Stack HCI](https://www.youtube.com/watch?v=fmm4iDbDiY4&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=23&t=0s)
- [Notas de campo de Dave Kawula sobre Azure Stack HCI](https://www.youtube.com/watch?v=OXv7fLlz0ew&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=2&t=0s)

Este es un vídeo de Hybrid Cloud Virtual Event:

- [Azure Stack HCI | Hybrid Cloud Virtual Event](https://www.youtube.com/watch?v=nxpoEva-R2Y)

## <a name="next-steps"></a>Pasos siguientes

- [Descargar Azure Stack HCI](https://azure.microsoft.com/products/azure-stack/hci/hci-download/)
- [Creación de un clúster de Azure Stack HCI y registro con Azure](deploy/deployment-quickstart.md)
- [Uso de Azure Stack HCI con Windows Admin Center](get-started.md)
