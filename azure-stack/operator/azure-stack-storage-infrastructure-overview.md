---
title: Administración de la infraestructura de almacenamiento para Azure Stack | Microsoft Docs
description: Administre la infraestructura de almacenamiento para Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: ''
ms.topic: article
ms.date: 03/11/2019
ms.author: mabrigg
ms.lastreviewed: 03/11/2019
ms.reviewer: jiahan
ms.openlocfilehash: c7ae7f0c8fa510b0f2b55e458266065544e1bd5e
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828217"
---
# <a name="manage-storage-infrastructure-for-azure-stack"></a>Administración de la infraestructura de almacenamiento para Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En este artículo se describen el estado de mantenimiento y el estado operativo de los recursos de infraestructura de almacenamiento de Azure Stack. Estos recursos incluyen unidades y volúmenes de almacenamiento. La información de este tema puede resultar muy útil cuando se intentan solucionar diferentes problemas, como la imposibilidad de agregar una unidad a un grupo.

## <a name="understand-drives-and-volumes"></a>Explicación de las unidades y los volúmenes

### <a name="drives"></a>Unidades

Con tecnología de software de Windows Server, Azure Stack define las funcionalidades de almacenamiento con una combinación de Espacios de almacenamiento directo (S2D) y clústeres de conmutación por error de Windows Server para proporcionar un servicio de almacenamiento de alto rendimiento, resistente y escalable.

Los asociados del sistema integrado de Azure Stack ofrecen muchas variantes de la solución, incluida una amplia gama de flexibilidad de almacenamiento. Actualmente, puede seleccionar una combinación de tres tipos de unidades: NVMe (memoria rápida no volátil), SSD SATA o SAS (unidad de estado sólido), HDD (unidad de disco duro).

Espacios de almacenamiento directo incluye una memoria caché para maximizar el rendimiento del almacenamiento. En el dispositivo de Azure Stack con uno o varios tipos de unidades, Espacios de almacenamiento directo usa automáticamente todas las unidades del tipo "más rápido" (NVMe &gt; SSD &gt; HDD) para el almacenamiento en caché. Las unidades restantes se usan para la capacidad. Las unidades se podrían agrupar en una implementación "all-flash" o "híbrida":

![Infraestructura de almacenamiento de Azure Stack](media/azure-stack-storage-infrastructure-overview/image1.png)

Todas las implementaciones all-flash tienen el objetivo de maximizar el rendimiento del almacenamiento y no incluyen unidades de disco duro (HDD) rotacionales.

![Infraestructura de almacenamiento de Azure Stack](media/azure-stack-storage-infrastructure-overview/image2.png)

Las implementaciones híbridas tienen como objetivo equilibrar el rendimiento y la capacidad o maximizar la capacidad, e incluyen unidades de disco duro (HDD) rotacionales.

El comportamiento de la memoria caché se determina automáticamente según los tipos de las unidades de las que se realiza el almacenamiento en caché. Al realizar el almacenamiento en caché de las unidades de estado sólido (por ejemplo, almacenamiento en caché NVMe para SSD), solo las escrituras se almacenan en caché. Esto reduce el desgaste de las unidades de capacidad, lo que disminuye el tráfico acumulativo en las unidades de capacidad y aumenta su duración. Mientras tanto, dado que las lecturas no afectan significativamente a la duración de la memoria flash y que las unidades de estado sólido ofrecen baja latencia de lectura de manera universal, las lecturas no se almacenan en caché. Al almacenar en caché las unidades de disco duro (por ejemplo, almacenar en caché los discos SSD para las unidades HDD), tanto las lecturas como las escrituras se almacenan en caché para proporcionar una latencia similar a la de la memoria flash (a menudo 10 veces mejor) para ambas.

![Infraestructura de almacenamiento de Azure Stack](media/azure-stack-storage-infrastructure-overview/image3.png)

