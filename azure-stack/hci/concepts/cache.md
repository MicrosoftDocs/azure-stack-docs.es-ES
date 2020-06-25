---
title: Descripción de la memoria caché en Azure Stack HCI
description: Funcionamiento del almacenamiento en caché de lectura y escritura en Espacios de almacenamiento directo y Azure Stack HCI.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 02/28/2020
ms.openlocfilehash: 063dab5f6f395e33a42a9722b399a469f6ce6ac7
ms.sourcegitcommit: 76af742a42e807c400474a337e29d088ede8a60d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2020
ms.locfileid: "85196926"
---
# <a name="understanding-the-cache-in-azure-stack-hci"></a>Descripción de la memoria caché en Azure Stack HCI

>Se aplica a: Windows Server 2019

[Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) incluye una memoria caché incorporada en el lado servidor para maximizar el rendimiento del almacenamiento. Se trata de una memoria caché grande, persistente y de lectura *y* escritura en tiempo real. La caché se configura automáticamente cuando se habilita Espacios de almacenamiento directo. En la mayoría de los casos, no se requiere ninguna administración manual.
El funcionamiento de la caché depende de los tipos de unidades presentes.

El vídeo siguiente incluye detalles sobre cómo funciona el almacenamiento en caché de Espacios de almacenamiento directo, así como otras consideraciones de diseño.

<strong>Consideraciones de diseño de Espacios de almacenamiento directo</strong><br>(20 minutos)<br>
<iframe src="https://channel9.msdn.com/Blogs/windowsserver/Design-Considerations-for-Storage-Spaces-Direct/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

## <a name="drive-types-and-deployment-options"></a>Tipos de unidad y opciones de implementación

Espacios de almacenamiento directo funciona actualmente con tres tipos de dispositivos de almacenamiento:

<table>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/NVMe-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            NVMe (memoria rápida no volátil)
        </td>
    </tr>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/SSD-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            SSD SATA o SAS (unidad de estado sólido)
        </td>
    </tr>
    <tr style="border: 0;">
        <td style="padding: 10px; border: 0; width:70px">
            <img src="media/cache/HDD-100px.png">
        </td>
        <td style="padding: 10px; border: 0;" valign="middle">
            HDD (Unidad de disco duro)
        </td>
    </tr>
</table>

Estos se pueden combinar de seis maneras que se agrupan en dos categorías: "todo flash" e "híbrida".

### <a name="all-flash-deployment-possibilities"></a>Posibilidades de implementación todo flash

Todas las implementaciones all-flash tienen el objetivo de maximizar el rendimiento del almacenamiento y no incluyen unidades de disco duro (HDD) rotacionales.

![Posibilidades de implementación de todo flash](media/cache/All-Flash-Deployment-Possibilities.png)

### <a name="hybrid-deployment-possibilities"></a>Posibilidades de implementación híbrida

Las implementaciones híbridas tienen como objetivo equilibrar el rendimiento y la capacidad o maximizar la capacidad, e incluyen unidades de disco duro (HDD) rotacionales.

![Posibilidades de implementación híbridas](media/cache/Hybrid-Deployment-Possibilities.png)

## <a name="cache-drives-are-selected-automatically"></a>Las unidades de caché se seleccionan de forma automática.

En las implementaciones con varios tipos de unidades, Espacios de almacenamiento directo usa automáticamente todas las unidades del tipo "más rápido" para el almacenamiento en caché. Las unidades restantes se usan para la capacidad.

El tipo "más rápido" se determina según la siguiente jerarquía.

![Jerarquía de tipos de unidad](media/cache/Drive-Type-Hierarchy.png)

Por ejemplo, si tiene NVMe y SSD, la NVMe se almacenará en caché para los SSD.

Si tiene SSD y HDD, los SSD se almacenarán en caché para los discos duros.

   >[!NOTE]
   > Las unidades de caché no contribuyen a la capacidad de almacenamiento utilizable. Todos los datos almacenados en la caché también se almacenan en otro lugar, o bien lo harán una vez que se retiren del almacenamiento provisional. Esto significa que la capacidad total de almacenamiento sin procesar de la implementación es la suma de las unidades de capacidad únicamente.

