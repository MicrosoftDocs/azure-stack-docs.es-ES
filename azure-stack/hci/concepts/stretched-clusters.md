---
title: Información general sobre clústeres extendidos
description: Más información sobre los clústeres extendidos
author: v-dasis
ms.topic: how-to
ms.date: 12/21/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 2a09d459a44171a864729fd4163197de35995f2e
ms.sourcegitcommit: a745662c7a5a18f135accf3f70d8508b57e83e2b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/22/2020
ms.locfileid: "97737936"
---
# <a name="stretched-clusters-overview"></a>Información general sobre clústeres extendidos

> Se aplica a Azure Stack HCl, versión v20H2

Una solución de clústeres extendidos de Azure Stack HCI extendida para la recuperación ante desastres proporciona conmutación automática por error para restaurar la producción rápidamente y sin necesidad de intervención manual. La réplica de almacenamiento proporciona la replicación de volúmenes entre sitios para la recuperación ante desastres y mantiene todos los servidores sincronizados.

La réplica de almacenamiento admite la replicación sincrónica y asincrónica:

- La replicación sincrónica refleja los datos entre sitios en una red de baja latencia con volúmenes coherentes frente a bloqueos para garantizar que no se pierdan datos en el nivel del sistema de archivos durante un error.
- La Replicación asincrónica refleja los datos entre sitios más allá de los intervalos metropolitanos a través de enlaces de red con latencias superiores, pero sin ninguna garantía de que ambos sitios tengan copias idénticas de los datos en el momento del error.

>[!NOTE]
> En el caso de la replicación asincrónica, debe poner en línea los volúmenes de destino del otro sitio manualmente después de la conmutación por error. Para más información, consulte [Replicación asincrónica](https://docs.microsoft.com/windows-server/storage/storage-replica/storage-replica-overview#asynchronous-replication).

Hay dos tipos de clústeres extendidos: activo-pasivo y activo-activo. Puede configurar la replicación del sitio activo-pasivo, donde hay un sitio y una dirección preferidos para la replicación. La replicación activa-activa es donde la replicación puede producirse de forma bidireccional desde cualquier sitio. En este artículo solo se trata la configuración activa-pasiva.

En términos simples, un sitio *activo* es uno que tiene recursos y proporciona roles y cargas de trabajo con los que los clientes se conectan. Un sitio *pasivo* es el que no proporciona roles o cargas de trabajo para clientes y está esperando una conmutación por error del sitio activo para la recuperación ante desastres.

Los sitios pueden estar en dos estados, ciudades, plantas o salas diferentes. Los clústeres extendidos que usan dos sitios ofrecen recuperación ante desastres y continuidad empresarial en caso de que un sitio sufra una interrupción o un error.

Dedique unos minutos a ver el vídeo sobre el clúster extendido de Azure Stack HCI:
> [!VIDEO https://www.youtube.com/embed/rYnZL1wMiqU]

## <a name="active-passive-stretched-cluster"></a>Clúster extendido activo-pasivo

En el diagrama siguiente se muestra el sitio 1 como el sitio activo con replicación en el sitio 2, una replicación unidireccional.

:::image type="content" source="media/stretched-cluster/active-passive-stretched-cluster.png" alt-text="Escenario del clúster extendido activo-pasivo"  lightbox="media/stretched-cluster/active-passive-stretched-cluster.png":::

## <a name="active-active-stretched-cluster"></a>Clúster extendido activo-activo

En el diagrama siguiente se muestran el sitio 1 y el sitio 2 como sitios activos, con replicación bidireccional en el otro sitio.

:::image type="content" source="media/stretched-cluster/active-active-stretched-cluster.png" alt-text="Escenario del clúster extendido activo-activo" lightbox="media/stretched-cluster/active-active-stretched-cluster.png":::

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre Réplica de almacenamiento. Consulte [Introducción a Réplica de almacenamiento](https://docs.microsoft.com/windows-server/storage/storage-replica/storage-replica-overview).
- Obtenga más información sobre el uso de Réplica de almacenamiento. Consulte [Configuración de un clúster de conmutación por error de Hyper-V o un servidor de archivos para un clúster de uso general](https://docs.microsoft.com/windows-server/storage/storage-replica/stretch-cluster-replication-using-shared-storage#configure-a-hyper-v-failover-cluster-or-a-file-server-for-a-general-use-cluster).
- Más información sobre los requisitos de hardware y de otro tipo para los clústeres extendidos. Consulte [Requisitos del sistema](system-requirements.md).
- Aprenda a implementar un clúster extendido con Windows Admin Center. Consulte [Creación de un clúster con Windows Admin Center](../deploy/create-cluster.md).
- Aprenda a implementar un clúster extendido con PowerShell. Consulte [Creación de un clúster con PowerShell](../deploy/create-cluster-powershell.md).
- Aprenda a crear volúmenes y configurar la replicación para clústeres extendidos. Consulte [Creación de volúmenes y configuración de la replicación para clústeres extendidos](../manage/create-stretched-volumes.md).
