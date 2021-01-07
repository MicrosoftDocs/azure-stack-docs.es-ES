---
title: Introducción a la infraestructura de almacenamiento de Azure Stack Hub
titleSuffix: Azure Stack
description: Aprenda a administrar la infraestructura de almacenamiento para Azure Stack Hub.
author: PatAltimore
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: patricka
ms.lastreviewed: 5/5/2020
ms.reviewer: jiaha
ms.custom: contperf-fy20q4
ms.openlocfilehash: 04a4e2314fb91a2a8a8eaedf0ca5d5849c8bee10
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97869175"
---
# <a name="azure-stack-hub-storage-infrastructure-overview"></a>Introducción a la infraestructura de almacenamiento de Azure Stack Hub

En este artículo se proporcionan conceptos sobre la infraestructura de almacenamiento de Azure Stack Hub. En él se incluye información acerca de las unidades y los volúmenes, y cómo se usan en Azure Stack Hub.

## <a name="drives"></a>Unidades

### <a name="drive-types"></a>Tipos de unidad

Los asociados del sistema integrado de Azure Stack Hub ofrecen muchas variantes de la solución, incluida una amplia gama de flexibilidad de almacenamiento. Puede seleccionar hasta **dos** tipos de unidad de los tres tipos de unidades compatibles:

1. NVMe (memoria rápida no volátil)
1. SSD SATA o SAS (unidad de estado sólido)
1. HDD (Unidad de disco duro)

### <a name="performance-vs-capacity"></a>Rendimiento frente a capacidad

Azure Stack Hub usa Espacios de almacenamiento directo (S2D) con los clústeres de conmutación por error de Windows Server. Esta combinación proporciona un servicio de almacenamiento eficaz, escalable y resistente.

Las implementaciones de Azure Stack pueden maximizar el rendimiento del almacenamiento o equilibrar el rendimiento y la capacidad.

Espacios de almacenamiento directo usa una memoria caché para maximizar el rendimiento del almacenamiento.

### <a name="how-drive-types-are-used"></a>Uso de los distintos tipos de unidad

Si un dispositivo de Azure Stack Hub tiene un tipo de unidad, todas las unidades se usan para capacidad.

Si hay dos tipos de unidad, Espacios de almacenamiento directo usa automáticamente todas las unidades del tipo más rápido (NVMe, SSD, HDD) para el almacenamiento en caché. Las unidades restantes se usan para la capacidad.

### <a name="all-flash-or-hybrid"></a>All-flash o híbrida

Las unidades se podrían agrupar en una implementación "all-flash" o en una "híbrida".

Todas las implementaciones all-flash tienen el objetivo de maximizar el rendimiento del almacenamiento y no incluyen unidades de disco duro (HDD) rotacionales.

![Diagrama que muestra un grupo de implementaciones all-flash.](media/azure-stack-storage-infrastructure-overview/image1.png)


Las implementaciones híbridas tienen como objetivo equilibrar el rendimiento y la capacidad o maximizar la capacidad, e incluyen unidades de disco duro (HDD) rotacionales.

![Diagrama que muestra un grupo de implementaciones híbridas.](media/azure-stack-storage-infrastructure-overview/image2.png)

### <a name="caching-behavior"></a>Comportamiento del almacenamiento en caché

El comportamiento de la memoria caché se determina automáticamente según los tipos de las unidades. Al realizar el almacenamiento en caché de las unidades de estado sólido (por ejemplo, almacenamiento en caché NVMe para SSD), solo las escrituras se almacenan en la caché. Esto reduce el desgaste de las unidades de capacidad, lo que disminuye el tráfico acumulativo en las unidades de capacidad y aumenta su duración.

Las lecturas no se almacenan en caché. Las lecturas no se almacenan en la caché porque no afectan significativamente a la duración de la memoria flash y porque las unidades de estado sólido ofrecen baja latencia de lectura de manera universal.

Al almacenar en caché las unidades de disco duro (por ejemplo, los discos SSD para las unidades HDD), tanto las lecturas como las escrituras se almacenan en la caché para proporcionar una latencia similar a la de la memoria flash (a menudo, 10 veces mejor) para ambas.

![Diagrama que muestra el comportamiento de almacenamiento en caché de Azure Stack Hub.](media/azure-stack-storage-infrastructure-overview/image3.svg)

