---
title: Planeamiento de volúmenes en Azure Stack HCI
description: Cómo planear volúmenes de almacenamiento en Azure Stack HCl.
author: khdownie
ms.author: v-kedow
ms.topic: conceptual
ms.date: 07/27/2020
ms.openlocfilehash: c40d1ca54bfe71088b18413371b90bd26f8b7386
ms.sourcegitcommit: b2337a9309c52aac9f5a1ffd89f1426d6c178ad5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/28/2020
ms.locfileid: "87250459"
---
# <a name="plan-volumes-in-azure-stack-hci"></a>Planeamiento de volúmenes en Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

En este tema se proporcionan instrucciones sobre cómo planear volúmenes en Azure Stack HCI para satisfacer las necesidades de rendimiento y capacidad de las cargas de trabajo, incluida la elección del sistema de archivos, el tipo de resistencia y el tamaño.

## <a name="review-what-are-volumes"></a>Revisión: Qué son los volúmenes

Los volúmenes son donde se colocan los archivos que las cargas de trabajo necesitan, como los archivos VHD o VHDX para máquinas virtuales de Hyper-V. Los volúmenes combinan las unidades del bloque de almacenamiento para introducir las ventajas de tolerancia a errores, escalabilidad y rendimiento de [Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/storage-spaces-direct-overview), la tecnología de almacenamiento definida mediante software en la que se basa Azure Stack HCI.

   >[!NOTE]
   > Volúmenes compartidos de clúster.En la documentación de Espacios de almacenamiento directo, usamos el término "volumen" para hacer referencia conjuntamente al volumen y al disco virtual que contiene, incluida la funcionalidad proporcionada por otras características integradas de Windows, como los volúmenes compartidos de clúster (CSV) y ReFS. No es necesario entender estas distinciones a nivel de implementación para planear e implementar Espacios de almacenamiento directo correctamente.

![what-are-volumes](media/plan-volumes/what-are-volumes.png)

Todos los volúmenes son accesibles para todos los servidores del clúster al mismo tiempo. Una vez creados, se muestran en **C:\ClusterStorage\\** en todos los servidores.

![csv-folder-screenshot](media/plan-volumes/csv-folder-screenshot.png)

## <a name="choosing-how-many-volumes-to-create"></a>Elección del número de volúmenes que crear

Se recomienda que el número de unidades de volúmenes sea un múltiplo del número de servidores del clúster. Por ejemplo, si tiene 4 servidores, experimentará un rendimiento más coherente con 4 volúmenes que con 3 o 5. Esto permite al clúster distribuir la "propiedad" del volumen (un servidor controla la orquestación de los metadatos de cada volumen) uniformemente entre los servidores.

Se recomienda limitar el número total de volúmenes a 64 por clúster.

## <a name="choosing-the-filesystem"></a>Elección del sistema de archivos

