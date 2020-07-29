---
title: Creación de volúmenes y configuración de la replicación para clústeres extendidos en Azure Stack HCI
description: Cómo crear volúmenes y configurar la replicación para clústeres extendidos en Azure Stack HCI mediante Windows Admin Center y PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.date: 07/21/2020
ms.openlocfilehash: 647fbbc1fc0ae070955f796aee7aa102290bbc4f
ms.sourcegitcommit: 0e52f460295255b799bac92b40122a22bf994e27
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/21/2020
ms.locfileid: "86868119"
---
# <a name="create-volumes-and-set-up-replication-for-stretched-clusters"></a>Creación de volúmenes y configuración de la replicación para clústeres extendidos

> Se aplica a: Azure Stack HCI, versión 20H2

En este tema se describe cómo crear volúmenes y configurar la replicación para clústeres extendidos en Azure Stack HCI mediante Windows Admin Center y PowerShell. Para obtener información sobre cómo crear volúmenes en clústeres de un solo sitio, trabajar con archivos en los volúmenes y habilitar la desduplicación de datos y la compresión en los volúmenes, consulte [Crear volúmenes](create-volumes.md).

## <a name="create-volumes-and-set-up-replication-for-stretched-clusters-using-windows-admin-center"></a>Creación de volúmenes y configuración de la replicación para clústeres extendidos mediante Windows Admin Center

Empecemos:

1. En Windows Admin Center, en **Tools** (Tools), seleccione **Volumes** (Volúmenes).
1. En el panel derecho, seleccione la pestaña **Inventory** (Inventario) y, a continuación **Create** (Crear).
1. En el panel **Create volume** (Crear volumen), seleccione **Replicate volume between sites** (Replicar volumen entre sitios).
1. Seleccione una dirección de replicación entre sitios en el cuadro desplegable.
1. En **Replication mode** (Modo de replicación), seleccione **Asynchronous** (Asincrónico) o **Synchronous** (Sincrónico).
1. Escriba un nombre de grupo de replicación de origen y otro de destino.
1. Escriba el tamaño deseado para el volumen de registro.
1. En **Advanced** (Opciones avanzadas), siga uno de estos pasos:
     - Escriba o cambie el **nombre del grupo de replicación de origen**.
     - Escriba o cambie el **nombre del grupo de replicación de destino**.
     - Para **usar los bloques ya inicializados en el destino**, active la casilla correspondiente.
     - Para **cifrar el tráfico de replicación**, active la casilla correspondiente.
     - Para **habilitar los grupos de coherencia**, active la casilla correspondiente.
1. Cuando haya terminado, haga clic en **Crear**.
1. En el panel derecho, compruebe que se crea un disco de datos y un disco de registro en el sitio principal (activo) y que los datos correspondientes y los discos de réplica de registro se crean en el sitio secundario (pasivo). En el caso de la replicación bidireccional, debería ver dos conjuntos de datos y discos de volumen.
1. En **Herramientas**, seleccione **Réplica de almacenamiento**.
1. En el panel derecho, en **Asociaciones**, compruebe que la asociación de replicación se ha creado correctamente.

Después, debe comprobar la correcta replicación de los datos entre sitios antes de implementar VM y otras cargas de trabajo. Consulte la sección de comprobación de la replicación en [Validación del clúster](../deploy/validate.md) para obtener más información.

## <a name="create-volumes-for-stretched-clusters-using-powershell"></a>Creación de volúmenes para clústeres extendidos mediante PowerShell

La creación de volúmenes es diferente para los clústeres estándar de un solo sitio y para los clústeres extendidos de dos sitios. No obstante, para ambos escenarios se usa el cmdlet `New-Volume` para crear un disco virtual, crear una partición y formatearla, crear un volumen con un nombre coincidente y agregarlo a los volúmenes compartidos del clúster (CSV).

