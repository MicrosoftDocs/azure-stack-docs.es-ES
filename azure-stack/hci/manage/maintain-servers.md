---
title: Desconexión de un servidor de Azure Stack HCI por mantenimiento
description: En este tema se proporcionan instrucciones sobre cómo pausar, purgar y reanudar correctamente los servidores que ejecutan el sistema operativo de Azure Stack HCI mediante Windows Admin Center y PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 10/23/2020
ms.openlocfilehash: 3b2c462ad8e6db96be5968074ce5478f1ca9c870
ms.sourcegitcommit: 6a51687a98c417a004cd4295ad06ae813e1978cc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/27/2020
ms.locfileid: "92641047"
---
# <a name="taking-an-azure-stack-hci-server-offline-for-maintenance"></a>Desconexión de un servidor de Azure Stack HCI por mantenimiento

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

Con Azure Stack HCl, para desconectar un servidor para su mantenimiento es necesario desconectar partes del almacenamiento que se comparten entre todos los servidores del clúster. Para ello, es necesario pausar el servidor que quiere desconectar, mover roles y máquinas virtuales (VM) a otros servidores del clúster, y comprobar que todos los datos están disponibles en los demás servidores del clúster. Este proceso garantiza que los datos permanezcan seguros y accesibles durante todo el período de mantenimiento.

Puede usar Windows Admin Center o PowerShell para desconectar un servidor para el mantenimiento. En este tema se explican ambos métodos.

   > [!IMPORTANT]
   > En este tema se da por supuesto que necesita apagar un servidor físico para realizar el mantenimiento o reiniciarlo por algún otro motivo. Para instalar las actualizaciones en un clúster de Azure Stack HCI, consulte [Actualización de clústeres de Azure Stack HCI](update-cluster.md), donde se explica cómo usar la Actualización compatible con clústeres (CAU) para realizar automáticamente todos los pasos de este tema y, al mismo tiempo actualizar los servidores y reiniciarlos si es necesario.

## <a name="take-a-server-offline-using-windows-admin-center"></a>Desconexión de un servidor mediante Windows Admin Center

La manera más sencilla de prepararse para desconectar un servidor en un clúster de Azure Stack HCI es mediante Windows Admin Center.

### <a name="verify-its-safe-to-take-the-server-offline"></a>Comprobación de la seguridad de la desconexión del servidor

1. Utilice Windows Admin Center para conectarse al servidor que desea desconectar. Seleccione **Storage > Disks** (Almacenamiento > Discos) en el menú **Tools** (Herramientas) y compruebe que la columna **Status** (Estado) de cada disco virtual muestra **Online** (En línea).

2. A continuación, seleccione **Storage > Volumes** (Almacenamiento > Volúmenes) y compruebe que la columna **Health** (Estado) de cada volumen muestra **Healthy** (Correcto) y que la columna **Status** (Estado) de cada volumen muestra **OK** (Correcto).

### <a name="pause-and-drain-the-server"></a>Pausa y purga del servidor

Antes de apagar o reiniciar un servidor, debe pausar el servidor y purgar (quitar) cualquier rol en clúster, como las VM que se ejecutan en él, para asegurarse de que el apagado del servidor no afecte al estado de la aplicación. Pause y purgue siempre los servidores en clúster antes de dejarlos sin conexión para el mantenimiento.

1. Mediante Windows Admin Center, conéctese al clúster y, a continuación, seleccione **Compute > Nodes** (Proceso > Nodos) en el menú **Tools** (Herramientas) del administrador de clústeres.

2. Haga clic en el nombre del servidor que desea pausar y purgar y, a continuación, seleccione **Pause** (Pausar). Debería ver el mensaje siguiente:

   *If you pause this node, all clustered roles move to other nodes and no roles can be added to this node until it's resumed. Are you sure you want to pause cluster node?* (Si pausa este nodo, todos los roles en clúster se moverán a otros nodos y no se podrán agregar roles a este nodo hasta que se reanude. ¿Está seguro de que desea pausar el nodo de clúster?)

