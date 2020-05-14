---
title: Introducción a la infraestructura de almacenamiento de Azure Stack Hub
titleSuffix: Azure Stack
description: Aprenda a administrar la infraestructura de almacenamiento para Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 5/11/2020
ms.author: inhenkel
ms.lastreviewed: 5/5/2020
ms.reviewer: jiaha
ms.custom: contperfq4
ms.openlocfilehash: 0712caec89d3a6e2203ca780b4877b330953c61c
ms.sourcegitcommit: 4a8d7203fd06aeb2c3026d31ffec9d4fbd403613
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/12/2020
ms.locfileid: "83202489"
---
# <a name="azure-stack-hub-storage-infrastructure-overview"></a>Introducción a la infraestructura de almacenamiento de Azure Stack Hub

En este artículo se proporciona información general sobre la infraestructura de almacenamiento de Azure Stack Hub.

## <a name="understand-drives-and-volumes"></a>Explicación de las unidades y los volúmenes

### <a name="drives"></a>Unidades

Con tecnología de software de Windows Server, Azure Stack Hub define las funcionalidades de almacenamiento con una combinación de Espacios de almacenamiento directo (S2D) y clústeres de conmutación por error de Windows Server. Esta combinación proporciona un servicio de almacenamiento eficaz, escalable y resistente.

Los asociados del sistema integrado de Azure Stack Hub ofrecen muchas variantes de la solución, incluida una amplia gama de flexibilidad de almacenamiento. Actualmente, puede seleccionar hasta dos tipos de unidad de los tres tipos de unidades compatibles: NVMe (memoria rápida no volátil), SSD SATA o SAS (unidad de estado sólido), HDD (unidad de disco duro). 

Espacios de almacenamiento directo incluye una memoria caché para maximizar el rendimiento del almacenamiento. En un dispositivo de Azure Stack Hub con un tipo de unidad (es decir, NVMe o SSD), todas las unidades se usan con fines de almacenamiento. Si hay dos tipos de unidad, Espacios de almacenamiento directo usa automáticamente todas las unidades del tipo más rápido (NVMe, SSD, HDD) para el almacenamiento en caché. Las unidades restantes se usan para la capacidad. Las unidades se podrían agrupar en una implementación "all-flash" o "híbrida":

![Infraestructura de almacenamiento de Azure Stack Hub](media/azure-stack-storage-infrastructure-overview/image1.png)

Todas las implementaciones all-flash tienen el objetivo de maximizar el rendimiento del almacenamiento y no incluyen unidades de disco duro (HDD) rotacionales.

![Infraestructura de almacenamiento de Azure Stack Hub](media/azure-stack-storage-infrastructure-overview/image2.png)

Las implementaciones híbridas tienen como objetivo equilibrar el rendimiento y la capacidad o maximizar la capacidad, e incluyen unidades de disco duro (HDD) rotacionales.

El comportamiento de la memoria caché se determina automáticamente según los tipos de las unidades de las que se realiza el almacenamiento en caché. Al realizar el almacenamiento en caché de las unidades de estado sólido (por ejemplo, almacenamiento en caché NVMe para SSD), solo las escrituras se almacenan en la caché. Esto reduce el desgaste de las unidades de capacidad, lo que disminuye el tráfico acumulativo en las unidades de capacidad y aumenta su duración. Mientras tanto, las lecturas no se almacenan en la caché. Las lecturas no se almacenan en la caché porque no afectan significativamente a la duración de la memoria flash y porque las unidades de estado sólido ofrecen baja latencia de lectura de manera universal. Al almacenar en caché las unidades de disco duro (por ejemplo, los discos SSD para las unidades HDD), tanto las lecturas como las escrituras se almacenan en la caché para proporcionar una latencia similar a la de la memoria flash (a menudo, 10 veces mejor) para ambas.

![Infraestructura de almacenamiento de Azure Stack Hub](media/azure-stack-storage-infrastructure-overview/image3.png)

