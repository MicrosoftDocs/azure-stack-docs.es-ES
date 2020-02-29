---
title: Introducción a Azure Stack HCI
description: Azure Stack HCI es un clúster de Windows Server 2019 hiperconvergido que usa hardware validado para ejecutar cargas de trabajo virtualizadas en el entorno local. Opcionalmente, también puede conectarse a servicios de Azure para las copias de seguridad basadas en la nube, la recuperación del sitio, etc. Las soluciones de Azure Stack HCI usan hardware validado por Microsoft para garantizar una confiabilidad y un rendimiento óptimos e incluyen compatibilidad con tecnologías como unidades de NVMe, memoria persistente y acceso directo a memoria remota (RDMA).
ms.topic: article
author: jasongerend
ms.author: jgerend
ms.localizationpriority: medium
ms.date: 11/04/2019
ms.openlocfilehash: 2bf9af3db5a3577b62c59e92879a3680d9a3532a
ms.sourcegitcommit: 97806b43314d306e0ddb15847c86be2c92ae001e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/20/2020
ms.locfileid: "77519510"
---
# <a name="azure-stack-hci-overview"></a>Introducción a Azure Stack HCI

Azure Stack HCI es un clúster de Windows Server 2019 hiperconvergido que usa hardware validado para ejecutar cargas de trabajo virtualizadas en el entorno local. Opcionalmente, también puede conectarse a servicios de Azure para las copias de seguridad basadas en la nube, la recuperación del sitio, etc. Las soluciones de Azure Stack HCI usan hardware validado por Microsoft para garantizar una confiabilidad y un rendimiento óptimos e incluyen compatibilidad con tecnologías como unidades de NVMe, memoria persistente y acceso directo a memoria remota (RDMA).

Azure Stack HCI es una solución que combina varios productos:

- Hardware de un asociado de OEM

- Windows Server 2019 Datacenter Edition

- Windows Admin Center

- Servicios de Azure (opcionales)

![Azure Stack HCI es la solución hiperconvergida de Microsoft disponible en una amplia variedad de asociados de hardware.](media/overview/azure-stack-hci-solution.png)

Azure Stack HCI es la solución hiperconvergida de Microsoft disponible en una amplia variedad de asociados de hardware. Tenga en cuenta los siguientes escenarios para una solución hiperconvergida para ayudarle a determinar si Azure Stack HCI es la solución más adecuada para sus necesidades:

- **Actualice el hardware anticuado.** Reemplace los antiguos servidores e infraestructura de almacenamiento y ejecute máquinas virtuales Windows y Linux en el entorno local y en el perímetro con las herramientas y aptitudes de TI existentes.

- **Consolidar cargas de trabajo virtualizadas.** Consolide las aplicaciones heredadas en una infraestructura eficaz e hiperconvergida. Aproveche los mismos tipos de eficiencia en la nube utilizada para ejecutar los centros de datos a gran escala, como Microsoft Azure.

- **Conéctese a Azure para obtener servicios en la nube híbrida.** Simplifique el acceso a los servicios de seguridad y administración en la nube de Azure, incluida la copia de seguridad fuera del sitio, la de sitios, la supervisión basada en la nube, etc.

## <a name="the-azure-stack-family"></a>La familia Azure Stack

Azure Stack HCI forma parte de Azure y la familia de Azure Stack, ya que usa los mismos procesos definidos por software, almacenamiento y software de red que Azure Stack Hub. A continuación se muestra un resumen rápido de las distintas soluciones (para más información, consulte [Diferencias entre Azure global, Azure Stack y Azure Stack HCI](../operator/compare-azure-azure-stack.md)):