La creación de volúmenes y discos virtuales para clústeres extendidos es un poco más complicada que en el caso de los clústeres de un solo sitio. Los clústeres extendidos requieren un mínimo de cuatro volúmenes: dos volúmenes de datos y dos de registro. En cada sitio reside al menos un par de volúmenes de datos/registro. A continuación creará un grupo de replicación para cada sitio y configurará la replicación entre ellos.

En primer lugar, es necesario mover los grupos de recursos de un nodo a otro. Para hacer esto, se usa el cmdlet `Move-ClusterGroup`.

En primer lugar, se mueve el grupo de recursos del bloque de almacenamiento al nodo Server1 de Site1 mediante el cmdlet `Move-ClusterGroup`:

```powershell
Move-ClusterGroup -Cluster ClusterS1 -Name ‘Available Storage’ -Node Server1
```

A continuación, cree el primer disco virtual (Disk1) para el nodo Server1 en el sitio Site1:

```powershell
New-Volume -CimSession Server1 -FriendlyName Disk1 -FileSystem REFS -DriveLetter F -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
```

Cree un segundo disco virtual (Disk2) para el nodo Server1:

```powershell
New-Volume -CimSession Server1 -FriendlyName Disk2 -FileSystem REFS -DriveLetter G -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 1"
```

Ahora, desconecte el grupo "Available Storage":

```powershell
Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
```

Y mueva el grupo "Almacenamiento disponible" al nodo Server3 en Site2:

```powershell
Move-ClusterGroup -Name 'Available Storage' -Node Server3
```

Cree el primer disco virtual (Disk3) en el nodo Server3 de Site2:

```powershell
New-Volume -CimSession Server3 -FriendlyName Disk3 -FileSystem REFS -DriveLetter H -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
```

Y cree un segundo disco virtual (Disk4) en el nodo Server3:

```powershell
New-Volume -CimSession Server3 -FriendlyName Disk4 -FileSystem REFS -DriveLetter I -ResiliencySettingName Mirror -Size 10GB -StoragePoolFriendlyName "Storage Pool for Site 2"
```

Ahora desconecte el grupo `Available Storage` y, a continuación, muévalo de nuevo a uno de los nodos de Site1:

```powershell
Stop-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage'
```

```powershell
Move-ClusterGroup -Cluster ClusterS1 -Name 'Available Storage' -Node Server1
```

Con el cmdlet `Get-ClusterResource`, asegúrese de que se crearon cuatro volúmenes de discos virtuales, dos en cada bloque de almacenamiento:

```powershell
Get-ClusterResource -Cluster ClusterS1
```

Ahora agregue Disk1 a los volúmenes compartidos del clúster:

```powershell
Add-ClusterSharedVolume -Name 'Cluster Virtual Disk (Disk1)'
```

## <a name="setup-replication-for-stretched-clusters-using-powershell"></a>Configuración de la replicación para clústeres extendidos mediante PowerShell

Al usar PowerShell para configurar la réplica de almacenamiento para un clúster extendido, el disco que se usará para los datos de origen tendrá que agregarse como volumen compartido de clúster (CSV). Todos los demás discos deben permanecer como unidades no CSV en el grupo de almacenamiento disponible. Estos discos se agregarán entonces como volúmenes compartidos de clúster durante el proceso de creación de réplica de almacenamiento.

En el paso anterior, los discos virtuales se agregaron mediante letras de unidad para facilitar su identificación. La réplica de almacenamiento es una replicación de uno a uno, lo que significa que un solo disco puede replicarse a otro disco.

### <a name="validate-the-topology-for-replication"></a>Validación de la topología para la replicación

Antes de empezar, debe ejecutar el cmdlet `Test-SRTopology` durante un período prolongado (p. ej., varias horas). El cmdlet `Test-SRTopology` valida una posible asociación de replicación y también valida el host local en el servidor de destino o de forma remota entre los servidores de origen y de destino.

Este cmdlet comprobará lo siguiente:

