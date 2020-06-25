---
title: Extensión de volúmenes en Azure Stack HCI
description: Cómo cambiar el tamaño de volúmenes en Azure Stack HCI mediante Windows Admin Center y PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: 1369d3bcd0393fd322d17e1977524732d5b97ccf
ms.sourcegitcommit: 76af742a42e807c400474a337e29d088ede8a60d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2020
ms.locfileid: "85196450"
---
# <a name="extending-volumes-in-storage-spaces-direct"></a>Extensión de volúmenes en Espacios de almacenamiento directo
> Se aplica a: Windows Server 2019

En este tema se proporcionan instrucciones para cambiar el tamaño de los volúmenes en un clúster de [Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/storage-spaces-direct-overview) mediante Windows Admin Center.

> [!WARNING]
> **No se permite cambiar el tamaño del almacenamiento subyacente que Espacios de almacenamiento directo utiliza.** Si ejecuta Espacios de almacenamiento directo en un entorno de almacenamiento virtualizado, incluido en Azure, no se permite admite cambiar el tamaño ni modificar las características de los dispositivos de almacenamiento que las máquinas virtuales utilizan, y los datos quedarán inaccesibles. En su lugar, siga las instrucciones de la sección [Adición de servidores o unidades](/windows-server/storage/storage-spaces/add-nodes) para agregar más capacidad antes de extender los volúmenes.

Vea un vídeo rápido sobre cómo cambiar el tamaño de un volumen.

