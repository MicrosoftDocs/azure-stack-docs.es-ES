---
title: Selección de unidades para Azure Stack HCI
description: Procedimiento para seleccionar unidades para Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 09/01/2020
ms.openlocfilehash: c53ef48ab191a831a981c0a0c91b59efa766c948
ms.sourcegitcommit: 08a421ab5792ab19cc06b849763be22f051e6d78
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/02/2020
ms.locfileid: "89364718"
---
# <a name="choose-drives-for-azure-stack-hci"></a>Selección de unidades para Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

En este tema se proporcionan instrucciones sobre cómo elegir las unidades para satisfacer los requisitos de rendimiento y capacidad de Azure Stack HCI.

## <a name="drive-types"></a>Tipos de unidad

Azure Stack HCI actualmente funciona con cuatro tipos de unidades:

| Tipo de unidad | Descripción |
|----------------------|--------------------------|
|![PMem](media/choose-drives/pmem-100px.png)|**PMem** hace referencia a la memoria persistente, un nuevo tipo de almacenamiento de baja latencia y alto rendimiento.|
|![NVMe](media/choose-drives/NVMe-100-px.png)|**NVMe** (memoria no volátil rápida) hace referencia a las unidades de estado sólido que se colocan directamente en el bus PCIe. Los factores de forma comunes son 2,5" U.2, AIC (Add-In Card) PCIe y M.2. NVMe ofrece mayor rendimiento de IOPS y E/S con latencia más baja que ningún otro tipo de unidad que se admita hoy en día excepto PMem.|
|![SSD](media/choose-drives/SSD-100-px.png)|**SSD** hace referencia a las unidades de estado sólido, que se conectan a través de un dispositivo SATA o SAS convencional.|
|![HDD](media/choose-drives/HDD-100-px.png)|**HDD** hace referencia a unidades de disco duro magnéticas de rotación que ofrecen gran capacidad de almacenamiento.|

## <a name="built-in-cache"></a>Memoria caché integrada

Azure Stack HCI incluye una memoria caché integrada del lado servidor. Se trata de una caché de lectura y escritura grande, persistente y en tiempo real. En implementaciones con varios tipos de unidades, se configura automáticamente para usar todas las unidades del tipo "más rápido". Las unidades restantes se usan para la capacidad.

Para más información, consulte [Descripción de la memoria caché de Azure Stack HCI](cache.md).

## <a name="option-1--maximizing-performance"></a>Opción 1: Maximización del rendimiento