- Se puede acceder a SMB desde la red, lo que significa que el puerto TCP 445 y el puerto 5445 están abiertos bidireccionalmente.
- Se puede acceder a WS-MAN mediante HTTP en la red, lo que significa que los puertos TCP 5985 y 5986 están abiertos.
- Se puede tener acceso a un proveedor de WMIv2 de SR y este puede aceptar solicitudes.
- Los volúmenes de datos de origen y de destino existen y se puede escribir en ellos.
- Los volúmenes de registro de origen y destino existen con formato NTFS o ReFS y suficiente espacio disponible.
- El almacenamiento se inicializa en formato GPT, no en MBR, con tamaños de sector coincidentes.
- Hay suficiente memoria física para ejecutar la replicación.

Además, el cmdlet `Test-SRTopology` también medirá lo siguiente:

- Latencia de ida y vuelta de ICMP e informe acerca del promedio.
- Contadores de rendimiento para la entrada/salida de escritura e informe acerca del promedio detectado en ese volumen.
- Hora de sincronización inicial estimada.

Al finalizar Test-SRTopology, se creará un archivo .html (TestSrTopologyReport con la fecha y la hora) en la carpeta temporal de Windows. Cualquier advertencia o error debe revisarse, ya que podría provocar que la réplica de almacenamiento no se creara correctamente.

Un comando de ejemplo que se ejecutaría durante 5 horas sería:

```powershell
Test-SRTopology -SourceComputerName Server1 -SourceVolumeName W: -SourceLogVolumeName X: -DestinationComputerName Server3 -DestinationVolumeName Y: -DestinationLogVolumeName Z: -DurationInMinutes 300 -ResultPath c:\temp
```

### <a name="create-the-replication-partnership"></a>Creación del grupo de replicación

Ahora que ha completado las pruebas de `Test-SRTopology`, está listo para configurar la réplica de almacenamiento y crear la asociación de replicación. En pocas palabras, configuraremos la réplica de almacenamiento mediante la creación de grupos de replicación (RG) para cada sitio y la especificación de los volúmenes de datos y de registro de los nodos de servidor de origen de Site1 (Server1, Server2) y los nodos de servidor de destino (replicados) de Site2 (Server3, Server4).

Empecemos:

1. Agregue el disco de datos de Site1 como volumen compartido del clúster (CSV):

   ```powershell
   Add-ClusterSharedVolume -Name "Cluster Virtual Disk (Site1)"
   ```

1. El grupo de almacenamiento disponible debe ser "propiedad" del nodo en el que se encuentra. El grupo se puede mover a Server1 mediante:

   ```powershell
   Move-ClusterGroup -Name “Available Storage” -Node Server1
   ```

1. Para crear la asociación de replicación, use el cmdlet `New-SRPartnership`. En este cmdlet también se especifican los nombres del volumen de registro y de datos de origen:

   ```powershell
   New-SRPartnership -SourceComputerName "Server1" -SourceRGName "Replication1" -SourceVolumeName "C:\ClusterStorage\Disk1\" -SourceLogVolumeName "G:" -DestinationComputerName "Server3" -DestinationRGName "Replication2" -DestinationVolumeName "H:" -DestinationLogVolumeName "I:"
   ```

El cmdlet `New-SRPartnership` crea una asociación de replicación entre los dos grupos de replicación para los dos sitios. En este ejemplo, `Replication1` es el grupo de replicación para el nodo principal Server1 en Site1 y `Replication2` es el grupo de replicación para el nodo de destino Server3 en Site2.

Ahora lo configurará todo la réplica de almacenamiento. Si hay datos que replicar, lo hará aquí. En función de la cantidad de datos que necesite replicar, esto puede tardar un poco. Se recomienda no mover ningún grupo hasta que finalice este proceso.

## <a name="next-steps"></a>Pasos siguientes

Para temas relacionados y otras tareas de administración del almacenamiento, vea también:

- [Información general sobre clústeres extendidos](../concepts/stretched-clusters.md)
- [Planeamiento de volúmenes](../concepts/plan-volumes.md)
- [Ampliar volúmenes](extend-volumes.md)
- [Eliminar volúmenes](delete-volumes.md)