Para la configuración de almacenamiento disponible, puede comprobar los asociados OEM de Azure Stack Hub (https://azure.microsoft.com/overview/azure-stack/partners/) ) para especificaciones detalladas.

> [!NOTE]
> El dispositivo de Azure Stack Hub se puede entregar en una implementación híbrida, con unidades HDD y SSD (o NVMe). No obstante, las unidades de tipo más rápido se usarían como unidades de caché y todas las unidades restantes se utilizarían como unidades de capacidad como un grupo. Los datos de inquilino (blobs, tablas, colas y discos) se colocarían en unidades de capacidad. El aprovisionamiento de discos prémium o la selección del tipo de cuenta de almacenamiento prémium no garantizan la asignación de los objetos en las unidades SSD o NVMe.

## <a name="volumes"></a>Volúmenes

El *servicio de almacenamiento* crea particiones del almacenamiento disponible en volúmenes separados que se asignan para almacenar los datos del sistema y del inquilino. Los volúmenes combinan las unidades del bloque de almacenamiento para proporcionar las ventajas de tolerancia a errores, escalabilidad y rendimiento de Espacios de almacenamiento directo.

![Diagrama que muestra las particiones del servicio de almacenamiento de Azure Stack Hub.](media/azure-stack-storage-infrastructure-overview/image4.svg)

### <a name="volume-types"></a>Tipos de volúmenes

En el bloque de almacenamiento de Azure Stack Hub se crean tres tipos de volúmenes:

1. Los volúmenes de **infraestructura** hospedan los archivos que se usan en las máquinas virtuales de infraestructura y los servicios principales de Azure Stack Hub.
1. Los volúmenes **temporales de máquina virtual** hospedan los discos temporales conectados a las máquinas virtuales de inquilino, y los datos se almacenan en estos discos.
1. Los volúmenes del **almacén de objetos** hospedan los datos de inquilino del mantenimiento de blobs, tablas, colas y discos de máquina virtual.

### <a name="volumes-in-a-multi-node-deployment"></a>Volúmenes en una implementación de varios nodos

En una implementación de varios nodos, hay tres volúmenes de infraestructura.

El número de volúmenes temporales de máquina virtual y de almacenamiento de objetos es igual al número de nodos en la implementación de Azure Stack Hub:

- En una implementación de cuatro nodos, hay cuatro volúmenes temporales de VM iguales y cuatro volúmenes de almacén de objetos iguales.

- Si agrega un nuevo nodo al clúster, se creará un nuevo volumen para ambos tipos.

- El número de volúmenes se mantiene aunque se quite un nodo o haya uno que no funcione correctamente.

> [!NOTE]
> Si utiliza el [Kit de desarrollo de Azure Stack](../asdk/index.yml), hay un único volumen con varios [recursos compartidos](azure-stack-manage-storage-shares.md).

### <a name="fault-tolerance-and-mirroring"></a>Tolerancia a errores y creación de reflejo

Los volúmenes de Espacios de almacenamiento directo proporcionan resistencia para la protección frente a problemas de hardware, como errores de unidad o de servidor. También habilitan la disponibilidad continua durante el mantenimiento del servidor, como las actualizaciones de software.

La creación de reflejo proporciona tolerancia a errores gracias al mantenimiento de varias copias de todos los datos. La manera de seccionar y colocar estos datos no es trivial ya que los datos almacenados mediante la creación de reflejo se escriben varias veces en su totalidad. Cada copia se escribe en distinto hardware físico (unidades diferentes en distintos servidores), que se supone que generan un error de forma independiente. 

La implementación de Azure Stack Hub usa la creación de reflejo tridireccional para garantizar la resistencia de datos. La creación de reflejo tridireccional puede tolerar sin ningún riesgo al menos dos de los problemas de hardware (unidad o servidor) a la vez. Por ejemplo, si se está reiniciando un servidor cuando, de repente, se produce un error en otra unidad u otro servidor, la seguridad y la accesibilidad de los datos se mantienen.

Se escriben tres copias de datos de inquilino en distintos servidores, donde se colocan en la memoria caché:

![Diagrama que muestra cómo se escriben tres copias de datos de inquilino en distintos servidores.](media/azure-stack-storage-infrastructure-overview/image5.png)

## <a name="next-step"></a>Paso siguiente

[Administración de la capacidad de almacenamiento](azure-stack-manage-storage-shares.md) 