Para obtener la configuración de almacenamiento disponible, puede comprobar los asociados OEM de Azure Stack (https://azure.microsoft.com/overview/azure-stack/partners/) a fin de obtener especificaciones detalladas.

> [!Note]  
> El dispositivo de Azure Stack se puede entregar en una implementación híbrida, con unidades HDD y SSD (o NVMe). No obstante, las unidades de tipo más rápido se usarían como unidades de caché y todas las unidades restantes se utilizarían como unidades de capacidad como un grupo. Los datos de inquilino (blobs, tablas, colas y discos) se colocarían en unidades de capacidad. Por lo tanto, el aprovisionamiento de discos prémium o la selección del tipo de cuenta de almacenamiento prémium no garantizan la asignación de los objetos en las unidades SSD o NVMe ni la obtención de un mayor rendimiento.

### <a name="volumes"></a>Volúmenes

El *servicio de almacenamiento* crea particiones del almacenamiento disponible en volúmenes separados que se asignan para almacenar los datos del sistema y del inquilino. Los volúmenes combinan las unidades del bloque de almacenamiento para introducir las ventajas de tolerancia a errores, escalabilidad y rendimiento de Espacios de almacenamiento directo.

![Infraestructura de almacenamiento de Azure Stack](media/azure-stack-storage-infrastructure-overview/image4.png)

En el bloque de almacenamiento de Azure Stack se crean tres tipos de volúmenes:

-   Infraestructura: hospeda los archivos que se usan en las VM de infraestructura y los servicios principales de Azure Stack.

-   Temporal de VM: hospeda los discos temporales conectados a VM de inquilino, y los datos se almacenan en estos discos.

-   Almacén de objetos: hospeda los datos de inquilino de mantenimiento de blobs, tablas, colas y discos de VM.

En una implementación de varios nodos, verá tres volúmenes de infraestructura, mientras que el número de volúmenes temporales de VM y volúmenes de almacén de objetos es igual al número de los nodos de la implementación de Azure Stack:

-   En una implementación de cuatro nodos, hay cuatro volúmenes temporales de VM iguales y cuatro volúmenes de almacén de objetos iguales.

-   Si agrega un nuevo nodo al clúster, se creará un nuevo volumen para ambos tipos.

-   El número de volúmenes se mantiene aunque se quite un nodo o haya uno que no funcione correctamente.

-   Si utiliza el Kit de desarrollador de Azure Stack, hay un único volumen con varios recursos compartidos.

Los volúmenes de Espacios de almacenamiento directo ofrecen resistencia para protegerse frente a problemas de hardware, como errores de unidad o de servidor, así como para permitir la disponibilidad continua a lo largo del mantenimiento del servidor, como las actualizaciones de software. La implementación de Azure Stack usa la creación de reflejo tridireccional para garantizar la resistencia de datos. Se escriben tres copias de datos de inquilino en distintos servidores, donde se colocan en la memoria caché:

![Infraestructura de almacenamiento de Azure Stack](media/azure-stack-storage-infrastructure-overview/image5.png)

La creación de reflejo proporciona tolerancia a errores gracias al mantenimiento de varias copias de todos los datos. La manera de seccionar y colocar estos datos no es trivial (consulte este blog para obtener más información), pero es absolutamente cierto que los datos almacenados mediante la creación de reflejo se escriben, en su totalidad, varias veces. Cada copia se escribe en distinto hardware físico (unidades diferentes en distintos servidores), que se supone que generan un error de forma independiente. La creación de reflejo tridireccional puede tolerar sin ningún riesgo al menos dos de los problemas de hardware (unidad o servidor) a la vez. Por ejemplo, si se está reiniciando un servidor cuando, de repente, se produce un error en otra unidad u otro servidor, la seguridad y la accesibilidad de los datos se mantienen.

## <a name="volume-states"></a>Estados de volumen

Para averiguar los volúmenes de estado incluidos, use los siguientes comandos de PowerShell:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsVolume -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object VolumeLabel, HealthStatus, OperationalStatus, RepairStatus, Description, Action, TotalCapacityGB, RemainingCapacityGB
```

El siguiente es un ejemplo de salida que muestra un volumen desasociado y un volumen degradado o incompleto:

| VolumeLabel | HealthStatus | OperationalStatus |
|-------------|--------------|------------------------|
| ObjStore_1 | Desconocido | Desasociado |
| ObjStore_2 | Advertencia | {Degraded, Incomplete} |

En las secciones siguientes se enumeran los estados de mantenimiento y operativo.

### <a name="volume-health-state-healthy"></a>Estado de mantenimiento de los volúmenes: Healthy

| Estado operativo | DESCRIPCIÓN |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| OK | El estado del volumen es correcto. |
| Subóptimo | Los datos no se escriben uniformemente en las unidades.<br> <br>**Acción:** Póngase en contacto con el soporte técnico para optimizar el uso de unidades en el bloque de almacenamiento. Antes de hacerlo, inicie el proceso de recopilación de archivos de registro siguiendo las instrucciones de https://aka.ms/azurestacklogfiles. Es posible que deba realizar la restauración a partir de la copia de seguridad una vez restaurada la conexión con errores. |


### <a name="volume-health-state-warning"></a>Estado de mantenimiento de los volúmenes: Advertencia

Cuando el volumen se encuentra en un estado de mantenimiento de advertencia, significa que una o varias copias de los datos no están disponibles, pero Azure Stack puede seguir leyendo al menos una copia de estos.

| Estado operativo | DESCRIPCIÓN |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| En servicio | Azure Stack está reparando el volumen, como sucede después de agregar o quitar una unidad. Una vez completada la reparación, el volumen debería volver al estado de mantenimiento correcto (OK).<br> <br>**Acción:** espere a que Azure Stack termine de reparar el volumen y, posteriormente, compruebe el estado. |
| Incompleto | La resistencia del volumen disminuye porque una o varias unidades faltan o generaron errores. Sin embargo, las unidades que faltan contienen copias actualizadas de los datos.<br> <br>**Acción:** vuelva a conectar las unidades que faltan, reemplace las unidades con errores y conecte todos los servidores que estén sin conexión. |
| Degradado | La resistencia del volumen disminuye porque una o varias unidades faltan o generaron errores, y existen copias obsoletas de los datos en estas unidades.<br> <br>**Acción:** vuelva a conectar las unidades que faltan, reemplace las unidades con errores y conecte todos los servidores que estén sin conexión. |

 

### <a name="volume-health-state-unhealthy"></a>Estado de mantenimiento de los volúmenes: Unhealthy (Incorrecto)

Cuando un volumen presenta un estado de mantenimiento incorrecto, algunos o todos los datos del volumen son inaccesibles en ese momento.

| Estado operativo | DESCRIPCIÓN |
|-------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Sin redundancia | El volumen perdió datos debido a errores en demasiadas unidades.<br> <br>**Acción:** Póngase en contacto con el soporte técnico. Antes de hacerlo, inicie el proceso de recopilación de archivos de registro siguiendo las instrucciones de https://aka.ms/azurestacklogfiles. |


### <a name="volume-health-state-unknown"></a>Estado de mantenimiento de los volúmenes: Desconocido

El volumen también puede presentar un estado de mantenimiento desconocido si se desasocia el disco virtual.

| Estado operativo | DESCRIPCIÓN |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Desasociado | Se produjo un error de dispositivo de almacenamiento que puede provocar que el volumen sea inaccesible. Se podrían perder algunos datos.<br> <br>**Acción:** <br>1. Compruebe la conectividad física y de red de todos los dispositivos de almacenamiento.<br>2. Si todos los dispositivos están conectados correctamente, póngase en contacto con el soporte técnico. Antes de hacerlo, inicie el proceso de recopilación de archivos de registro siguiendo las instrucciones de https://aka.ms/azurestacklogfiles. Es posible que deba realizar la restauración a partir de la copia de seguridad una vez restaurada la conexión con errores. |

## <a name="drive-states"></a>Estados de unidad

Utilice los siguientes comandos de PowerShell para supervisar el estado de mantenimiento de las unidades:

```powershell
$scaleunit_name = (Get-AzsScaleUnit)[0].name

$subsystem_name = (Get-AzsStorageSubSystem -ScaleUnit $scaleunit_name)[0].name

Get-AzsDrive -ScaleUnit $scaleunit_name -StorageSubSystem $subsystem_name | Select-Object StorageNode, PhysicalLocation, HealthStatus, OperationalStatus, Description, Action, Usage, CanPool, CannotPoolReason, SerialNumber, Model, MediaType, CapacityGB
```

En las secciones siguientes se describen los estados de mantenimiento que puede presentar una unidad.

### <a name="drive-health-state-healthy"></a>Estado de mantenimiento de las unidades: Healthy

| Estado operativo | DESCRIPCIÓN |
|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------|
| OK | El estado del volumen es correcto. |
| En servicio | La unidad está realizando algunas operaciones de mantenimiento interno. Una vez completada la acción, la unidad debería volver al estado de mantenimiento correcto (OK). |

### <a name="drive-health-state-healthy"></a>Estado de mantenimiento de las unidades: Healthy

Una unidad en estado de advertencia puede leer y escribir datos correctamente, pero tiene un problema.

| Estado operativo | DESCRIPCIÓN |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Comunicación perdida | Se perdió la conectividad a la unidad.<br> <br>**Acción:** vuelva a conectar todos los servidores. Si no se soluciona el problema, vuelva a conectar la unidad. Si el problema persiste, reemplace la unidad para garantizar una resistencia total. |
| Error predictivo | Se prevé que se producirá en breve un error de la unidad.<br> <br>**Acción:** reemplace la unidad tan pronto como sea posible para garantizar una resistencia total. |
| Error de E/S | Se produjo un error temporal al acceder a la unidad.<br> <br>**Acción:** si el problema persiste, reemplace la unidad para garantizar una resistencia total. |
| Error transitorio | Se produjo un error temporal con la unidad. Esto suele indicar que hubo una falta de respuesta de la unidad, pero también podría ser que la partición protectora Espacios de almacenamiento directo se hubiese quitado incorrectamente de la unidad. <br> <br>**Acción:** si el problema persiste, reemplace la unidad para garantizar una resistencia total. |
| Latencia anómala | A veces la unidad no responde y muestra signos de error.<br> <br>**Acción:** si el problema persiste, reemplace la unidad para garantizar una resistencia total. |
| Quitando del grupo | Azure Stack está quitando la unidad del bloque de almacenamiento.<br> <br>**Acción:** espere a que Azure Stack termine de quitar la unidad y, posteriormente, compruebe el estado.<br>Si el estado se mantiene, póngase en contacto con el soporte técnico. Antes de hacerlo, inicie el proceso de recopilación de archivos de registro siguiendo las instrucciones de https://aka.ms/azurestacklogfiles. |
|  |  |
| Iniciando el modo de mantenimiento | Azure Stack está en poniendo la unidad en modo de mantenimiento. Este es un estado temporal: en breve, la unidad debería estar en el estado En modo de mantenimiento.<br> <br>**Acción:** espere a que Azure Stack finalice el proceso y, posteriormente, compruebe el estado. |
| En modo de mantenimiento | La unidad está en modo de mantenimiento, y detiene las lecturas y escrituras de la unidad. Esto suele significar que las tareas de administración de Azure Stack, como PNU o FRU, dirigen la unidad. No obstante, el administrador también puede colocar la unidad en modo de mantenimiento.<br> <br>**Acción:** espere a que Azure Stack finalice la tarea de administración y, posteriormente, compruebe el estado.<br>Si el estado se mantiene, póngase en contacto con el soporte técnico. Antes de hacerlo, inicie el proceso de recopilación de archivos de registro siguiendo las instrucciones de https://aka.ms/azurestacklogfiles. |
|  |  |
| Deteniendo el modo de mantenimiento | Azure Stack está en el proceso de volver a conectar la unidad. Este es un estado temporal: en breve, la unidad debería estar en otro estado, preferiblemente Correcto.<br> <br>**Acción:** espere a que Azure Stack finalice el proceso y, posteriormente, compruebe el estado. |

 

### <a name="drive-health-state-unhealthy"></a>Estado de mantenimiento de las unidades: Unhealthy (Incorrecto)

No se puede escribir en una unidad en el estado incorrecto, ni tampoco acceder a ella.

| Estado operativo | DESCRIPCIÓN |
|-------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Dividir | La unidad se ha separado del grupo.<br> <br>**Acción:** reemplace la unidad por un disco nuevo. Si debe utilizar este disco, quítelo del sistema, asegúrese de que no contiene ningún dato útil, bórrelo y, a continuación, vuelva a colocarlo. |
| No se puede usar | El disco físico está en cuarentena porque no es compatible con su proveedor de soluciones. Solo se admiten los discos aprobados para la solución y que tienen el firmware del disco correcto.<br> <br>**Acción:** reemplace la unidad por un disco que tenga un fabricante aprobado y un número de modelo de la solución. |
| Metadatos obsoletos | El disco de reemplazo se utilizó previamente y podría contener datos de un sistema de almacenamiento desconocido. El disco está en cuarentena.        <br> <br>**Acción:** reemplace la unidad por un disco nuevo. Si debe utilizar este disco, quítelo del sistema, asegúrese de que no contiene ningún dato útil, bórrelo y, a continuación, vuelva a colocarlo. |
| Metadatos no reconocidos | Se encontraron metadatos no reconocidos en la unidad, lo que suele significar que la unidad contiene metadatos de un grupo diferente.<br> <br>**Acción:** reemplace la unidad por un disco nuevo. Si debe utilizar este disco, quítelo del sistema, asegúrese de que no contiene ningún dato útil, bórrelo y, a continuación, vuelva a colocarlo. |
| Error en los medios | Se produjo un error en la unidad y Espacios de almacenamiento ya no la podrá usar.<br> <br>**Acción:** reemplace la unidad tan pronto como sea posible para garantizar una resistencia total. |
| Error de hardware del dispositivo | Se produjo un error de hardware en esta unidad. <br> <br>**Acción:** reemplace la unidad tan pronto como sea posible para garantizar una resistencia total. |
| Actualizando el firmware | Azure Stack está actualizando el firmware en la unidad. Se trata de un estado temporal que suele durar menos de un minuto y durante el cual otras unidades del grupo controlan todas la lecturas y escrituras.<br> <br>**Acción:** espere a que Azure Stack finalice la actualización y, posteriormente, compruebe el estado. |
| Iniciando | La unidad se está preparando para la operación. Debería ser un estado temporal: una vez finalizado, la unidad debería cambiar a un estado operativo diferente.<br> <br>**Acción:** espere a que Azure Stack finalice la operación y, posteriormente, compruebe el estado. |
 

## <a name="reasons-a-drive-cant-be-pooled"></a>Motivos por los que no se puede agrupar una unidad

Simplemente, algunas unidades no están listas para estar en el bloque de almacenamiento de Azure Stack. Puede averiguar por qué una unidad no es válida para la agrupación si echa un vistazo a la propiedad CannotPoolReason de una unidad. En la tabla siguiente se ofrece un poco más de información sobre cada uno de los motivos.

| Motivo | DESCRIPCIÓN |
|--------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Hardware no compatible | La unidad no se encuentra en la lista de modelos de almacenamiento aprobados especificado mediante el Servicio de mantenimiento.<br> <br>**Acción:** reemplace la unidad por un disco nuevo. |
| Firmware no compatible | El firmware de la unidad física no se encuentra en la lista de revisiones de firmware aprobadas con el Servicio de mantenimiento.<br> <br>**Acción:** reemplace la unidad por un disco nuevo. |
| Usado por el clúster | Un clúster de conmutación por error está usando la unidad actualmente.<br> <br>**Acción:** reemplace la unidad por un disco nuevo. |
| Medios extraíbles | La unidad está clasificada como una unidad extraíble. <br> <br>**Acción:** reemplace la unidad por un disco nuevo. |
| Incorrecto | La unidad no está en un estado correcto y es posible que deba reemplazarse.<br> <br>**Acción:** reemplace la unidad por un disco nuevo. |
| Capacidad insuficiente | Existen particiones que ocupan el espacio disponible en la unidad.<br> <br>**Acción:** reemplace la unidad por un disco nuevo. Si debe utilizar este disco, quítelo del sistema, asegúrese de que no contiene ningún dato útil, bórrelo y, a continuación, vuelva a colocarlo. |
| Verificación en curso | El Servicio de mantenimiento está comprobando si la unidad o el firmware en la unidad está aprobados para su uso.<br> <br>**Acción:** espere a que Azure Stack finalice el proceso y, posteriormente, compruebe el estado. |
| Error de verificación | El Servicio de mantenimiento no pudo comprobar si la unidad o el firmware en la unidad están aprobados para su uso.<br> <br>**Acción:** Póngase en contacto con el soporte técnico. Antes de hacerlo, inicie el proceso de recopilación de archivos de registro siguiendo las instrucciones de https://aka.ms/azurestacklogfiles. |
| Sin conexión | La unidad está sin conexión. <br> <br>**Acción:** Póngase en contacto con el soporte técnico. Antes de hacerlo, inicie el proceso de recopilación de archivos de registro siguiendo las instrucciones de https://aka.ms/azurestacklogfiles. |

## <a name="next-step"></a>Paso siguiente

[Administración de la capacidad de almacenamiento](azure-stack-manage-storage-shares.md) 