Para lograr latencias inferiores al milisegundo predecibles y uniformes entre lecturas y escrituras aleatorias en cualquier dato, o para lograr un número de IOPS (se han realizado [más de 13 millones](https://techcommunity.microsoft.com/t5/storage-at-microsoft/the-new-hci-industry-record-13-7-million-iops-with-windows/ba-p/428314)) o un rendimiento de E/S (se han realizado lectura a más de 500 GB/segundo) extremadamente altos, es necesario usar todo flash.

Hay varias formas de hacerlo:

![Posibilidades de implementación de todo flash](media/choose-drives/All-Flash-Deployment-Possibilities.png)

1. **Todo NVMe.** El uso de todo NVMe proporciona un rendimiento incomparable, incluida la baja latencia más predecible. Si todas las unidades son el mismo modelo, no hay ninguna caché. También es posible combinar modelos de NVMe de alta y baja resistencia, y configurar el primero para almacenar en caché las escrituras del segundo ([requiere instalación](/windows-server/storage/storage-spaces/understand-the-cache#manual-configuration)).

2. **NVMe + SSD.** El uso combinado de NVMe y SSD permite que NVMe almacene automáticamente en caché las escrituras en las unidades SSD. De esta manera, las escrituras se fusionan en la caché y se anula su almacenamiento provisional solo cuando es necesario, para reducir el desgaste en las SSD. Este procedimiento proporciona características de escritura de tipo NVMe, mientras que las lecturas se atienden directamente desde las SSD también rápidas.

3. **Todo SSD.** Como con todo NVMe, no hay ninguna caché si todas las unidades tienen el mismo modelo. Si combina modelos de mayor y menor resistencia, puede configurar el primero para almacenar en caché las escrituras del segundo ([requiere instalación](/windows-server/storage/storage-spaces/understand-the-cache#manual-configuration)).

   >[!NOTE]
   > Una ventaja de usar todo NVMe o todo SSD sin caché es que se obtiene la capacidad de almacenamiento utilizable de todas las unidades. No hay ninguna capacidad "dedicada" al almacenamiento en caché, lo que puede resultar atractivo a menor escala.

## <a name="option-2--balancing-performance-and-capacity"></a>Opción 2: Equilibrio de rendimiento y capacidad

En el caso de entornos con una gran variedad de aplicaciones y cargas de trabajo, algunos con requisitos de rendimiento rigurosos y otros que requieren una capacidad de almacenamiento considerable, debe decantarse por una opción "híbrida" con almacenamiento en caché NVMe o SSD para unidades HDD de mayor tamaño.

![Posibilidades de implementación híbrida](media/choose-drives/Hybrid-Deployment-Possibilities.png)

1. **NVMe + HDD**. Las unidades NVMe acelerarán las lecturas y escrituras al almacenar en caché ambas. El almacenamiento en caché de las lecturas permite que las unidades HDD se centren en las escrituras. El almacenamiento en caché de las escrituras absorbe las ráfagas y permite que las escrituras se fusionen y se anule su almacenamiento provisional, según sea necesario, de una manera serializada artificialmente que maximice el rendimiento de IOPS y E/S de HDD. Este procedimiento proporciona características de escritura de tipo NVMe y, para los datos leídos con frecuencia o recientemente, también características de lectura de tipo NVMe.

2. **SSD + HDD**. De forma similar a lo anterior, las SSD acelerarán las lecturas y escrituras al almacenar en caché ambas. Como resultado, se proporcionan características de lectura y escritura de tipo SSD para datos leídos con frecuencia o recientemente.

    Hay una opción adicional más exótica: usar unidades de *los tres* tipos.

3. **NVMe + SSD + HDD.** Con las unidades de los tres tipos, las unidades NVMe realizan el almacenamiento en caché de los SSD y HDD. Lo interesante es que puede crear volúmenes en SSD y en HHD, en paralelo en el mismo clúster, todos ellos acelerados mediante NVMe. Los primeros son exactamente como en una implementación todo flash, y los segundos son exactamente como en las implementaciones "híbridas" descritas anteriormente. Desde un punto de vista conceptual, es como tener dos grupos, con ciclos de reparación, errores y administración de la capacidad en gran medida independientes, etc.

   >[!IMPORTANT]
   > Se recomienda usar el nivel SSD para colocar las cargas de trabajo más sensibles al rendimiento en todo flash.

## <a name="option-3--maximizing-capacity"></a>Opción 3: Maximización de la capacidad

En el caso de cargas de trabajo que requieran gran capacidad y poca escritura, como archivado, destinos de copia de seguridad, almacenes de datos o almacenamiento "en frío", debe combinar algunos SSD para el almacenamiento en caché con muchos HDD para la capacidad.

![Opciones de implementación para maximizar la capacidad](media/choose-drives/maximizing-capacity.png)

1. **SSD + HDD**. Las SSD almacenarán en caché las lecturas y escrituras para absorber las ráfagas y proporcionarán rendimiento de escritura de tipo SSD, con anulación de almacenamiento provisional optimizada más tarde en las HDD.

>[!IMPORTANT]
>No se admite la configuración con solo HDD. No se recomienda el almacenamiento en caché de SSD de alta resistencia en SSD de baja resistencia.

## <a name="sizing-considerations"></a>Consideraciones de tamaño

### <a name="cache"></a>Cache

Cada servidor debe tener al menos dos unidades de caché (el mínimo necesario para la redundancia). Se recomienda que el número de unidades de capacidad sea un múltiplo del número de unidades de caché. Por ejemplo, si tiene 4 unidades de caché, experimentará un rendimiento más coherente con 8 unidades de capacidad (relación 1:2) que con 7 o 9.

El tamaño de la caché debe ajustarse para adaptarse al espacio de trabajo de las aplicaciones y las cargas de trabajo, es decir, todos los datos que leen y escriben activamente en un momento dado. Aparte de eso, no hay ningún requisito de tamaño de la caché. En el caso de implementaciones con unidades HDD, un buen punto de partida es un 10 por ciento de la capacidad; por ejemplo, si cada servidor tiene 4 HDD de 4 TB = 16 TB de capacidad, entonces 2 SSD de 800 GB = 1,6 TB de caché por servidor. En el caso de las implementaciones todo-flash, especialmente con unidades SSD de [alta resistencia](https://techcommunity.microsoft.com/t5/storage-at-microsoft/understanding-ssd-endurance-drive-writes-per-day-dwpd-terabytes/ba-p/426024), sería más justo empezar más cerca del 5 por ciento de capacidad; por ejemplo, si cada servidor tiene 24 SSD de 1,2 TB = 28,8 TB de capacidad, entonces 2 NVMe de 750 GB = 1,5 TB de caché por servidor. Siempre puede agregar o quitar unidades de caché más adelante para realizar los ajustes necesarios.

### <a name="general"></a>General

Se recomienda limitar la capacidad total de almacenamiento por servidor a aproximadamente 400 terabytes (TB). Cuanto mayor sea la capacidad de almacenamiento por servidor, mayor será el tiempo necesario para volver a sincronizar los datos después del tiempo de inactividad o el reinicio, como cuando se aplican actualizaciones de software. El tamaño máximo actual por grupo de almacenamiento es de 4 petabytes (PB) (4000 TB) para Windows Server 2019.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, vea también:

- [Descripción de la memoria caché](cache.md)
- [Determinar los requisitos de hardware](../deploy/before-you-start.md#determine-hardware-requirements)
- [Consideraciones de simetría de unidad](drive-symmetry-considerations.md)
- [Planeamiento de volúmenes](plan-volumes.md)
- [Tolerancia a errores y eficiencia del almacenamiento](fault-tolerance.md)
- [Descripción e implementación de memoria persistente](/windows-server/storage/storage-spaces/deploy-pmem)