3. Seleccione **yes** (sí) para pausar el servidor e iniciar el proceso de purga. El estado del nodo de clúster se mostrará como **Draining** (Purgando) y los roles como Hyper-V y las VM iniciarán inmediatamente la migración en vivo a otros servidores del clúster. Esta operación puede tardar unos minutos.

   > [!NOTE]
   > Cuando pausa y purga el servidor correctamente, Azure Stack HCI realiza una comprobación de seguridad automática para asegurarse de que es seguro continuar. Si hay volúmenes en mal estado, se detendrá y le avisará de que no es seguro continuar.

### <a name="shut-down-the-server"></a>Apagado del servidor

Una vez que el servidor haya finalizado la purga, el estado se mostrará como **Paused** (En pausa) en Windows Admin Center. Ahora puede apagar el servidor de forma segura para el mantenimiento o reiniciarlo.

### <a name="resume-the-server"></a>Reanudación del servidor

Cuando esté listo para que el servidor empiece a hospedar de nuevo los roles en clúster y las VM, solo tiene que activar el servidor, esperar a que arranque y reanudar el servidor con los pasos siguientes.

1. En el administrador de clústeres, seleccione **Compute > Nodes** (Proceso > Nodos) en el menú **Tools** (Herramientas) de la izquierda.

2. Seleccione el nombre del servidor que desea reanudar y, a continuación, haga clic en **Resume** (Reanudar). Debería ver el mensaje siguiente:

   *Are you sure you want to resume cluster node?* (¿Seguro que quiere reanudar el nodo de clúster?)

3. En la mayoría de los casos, debería activar la casilla que indica *Transfer clustered roles back to this node* (Volver a transferir roles en clúster a este nodo). Seleccione **yes** (sí) para reanudar el servidor.

Si activó la casilla del paso 3 anterior, los roles en clúster y las VM iniciarán de inmediato la migración en vivo al servidor. Esta operación puede tardar unos minutos.

### <a name="wait-for-storage-to-resync"></a>Espera a la resincronización del almacenamiento

Cuando el servidor se reanuda, las nuevas escrituras que se hayan producido mientras no estaba disponible deben volver a sincronizarse. Esto sucede automáticamente mediante el seguimiento de cambios inteligente. No es necesario que se examinen o sincronicen *todos* los datos, sino solo los cambios. Este proceso se limita para mitigar el impacto en las cargas de trabajo de producción. Dependiendo de cuánto tiempo se haya pausado el servidor y de la cantidad de datos nuevos que se hayan escrito, es posible que tarde varios minutos en completarse.

   > [!IMPORTANT]
   > Debe esperar a que la resincronización se complete antes de desconectar otros servidores del clúster.

Para comprobar si la resincronización se ha completado, conéctese al servidor mediante Windows Admin Center y seleccione **Storage > Volumes** (Almacenamiento > Volúmenes) en el menú **Tools** (Herramientas) de la izquierda y, a continuación, seleccione **Volumes** (Volúmenes) cerca de la parte superior de la página. Si la columna **Health** (Estado) de cada volumen muestra **Healthy** (Correcto) y la columna **Status** (Estado) de cada volumen muestra **OK** (Correcto), la resincronización se ha completado y ahora es seguro desconectar otros servidores del clúster.

## <a name="take-a-server-offline-using-powershell"></a>Desconexión de un servidor mediante PowerShell

Use los procedimientos siguientes para pausar, purgar y reanudar correctamente un servidor en un clúster de Azure Stack HCI mediante PowerShell.

### <a name="verify-its-safe-to-take-the-server-offline"></a>Comprobación de la seguridad de la desconexión del servidor

Para comprobar que el estado de todos los volúmenes es correcto, ejecute el siguiente cmdlet como administrador:

```PowerShell
Get-VirtualDisk
```

El ejemplo siguiente muestra el aspecto que podría tener la salida:

```
FriendlyName              ResiliencySettingName FaultDomainRedundancy OperationalStatus HealthStatus    Size FootprintOnPool StorageEfficiency
------------              --------------------- --------------------- ----------------- ------------    ---- --------------- -----------------
Mirror II                 Mirror                1                     OK                Healthy         4 TB         8.01 TB            49.99%
Mirror-accelerated parity                                             OK                Healthy      1002 GB         1.96 TB            49.98%
Mirror                    Mirror                1                     OK                Healthy         1 TB            2 TB            49.98%
ClusterPerformanceHistory Mirror                1                     OK                Healthy        24 GB           49 GB            48.98%
```