Se recomienda usar el nuevo [Sistema de archivos resistente (ReFS)](/en-us/windows-server/storage/refs/refs-overview) para Espacios de almacenamiento directo. ReFS es un sistema de archivos superior creado específicamente para la virtualización que ofrece muchas ventajas, entre las que se incluyen importantes aceleraciones de rendimiento y una protección integrada frente a daños en los datos. Admite casi todas las características de NTFS clave, incluida la desduplicación de datos en Windows Server, versión 1709 y posteriores. Consulte la [tabla de comparación de características de ReFS](/windows-server/storage/refs/refs-overview#feature-comparison) para más información.

Si la carga de trabajo requiere una característica que ReFS no admite todavía, puede usar NTFS en su lugar.

   > [!TIP]
   > En el mismo clúster pueden coexistir volúmenes con distintos sistemas de archivos.

## <a name="choosing-the-resiliency-type"></a>Elección del tipo de resistencia

Los volúmenes de Espacios de almacenamiento directo ofrecen resistencia para protegerse frente a problemas de hardware, como errores de unidad o de servidor, así como para permitir la disponibilidad continua a lo largo del mantenimiento del servidor, como las actualizaciones de software.

   > [!NOTE]
   > Los tipos de resistencia que puede elegir son independientes de los tipos de unidades que tiene.

### <a name="with-two-servers"></a>Con dos servidores

Con dos servidores en el clúster, puede usar la creación de reflejo bidireccional o puede utilizar la resistencia anidada.

La creación de reflejo bidireccional mantiene dos copias de todos los datos, una copia en las unidades de cada servidor. Su eficacia de almacenamiento es del 50 por ciento; para escribir 1 TB de datos, necesita al menos 2 TB de capacidad de almacenamiento físico en el bloque de almacenamiento. La creación de reflejo bidireccional puede tolerar de manera segura un error de hardware cada vez (un servidor o una unidad).

![two-way-mirror](media/plan-volumes/two-way-mirror.png)

La resistencia anidada proporciona resistencia de datos entre servidores con creación de reflejo bidireccional y, a continuación, agrega resistencia dentro de un servidor con creación de reflejo bidireccional o paridad acelerada por reflejo. El anidamiento proporciona resistencia de datos incluso cuando un servidor se está reiniciando o no está disponible. Su eficacia de almacenamiento es del 25 por ciento con la creación de reflejo bidireccional anidada y en torno al 35-40 por ciento para la paridad acelerada por reflejo anidada. La resistencia anidada puede tolerar de manera segura dos errores de hardware a la vez (dos unidades, o un servidor y una unidad del servidor restante). Debido a esta resistencia de datos adicional, se recomienda usar la resistencia anidada en las implementaciones de producción de clústeres de dos servidores. Para más información, consulte [Resistencia anidada](/windows-server/storage/storage-spaces/nested-resiliency).

![Paridad acelerada por reflejo anidada](media/plan-volumes/nested-mirror-accelerated-parity.png)

### <a name="with-three-servers"></a>Con tres servidores

Con tres servidores, debe usar la creación de reflejo triple para mejorar la tolerancia a errores y el rendimiento. La creación de reflejo triple mantiene tres copias de todos los datos, una copia en las unidades de cada servidor. Su eficacia de almacenamiento es del 33,3 por ciento; para escribir 1 TB de datos, necesita al menos 3 TB de capacidad de almacenamiento físico en el bloque de almacenamiento. La creación de reflejo triple puede tolerar de forma segura [al menos dos problemas de hardware (unidad o servidor) a la vez](/windows-server/storage/storage-spaces/storage-spaces-fault-tolerance#examples). Si dos nodos dejan de estar disponibles, el bloque de almacenamiento perderá el cuórum, ya que 2/3 de los discos no están disponibles y no se podrá acceder a los discos virtuales. Sin embargo, un nodo puede estar inactivo y se puede producir un error en uno o varios discos de otro nodo y los discos virtuales permanecerán en línea. Por ejemplo, si se está reiniciando un servidor cuando, de repente, se produce un error en otra unidad u otro servidor, la seguridad y la accesibilidad de los datos se mantienen.

![three-way-mirror](media/plan-volumes/three-way-mirror.png)

### <a name="with-four-or-more-servers"></a>Con cuatro o más servidores

Con cuatro o más servidores, puede elegir para cada volumen si desea utilizar la creación de reflejo triple, la paridad dual (denominada a menudo "codificación de borrado") o una combinación de ambas con la paridad acelerada por reflejo.

La paridad dual proporciona la misma tolerancia a errores que la creación de reflejo triple, pero con una mayor eficacia de almacenamiento. Con cuatro servidores, su eficacia de almacenamiento es del 50 por ciento; para almacenar 2 TB de datos, necesita 4 TB de capacidad de almacenamiento físico en el bloque de almacenamiento. Esto aumenta hasta el 66,7% la eficacia de almacenamiento con siete servidores y continúa hasta una eficacia de almacenamiento del 80,0%. La contrapartida es que la codificación de paridad es un proceso más intensivo, lo que puede limitar su rendimiento.

![dual-parity](media/plan-volumes/dual-parity.png)

El tipo de resistencia que usar depende de las necesidades de la carga de trabajo. En esta tabla se resume qué cargas de trabajo son una buena opción para cada tipo de resistencia, así como el rendimiento y la eficacia de almacenamiento de cada tipo de resistencia.

| Tipo de resistencia | Eficacia de capacidad | Velocidad | Cargas de trabajo |
| ------------------- | ----------------------  | --------- | ------------- |
| **Reflejo**         | ![Eficacia de almacenamiento al 33 %](media/plan-volumes/3-way-mirror-storage-efficiency.png)<br>Reflejo triple: 33 % <br>Reflejo doble: 50 %     |![Rendimiento al 100 %](media/plan-volumes/three-way-mirror-perf.png)<br> Mayor rendimiento  | Cargas de trabajo virtualizadas<br> Bases de datos<br>Otras cargas de trabajo de alto rendimiento |
| **Paridad acelerada por reflejo** |![Eficacia de almacenamiento al 50 % aproximadamente](media/plan-volumes/mirror-accelerated-parity-storage-efficiency.png)<br> Depende de la proporción de reflejo y paridad | ![Rendimiento al 20 % aproximadamente](media/plan-volumes/mirror-accelerated-parity-perf.png)<br>Mucho más lento que el reflejo, pero hasta dos veces más rápido que la paridad dual<br> Mejor para operaciones de lectura y escritura secuenciales grandes | Archivado y copia de seguridad<br> Infraestructura de escritorio virtualizado     |
| **Paridad dual**               | ![Eficacia de almacenamiento al 80 % aproximadamente](media/plan-volumes/dual-parity-storage-efficiency.png)<br>4 servidores: 50 % <br>16 servidores: hasta el 80 % | ![Rendimiento al 10 % aproximadamente](media/plan-volumes/dual-parity-perf.png)<br>Mayor latencia de E/S y uso de CPU en operaciones de escritura<br> Mejor para operaciones de lectura y escritura secuenciales grandes | Archivado y copia de seguridad<br> Infraestructura de escritorio virtualizado  |

#### <a name="when-performance-matters-most"></a>Cuando el rendimiento es lo más importante

Las cargas de trabajo con requisitos de latencia estrictos o que necesiten una gran cantidad de IOPS aleatorias mixtas, como bases de datos de SQL Server o máquinas virtuales de Hyper-V sensibles al rendimiento, deben ejecutarse en volúmenes que usen la creación de reflejo para maximizar el rendimiento.

   >[!TIP]
   > La creación de reflejo es más rápida que cualquier otro tipo de resistencia. Usamos la creación de reflejo para casi todos nuestros ejemplos de rendimiento.

#### <a name="when-capacity-matters-most"></a>Cuando la capacidad es lo más importante

Las cargas de trabajo con poca escritura, como los almacenes de datos o el almacenamiento "en frío", deben ejecutarse en volúmenes que usen paridad dual para maximizar la eficacia del almacenamiento. Algunas otras cargas de trabajo, como los servidores de archivos tradicionales, la infraestructura de escritorio virtual (VDI) u otras que no crean mucho tráfico de E/S aleatorio de desplazamiento rápido o que no requieren el mejor rendimiento también pueden usar la paridad dual, a su discreción. La paridad aumenta inevitablemente el uso de la CPU y la latencia de E/S, especialmente en las operaciones de escritura, en comparación con la creación de reflejo.

#### <a name="when-data-is-written-in-bulk"></a>Cuando se escriben datos de forma masiva

Las cargas de trabajo que escriben en pasos grandes y secuenciales, como los destinos de archivo o de copia de seguridad, tienen otra opción: un volumen puede combinar la creación de reflejo y la paridad dual. Las operaciones de escritura llegan primero a la parte reflejada y se mueven gradualmente a la parte de la paridad más adelante. Esto acelera la ingesta y reduce el uso de recursos cuando llegan grandes operaciones de escritura al permitir que se realice la codificación de paridad de proceso intensivo durante más tiempo. Al definir el tamaño de las partes, tenga en cuenta que la cantidad de operaciones de escritura que se producen a la vez (por ejemplo, una copia de seguridad diaria) debe poder incluirse sobradamente en la parte reflejada. Por ejemplo, si se ingieren 100 GB una vez al día, considere la posibilidad de usar la creación de reflejo para 150 GB a 200 GB y la paridad dual para el resto.

La eficacia de almacenamiento resultante depende de las proporciones que elija. Vea algunos ejemplos en [esta demostración](https://www.youtube.com/watch?v=-LK2ViRGbWs&t=36m55s).

   > [!TIP]
   > Si observa una disminución brusca en el rendimiento de escritura a mitad de la ingesta de datos, esto puede ser un indicio de que la parte reflejada no es suficientemente grande o de que la paridad acelerada por reflejo no es adecuada para su caso de uso. Por ejemplo, si el rendimiento de escritura se reduce de 400 MB/s a 40 MB/s, considere la posibilidad de ampliar la parte reflejada o de cambiar a creación de reflejo triple.

### <a name="about-deployments-with-nvme-ssd-and-hdd"></a>Acerca de las implementaciones con NVMe, SSD y HDD

En implementaciones con dos tipos de unidades, las unidades más rápidas proporcionan almacenamiento en caché mientras que las unidades más lentas proporcionan capacidad. Esto ocurre automáticamente. Para más información, consulte [Descripción de la memoria caché de Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/understand-the-cache). En estas implementaciones, todos los volúmenes residen en última instancia en el mismo tipo de unidades: las unidades de capacidad.

En implementaciones con los tres tipos de unidades, solo las unidades más rápidas (NVMe) proporcionan almacenamiento en caché, lo que deja dos tipos de unidades (SSD y HDD) para proporcionar capacidad. Para cada volumen, puede elegir si reside completamente en el nivel SSD, en el nivel HDD o si abarca los dos.

   > [!IMPORTANT]
   > Se recomienda usar el nivel SSD para colocar las cargas de trabajo más sensibles al rendimiento en todo flash.

## <a name="choosing-the-size-of-volumes"></a>Selección del tamaño de los volúmenes

Se recomienda limitar el tamaño de cada volumen a 64 TB en Windows Server 2019.

   > [!TIP]
   > Si usa una solución de copia de seguridad que se basa en el servicio de instantáneas de volumen (VSS) y el proveedor de software Volsnap (como es habitual con las cargas de trabajo de servidor de archivos), si se limita el tamaño del volumen a 10 TB, mejorará el rendimiento y la confiabilidad. Las soluciones de copia de seguridad que usan la versión más reciente de RCT API de Hyper-V, la clonación del bloque ReFS o las API nativas de copia de seguridad de SQL funcionan bien hasta 32 TB y más.

### <a name="footprint"></a>Superficie

El tamaño de un volumen hace referencia a su capacidad utilizable, la cantidad de datos que puede almacenar. Este tamaño viene proporcionado por el parámetro **-Size** del cmdlet **New-Volume** y, a continuación, aparece en la propiedad **Size** al ejecutar el cmdlet **Get-Volume**.

El tamaño es distinto de la *superficie* del volumen, la capacidad total de almacenamiento físico que ocupa en el bloque de almacenamiento. La superficie depende de su tipo de resistencia. Por ejemplo, la superficie de los volúmenes que usan la creación de reflejo triple es tres veces mayor que su tamaño.

Las superficies de los volúmenes deben caber en el bloque de almacenamiento.

![size-versus-footprint](media/plan-volumes/size-versus-footprint.png)

### <a name="reserve-capacity"></a>Capacidad de reserva

Al dejar sin asignar algo de capacidad en el bloque de almacenamiento, se proporciona a los volúmenes espacio para realizar la reparación "en contexto" después de que se produzca un error en las unidades, lo que mejora el rendimiento y la seguridad de los datos. Si hay capacidad suficiente, una reparación en contexto, paralela e inmediata puede restaurar volúmenes a su resistencia completa incluso antes de que se reemplacen las unidades con errores. Esto ocurre automáticamente.

Se recomienda reservar el equivalente de una unidad de capacidad por servidor, hasta 4 unidades. Puede reservar más a su discreción, pero esta recomendación mínima garantiza que una reparación en contexto, paralela e inmediata pueda realizarse correctamente después del error de cualquier unidad.

![reserva](media/plan-volumes/reserve.png)

Por ejemplo, si tiene 2 servidores y usa unidades de 1 TB de capacidad, disponga 2 x 1 = 2 TB del bloque como reserva. Si tiene 3 servidores y unidades de 1 TB de capacidad, disponga 3 x 1 = 3 TB como reserva. Si tiene 4 servidores y unidades de 1 TB de capacidad, disponga 4 x 1 = 4 TB como reserva.

   >[!NOTE]
   > En clústeres con unidades de los tres tipos (NVMe + SSD + HDD), se recomienda reservar el equivalente de un SSD más un HDD por servidor, hasta 4 unidades de cada uno.

## <a name="example-capacity-planning"></a>Ejemplo: Planificación de capacidad

Considere un clúster de cuatro servidores. Cada servidor tiene algunas unidades de caché más 16 unidades de 2 TB para capacidad.

```
4 servers x 16 drives each x 2 TB each = 128 TB
```

De estos 128 TB del bloque de almacenamiento, se reservan cuatro unidades (u 8 TB), de modo que las reparaciones en contexto pueden producirse sin prisa para reemplazar unidades con errores. Esto deja 120 TB de capacidad de almacenamiento físico en el bloque con los que se pueden crear volúmenes.

```
128 TB – (4 x 2 TB) = 120 TB
```

Supongamos que necesitamos que nuestra implementación hospede algunas máquinas virtuales de Hyper-V altamente activas, pero también tenemos gran cantidad de almacenamiento en frío: archivos y copias de seguridad antiguos que necesitamos conservar. Dado que tenemos cuatro servidores, vamos a crear cuatro volúmenes.

Vamos a colocar las máquinas virtuales en los dos primeros volúmenes, *Volume1* y *Volume2*. Elegimos ReFS como sistema de archivos (para mayor rapidez en la creación y los puntos de comprobación) y creación de reflejo tridireccional para la resistencia con el fin de maximizar el rendimiento. Vamos a colocar el almacenamiento en frío en los otros dos volúmenes, *Volume3* y *Volume4*. Elegimos NTFS como sistema de archivos (para desduplicación de datos) y paridad dual para la resistencia con el fin de maximizar la capacidad.

No es necesario que todos los volúmenes tengan el mismo tamaño, pero para simplificar, podemos hacer que sean todos de 12 TB.

*Volume1* y *Volume2* ocuparán cada uno 12 TB x 33,3 % de eficacia = 36 TB de capacidad de almacenamiento físico.

*Volume3* y *Volume4* ocuparán cada uno 12 TB x 50 % de eficacia = 24 TB de capacidad de almacenamiento físico.

```
36 TB + 36 TB + 24 TB + 24 TB = 120 TB
```

Los cuatro volúmenes se ajustan exactamente a la capacidad de almacenamiento físico disponible en nuestro bloque, lo que resulta ideal.

![ejemplo](media/plan-volumes/example.png)

   >[!TIP]
   > No es necesario crear todos los volúmenes inmediatamente. Siempre puede ampliar volúmenes o crear nuevos volúmenes más adelante.

Para simplificar, a lo largo de este ejemplo se usan unidades decimales (base 10), lo que significa que 1 TB = 1 000 000 000 000 bytes. Sin embargo, las cantidades de almacenamiento en Windows aparecen en unidades binarias (base 2). Por ejemplo, cada unidad de 2 TB aparecería como 1,82 TiB en Windows. Del mismo modo, el bloque de almacenamiento de 128 TB aparecería como 116,41 TiB. Se espera que esto sea así.

## <a name="usage"></a>Uso

Consulte [Creación de volúmenes en Azure Stack HCI](../manage/create-volumes.md).

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, vea también:

- [Selección de unidades para Espacios de almacenamiento directo](choose-drives.md)
- [Tolerancia a errores y eficacia del almacenamiento](fault-tolerance.md)