> [!VIDEO https://www.youtube-nocookie.com/embed/hqyBzipBoTI]

## <a name="extending-volumes-using-windows-admin-center"></a>Extensión de volúmenes mediante Windows Admin Center

1. En Windows Admin Center, conéctese a un clúster de Espacios de almacenamiento directo y seleccione **Volumes** (Volúmenes) en el panel **Tools** (Herramientas).
2. En la página **Volumes** (Volúmenes), seleccione la pestaña **Inventory** (Inventario) y, a continuación, seleccione el volumen cuyo tamaño quiere cambiar.

    En la página de detalles del volumen, se indica la capacidad de almacenamiento del volumen. También puede abrir la página de detalles de los volúmenes directamente desde el panel. En el panel Alertas, seleccione la alerta que notifica si un volumen se está quedando sin capacidad de almacenamiento, y seleccione **Go To Volume** (Ir al volumen).

4. En la parte superior de la página de detalles del volumen, seleccione **Resize** (Cambiar tamaño).
5. Especifique un tamaño mayor y seleccione **Resize** (Cambiar tamaño).

    En la página de detalles del volumen aparece la nueva capacidad de almacenamiento del volumen y se borra la alerta del panel.

## <a name="extending-volumes-using-powershell"></a>Extensión de volúmenes con PowerShell

### <a name="capacity-in-the-storage-pool"></a>Capacidad en el bloque de almacenamiento

Antes de cambiar el tamaño de un volumen, asegúrese de que tiene suficiente capacidad en el bloque de almacenamiento para acomodar la nueva superficie de mayor tamaño. Por ejemplo, al cambiar el tamaño de un volumen de reflejo tridireccional de 1 TB a 2 TB, su superficie de memoria crecería de 3 TB a 6 TB. Para que el cambio de tamaño se realice correctamente, necesitaría al menos (6-3) = 3 TB de capacidad disponible en el bloque de almacenamiento.

### <a name="familiarity-with-volumes-in-storage-spaces"></a>Descripción de los volúmenes en Espacios de almacenamiento directo

En Espacios de almacenamiento directo, cada volumen consta de varios objetos apilados: el volumen compartido del clúster (CSV), que es un volumen; la partición; el disco, que es un disco virtual; y una o más capas de almacenamiento (si procede). Para cambiar el tamaño de un volumen, deberá cambiar el tamaño de varios de estos objetos.

![volumes-in-smapi](media/extend-volumes/volumes-in-smapi.png)

Para familiarizarse con ellos, pruebe a ejecutar **Get-** con el nombre correspondiente en PowerShell.

Por ejemplo:

```PowerShell
Get-VirtualDisk
```

Para seguir las asociaciones entre los objetos de la pila, canalice un cmdlet **Get-** al siguiente.

Por ejemplo, aquí se muestra cómo obtener un disco virtual hasta su volumen:

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-Disk | Get-Partition | Get-Volume
```

### <a name="step-1--resize-the-virtual-disk"></a>Paso 1: Cambio de tamaño del disco virtual

El disco virtual puede usar o no capas de almacenamiento, en función de cómo se haya creado.

Para comprobarlo, ejecute el siguiente cmdlet:

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier
```

Si el cmdlet no devuelve nada, el disco virtual no usa capas de almacenamiento.

#### <a name="no-storage-tiers"></a>Sin capas de almacenamiento

Si el disco virtual no tiene capas de almacenamiento, puede cambiar su tamaño directamente con el cmdlet **Resize-VirtualDisk**.

Indique el nuevo tamaño en el parámetro **-Size**.

```PowerShell
Get-VirtualDisk <FriendlyName> | Resize-VirtualDisk -Size <Size>
```

Cuando se cambia el tamaño de **VirtualDisk**, también se cambia el tamaño de **Disk** automáticamente.

![Resize-VirtualDisk](media/extend-volumes/Resize-VirtualDisk.gif)

#### <a name="with-storage-tiers"></a>Con capas de almacenamiento

Si el disco virtual usa capas de almacenamiento, puede cambiar el tamaño de cada capa por separado con el cmdlet **Resize-StorageTier**.

Para obtener los nombres de las capas de almacenamiento, siga las asociaciones desde el disco virtual.

```PowerShell
Get-VirtualDisk <FriendlyName> | Get-StorageTier | Select FriendlyName
```

Después, para cada capa, indique el nuevo tamaño en el parámetro **-Size**.

```PowerShell
Get-StorageTier <FriendlyName> | Resize-StorageTier -Size <Size>
```

> [!TIP]
> Si las capas son tipos de medios físicos diferentes (como **MediaType = SSD** y **MediaType = HDD**) asegúrese de que tiene suficiente capacidad de cada tipo de medio en el bloque de almacenamiento para acomodar la nueva superficie de memoria de mayor tamaño de cada capa.

Cuando se cambia el tamaño de **StorageTier**, también se cambia el tamaño de **VirtualDisk** y **Disk** automáticamente.

![Resize-StorageTier](media/extend-volumes/Resize-StorageTier.gif)

### <a name="step-2--resize-the-partition"></a>Paso 2: Cambio de tamaño de la partición

A continuación, cambie el tamaño de la partición con el cmdlet **Resize-Partition**. Se espera que el disco virtual tenga dos particiones: la primera es Reserved (Reserva) y no debe modificarse; la que tiene que cambiar de tamaño tiene los valores **PartitionNumber = 2** y **Type = Basic**.

Indique el nuevo tamaño en el parámetro **-Size**. Se recomienda usar el tamaño máximo admitido, como se muestra a continuación.

```PowerShell
# Choose virtual disk
$VirtualDisk = Get-VirtualDisk <FriendlyName>

# Get its partition
$Partition = $VirtualDisk | Get-Disk | Get-Partition | Where PartitionNumber -Eq 2

# Resize to its maximum supported size
$Partition | Resize-Partition -Size ($Partition | Get-PartitionSupportedSize).SizeMax
```

Al cambiar el tamaño de **Partition**, también se cambia el tamaño de **Volume** y **ClusterSharedVolume** automáticamente.

![Resize-Partition](media/extend-volumes/Resize-Partition.gif)

Eso es todo.

> [!TIP]
> Para comprobar que el volumen tiene el nuevo tamaño, ejecute **Get-Volume**.

## <a name="next-steps"></a>Pasos siguientes

Para obtener instrucciones detalladas sobre otras tareas de administración del almacenamiento esenciales, consulte también:

- [Planeamiento de volúmenes en Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/plan-volumes)
- [Creación de volúmenes en Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/create-volumes)
- [Eliminación de volúmenes en Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/delete-volumes)