- [Azure](https://azure.microsoft.com): use los servicios en la nube pública para que los recursos informáticos a petición y de autoservicio migren y modernicen las aplicaciones existentes y compilen nuevas aplicaciones nativas en la nube.
- [Azure Stack Edge](https://docs.microsoft.com/azure/databox-online/data-box-edge-overview): acelere las cargas de trabajo de aprendizaje automático y ejecute aplicaciones en contenedores o cargas de trabajo virtualizadas en el entorno local, en un dispositivo administrado en la nube.
- [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci): ejecute aplicaciones virtualizadas en el entorno local, reemplace y consolide la infraestructura de servidor anticuada y conéctese a Azure para acceder a servicios en la nube.
- [Azure Stack Hub](../operator/azure-stack-overview.md): ejecute aplicaciones en la nube en el entorno local sin conexión o para cumplir los requisitos normativos mediante servicios de Azure coherentes.

![Azure Stack Edge es una aplicación administrada en la nube para ejecutar aplicaciones de aprendizaje automático y en contenedores en el perímetro, Azure Stack HCl es una solución hiperconvergida para la ejecución de máquinas virtuales y almacenamiento en el entorno local, mientras que Azure Stack Hub proporciona servicios coherentes con Azure y nativos de la nube en el entorno local.](media/overview/azure-family.png)

Para obtener más información:

- Obtenga más información en nuestro sitio web de soluciones de [Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci).
- Vea a los expertos de Microsoft Jeff Woolsey y Vijay Tewari explicar las [nuevas soluciones de Azure Stack HCI](https://aka.ms/AzureStackOverviewVideo).

## <a name="hyperconverged-efficiencies"></a>Eficiencias hiperconvergidas

Las soluciones de Azure Stack HCI reúnen un proceso, almacenamiento y redes altamente virtualizados en componentes y servidores x86 estándares del sector. La combinación de recursos en el mismo clúster facilita la implementación, administración y escala. Use para la administración su elección de automatización de la línea de comandos o Windows Admin Center.

Obtenga un rendimiento de la máquina virtual líder del sector para sus aplicaciones de servidor con Hyper-V, la tecnología de hipervisor de base de la nube de Microsoft y la tecnología Espacios de almacenamiento directo, con compatibilidad integrada para NVMe, memoria persistente y redes de acceso directo a memoria remota (RDMA).

Contribuya a proteger las aplicaciones y los datos con máquinas virtuales protegidas, microsegmentación de red y cifrado nativo.

## <a name="hybrid-capabilities"></a>Funcionalidades híbridas

Puede aprovechar las ventajas de combinar las funcionalidades en la nube con las del entorno local mediante una plataforma de infraestructura hiperconvergida en la nube pública. El equipo puede empezar a desarrollar aptitudes en la nube con la integración incorporada a los servicios de administración de infraestructuras de Azure:

- Azure Site Recovery para la alta disponibilidad y recuperación ante desastres como servicio (DRaaS).

- Azure Monitor, una central centralizada para realizar un seguimiento de lo que sucede en sus aplicaciones, red e infraestructura, con análisis avanzados con tecnología de inteligencia artificial.

- Testigo en la nube, para usar Azure como ligero desempate para el cuórum de clúster.

- Azure Backup para la protección de datos sin conexión y para proteger frente al ransomware.

- Azure Update Management para la evaluación de la actualización y las implementaciones de actualización para VM Windows que se ejecutan en Azure y en el entorno local.

- Adaptador de red de Azure para conectar recursos en el entorno local con sus máquinas virtuales en Azure a través de una VPN de punto a sitio.

- Sincronización del servidor de archivos con la nube mediante Azure File Sync.

Para obtener más información, consulte [Connecting Windows Server to Azure hybrid services](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index) (Conexión de Windows Server a los servicios híbridos de Azure).

## <a name="management-tools-and-system-center"></a>Herramientas de administración y System Center

Azure Stack HCI usa la misma virtualización, almacenamiento definido por software y software de red que Azure Stack Hub. Sin embargo, con Azure Stack HCI, tiene derechos administrativos completos en el clúster y puede administrar cualquiera de sus tecnologías directamente:

- [Hyper-V](https://docs.microsoft.com/windows-server/virtualization/hyper-v/hyper-v-on-windows-server)
- [Espacios de almacenamiento directo](https://docs.microsoft.com/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Red definida por software](https://docs.microsoft.com/windows-server/networking/sdn/)
- [Clústeres de conmutación por error](https://docs.microsoft.com/windows-server/failover-clustering/failover-clustering-overview)

Para administrar estas tecnologías, puede usar las herramientas de administración siguientes:

- [Windows Admin Center](https://docs.microsoft.com/windows-server/manage/windows-admin-center/overview)
- [System Center](https://www.microsoft.com/cloud-platform/system-center)
- [PowerShell](https://docs.microsoft.com/powershell/?view=powershell-6)
- Otras herramientas de administración, como el [Administrador del servidor](https://docs.microsoft.com/windows-server/administration/server-manager/server-manager) y complementos MMC
- Herramientas que no sean de Microsoft, como 5Nine Manager

Si decide usar System Center para implementar y administrar su infraestructura, usará System Center Virtual Machine Management (VMM) y System Center Operations Manager. Con VMM, aprovisiona y administra los recursos necesarios para crear e implementar máquinas virtuales y servicios en nubes privadas. Con Operations Manager, supervisa servicios, dispositivos y operaciones en toda la empresa para identificar problemas que requieren una acción inmediata.

## <a name="hardware-partners"></a>Asociados de hardware

Puede adquirir soluciones de Azure Stack HCI validadas que ejecutan Windows Server 2019 de veinte asociados. Su asociado de Microsoft preferido puede prepararlo todo sin tener que pasar por un largo tiempo de diseño y compilación. También ofrecen un único punto de contacto para los servicios de implementación y soporte técnico.

Visite el [sitio web de Azure Stack HCI](https://azure.microsoft.com/overview/azure-stack/hci) para ver las más de 70 soluciones de Azure Stack HCI actualmente disponibles de estos asociados de Microsoft: ASUS, Axellio, bluechip, DataON, Dell EMC, Fujitsu, HPE, Hitachi, Huawei, Lenovo, NEC, primeLine Solutions, QCT, SecureGUARD y Supermicro.

## <a name="video-based-learning"></a>Aprendizaje basado en vídeos

Estos son algunos vídeos de las sesiones de Microsoft Ignite 2019:

- [Microsoft Ignite Live 2019: Introducción a Azure Stack HCI](https://www.youtube.com/watch?v=vueHIBqNIEU)
- [Conozca Azure Stack HCI](https://www.youtube.com/watch?v=4aGZK0Ndmh8&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=13&t=0s)
- [Modernización de tiendas o sucursales con Azure Stack HCI](https://www.youtube.com/watch?v=-JzLhjfkhmM&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=9&t=0s)
- [Novedades de Azure Stack HCI: 45 datos en 45 minutos](https://www.youtube.com/watch?v=C5J4IEnlS_E&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=12&t=0s)
- [Empiece a trabajar con su implementación de Azure Stack HCI](https://www.youtube.com/watch?v=gxaPJLrWy5w&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=11&t=0s)
- [El caso de la reducción de datos: Desduplicación de datos en Azure Stack HCI](https://www.youtube.com/watch?v=fmm4iDbDiY4&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=23&t=0s)
- [Notas de campo de Dave Kawula sobre Azure Stack HCI](https://www.youtube.com/watch?v=OXv7fLlz0ew&list=PLQXpv_NQsPICdXZoH-EzlIFa4P6VS5m11&index=2&t=0s)

Este es un vídeo de Hybrid Cloud Virtual Event:

- [Azure Stack HCI | Hybrid Cloud Virtual Event](https://www.youtube.com/watch?v=nxpoEva-R2Y)

## <a name="faq"></a>Preguntas más frecuentes

### <a name="what-do-azure-stack-hub-and-azure-stack-hci-solutions-have-in-common"></a>¿Qué tienen en común las soluciones de Azure Stack Hub y Azure Stack HCI?

Las soluciones de Azure Stack HCI presentan las mismas tecnologías de proceso, almacenamiento y red definidas por software y basadas en Hyper-V que Azure Stack Hub. Ambas ofertas cumplen rigurosos criterios de prueba y validación para garantizar la confiabilidad y la compatibilidad con la plataforma de hardware subyacente.

### <a name="how-are-they-different"></a>¿En qué se diferencian?

Con Azure Stack Hub, ejecuta servicios en la nube en el entorno local. Puede ejecutar servicios Azure IaaS y PaaS en el entorno local para compilar y ejecutar de forma coherente aplicaciones en la nube en cualquier lugar, administrados desde Azure Portal en el entorno local.

Con Azure Stack HCI, ejecuta cargas de trabajo virtualizadas en el entorno local, administradas con Windows Admin Center y herramientas familiares de Windows Server. Opcionalmente, puede conectarse a Azure para los escenarios híbridos, como la recuperación del sitio basada en la nube, la supervisión, etc.

### <a name="why-is-microsoft-bringing-its-hci-offering-to-the-azure-stack-family"></a>¿Por qué Microsoft trae su oferta de HCI a la familia de productos de Azure Stack?

La tecnología hiperconvergida de Microsoft ya es la base de Azure Stack Hub.

Muchos clientes de Microsoft tienen entornos de TI complejos y nuestro objetivo es proporcionar soluciones que los encuentren donde estén con la tecnología adecuada para la necesidad de negocio adecuada. Azure Stack HCI es una evolución de las soluciones de Windows Server definidas por software (WSSD) basadas en Windows Server 2016 disponibles anteriormente en nuestros asociados de hardware. Lo trajimos a la familia de productos Azure Stack porque hemos empezado a ofrecer nuevas opciones para conectarse sin problemas con Azure para los servicios de administración de la infraestructura.

### <a name="does-azure-stack-hci-need-to-be-connected-to-azure"></a>¿Azure Stack HCI necesita estar conectado a Azure?

No, es opcional. Puede aprovechar la integración con Azure para escenarios híbridos, como la copia de seguridad y la recuperación ante desastres fuera del sitio, y la administración de supervisión y actualización en la nube, pero son opcionales. Se puede ejecutar sin conexión a Internet sin ningún problema.

### <a name="how-does-azure-stack-hci-relate-to-windows-server"></a>¿Qué relación existe entre Azure Stack HCI y Windows Server?

Windows Server 2019 es la base de casi cualquier producto de Azure. Todas las características que valora aún están disponibles y se admitirán en Windows Server. Azure Stack HCI es la manera recomendada de implementar HCI en el entorno local mediante el hardware de nuestros asociados validado por Microsoft.

### <a name="can-i-upgrade-from-azure-stack-hci-to-azure-stack-hub"></a>¿Puedo actualizar de Azure Stack HCI a Azure Stack Hub? 

No, pero los clientes pueden migrar sus cargas de trabajo de Azure Stack HCI a Azure Stack Hub o Azure.

### <a name="what-azure-services-can-i-connect-to-azure-stack-hci"></a>¿Qué servicios de Azure puedo conectar a Azure Stack HCI?

Para una lista actualizada de los servicios de Azure a los que puede conectar Azure Stack HCI, consulte [Connecting Windows Server to Azure hybrid services](https://docs.microsoft.com/windows-server/manage/windows-admin-center/azure/index) (Conexión de Windows Server a los servicios híbridos de Azure).

### <a name="how-does-the-cost-of-azure-stack-hci-compare-to-azure-stack-hub"></a>¿Cuál es la diferencia entre Azure Stack HCI y Azure Stack Hub en relación con el costo? 

Azure Stack Hub se vende como sistema completamente integrado que incluye servicios y soporte técnico. Se puede comprar Azure Stack Hub como un sistema administrado por usted o como un servicio completamente administrado en nuestros asociados. Además del sistema base, los servicios de Azure que se ejecutan en Azure Stack Hub o Azure se venden conforme al principio del pago por uso.

Las soluciones de Azure Stack HCI siguen el modelo de compra tradicional. Puede comprar hardware validado en asociados de Azure Stack HCI y software (edición Windows Server 2019 Datacenter con funcionalidades del centro de datos definidas por software y Windows Admin Center) en diversos canales existentes. En los servicios de Azure que puede usar con Windows Admin Center, paga con una suscripción a Azure.

### <a name="how-do-i-buy-azure-stack-hci-solutions"></a>¿Cómo compro las soluciones de Azure Stack HCI?

Siga estos pasos:

1. Compre un sistema de hardware validado por Microsoft a su asociado de hardware preferido.
1. Instale la edición Windows Server 2019 Datacenter y Windows Admin Center para la administración y la capacidad de conectarse a Azure para los servicios en la nube.
1. Opcionalmente, puede usar su cuenta de Azure a fin de asociar los servicios de administración y seguridad basados en la nube a sus cargas de trabajo.

![Para comprar soluciones de Azure Stack HCI, elija los asociados de hardware y la configuración que mejor se adapte a sus necesidades.](media/overview/buying-azure-stack-hci.png)

## <a name="compare-azure-stack-hub-and-azure-stack-hci"></a>Comparación entre Azure Stack Hub y Azure Stack HCI

A medida que la organización se transforma digitalmente, puede que se dé cuenta de que puede avanzar más rápido utilizando los servicios de la nube pública para compilar soluciones en arquitecturas modernas y actualizar aplicaciones heredadas. Sin embargo, por motivos que incluyen obstáculos tecnológicos y normativos, las cargas de trabajo deben permanecer en el entorno local. La tabla siguiente le ayuda a determinar qué estrategia de nube híbrida de Microsoft proporciona lo que necesita cuando lo necesita, lo que ofrece innovación para cargas de trabajo, estén donde estén.

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Nuevas aptitudes, procesos innovadores | Mismas aptitudes, procesos familiares |
| Los servicios de Azure en su centro de datos | Conecte el centro de datos a los servicios de Azure |

### <a name="when-to-use-azure-stack-hub"></a>Cuándo usar Azure Stack Hub

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Use Azure Stack Hub para la infraestructura como servicio (IaaS) de autoservicio, con un fuerte aislamiento y un seguimiento de uso preciso, así como contracargos para varios inquilinos colocados. Ideal para proveedores de servicios y nubes privadas de empresa. Plantillas de Azure Marketplace. | Azure Stack HCI no aplica de forma nativa ni permite usar varios inquilinos. |
| Use Azure Stack Hub para desarrollar y ejecutar aplicaciones que se basan en los servicios de plataforma como servicio (PaaS), como Web Apps, Functions o Event Hubs en el entorno local. Estos servicios se ejecutan en Azure Stack Hub exactamente como lo hacen en Azure, lo que proporciona un entorno de desarrollo y tiempo de ejecución híbrido coherente. | Azure Stack HCI no ejecuta servicios de PaaS en el entorno local. |
| Use Azure Stack Hub para modernizar la implementación de aplicaciones y el funcionamiento con prácticas de DevOps como infraestructura como código, integración continua e implementación continua (CI/CD) y funciones prácticas, como extensiones de VM coherentes con Azure. Ideal para equipos de desarrollo y DevOps. | Azure Stack HCI no incluye de forma nativa ninguna herramienta de DevOps. |

### <a name="when-to-use-azure-stack-hci"></a>Cuándo usar Azure Stack HCI

| Azure Stack Hub | Azure Stack HCI |
| --------------- | --------------- |
| Azure Stack Hub requiere un mínimo de cuatro nodos y sus propios conmutadores de red. | Use Azure Stack HCI para una superficie mínima para oficinas y sucursales remotas. Comience con solo 2 nodos de servidor y redes inmutables de salida a entrada para la máxima simplicidad y rentabilidad. Las ofertas de hardware empiezan a partir de 4 unidades y 64 GB de memoria, por debajo de 10 000 USD por nodo. |
| Azure Stack Hub restringe la capacidad de configuración de Hyper V y el conjunto de características para mantener la coherencia con Azure. | Use Azure Stack HCI para la virtualización básica de Hyper-V para aplicaciones empresariales clásicas como Exchange, SharePoint y SQL Server y para virtualizar los roles de Windows Server como el servidor de archivos, DNS, DHCP, IIS y AD. Acceso sin restricciones a todas las características de Hyper-V, como las VM blindadas.|
| Azure Stack Hub no expone estas tecnologías de infraestructura. | Use Azure Stack HCI para usar la infraestructura definida por software en lugar de matrices de almacenamiento anticuadas o dispositivos de red, sin rediseñar la arquitectura principal. Hyper-V integrado, Espacios de almacenamiento directo y redes definidas mediante software (SDN) son características fáciles de administrar y accesibles directamente. |
