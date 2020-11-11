---
title: Consideraciones sobre la simetría de unidades para Azure Stack HCI
description: En este tema se explican las restricciones en cuanto a simetría de unidades y se proporcionan ejemplos de configuraciones compatibles y no compatibles.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/29/2020
ms.openlocfilehash: 6cf983d6cf64b0b41bb9710bdf720dd1777c9ad6
ms.sourcegitcommit: 296c95cad20ed62bdad0d27f1f5246bfc1c81d5e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/30/2020
ms.locfileid: "93064810"
---
# <a name="drive-symmetry-considerations-for-azure-stack-hci"></a>Consideraciones sobre la simetría de unidades para Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

Azure Stack HCI funciona de manera óptima si cada servidor tiene exactamente las mismas unidades.

En realidad, somos conscientes de que esto no siempre puede ponerse en práctica, porque Azure Stack HCI está diseñado para ejecutarse durante años y escalarse a medida que aumenten las necesidades de la organización. Hoy en día, es posible comprar espaciosos discos duros de 3 TB, pero al año siguiente, quizá resulte imposible encontrar unidades tan pequeñas con ese tamaño. Por lo tanto, se prevé y se admite cierto grado de combinación. No obstante, tenga en cuenta que siempre es mejor tener la mayor simetría posible.

En este tema se explican las restricciones y se proporcionan ejemplos de configuraciones compatibles y no compatibles.

## <a name="constraints"></a>Restricciones

En esta sección se explican las restricciones en cuanto al tipo de unidad, el modelo, el tamaño y el número de unidades.

### <a name="type"></a>Tipo