Compruebe que el valor de la propiedad **HealthStatus** de cada volumen sea **Healthy** (Correcto) y que el valor de **OperationalStatus** sea OK (Correcto).

### <a name="pause-and-drain-the-server"></a>Pausa y purga del servidor

Ejecute el siguiente cmdlet como administrador para pausar y pugar el servidor:

```PowerShell
Suspend-ClusterNode -Drain
```

### <a name="shut-down-the-server"></a>Apagado del servidor

Una vez que el servidor haya finalizado la purga, mostrará el estado **En pausa** en PowerShell.

Ahora puede apagar el servidor de forma segura o reiniciarlo con los cmdlets `Stop-Computer` o `Restart-Computer` de PowerShell.

   > [!NOTE]
   > Al ejecutar un comando `Get-VirtualDisk` en servidores que están cerrando o iniciando/deteniendo el servicio de clúster, el estado operativo del servidor puede aparecer como incompleto o degradado, y la columna Estado de mantenimiento puede mostrar una advertencia. Esto es normal y no debe causar ningún problema. Todos los volúmenes permanecen en línea y accesibles.

### <a name="resume-the-server"></a>Reanudación del servidor

Ejecute el siguiente cmdlet como administrador para reanudar el servidor en el clúster. Para devolver los roles en clúster y las VM que se estaban ejecutando previamente en el servidor, use la marca **-Failback** opcional:

```PowerShell
Resume-ClusterNode –Failback Immediate
```

Una vez que se haya reanudado el servidor, se mostrará como **Up** (Activo) en PowerShell.

### <a name="wait-for-storage-to-resync"></a>Espera a la resincronización del almacenamiento

Cuando se reanude el servidor, debe esperar a que se complete la resincronización antes de desconectar ningún otro servidor del clúster.

Ejecute el siguiente cmdlet como administrador para supervisar el progreso:

```PowerShell
Get-StorageJob
```

Si la resincronización ya se completó, no obtendrá ninguna salida.

Esta es una salida de ejemplo que muestra trabajos de resincronización (reparación) que todavía se están ejecutando:

```
Name   IsBackgroundTask ElapsedTime JobState  PercentComplete BytesProcessed BytesTotal
----   ---------------- ----------- --------  --------------- -------------- ----------
Repair True             00:06:23    Running   65              11477975040    17448304640
Repair True             00:06:40    Running   66              15987900416    23890755584
Repair True             00:06:52    Running   68              20104802841    22104819713
```

La columna **BytesTotal** muestra la cantidad de almacenamiento necesario para la resincronización. La columna **PercentComplete** muestra el progreso.

   > [!WARNING]
   > No es seguro desconectar otro servidor hasta que finalicen estos trabajos de reparación.

Durante este tiempo, en **HealthStatus** , los volúmenes se seguirán mostrando como **Warning** , que es normal.

Por ejemplo, si usa el cmdlet `Get-VirtualDisk` mientras se vuelve a sincronizar el almacenamiento, es posible que vea la siguiente salida:

```
FriendlyName ResiliencySettingName OperationalStatus HealthStatus IsManualAttach Size
------------ --------------------- ----------------- ------------ -------------- ----
MyVolume1    Mirror                InService         Warning      True           1 TB
MyVolume2    Mirror                InService         Warning      True           1 TB
MyVolume3    Mirror                InService         Warning      True           1 TB
```

Una vez finalizados los trabajos, compruebe que los volúmenes muestran **Healthy** de nuevo mediante el cmdlet `Get-VirtualDisk`. A continuación se muestra una salida de ejemplo:

```
FriendlyName ResiliencySettingName OperationalStatus HealthStatus IsManualAttach Size
------------ --------------------- ----------------- ------------ -------------- ----
MyVolume1    Mirror                OK                Healthy      True           1 TB
MyVolume2    Mirror                OK                Healthy      True           1 TB
MyVolume3    Mirror                OK                Healthy      True           1 TB
```

Ahora es seguro pausar y reiniciar otros servidores del clúster.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Introducción a Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/storage-spaces-direct-overview)
- [Actualización de clústeres de Azure Stack HCl](update-cluster.md)