---
title: Implementación de SQL Server en Azure Stack HCI (AKS-HCI)
description: En este tema se proporcionan instrucciones sobre cómo implementar SQL Server en Azure Stack HCl.
author: JohnCobb1
ms.author: v-johcob
ms.topic: how-to
ms.date: 01/11/2021
ms.openlocfilehash: 8f93a56840d4e4410a42aafe117f6cb1eebe84b4
ms.sourcegitcommit: 0983c1f90734b7ea5e23ae614eeaed38f9cb3c9a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/19/2021
ms.locfileid: "98571603"
---
# <a name="deploy-sql-server-on-azure-stack-hci"></a>Implementación de SQL Server en Azure Stack HCI

>Se aplica a: Azure Stack HCl, versión 20H2; SQL Server (todas las versiones compatibles)

En este tema se proporcionan instrucciones sobre cómo planear, configurar e implementar SQL Server en el sistema operativo de Azure Stack HCI. El sistema operativo es una solución de clúster de infraestructura hiperconvergida (HCI) que hospeda cargas de trabajo virtualizadas de Windows y Linux y su almacenamiento en un entorno local híbrido.

## <a name="solution-overview"></a>Información general de la solución
Azure Stack HCl proporciona una plataforma de alta disponibilidad, rentable y flexible para ejecutar SQL Server y Espacios de almacenamiento directo. Azure Stack HCI puede ejecutar cargas de trabajo de procesamiento de transacciones en línea (OLTP), almacenamiento de datos y BI, y análisis avanzado y de inteligencia artificial sobre macrodatos.

La flexibilidad de la plataforma es especialmente importante para las bases de datos de misión crítica. Puede ejecutar SQL Server en máquinas virtuales (VM) que usan Windows Server o Linux, lo que le permite consolidar varias cargas de trabajo de base de datos y agregar más VM a su entorno de Azure Stack HCl según sea necesario. Azure Stack HCI también le permite integrar SQL Server con Azure Site Recovery para proporcionar una solución de migración, restauración y protección basada en la nube para los datos de la organización que sea confiable y segura.

## <a name="deploy-sql-server"></a>Implementar SQL Server
En esta sección se describe de forma general cómo adquirir hardware para SQL Server en Azure Stack HCl y cómo usar Windows Admin Center para administrar el sistema operativo en los servidores. Se incluye información sobre la configuración de SQL Server, la supervisión y el ajuste del rendimiento, así como el uso de servicios híbridos de Azure y alta disponibilidad (HA).

