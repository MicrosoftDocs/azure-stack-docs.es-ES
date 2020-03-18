---
title: Tolerancia a errores y eficacia del almacenamiento en Azure Stack HCI
description: Descripción de las opciones de resistencia en Espacios de almacenamiento directo, como la creación de reflejo y la paridad.
author: khdownie
ms.author: v-kedow
ms.topic: article
ms.date: 02/28/2020
ms.openlocfilehash: 9ace3960b4c54461a4153c4997694e6d17ee4fd1
ms.sourcegitcommit: a77dea675af6500bdad529106f5782d86bec6a34
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/10/2020
ms.locfileid: "79026119"
---
# <a name="fault-tolerance-and-storage-efficiency-in-azure-stack-hci"></a>Tolerancia a errores y eficacia del almacenamiento en Azure Stack HCI

>Se aplica a: Windows Server 2019

En este tema se presentan las opciones de resistencia disponibles en Espacios de almacenamiento directo y se describen los requisitos de escala, la eficacia de almacenamiento y las ventajas y desventajas generales de cada una. También presenta algunas instrucciones de uso para empezar a trabajar y se incluyen referencias a artículos, blogs y contenidos adicionales interesantes donde puede obtener más información.

Si ya está familiarizado con Espacios de almacenamiento, puede que desee ir directamente a la sección [Resumen](#summary).

## <a name="overview"></a>Información general

Espacios de almacenamiento proporciona esencialmente tolerancia a errores, a menudo denominada "resistencia", para los datos. Su implementación es similar a RAID, excepto que se distribuye entre servidores y se implementa en software.

Al igual que con RAID, Espacios de almacenamiento puede hacer esto de varias maneras, con distintas ventajas y desventajas para la tolerancia a errores, la eficacia de almacenamiento y la complejidad de los procesos. En general, existen dos categorías: "creación de reflejo" y "paridad" (también denominada "codificación de borrado").

## <a name="mirroring"></a>Creación de reflejo

La creación de reflejo proporciona tolerancia a errores gracias al mantenimiento de varias copias de todos los datos. Esto se parece más a RAID-1. La manera de seccionar y colocar estos datos no es trivial (consulte [este blog](https://blogs.technet.microsoft.com/filecab/2016/11/21/deep-dive-pool-in-spaces-direct/) para obtener más información), pero es absolutamente cierto que los datos almacenados mediante la creación de reflejo se escriben, en su totalidad, varias veces. Cada copia se escribe en distinto hardware físico (unidades diferentes en distintos servidores), que se supone que generan un error de forma independiente.

Espacios de almacenamiento ofrece dos tipos de creación de reflejo: "bidireccional" y "tridireccional".

### <a name="two-way-mirror"></a>Reflejo bidireccional

La creación de reflejo bidireccional escribe dos copias de todo. Su eficacia de almacenamiento es del 50 por ciento; para escribir 1 TB de datos, necesita al menos 2 TB de capacidad de almacenamiento físico. Del mismo modo, necesita al menos dos [dominios de error de hardware](/windows-server/failover-clustering/fault-domains); con Espacios de almacenamiento directo, esto significa dos servidores.

![two-way-mirror](media/fault-tolerance/two-way-mirror-180px.png)

   >[!WARNING]
   > Si tiene más de dos servidores, se recomienda utilizar la creación de reflejo triple en su lugar.

### <a name="three-way-mirror"></a>Reflejo triple

La creación de reflejo triple escribe tres copias de todo. Su eficacia de almacenamiento es del 33,3 por ciento; para escribir 1 TB de datos, necesita al menos 3 TB de capacidad de almacenamiento físico. Del mismo modo, necesita al menos tres dominios de error de hardware; con Espacios de almacenamiento directo, esto significa tres servidores.

La creación de reflejo triple puede tolerar de forma segura al menos [dos problemas de hardware (unidad o servidor) a la vez](#examples). Por ejemplo, si se está reiniciando un servidor cuando, de repente, se produce un error en otra unidad u otro servidor, la seguridad y la accesibilidad de los datos se mantienen.

![three-way-mirror](media/fault-tolerance/three-way-mirror-180px.png)

## <a name="parity"></a>Parity

La codificación de paridad, que a menudo se denomina "codificación de borrado", proporciona tolerancia a errores mediante aritmética bit a bit, lo que puede resultar [considerablemente complicado](https://www.microsoft.com/research/wp-content/uploads/2016/02/LRC12-cheng20webpage.pdf). Su funcionamiento no es tan evidente como el de la creación de reflejo. Hay muchos recursos en línea excelentes (por ejemplo, la guía de terceros [Dummies Guide to Erasure Coding](http://smahesh.com/blog/2012/07/01/dummies-guide-to-erasure-coding/) [Guía sobre codificación de borrado para principiantes]) que pueden ayudarle a familiarizarse. Basta decir que proporciona una mayor eficacia de almacenamiento sin comprometer la tolerancia a errores.

Espacios de almacenamiento ofrece dos tipos de paridad: paridad "única" y paridad "dual"; esta última emplea una técnica avanzada denominada "códigos de reconstrucción local" a mayor escala.

> [!IMPORTANT]
> Se recomienda utilizar la creación de reflejo para la mayoría de las cargas de trabajo sensibles al rendimiento. Para más información sobre cómo equilibrar el rendimiento y la capacidad en función de la carga de trabajo, consulte [Planeamiento de volúmenes](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type).

### <a name="single-parity"></a>Paridad única
La paridad única solo mantiene un símbolo de paridad bit a bit, lo que proporciona tolerancia a errores frente a un solo error cada vez. Esto se parece más a RAID-5. Para usar la paridad única, necesita al menos tres dominios de error de hardware; con Espacios de almacenamiento directo, esto significa tres servidores. Dado que la creación de reflejo triple proporciona una mayor tolerancia a errores a la misma escala, se desaconseja el uso de paridad única. No obstante, dispone de la posibilidad de usarla si así lo desea y es completamente compatible.

   >[!WARNING]
   > Desaconsejamos el uso de la paridad única porque solo puede tolerar de manera segura un error de hardware cada vez: si está reiniciando un servidor y de repente se produce un error en otra unidad o servidor, experimentará un tiempo de inactividad. Si solo tiene tres servidores, se recomienda utilizar la creación de reflejo triple. Si tiene cuatro o más, consulte la sección siguiente.

### <a name="dual-parity"></a>Paridad dual

La paridad dual implementa códigos de corrección de errores Reed-Solomon para mantener dos símbolos de paridad bit a bit, lo que proporciona la misma tolerancia a errores que la creación de reflejo triple (es decir, hasta dos errores a la vez), pero con una mayor eficacia de almacenamiento. Esto se parece más a RAID-6. Para usar la paridad dual, necesita al menos cuatro dominios de error de hardware; con Espacios de almacenamiento directo, esto significa cuatro servidores. A esa escala, la eficacia de almacenamiento es del 50 %; para escribir 2 TB de datos, necesita 4 TB de capacidad de almacenamiento físico.

![dual-parity](media/fault-tolerance/dual-parity-180px.png)

La eficacia de almacenamiento de la paridad dual aumenta cuantos más dominios de error de hardware tiene, del 50 % al 80 %. Por ejemplo, con siete (con Espacios de almacenamiento directo, eso significa siete servidores) la eficacia alcanza el 66,7%: para almacenar 4 TB de datos, solo necesita 6 TB de capacidad de almacenamiento físico.

![dual-parity-wide](media/fault-tolerance/dual-parity-wide-180px.png)

Consulte la sección [Resumen](#summary) para ver la eficacia de la paridad dual y los códigos de reconstrucción local en cada escala.

### <a name="local-reconstruction-codes"></a>Códigos de reconstrucción local

Espacios de almacenamiento introduce una técnica avanzada desarrollada por Microsoft Research denominada "códigos de reconstrucción local" (LRC). A gran escala, la paridad dual usa LRC para dividir su codificación/descodificación en unos pocos grupos más pequeños, a fin de reducir la sobrecarga necesaria para realizar operaciones de escritura o recuperación de errores.

Con unidades de disco duro (HDD), el tamaño del grupo es de cuatro símbolos; con unidades de estado sólido (SSD), el tamaño del grupo es de seis símbolos. Por ejemplo, este es el aspecto del diseño con unidades de disco duro y 12 dominios de error de hardware (es decir, 12 servidores): hay dos grupos de cuatro símbolos de datos. Logra una eficacia de almacenamiento del 72,7 %.

![local-reconstruction-codes](media/fault-tolerance/local-reconstruction-codes-180px.png)

Se recomienda consultar este completo tutorial sobre [cómo los códigos de reconstrucción local controlan distintos escenarios de error y por qué resultan útiles](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/), de [Claus Joergensen](https://twitter.com/clausjor).

## <a name="mirror-accelerated-parity"></a>Paridad acelerada por reflejo

Un volumen de Espacios de almacenamiento directo puede combinar reflejo y paridad. Las operaciones de escritura llegan primero a la parte reflejada y se mueven gradualmente a la parte de la paridad más adelante. De hecho, esto es [usar la creación de reflejo para acelerar la codificación](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/).

Para combinar el reflejo triple y la paridad dual, se necesitan al menos cuatro dominios de error (es decir, cuatro servidores).

La eficacia de almacenamiento de la paridad acelerada por reflejo se sitúa entre la que obtendría al usar solo reflejo o solo paridad, y depende de las proporciones que elija. Por ejemplo, la demostración del minuto 37 de esta presentación muestra [varias combinaciones que consiguen una eficacia de un 46 por ciento, un 54 por ciento y un 65 por ciento](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s) con 12 servidores.

> [!IMPORTANT]
> Se recomienda utilizar la creación de reflejo para la mayoría de las cargas de trabajo sensibles al rendimiento. Para más información sobre cómo equilibrar el rendimiento y la capacidad en función de la carga de trabajo, consulte [Planeamiento de volúmenes](/windows-server/storage/storage-spaces/plan-volumes#choosing-the-resiliency-type).

## <a name="summary"></a>Resumen

En esta sección se resumen los tipos de resistencia disponibles en Espacios de almacenamiento directo, los requisitos de escala mínimos para usar cada tipo, el número de errores que puede tolerar cada tipo y la eficacia de almacenamiento correspondiente.

### <a name="resiliency-types"></a>Tipos de resistencia

|    Resistencia          |    Tolerancia a errores       |    Eficacia de almacenamiento      |
|------------------------|----------------------------|----------------------------|
|    Reflejo bidireccional      |    1                       |    50,0 %                   |
|    Reflejo triple    |    2                       |    33,3 %                   |
|    Paridad dual         |    2                       |    50,0 % - 80,0 %           |
|    Mixto               |    2                       |    33,3 % - 80,0 %           |

### <a name="minimum-scale-requirements"></a>Requisitos de escala mínimos

|    Resistencia          |    Dominios de error mínimos requeridos   |
|------------------------|-------------------------------------|
|    Reflejo bidireccional      |    2                                |
|    Reflejo triple    |    3                                |
|    Paridad dual         |    4                                |
|    Mixto               |    4                                |

   >[!TIP]
   > A menos que use [tolerancia a errores de chasis o bastidor](/windows-server/failover-clustering/fault-domains), el número de dominios de error hace referencia al número de servidores. El número de unidades de cada servidor no afecta a los tipos de resistencia que se pueden usar, siempre y cuando se cumplan los requisitos mínimos para Espacios de almacenamiento directo.

### <a name="dual-parity-efficiency-for-hybrid-deployments"></a>Eficacia de paridad dual para implementaciones híbridas

En esta tabla se muestra la eficacia de almacenamiento de la paridad dual y los códigos de reconstrucción local en cada escala para implementaciones híbridas que contienen unidades de disco duro (HDD) y unidades de estado sólido (SSD).

|    Dominios de error      |    Diseño           |    Eficacia   |
|-----------------------|---------------------|-----------------|
|    2                  |    –                |    –            |
|    3                  |    –                |    –            |
|    4                  |    RS 2+2           |    50,0 %        |
|    5                  |    RS 2+2           |    50,0 %        |
|    6                  |    RS 2+2           |    50,0 %        |
|    7                  |    RS 4+2           |    66,7 %        |
|    8                  |    RS 4+2           |    66,7 %        |
|    9                  |    RS 4+2           |    66,7 %        |
|    10                 |    RS 4+2           |    66,7 %        |
|    11                 |    RS 4+2           |    66,7 %        |
|    12                 |    LRC (8, 2, 1)    |    72,7 %        |
|    13                 |    LRC (8, 2, 1)    |    72,7 %        |
|    14                 |    LRC (8, 2, 1)    |    72,7 %        |
|    15                 |    LRC (8, 2, 1)    |    72,7 %        |
|    16                 |    LRC (8, 2, 1)    |    72,7 %        |

### <a name="dual-parity-efficiency-for-all-flash-deployments"></a>Eficacia de paridad dual para implementaciones todo flash

En esta tabla se muestra la eficacia de almacenamiento de la paridad dual y los códigos de reconstrucción local en cada escala para implementaciones todo flash que solo contienen unidades de estado sólido (SSD). El diseño de paridad puede usar tamaños de grupo más grandes y lograr una mayor eficacia de almacenamiento en una configuración todo flash.

|    Dominios de error      |    Diseño           |    Eficacia   |
|-----------------------|---------------------|-----------------|
|    2                  |    –                |    –            |
|    3                  |    –                |    –            |
|    4                  |    RS 2+2           |    50,0 %        |
|    5                  |    RS 2+2           |    50,0 %        |
|    6                  |    RS 2+2           |    50,0 %        |
|    7                  |    RS 4+2           |    66,7 %        |
|    8                  |    RS 4+2           |    66,7 %        |
|    9                  |    RS 6+2           |    75,0 %        |
|    10                 |    RS 6+2           |    75,0 %        |
|    11                 |    RS 6+2           |    75,0 %        |
|    12                 |    RS 6+2           |    75,0 %        |
|    13                 |    RS 6+2           |    75,0 %        |
|    14                 |    RS 6+2           |    75,0 %        |
|    15                 |    RS 6+2           |    75,0 %        |
|    16                 |    LRC (12, 2, 1)   |    80,0 %        |

## <a name="examples"></a>Ejemplos

A menos que tenga solo dos servidores, se recomienda utilizar la creación de reflejo triple o la paridad dual, ya que ofrecen una mejor tolerancia a errores. En concreto, mantienen la seguridad y accesibilidad continua de todos los datos incluso cuando dos dominios de error (con Espacios de almacenamiento directo, esto significa dos servidores) se ven afectados por errores simultáneos.

### <a name="examples-where-everything-stays-online"></a>Ejemplos donde todo permanece en línea

En estos seis ejemplos se muestra lo que la creación de reflejo triple y la paridad dual **pueden** tolerar.

- **1.**    Una unidad perdida (incluye unidades de caché)
- **2.**    Un servidor perdido

![fault-tolerance-examples-1-and-2](media/fault-tolerance/Fault-Tolerance-Example-12.png)

- **3.**    Un servidor y una unidad perdidos
- **4.**    Dos unidades perdidas en servidores diferentes

![fault-tolerance-examples-3-and-4](media/fault-tolerance/Fault-Tolerance-Example-34.png)

- **5.**    Más de dos unidades perdidas, siempre que se vean afectados como máximo dos servidores
- **6.**    Dos servidores perdidos

![fault-tolerance-examples-5-and-6](media/fault-tolerance/Fault-Tolerance-Example-56.png)

...en todos los casos, todos los volúmenes permanecerán en línea. (Asegúrese de que el clúster mantiene el cuórum).

### <a name="examples-where-everything-goes-offline"></a>Ejemplos donde todo se queda sin conexión

Durante su vigencia, Espacios de almacenamiento puede tolerar cualquier número de errores, ya que, con el tiempo suficiente, se restaura a una resistencia completa después de cada uno de ellos. No obstante, al menos dos dominios de error pueden verse afectados de forma segura por errores en un momento dado. A continuación, se muestran ejemplos de lo que la creación de reflejo triple y la paridad dual **no pueden** tolerar.

- **7.** Unidades perdidas en tres o más servidores a la vez
- **8.** Tres o más servidores perdidos a la vez

![fault-tolerance-examples-7-and-8](media/fault-tolerance/Fault-Tolerance-Example-78.png)

## <a name="usage"></a>Uso

Consulte [Creación de volúmenes en Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/create-volumes).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los temas que se mencionan en este artículo, consulte lo siguiente:

- [Codificación de borrado en Azure de Microsoft Research](https://www.microsoft.com/research/publication/erasure-coding-in-windows-azure-storage/)
- [Códigos de reconstrucción locales y aceleración de volúmenes de paridad](https://blogs.technet.microsoft.com/filecab/2016/09/06/volume-resiliency-and-efficiency-in-storage-spaces-direct/)
- [Volúmenes en la API de administración de almacenamiento](https://blogs.technet.microsoft.com/filecab/2016/08/29/deep-dive-volumes-in-spaces-direct/)
- [Demostración de la eficacia de almacenamiento en Microsoft Ignite 2016](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s)
- [Versión preliminar de la calculadora de capacidad para Espacios de almacenamiento directo](https://aka.ms/s2dcalc)