Para la configuración de almacenamiento disponible, puede comprobar los asociados OEM de Azure Stack Hub (https://azure.microsoft.com/overview/azure-stack/partners/)) para especificaciones detalladas.

> [!Note]  
> El dispositivo de Azure Stack Hub se puede entregar en una implementación híbrida, con unidades HDD y SSD (o NVMe). No obstante, las unidades de tipo más rápido se usarían como unidades de caché y todas las unidades restantes se utilizarían como unidades de capacidad como un grupo. Los datos de inquilino (blobs, tablas, colas y discos) se colocarían en unidades de capacidad. El aprovisionamiento de discos prémium o la selección del tipo de cuenta de almacenamiento prémium no garantizan la asignación de los objetos en las unidades SSD o NVMe.

### <a name="volumes"></a>Volúmenes

El *servicio de almacenamiento* crea particiones del almacenamiento disponible en volúmenes separados que se asignan para almacenar los datos del sistema y del inquilino. Los volúmenes combinan las unidades del bloque de almacenamiento para proporcionar las ventajas de tolerancia a errores, escalabilidad y rendimiento de Espacios de almacenamiento directo.

![Infraestructura de almacenamiento de Azure Stack Hub](media/azure-stack-storage-infrastructure-overview/image4.png)

En el bloque de almacenamiento de Azure Stack Hub se crean tres tipos de volúmenes:

- Infraestructura: hospeda los archivos que se usan en las máquinas virtuales de infraestructura y los servicios principales de Azure Stack Hub.

- Temporal de VM: hospeda los discos temporales conectados a VM de inquilino, y los datos se almacenan en estos discos.

- Almacén de objetos: hospeda los datos de inquilino de mantenimiento de blobs, tablas, colas y discos de máquina virtual.

En una implementación de varios nodos, verá tres volúmenes de infraestructura, mientras que el número de volúmenes temporales de máquina virtual y volúmenes de almacén de objetos es igual al número de los nodos de la implementación de Azure Stack Hub:

- En una implementación de cuatro nodos, hay cuatro volúmenes temporales de VM iguales y cuatro volúmenes de almacén de objetos iguales.

- Si agrega un nuevo nodo al clúster, se creará un nuevo volumen para ambos tipos.

- El número de volúmenes se mantiene aunque se quite un nodo o haya uno que no funcione correctamente.

- Si utiliza el Kit de desarrollo de Azure Stack, hay un único volumen con varios recursos compartidos.

Los volúmenes de Espacios de almacenamiento directo proporcionan resistencia para la protección frente a problemas de hardware, como errores de unidad o de servidor. También habilitan la disponibilidad continua durante el mantenimiento del servidor, como las actualizaciones de software. La implementación de Azure Stack Hub usa la creación de reflejo tridireccional para garantizar la resistencia de datos. Se escriben tres copias de datos de inquilino en distintos servidores, donde se colocan en la memoria caché:

![Infraestructura de almacenamiento de Azure Stack Hub](media/azure-stack-storage-infrastructure-overview/image5.png)

La creación de reflejo proporciona tolerancia a errores gracias al mantenimiento de varias copias de todos los datos. La manera de seccionar y colocar estos datos no es trivial, pero es cierto afirmar que los datos almacenados mediante la creación de reflejo se escriben varias veces en su totalidad. Cada copia se escribe en distinto hardware físico (unidades diferentes en distintos servidores), que se supone que generan un error de forma independiente. La creación de reflejo tridireccional puede tolerar sin ningún riesgo al menos dos de los problemas de hardware (unidad o servidor) a la vez. Por ejemplo, si se está reiniciando un servidor cuando, de repente, se produce un error en otra unidad u otro servidor, la seguridad y la accesibilidad de los datos se mantienen.

## <a name="next-step"></a>Paso siguiente

[Administración de la capacidad de almacenamiento](azure-stack-manage-storage-shares.md) 
