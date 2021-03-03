---
title: Creación de volúmenes en Azure Stack HCI
description: Cómo crear volúmenes en Azure Stack HCI mediante Windows Admin Center y PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 02/04/2021
ms.openlocfilehash: 9bb0ff34863f8262d5919e5eae6f735709097bf5
ms.sourcegitcommit: 283b1308142e668749345bf24b63d40172559509
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/05/2021
ms.locfileid: "99570742"
---
# <a name="create-volumes-in-azure-stack-hci"></a>Creación de volúmenes en Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2

En este tema se describe cómo crear volúmenes en un clúster de Azure Stack HCI mediante Windows Admin Center y Windows PowerShell, cómo trabajar con archivos en los volúmenes y cómo habilitar la compresión y la desduplicación de datos en los volúmenes. Para aprender a crear volúmenes y configurar la replicación para clústeres extendidos, consulte [Creación de volúmenes extendidos](create-stretched-volumes.md).

## <a name="create-a-three-way-mirror-volume"></a>Creación de un volumen de reflejo tridireccional

Para crear un volumen de reflejo tridireccional mediante Windows Admin Center:

1. En Windows Admin Center, conéctese a un clúster y seleccione **Volumes** (Volúmenes) en el panel **Tools** (Herramientas).
2. En la página **Volumes** (Volúmenes), seleccione la pestaña **Inventory** (Inventario) y, a continuación, seleccione **Create volume** (Crear volumen).
3. En el panel **Create volume** (Crear volumen), escriba un nombre para el volumen y deje **Resiliency** (Resistencia) como **Three-way mirror** (Reflejo triple).
4. En **Size on HDD**, especifique el tamaño del volumen. Por ejemplo, 5 TB (terabytes).
5. Seleccione **Crear**.

En función del tamaño, la creación del volumen puede tardar unos minutos. Las notificaciones de la parte superior derecha le indicarán cuándo está creado el volumen. El nuevo volumen aparece en la lista de inventario.

Vea un breve vídeo sobre cómo crear un volumen de reflejo triple.