Si todas las unidades son del mismo tipo, no se configura automáticamente ninguna caché. Tiene la opción de configurar manualmente unidades de mayor resistencia para almacenar en la caché unidades de menor resistencia del mismo tipo. Para más información sobre cómo hacerlo, consulte la sección de [configuración manual](#manual-configuration).

   >[!TIP]
   > En las implementaciones con todo NVMe o todo SSD, especialmente en escalados muy pequeños, no tener unidades "dedicadas" en la memoria caché puede mejorar la eficacia del almacenamiento de forma significativa.

## <a name="cache-behavior-is-set-automatically"></a>El comportamiento de la memoria caché se establece automáticamente

El comportamiento de la memoria caché se determina automáticamente según los tipos de las unidades de las que se realiza el almacenamiento en caché. Al realizar el almacenamiento en caché de las unidades de estado sólido (por ejemplo, almacenamiento en caché NVMe para SSD), solo las escrituras se almacenan en caché. Al almacenar en caché las unidades de disco duro (por ejemplo, almacenar en caché los discos SSD para las unidades HDD), tanto las lecturas como las escrituras se almacenan en caché.

![Comportamiento de lectura y escritura en caché](media/cache/Cache-Read-Write-Behavior.png)

### <a name="write-only-caching-for-all-flash-deployments"></a>Almacenamiento en caché de solo escritura para las implementaciones todo flash

Al realizar el almacenamiento en caché de las unidades de estado sólido (NVMe o SSD), solo las escrituras se almacenan en caché. Esto reduce el desgaste en las unidades de capacidad, ya que muchas escrituras y reescrituras pueden fusionarse en la memoria caché y, posteriormente, retirarse del almacenamiento provisional solo según sea necesario, lo que reduce el tráfico acumulativo a las unidades de capacidad y amplía su duración. Por esta razón, se recomienda seleccionar unidades de [mayor resistencia y optimizadas para escritura](http://whatis.techtarget.com/definition/DWPD-device-drive-writes-per-day) para la caché. Las unidades de capacidad pueden tener, por consiguiente, una menor resistencia de escritura.

Dado que las lecturas no afectan de forma significativa a la duración de la memoria flash y dado que las unidades de estado sólido ofrecen generalmente una baja latencia de lectura, las lecturas no se almacenan en caché: se envían directamente desde las unidades de capacidad (excepto cuando los datos se han escrito tan recientemente que aún no se han retirado del almacenamiento provisional). Esto permite que la memoria caché se dedique por completo a las escrituras, lo cual aumenta al máximo su eficacia.

Esto hace que algunas características de escritura, como la latencia, las dictaminen las unidades de caché, mientras que las unidades de capacidad son las que hacen lo propio con las características de lectura. Ambos son coherentes, predecibles y uniformes.

### <a name="readwrite-caching-for-hybrid-deployments"></a>Almacenamiento en caché de lectura y escritura para implementaciones híbridas

Al almacenar en caché las unidades de disco duro (HDD), las lecturas *y* las escrituras se almacenan en caché para proporcionar una latencia similar a la de la memoria flash (a menudo 10 veces mejor) para ambas. La memoria caché de lectura almacena datos que se han leído recientemente y con frecuencia para obtener un acceso rápido y para minimizar el tráfico aleatorio a las unidades de disco duro. (Debido a los retrasos de búsqueda y rotación, la latencia y el tiempo perdido en el que incurre el acceso aleatorio a una unidad de disco duro son significativos). Las escrituras se almacenan en la caché para absorber las ráfagas y, al igual que antes, para fusionar las escrituras y reescrituras y minimizar el tráfico acumulativo a las unidades de capacidad.

Espacios de almacenamiento directo implementa un algoritmo que anula la aleatorización de las escrituras antes de retirarlas del almacenamiento provisional, para emular un patrón de E/S en el disco que parece secuencial incluso cuando el patrón de E/S real procedente de la carga de trabajo (por ejemplo, de las máquinas virtuales) es aleatorio. Esto maximiza el número de operaciones de entrada y salida y el rendimiento de las unidades HDD.

### <a name="caching-in-deployments-with-drives-of-all-three-types"></a>Almacenamiento en caché en implementaciones con unidades de los tres tipos

Cuando hay unidades de los tres tipos, las unidades de NVMe proporcionan almacenamiento en caché para los discos SSD y HDD. El comportamiento es como el descrito anteriormente: solo se almacenan en caché las escrituras para los discos SSD, y las lecturas y escrituras para las unidades de disco duro. La carga del almacenamiento en caché para las unidades de disco duro se distribuye uniformemente entre las unidades de caché.

## <a name="summary"></a>Resumen

En esta tabla se resumen las unidades que se usan para el almacenamiento en caché, para la capacidad, y cuál es su comportamiento para cada posibilidad de implementación.

| Implementación     | Unidades de caché                        | Unidades de capacidad | Comportamiento de la caché (predeterminado)  |
| -------------- | ----------------------------------- | --------------- | ------------------------- |
| Todo NVMe         | Ninguno (Opcional: configurar manualmente) | NVMe            | Solo escritura (si está configurado)  |
| Todo SSD          | Ninguno (Opcional: configurar manualmente) | SSD             | Solo escritura (si está configurado)  |
| NVMe + SSD       | NVMe                                | SSD             | Solo escritura                  |
| NVMe + HDD       | NVMe                                | HDD             | Lectura + escritura                |
| SSD + HDD        | SSD                                 | HDD             | Lectura + escritura                |
| NVMe + SSD + HDD | NVMe                                | SSD + HDD       | Lectura + escritura para HDD, solo escritura para SSD  |

## <a name="server-side-architecture"></a>Arquitectura en el lado servidor

La memoria caché se implementa en el nivel de unidad: las unidades de caché individuales dentro de un servidor se enlazan a una o varias unidades de capacidad del mismo servidor.

Dado que la memoria caché está por debajo del resto de la pila de almacenamiento definida por software de Windows, no tiene ni son necesarios conceptos como los espacios de almacenamiento o la tolerancia a errores. Puede considerarlo como la creación de unidades "híbridas" (en parte flash, en parte disco) que luego se presentan a Windows. Al igual que con una unidad híbrida real, el movimiento en tiempo real de los datos activos e inactivos entre las partes más rápidas y lentas del soporte físico es prácticamente invisible al exterior.

Dado que la resistencia en Espacios de almacenamiento directo se produce al menos en el nivel de servidor (lo que significa que las copias de datos siempre se escriben en servidores diferentes; como máximo una copia por servidor), los datos de la caché se benefician de la misma resistencia que los datos que no están en la caché.

![Arquitectura de la caché en el lado servidor](media/cache/Cache-Server-Side-Architecture.png)

Por ejemplo, cuando se usa la creación de reflejo triple, se escriben tres copias de los datos en servidores diferentes, y se colocan en la memoria caché. Independientemente de si se retiran del almacenamiento provisional posteriormente o no, siempre existirán tres copias.

## <a name="drive-bindings-are-dynamic"></a>Los enlaces de unidades son dinámicos

El enlace entre las unidades de caché y de capacidad puede tener cualquier proporción, desde 1:1 hasta 1:12 e incluso más. Se ajusta dinámicamente cada vez que se agregan o quitan unidades como, por ejemplo, al escalar verticalmente o después de errores. Esto significa que puede agregar unidades de caché o unidades de capacidad de forma independiente, siempre que lo desee.

![Enlace dinámico](media/cache/Dynamic-Binding.gif)

Se recomienda que el número de unidades de capacidad sea un múltiplo del número de unidades de caché por motivos de simetría. Por ejemplo, si tiene 4 unidades de caché, experimentará un rendimiento más coherente con 8 unidades de capacidad (proporción 1:2) que con 7 o 9.

## <a name="handling-cache-drive-failures"></a>Control de errores en la unidad de caché

Cuando se produce un error en una unidad de caché, las escrituras que todavía no se han retirado del almacenamiento provisional se pierden *en el servidor local*, lo que significa que solo existen en las otras copias (en otros servidores). Al igual que después de cualquier otro error de unidad, los espacios de almacenamiento pueden recuperarse automáticamente, y así lo hacen, mediante la consulta de las copias supervivientes.

Durante un breve período, las unidades de capacidad que estaban enlazadas a la unidad de caché perdida aparecerán en estado incorrecto. Una vez que se ha completado el reenlace de la memoria caché (de forma automática) y la reparación de datos se ha completado (de forma automática), las unidades se reanudarán y aparecerán con estado correcto.

Este escenario es el motivo por el que se necesitan al menos dos unidades de caché por servidor para mantener el rendimiento.

![Control de errores](media/cache/Handling-Failure.gif)

Después, puede reemplazar la unidad de caché igual que lo haría con cualquier otra unidad.

   >[!NOTE]
   > Es posible que tenga que apagar para reemplazar de forma segura una NVMe que sea una tarjeta de expansión (AIC) o un factor de forma M.2.

## <a name="relationship-to-other-caches"></a>Relación con otras memorias caché

Hay varias memorias caché no relacionadas en la pila de almacenamiento definida por software de Windows. Entre los ejemplos se incluyen la caché con reescritura de Espacios de almacenamiento y la caché de lectura en memoria del volumen compartido de clúster (CSV).

Con Espacios de almacenamiento directo, no se debería modificar el comportamiento predeterminado de la caché con reescritura de espacios de almacenamiento. Por ejemplo, no se deben usar parámetros como **-WriteCacheSize** en el cmdlet **New-Volume**.

Puede optar por usar la memoria caché de CSV o no: depende de usted. Está desactivada de forma predeterminada en Espacios de almacenamiento directo, pero no entra en conflicto con la nueva caché que se describe en este tema de ningún modo. En algunos escenarios, puede mejorar el rendimiento significativamente. Para más información, consulte [Cómo habilitar la caché de CSV](/windows-server/failover-clustering/failover-cluster-csvs#enable-the-csv-cache-for-read-intensive-workloads-optional).

## <a name="manual-configuration"></a>Configuración manual

Para la mayoría de las implementaciones, no se requiere la configuración manual. En caso de que lo necesite, consulte las secciones siguientes.

Si necesita realizar cambios en el modelo de dispositivo de la caché después de la instalación, edite el documento de componentes de soporte del Servicio de mantenimiento, como se describe en la [introducción al Servicio de mantenimiento](/windows-server/failover-clustering/health-service-overview#supported-components-document).

### <a name="specify-cache-drive-model"></a>Especificación del modelo de unidad de caché

En las implementaciones en las que todas las unidades son del mismo tipo, como las implementaciones con todo NVMe o todo SSD, no se configura ninguna caché porque Windows no puede distinguir características como la resistencia de escritura de forma automática entre las unidades del mismo tipo.

Para usar unidades de mayor resistencia para almacenar en la caché las unidades de menor resistencia del mismo tipo, puede especificar qué modelo de unidad se va a usar con el parámetro **-CacheDeviceModel** del cmdlet **Enable-ClusterS2D**. Una vez que se habilita Espacios de almacenamiento directo, todas las unidades de ese modelo se usarán para el almacenamiento en caché.

   >[!TIP]
   > Asegúrese de que la cadena del modelo coincida exactamente como aparece en la salida de **Get-PhysicalDisk**.

####  <a name="example"></a>Ejemplo

En primer lugar, obtenga una lista de discos físicos:

```PowerShell
Get-PhysicalDisk | Group Model -NoElement
```

A continuación se muestra una salida de ejemplo:

```
Count Name
----- ----
    8 FABRIKAM NVME-1710
   16 CONTOSO NVME-1520
```

A continuación, escriba el siguiente comando, especificando el modelo de dispositivo de la caché:

```PowerShell
Enable-ClusterS2D -CacheDeviceModel "FABRIKAM NVME-1710"
```

Para comprobar que las unidades de disco que desea se están usando para el almacenamiento en caché, ejecute **Get-PhysicalDisk** en PowerShell y compruebe que su propiedad **Usage** (Uso) indica **"Journal"** (Diario).

### <a name="manual-deployment-possibilities"></a>Posibilidades de implementación manual

La configuración manual habilita las siguientes posibilidades de implementación:

![Posibilidades de implementación exóticas](media/cache/Exotic-Deployment-Possibilities.png)

### <a name="set-cache-behavior"></a>Establecimiento del comportamiento de la caché

Es posible reemplazar el comportamiento predeterminado de la memoria caché. Por ejemplo, puede establecerlo en lecturas de caché en una implementación con todo flash. No es recomendable que modifique el comportamiento a menos que esté seguro de que el predeterminado no es adecuado para su carga de trabajo.

Para reemplazar el comportamiento, use el cmdlet **Set-ClusterStorageSpacesDirect**y sus parámetros **-CacheModeSSD** y **-CacheModeHDD**. El parámetro **CacheModeSSD** establece el comportamiento de la caché al almacenar en caché para las unidades de estado sólido. El parámetro **CacheModeHDD** establece el comportamiento de la caché al almacenar en caché para las unidades de disco duro. Esto se puede realizar en cualquier momento una vez que se haya habilitado Espacios de almacenamiento directo.

Puede usar **Get-ClusterStorageSpacesDirect** para comprobar que se ha establecido el comportamiento.

#### <a name="example"></a>Ejemplo

Primero, obtenga la configuración de Espacios de almacenamiento directo:

```PowerShell
Get-ClusterStorageSpacesDirect
```

A continuación se muestra una salida de ejemplo:

```
CacheModeHDD : ReadWrite
CacheModeSSD : WriteOnly
```

A continuación, haga lo siguiente:

```PowerShell
Set-ClusterStorageSpacesDirect -CacheModeSSD ReadWrite

Get-ClusterS2D
```

A continuación se muestra una salida de ejemplo:

```
CacheModeHDD : ReadWrite
CacheModeSSD : ReadWrite
```

## <a name="sizing-the-cache"></a>Cambio de tamaño de la caché

El tamaño de la caché debe ajustarse para adaptarse al espacio de trabajo (los datos que se leen y escriben activamente en un momento dado) de las aplicaciones y cargas de trabajo.

Esto resulta especialmente importante en implementaciones híbridas con unidades de disco duro. Si el espacio de trabajo activo supera el tamaño de la caché, o si en este se produce un desfase demasiado rápidamente, los errores de la caché de lectura aumentarán y las escrituras tendrán que retirarse del almacenamiento provisional de forma más intensa, lo cual afectará al rendimiento general.

Puede usar la utilidad Monitor de rendimiento (PerfMon.exe) integrada en Windows para inspeccionar el número de errores de la caché. Concretamente, puede comparar las **lecturas con errores de caché por segundo** en el conjunto de contadores de los **discos híbridos de almacenamiento en el clúster** con el número total de operaciones de entrada y salida de lectura por segundo de la implementación. Cada "disco híbrido" se corresponde con una unidad de capacidad.

Por ejemplo, 2 unidades de caché enlazadas a 4 unidades de capacidad dan como resultado 4 instancias de objetos de "disco híbrido" por servidor.

![Supervisión del rendimiento](media/cache/PerfMon.png)

No hay regla universal, pero si se producen demasiados errores de lectura en la caché, esta podría resultar insuficiente y debería considerar la posibilidad de agregar unidades de caché para expandirla. Puede agregar unidades de caché o unidades de capacidad de forma independiente, siempre que lo desee.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre almacenamiento, consulte también:

- [Tolerancia a errores y eficacia del almacenamiento](fault-tolerance.md)
- [Cuórum de clúster y grupo](quorum.md)