Todos los servidores deben tener los mismos [tipos de unidades](choose-drives.md#drive-types).

Por ejemplo, si un servidor tiene NVMe, *todos* deben tener NVMe.

### <a name="number"></a>Number

Todos los servidores deben tener el mismo número de unidades de cada tipo.

Por ejemplo, si un servidor tiene seis SSD, *todos* deben tener seis SSD.

   > [!NOTE]
   > El número de unidades puede diferir temporalmente durante los errores o al agregar o quitar unidades.

### <a name="model"></a>Modelo

Siempre que sea posible, se recomienda usar unidades del mismo modelo y versión de firmware. Si no es posible, elija cuidadosamente unidades que sean lo más parecidas posible. Se desaconseja combinar unidades del mismo tipo con características de rendimiento o resistencia muy diferentes (a menos que una sea de caché y la otra sea de capacidad), porque Azure Stack HCI distribuye la E/S uniformemente y no discrimina según el modelo.

   > [!NOTE]
   > Se pueden combinar unidades SATA y SAS similares.

### <a name="size"></a>Size

Se recomienda usar unidades del mismo tamaño siempre que sea posible. El uso de unidades de capacidad de distintos tamaños podría dar lugar a una capacidad inutilizable, y el uso de unidades de caché de distintos tamaños podría no resultar útil para mejorar el rendimiento de la memoria caché. Para obtener más información, vea la siguiente sección.

   > [!WARNING]
   > El uso de unidades de capacidad de diferentes tamaños entre servidores puede provocar una capacidad inmovilizada.

## <a name="understand-capacity-imbalance"></a>Descripción: desequilibrio de capacidad

Azure Stack HCI es estable en términos de desequilibrio de capacidad entre unidades y servidores. Incluso si el desequilibrio es grave, todo seguirá funcionando. Sin embargo, en función de varios factores, es posible que no se pueda usar la capacidad que no esté disponible en todos los servidores.

Para conocer por qué ocurre esto, examine la ilustración simplificada a continuación. Cada cuadro con color representa una copia de los datos reflejados. Por ejemplo, los cuadros marcados como, A, A' y A'' son tres copias de los mismos datos. Para respetar la tolerancia a errores del servidor, estas copias *deben* almacenarse en servidores diferentes.

### <a name="stranded-capacity"></a>Capacidad inmovilizada

Tal como se muestra en la ilustración, el servidor 1 (10 TB) y el servidor 2 (10 TB) están llenos. El servidor 3 tiene unidades más grandes, por lo que su capacidad total es mayor (15 TB). Sin embargo, para almacenar más datos de reflejo triple en el servidor 3, también se necesitarían copias en el servidor 1 y el servidor 2, que ya están llenos. La capacidad restante de 5 TB del servidor 3 no se puede usar; se trata de capacidad *inmovilizada*.

![Reflejo triple, tres servidores, capacidad inmovilizada](media/drive-symmetry-considerations/Size-Asymmetry-3N-Stranded.png)

### <a name="optimal-placement"></a>Selección de ubicación óptima

Por el contrario, con cuatro servidores de 10 TB, 10 TB, 10 TB y 15 TB de capacidad y una resistencia de reflejo triple, *se* pueden colocar de manera válida copias de una manera que use toda la capacidad disponible, tal como se muestra en la ilustración. Siempre que esto sea posible, el asignador de Espacios de almacenamiento directo buscará y usará la selección de ubicación óptima, sin dejar capacidad inmovilizada.

![Reflejo triple, cuatro servidores, sin capacidad inmovilizada](media/drive-symmetry-considerations/Size-Asymmetry-4N-No-Stranded.png)

El número de servidores, la resistencia, la gravedad del desequilibrio de capacidad y otros factores influyen en la existencia de capacidad inmovilizada. **La regla general más prudente es suponer que solo se garantiza que se pueda usar la capacidad disponible en cada servidor.**

## <a name="understand-cache-imbalance"></a>Descripción: desequilibrio de caché

Azure Stack HCI es estable en términos de desequilibrio de caché entre unidades y servidores. Incluso si el desequilibrio es grave, todo seguirá funcionando. Además, Azure Stack HCI siempre usa toda la memoria caché disponible al máximo.

Sin embargo, es posible que el uso de unidades de caché de distintos tamaños no mejore el rendimiento de la memoria caché de forma uniforme o previsible: solo la E/S para [enlaces de unidad](cache.md#server-side-architecture) con unidades de caché más grandes puede tener un rendimiento mejorado. Azure Stack HCI distribuye la E/S uniformemente entre enlaces y no discrimina en función de la relación caché-capacidad.

![Desequilibrio de caché](media/drive-symmetry-considerations/Cache-Asymmetry.png)

   > [!TIP]
   > Consulte [Descripción de la memoria caché en Azure Stack HCI](cache.md) para obtener más información sobre los enlaces de caché.

## <a name="example-configurations"></a>Configuraciones de ejemplo

A continuación se describen algunas configuraciones compatibles y no compatibles:

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-models-between-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Compatible: diferentes modelos entre servidores

Los dos primeros servidores usan el modelo "X" de NVMe, pero el tercer servidor usa el modelo "Z" de NVMe, que es muy similar.

| Servidor 1                    | Servidor 2                    | Servidor 3                    |
|-----------------------------|-----------------------------|-----------------------------|
| 2 NVMe modelo X (caché)    | 2 NVMe modelo X (caché)    | 2 NVMe modelo Z (caché)    |
| 10 SSD modelo Y (capacidad) | 10 SSD modelo Y (capacidad) | 10 SSD modelo Y (capacidad) |

Esta configuración es compatible.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-models-within-server"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Compatible: diferentes modelos en el servidor

Cada servidor utiliza una combinación diferente de modelos de HDD "Y" y "Z", que son muy similares. Cada servidor tiene 10 HDD en total.

| Servidor 1                   | Servidor 2                   | Servidor 3                   |
|----------------------------|----------------------------|----------------------------|
| 2 SSD modelo X (caché)    | 2 SSD modelo X (caché)    | 2 SSD modelo X (caché)    |
| 7 HDD modelo Y (capacidad) | 5 HDD modelo Y (capacidad) | 1 HDD modelo Y (capacidad) |
| 3 HDD modelo Z (capacidad) | 5 HDD modelo Z (capacidad) | 9 HDD modelo Z (capacidad) |

Esta configuración es compatible.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-sizes-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Compatible: tamaños diferentes entre servidores

Los dos primeros servidores usan una unidad de disco duro de 4 TB, pero el tercer servidor usa un HDD de 6 TB muy similar.

| Servidor 1                | Servidor 2                | Servidor 3                |
|-------------------------|-------------------------|-------------------------|
| 2 NVMe de 800 GB (caché) | 2 NVMe de 800 GB (caché) | 2 NVMe de 800 GB (caché) |
| 4 HDD de 4 TB (capacidad) | 4 HDD de 4 TB (capacidad) | 4 HDD de 6 TB (capacidad) |

Si bien esto es compatible, producirá una capacidad inmovilizada.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationssupportedpng-borderfalse-supported-different-sizes-within-server"></a>:::image type="icon" source="media/drive-symmetry-considerations/supported.png" border="false"::: Compatible: diferentes modelos en el servidor

Cada servidor utiliza una combinación diferente de SSD de 1,2 TB y de 1,6 TB muy similar. Cada servidor tiene 4 SSD en total.

| Servidor 1                 | Servidor 2                 | Servidor 3                 |
|--------------------------|--------------------------|--------------------------|
| 3 SSD de 1,2 TB (caché)   | 2 SSD de 1,2 TB (caché)   | 4 SSD de 1,2 TB (caché)   |
| 1 SSD de 1,6 TB (caché)   | 2 SSD de 1,6 TB (caché)   | -                        |
| 20 HDD de 4 TB (capacidad) | 20 HDD de 4 TB (capacidad) | 20 HDD de 4 TB (capacidad) |

Esta configuración es compatible.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-different-types-of-drives-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: No compatible: diferentes tipos de unidades entre servidores

El servidor 1 tiene NVMe, pero los otros no.

| Servidor 1            | Servidor 2            | Servidor 3            |
|---------------------|---------------------|---------------------|
| 6 NVMe (caché)    | -                   | -                   |
| -                   | 6 SSD (caché)     | 6 SSD (caché)     |
| 18 HDD (capacidad) | 18 HDD (capacidad) | 18 HDD (capacidad) |

No es una opción admitida. Los tipos de unidades deben ser los mismos en todos los servidores.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-different-number-of-each-type-across-servers"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: No compatible: número diferente de cada tipo entre servidores

El servidor 3 tiene más unidades que los demás.

| Servidor 1            | Servidor 2            | Servidor 3            |
|---------------------|---------------------|---------------------|
| 2 NVMe (caché)    | 2 NVMe (caché)    | 4 NVMe (caché)    |
| 10 HDD (capacidad) | 10 HDD (capacidad) | 20 HDD (capacidad) |

No es una opción admitida. El número de unidades de cada tipo debe ser el mismo en todos los servidores.

### <a name="image-typeicon-sourcemediadrive-symmetry-considerationsunsupportedpng-borderfalse-not-supported-only-hdd-drives"></a>:::image type="icon" source="media/drive-symmetry-considerations/unsupported.png" border="false"::: No compatible: solo unidades HDD

Todos los servidores tienen unidades HDD conectadas solamente.

|Servidor 1|Servidor 2|Servidor 3|
|-|-|-|
|18 HDD (capacidad) |18 HDD (capacidad)|18 HDD (capacidad)|

No es una opción admitida. Se debe agregar un mínimo de dos unidades de caché (NVMe o SSD) conectadas a cada uno de los servidores.

## <a name="summary"></a>Resumen

En resumen, cada servidor del clúster debe tener los mismos tipos de unidades y el mismo número de cada tipo. Se admite la combinación de modelos y tamaños de unidad según sea necesario, siempre que se respeten las consideraciones descritas anteriormente.

| Restricción | State |
|--|--|
| Mismos tipos de unidades en cada servidor | **Requerido** |
| Mismo número de cada tipo en cada servidor | **Requerido** |
| Mismos modelos de unidad en cada servidor | Recomendado |
| Mismos tamaños de unidad en cada servidor | Recomendado |

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Requisitos del sistema](system-requirements.md)
- [Elegir las unidades](choose-drives.md)