### <a name="step-1-acquire-hardware-from-the-azure-stack-hci-catalog"></a>Paso 1: adquirir hardware del Catálogo de Azure Stack HCI
En primer lugar, deberá adquirir el hardware. La forma más fácil de hacerlo es encontrar su asociado de hardware de Microsoft preferido en el [Catálogo de Azure Stack HCI](https://hcicatalog.azurewebsites.net) y adquirir un sistema integrado con el sistema operativo de Azure Stack HCI preinstalado. En el catálogo, puede filtrar para ver el hardware del proveedor optimizado para este tipo de carga de trabajo.

En caso contrario, tendrá que implementar el sistema operativo de Azure Stack HCI en su propio hardware. Para obtener más información acerca de las opciones de implementación de Azure Stack HCI y la instalación de Windows Admin Center, consulte [Implementación del sistema operativo de Azure Stack HCI](./operating-system.md).

A continuación, use Windows Admin Center para [crear un clúster de Azure Stack HCI](./create-cluster.md).

### <a name="step-2-install-sql-server-on-azure-stack-hci"></a>Paso 2: instalar SQL Server en Azure Stack HCI
Puede instalar SQL Server en VM que ejecuten Windows Server o Linux, en función de sus requisitos.

Para obtener instrucciones sobre la instalación de SQL Server, consulte:
- [Guía de instalación de SQL Server en Windows](https://docs.microsoft.com/sql/database-engine/install-windows/install-sql-server?view=sql-server-ver15&preserve-view=true).
- [Guía de instalación de SQL Server en Linux](https://docs.microsoft.com/sql/linux/sql-server-linux-setup?view=sql-server-ver15&preserve-view=true).

### <a name="step-3-monitor-and-performance-tune-sql-server"></a>Paso 3: supervisar SQL Server y optimizar el rendimiento de SQL Server
Microsoft proporciona un completo conjunto de herramientas para supervisar los eventos en SQL Server y para optimizar el diseño de la base de datos física. La elección de la herramienta depende del tipo de supervisión o de la optimización que quiera realizar.

Para garantizar el rendimiento y el mantenimiento de las instancias de SQL Server en Azure Stack HCl, consulte [Herramientas de supervisión y optimización del rendimiento](https://docs.microsoft.com/sql/relational-databases/performance/performance-monitoring-and-tuning-tools?view=sql-server-ver15&preserve-view=true).

Para optimizar SQL Server 2017 y SQL Server 2016, consulte [Actualizaciones recomendadas y opciones de configuración para SQL Server 2017 y 2016 con cargas de trabajo de alto rendimiento](https://support.microsoft.com/help/4465518/recommended-updates-and-configurations-for-sql-server).

### <a name="step-4-use-sql-server-high-availability-features"></a>Paso 4: usar las características de alta disponibilidad de SQL Server
Azure Stack HCl aprovecha los [Clústeres de conmutación por error de Windows Server con SQL Server](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/windows-server-failover-clustering-wsfc-with-sql-server) (WSFC) para admitir la ejecución de SQL Server en VM en caso de error de hardware. SQL Server ofrece también [Grupos de disponibilidad (AG) Always On](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server) para proporcionar alta disponibilidad de nivel de base de datos diseñada para ayudar con los errores de aplicaciones y software. Además de WSFC y AG, Azure Stack HCI puede usar [Instancias de clúster de conmutación por error (FCI) de Always On](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server), que se basan en la tecnología de [Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) para el almacenamiento compartido.

Todas estas opciones funcionan con el [testigo en la nube](https://docs.microsoft.com/windows-server/failover-clustering/deploy-cloud-witness) de Microsoft Azure para el control de cuórum. Se recomienda el uso de reglas de [antiafinidad](https://docs.microsoft.com/windows-server/failover-clustering/cluster-affinity) de clúster en WSFC para las VM ubicadas en distintos nodos físicos a fin de mantener el tiempo de actividad de SQL Server en caso de que se produzcan errores de host al configurar Grupos de disponibilidad Always On.

### <a name="step-5-set-up-azure-hybrid-services"></a>Paso 5: configurar servicios híbridos de Azure
Existen varios servicios híbridos de Azure que puede usar para ayudar a proteger los datos y las aplicaciones de SQL Server. [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) es una herramienta de recuperación ante desastres como servicio (DRaaS). Para obtener más información acerca del uso de este servicio para proteger el back-end de SQL Server de una aplicación para ayudar a mantener las cargas de trabajo en línea, consulte [Configuración de la recuperación ante desastres para SQL Server](https://docs.microsoft.com/azure/site-recovery/site-recovery-sql).

[Azure Backup](https://azure.microsoft.com/services/backup/) le permite definir directivas de copia de seguridad para proteger las cargas de trabajo empresariales y admite la copia de seguridad y la restauración de coherencia con SQL Server. Para obtener más información sobre cómo realizar una copia de seguridad de los datos locales de SQL, consulte [Instalación de Azure Backup Server](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup).

Como alternativa, puede usar la característica [Copia de seguridad administrada de SQL Server](https://docs.microsoft.com/sql/relational-databases/backup-restore/sql-server-managed-backup-to-microsoft-azure?view=sql-server-ver15&preserve-view=true) para administrar copias de seguridad de Azure Blob Storage.

Para obtener más información sobre el uso de esta opción, que es adecuada para el archivado fuera del sitio, consulte: 

- [Tutorial: Usar el servicio Azure Blob Storage con SQL Server 2016](https://docs.microsoft.com/sql/relational-databases/tutorial-use-azure-blob-storage-service-with-sql-server-2016?view=sql-server-ver15&preserve-view=true)
- [Inicio rápido: Copia de seguridad y restauración de SQL en el servicio Azure Blob Storage](https://docs.microsoft.com/sql/relational-databases/tutorial-sql-server-backup-and-restore-to-azure-blob-storage-service?view=sql-server-ver15&tabs=SSMS&preserve-view=true)

Además de estos escenarios de copia de seguridad, puede configurar otros servicios de bases de datos que ofrece SQL Server, como [Azure Data Factory](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/move-sql-azure-adf) y [Azure Feature Pack para Integration Services (SSIS)](https://docs.microsoft.com/sql/integration-services/azure-feature-pack-for-integration-services-ssis?view=sql-server-ver15&preserve-view=true).

## <a name="next-steps"></a>Pasos siguientes
Para obtener más información sobre cómo trabajar con SQL Server, consulte:
- [Tutorial: Introducción al motor de base de datos](https://docs.microsoft.com/sql/relational-databases/tutorial-getting-started-with-the-database-engine?view=sql-server-ver15&preserve-view=true)