> [!VIDEO https://www.youtube-nocookie.com/embed/o66etKq70N8]

## <a name="create-a-mirror-accelerated-parity-volume"></a>Creación de un volumen con paridad acelerada por reflejo

La paridad acelerada por reflejo (MAP) reduce la superficie de memoria del volumen en el HDD. Por ejemplo, un volumen de reflejo triple significa que, por cada 10 terabytes de tamaño, necesitará 30 terabytes como superficie de memoria. Para reducir la sobrecarga en la superficie de memoria, cree un volumen con paridad acelerada por reflejo. Esto reduce la superficie de memoria de 30 terabytes a tan solo 22 terabytes, incluso con 4 servidores solo. Para ello, se crea un reflejo del 20 % de los datos más activos y se usa la paridad, que es más eficaz, para almacenar el resto. Puede ajustar esta proporción entre paridad y reflejo para lograr el equilibrio entre el rendimiento y capacidad adecuado para su carga de trabajo. Por ejemplo, una paridad del 90 % y un reflejo del 10 % producen menos rendimiento, pero optimiza aún más la superficie de memoria.

  >[!NOTE]
  >Los volúmenes de paridad acelerada por reflejo requieren el sistema de archivos resistente (ReFS).

Para crear un volumen con paridad acelerada por reflejo en Windows Admin Center:

1. En Windows Admin Center, conéctese a un clúster y seleccione **Volumes** (Volúmenes) en el panel **Tools** (Herramientas).
2. En la página Volumes (Volúmenes), seleccione la pestaña **Inventory** (Inventario) y, a continuación, seleccione **Create volume** (Crear volumen).
3. En el panel **Create volume** (Crear volumen), escriba un nombre para el volumen.
4. En **Resiliency** (Resistencia), seleccione **Mirror-accelerated parity** (Paridad acelerada por reflejo).
5. En **Parity percentage** (Porcentaje de paridad), seleccione el porcentaje de paridad.
6. Seleccione **Crear**.

Vea un vídeo rápido sobre cómo crear un volumen de paridad acelerada por reflejo.

> [!VIDEO https://www.youtube-nocookie.com/embed/R72QHudqWpE]

## <a name="open-volume-and-add-files"></a>Apertura del volumen y adición de archivos

Para abrir un volumen y agregarle archivos en Windows Admin Center:

1. En Windows Admin Center, conéctese a un clúster y seleccione **Volumes** (Volúmenes) en el panel **Tools** (Herramientas).
2. En la página **Volumes** (Volúmenes), seleccione la pestaña **Inventory** (Inventario).
2. En la lista de volúmenes, seleccione el nombre del volumen que quiere abrir.

    En la página de detalles del volumen, verá la ruta de acceso al volumen.

4. En la parte superior de la página, seleccione **Open** (Abrir). Se inicia la herramienta **Files** (Archivos) en Windows Admin Center.
5. Vaya a la ruta de acceso del volumen. Aquí puede examinar los archivos del volumen.
6. Seleccione **Upload** (Cargar) y, a continuación, seleccione un archivo para cargarlo.
7. Use el botón **Back** (Atrás) del explorador para volver al panel **Tools** (Herramientas) de Windows Admin Center.

Vea un vídeo rápido sobre cómo abrir un volumen y agregar archivos.

> [!VIDEO https://www.youtube-nocookie.com/embed/j59z7ulohs4]

## <a name="turn-on-deduplication-and-compression"></a>Activación de la desduplicación y la compresión

La desduplicación y la compresión se administran en cada volumen. La desduplicación y la compresión utilizan un modelo de procesamiento posterior, lo que significa que no verá cuánto ahorra hasta que se ejecute. Cuando lo hace, funcionará en todos los archivos, incluso en los que ya estaban allí.

Para más información, consulte [Habilitación del cifrado de volumen, desduplicación y compresión](volume-encryption-deduplication.md).

## <a name="create-volumes-using-windows-powershell"></a>Creación de volúmenes con Windows PowerShell

En primer lugar, inicie Windows PowerShell desde el menú Inicio de Windows. Se recomienda usar el cmdlet **New-Volume** para crear volúmenes para Azure Stack HCI. Ofrece la experiencia más rápida y sencilla. Este cmdlet crea automáticamente el disco virtual, las particiones y los formatea, crea el volumen con el nombre correspondiente y lo agrega a los volúmenes compartidos del clúster, todo en un solo paso.

El cmdlet **New-Volume** tiene cuatro parámetros que debe proporcionar siempre:

- **FriendlyName:** cualquier cadena que desee, por ejemplo *"Volume1"* .
- **FileSystem:** (Sistema de archivos) **CSVFS_ReFS** (recomendado para todos los volúmenes; necesario para volúmenes de paridad acelerada por reflejo) o **CSVFS_NTFS**
- **StoragePoolFriendlyName:** el nombre del bloque de almacenamiento; por ejemplo *"S2D en ClusterName"* .
- **Size:** el tamaño del volumen, por ejemplo *"10TB"* .

   > [!NOTE]
   > Windows, incluido PowerShell, cuenta con números binarios (en base 2), mientras que las unidades suelen etiquetarte con números decimales (en base 10). Esto explica por qué una unidad de "un terabyte", definida como 1 billón de bytes, aparece en Windows como unos "909 GB". Se espera que esto sea así. Al crear volúmenes mediante **New-Volume**, debe especificar el parámetro **Size** (Tamaño) en números binarios (en base 2). Por ejemplo, si se especifica "909GB" o "0,909495TB", se creará un volumen de aproximadamente 1 billón de bytes.

### <a name="example-with-2-or-3-servers"></a>Ejemplo: Con dos o tres servidores

Para facilitar las cosas, si la implementación tiene solo dos servidores, Espacios de almacenamiento directo usará automáticamente la resistencia de reflejo bidireccional. Si la implementación tiene solo tres servidores, usará automáticamente el reflejo triple.

```PowerShell
New-Volume -FriendlyName "Volume1" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB
```

### <a name="example-with-4-servers"></a>Ejemplo: Con más de cuatro servidores

Si tiene cuatro o más servidores, puede usar el parámetro opcional **ResiliencySettingName** para elegir el tipo de resistencia.

-   **ResiliencySettingName:** **Mirror** o **Parity**.

En el ejemplo siguiente, *"Volume2"* usa el reflejo triple y *"Volume3"* usa paridad dual (también conocidad como "codificación de borrado").

```PowerShell
New-Volume -FriendlyName "Volume2" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Mirror
New-Volume -FriendlyName "Volume3" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -Size 1TB -ResiliencySettingName Parity
```

## <a name="using-storage-tiers"></a>Uso de capas de almacenamiento

En las implementaciones con tres tipos de unidades, un volumen puede abarcar las capas SSD y HDD, y residir parcialmente en cada uno. Del mismo modo, en implementaciones con cuatro o más servidores, un volumen puede mezclar reflejo y paridad dual para residir parcialmente en cada uno de ellos.

Para ayudarle a crear estos volúmenes, Azure Stack HCI proporciona las plantillas de nivel predeterminadas llamadas **MirrorOn*MediaType*** y **NestedMirrorOn*MediaType*** (para el rendimiento), y **ParityOn*MediaType*** y **NestedParityOn*MediaType*** (para la capacidad), donde *MediaType* es HDD o SSD. Las plantillas representan niveles de almacenamiento en función del tipo de medio y encapsulan definiciones para la creación de reflejo triple en las unidades más rápidas (si procede) y paridad dual en las unidades más lentas (si procede).

Puede verlo mediante la ejecución del cmdlet **Get-StorageTier** en cualquier servidor del clúster.

```PowerShell
Get-StorageTier | Select FriendlyName, ResiliencySettingName, PhysicalDiskRedundancy
```

Por ejemplo, si tiene un clúster de dos nodos solo con HDD, la salida podría tener un aspecto similar al siguiente:

```PowerShell
FriendlyName      ResiliencySettingName PhysicalDiskRedundancy
------------      --------------------- ----------------------
NestedParityOnHDD Parity                                     1
Capacity          Mirror                                     1
NestedMirrorOnHDD Mirror                                     3
MirrorOnHDD       Mirror                                     1
```

Para crear volúmenes en capas, haga referencia a estas plantillas de capa con los parámetros **StorageTierFriendlyNames** y **StorageTierSizes** del cmdlet **New-Volume**. Por ejemplo, el siguiente cmdlet crea un volumen que combina reflejo triple y paridad dual en una proporción 30:70.

```PowerShell
New-Volume -FriendlyName "Volume1" -FileSystem CSVFS_ReFS -StoragePoolFriendlyName S2D* -StorageTierFriendlyNames MirrorOnHDD, Capacity -StorageTierSizes 300GB, 700GB
```

Repita el procedimiento si necesita crear más de un volumen.

### <a name="nested-resiliency-volumes"></a>Volúmenes de resistencia anidada

La resistencia anidada solo se aplica a los clústeres de dos servidores; no se puede usar la resistencia anidada si el clúster tiene tres servidores o más. La resistencia anidada permite que un clúster de dos servidores resista varios errores de hardware al mismo tiempo sin perder la disponibilidad del almacenamiento, lo que permite a los usuarios, las aplicaciones y las máquinas virtuales seguir su ejecución sin interrupciones. Para más información, consulte [Planeamiento de volúmenes en Azure Stack HCI: elección del tipo de resistencia](../concepts/plan-volumes.md#choosing-the-resiliency-type).

#### <a name="create-nested-storage-tiers"></a>Creación de niveles de almacenamiento anidados

Para crear un nivel NestedMirror:

```PowerShell
New-StorageTier -StoragePoolFriendlyName S2D* -FriendlyName NestedMirror -ResiliencySettingName Mirror -NumberOfDataCopies 4 -MediaType HDD -CimSession 2nodecluster
```

Para crear un nivel NestedParity:

```PowerShell
New-StorageTier -StoragePoolFriendlyName S2D* -FriendlyName NestedParity -ResiliencySettingName Parity -NumberOfDataCopies 2 -PhysicalDiskRedundancy 1 -NumberOfGroups 1 -FaultDomainAwareness StorageScaleUnit -ColumnIsolation PhysicalDisk -MediaType HDD -CimSession 2nodecluster
```

#### <a name="create-nested-volumes"></a>Creación de volúmenes anidados

Para crear un volumen NestedMirror:

```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName MyMirrorNestedVolume -StorageTierFriendlyNames NestedMirror -StorageTierSizes 500GB -CimSession 2nodecluster
```

Para crear un volumen NestedParity:

```PowerShell
New-Volume -StoragePoolFriendlyName S2D* -FriendlyName MyParityNestedVolume -StorageTierFriendlyNames NestedMirror,NestedParity -StorageTierSizes 200GB, 1TB -CimSession 2nodecluster
```

### <a name="storage-tier-summary-table"></a>Tabla resumen de niveles de almacenamiento

En las tablas siguientes se resumen los niveles de almacenamiento que hay y se pueden crear en Azure Stack HCI.

**NumberOfNodes: 2**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Nota:         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | Reflejo                | 2                  | 1                      | 1              | StorageScaleUnit     | Disco físico    | creación automática |
| MirrorOnSSD       | SSD       | Reflejo                | 2                  | 1                      | 1              | StorageScaleUnit     | Disco físico    | creación automática |
| MirrorOnSCM       | SCM       | Reflejo                | 2                  | 1                      | 1              | StorageScaleUnit     | Disco físico    | creación automática |
| NestedMirrorOnHDD | HDD       | Reflejo                | 4                  | 3                      | 1              | StorageScaleUnit     | Disco físico    | manual       |
| NestedMirrorOnSSD | SSD       | Reflejo                | 4                  | 3                      | 1              | StorageScaleUnit     | Disco físico    | manual       |
| NestedMirrorOnSCM | SCM       | Reflejo                | 4                  | 3                      | 1              | StorageScaleUnit     | Disco físico    | manual       |
| NestedParityOnHDD | HDD       | Parity                | 2                  | 1                      | 1              | StorageScaleUnit     | Disco físico    | manual       |
| NestedParityOnSSD | SSD       | Parity                | 2                  | 1                      | 1              | StorageScaleUnit     | Disco físico    | manual       |
| NestedParityOnSCM | SCM       | Parity                | 2                  | 1                      | 1              | StorageScaleUnit     | Disco físico    | manual       |

**NumberOfNodes: 3**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Nota:         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | Reflejo                | 3                  | 2                      | 1              | StorageScaleUnit     | Disco físico    | creación automática |
| MirrorOnSSD       | SSD       | Reflejo                | 3                  | 2                      | 1              | StorageScaleUnit     | Disco físico    | creación automática |
| MirrorOnSCM       | SCM       | Reflejo                | 3                  | 2                      | 1              | StorageScaleUnit     | Disco físico    | creación automática |

**NumberOfNodes: 4+**

| FriendlyName      | MediaType | ResiliencySettingName | NumberOfDataCopies | PhysicalDiskRedundancy | NumberOfGroups | FaultDomainAwareness | ColumnIsolation | Nota:         |
| ----------------- | :-------: | :-------------------: | :----------------: | :--------------------: |:--------------:| :------------------: | :-------------: | :----------: |
| MirrorOnHDD       | HDD       | Reflejo                | 3                  | 2                      | 1              | StorageScaleUnit     | Disco físico    | creación automática |
| MirrorOnSSD       | SSD       | Reflejo                | 3                  | 2                      | 1              | StorageScaleUnit     | Disco físico    | creación automática |
| MirrorOnSCM       | SCM       | Reflejo                | 3                  | 2                      | 1              | StorageScaleUnit     | Disco físico    | creación automática |
| ParityOnHDD       | HDD       | Parity                | 1                  | 2                      | Auto           | StorageScaleUnit     | StorageScaleUnit| creación automática |
| ParityOnSSD       | SSD       | Parity                | 1                  | 2                      | Auto           | StorageScaleUnit     | StorageScaleUnit| creación automática |
| ParityOnSCM       | SCM       | Parity                | 1                  | 2                      | Auto           | StorageScaleUnit     | StorageScaleUnit| creación automática |

## <a name="next-steps"></a>Pasos siguientes

Para temas relacionados y otras tareas de administración del almacenamiento, vea también:

- [Introducción a Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Planeamiento de volúmenes](../concepts/plan-volumes.md)
- [Ampliar volúmenes](extend-volumes.md)
- [Eliminar volúmenes](delete-volumes.md)