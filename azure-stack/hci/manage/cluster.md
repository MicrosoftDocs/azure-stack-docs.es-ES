---
title: Administración de clústeres de Azure Stack HCI mediante Windows Admin Center
description: Aprenda a administrar clústeres de Azure Stack HCI mediante Windows Admin Center.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 07/21/2020
ms.openlocfilehash: d1b8556908da268bbd99c7aa9341128c9dc5be36
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573792"
---
# <a name="manage-azure-stack-hci-clusters-using-windows-admin-center"></a>Administración de clústeres de Azure Stack HCI mediante Windows Admin Center

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

Windows Admin Center se puede utilizar para administrar los clústeres de Azure Stack HCI. Concretamente, utilizará la extensión del administrador de clústeres de Windows Admin Center para administrar los clústeres.

## <a name="view-the-cluster-dashboard"></a>Visualización del panel del clúster

El panel del clúster muestra información sobre el estado y el rendimiento del clúster.

:::image type="content" source="media/manage-cluster/cluster-dashboard.png" alt-text="Pantalla del panel del clúster" lightbox="media/manage-cluster/cluster-dashboard.png":::

Para ver esta información, seleccione el nombre del clúster en **Todas las conexiones** y, en **Herramientas**, situado a la izquierda, seleccione **Panel**. Puede ver lo siguiente:

- Alertas de eventos de clúster
- Lista de los servidores unidos al clúster
- Lista de las máquinas virtuales que se ejecutan en el clúster
- Lista de las unidades de disco disponibles en el clúster
- Lista de los volúmenes disponibles en el clúster
- Uso total de la CPU del clúster
- Uso total de la memoria del clúster
- Uso total del almacenamiento del clúster
- Total de operaciones de entrada/salida del clúster por segundo (IOPS)
- Latencia media del clúster en milisegundos

## <a name="change-cluster-storage-settings"></a>Cambio de la configuración de almacenamiento del clúster

Hay dos opciones de configuración que puede cambiar relacionadas con Espacios de almacenamiento directo que se pueden aplicar al clúster.

1. En Windows Admin Center, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Herramientas**, seleccione **Configuración** en la parte inferior.
1. Para configurar la memoria caché de almacenamiento, seleccione **Espacios de almacenamiento directo** y, a continuación, configure lo siguiente:

   - En **Caché persistente**, seleccione **Habilitada** o **Deshabilitada**.

   - En **Cache mode for HDD** (Modo de caché para HDD), seleccione **Solo lectura**, **Solo escritura** o **Lectura/Escritura**

   - En **Cache mode for SSD** (Mode de caché para SSD), seleccione **Solo lectura**, **Solo escritura** o **Lectura/Escritura**

        :::image type="content" source="media/manage-cluster/cluster-settings-ssd.png" alt-text="pantalla de Espacios de almacenamiento directo del clúster" lightbox="media/manage-cluster/cluster-settings-ssd.png":::

1. Para usar la memoria del servidor para almacenar en caché lecturas frecuentes, seleccione **In-memory cache** (Caché en memoria) y especifique la memoria máxima que se va a usar por servidor. Consulte también [Descripción de la memoria caché en Azure Stack HCI](../concepts/cache.md).

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="pantalla de caché en memoria del clúster" lightbox="media/manage-cluster/cluster-settings-memory.png":::

## <a name="change-cluster-general-settings"></a>Cambio de la configuración general del clúster

Hay cinco valores generales que se pueden aplicar al clúster. Aquí puede establecer y administrar puntos de acceso, el comportamiento de apagado del nodo, el cifrado de tráfico, el equilibrio de carga de máquinas virtuales y el testigo del clúster.

1. En Windows Admin Center, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Herramientas**, seleccione **Configuración**.
1. Para cambiar el nombre del clúster, seleccione **Punto de acceso** y escriba el nuevo nombre.

    :::image type="content" source="media/manage-cluster/cluster-settings-access.png" alt-text="Escenario del clúster extendido activo/activo" lightbox="media/manage-cluster/cluster-settings-access.png":::

1. Para controlar el comportamiento de cierre del nodo, seleccione **Node shutdown behavior** (Comportamiento de apagado del nodo) y asegúrese de que la casilla está habilitada. Esto mueve todas las máquinas virtuales del nodo primero para permitir el cierre correcto del nodo.

    :::image type="content" source="media/manage-cluster/cluster-settings-shutdown.png" alt-text="pantalla de comportamiento de apagado del nodo del clúster" lightbox="media/manage-cluster/cluster-settings-shutdown.png":::

1. Para cifrar las conexiones SMB usadas para enviar datos entre los nodos del clúster, seleccione **Cluster traffic encryption** (Cifrado del tráfico del clúster) y, a continuación, seleccione **Cifrar** en los cuadros desplegables para lo siguiente:

   - **Tráfico principal**: cifra el tráfico enviado a través de NetFT (adaptador virtual de clúster) en el puerto 3343

   - **Tráfico de servidor**: cifra el volumen compartido de clúster (CSV) y el tráfico de la capa de bus de almacenamiento (SBL)

        :::image type="content" source="media/manage-cluster/cluster-settings-encryption.png" alt-text="pantalla de cifrado del tráfico del clúster" lightbox="media/manage-cluster/cluster-settings-encryption.png":::

1. Para equilibrar la carga de las máquinas virtuales del clúster de forma automática, seleccione **Virtual machine load balancing** (Equilibrio de carga de la máquina virtual) y haga lo siguiente:

   - En **Balance virtual machines** (Equilibrar máquinas virtuales), seleccione la acción adecuada.
   - En **Aggressiveness** (Intensidad), seleccione el comportamiento adecuado.

     Para más información sobre cómo funciona esto, consulte [Introducción al equilibrio de carga de las máquinas virtuales](/windows-server/failover-clustering/vm-load-balancing-overview).

        :::image type="content" source="media/manage-cluster/cluster-settings-vm-load.png" alt-text="pantalla de equilibrio de carga de la máquina virtual del clúster" lightbox="media/manage-cluster/cluster-settings-vm-load.png":::

1. Para seleccionar un tipo de testigo de cuórum, seleccione **Testigo** y, después, seleccione una de las opciones siguientes:

   - **Testigo de la nube**: para usar un recurso de la nube de Azure como testigo
   - **Testigo de disco**: para usar un recurso de disco como testigo (no se usa para clústeres extendidos).
   - **Testigo del recurso compartido de archivos**: para usar un recurso compartido de archivos como testigo

        Para obtener más información, consulte [Descripción del cuórum de clúster y de grupo en Azure Stack HCl](../concepts/quorum.md).

        :::image type="content" source="media/manage-cluster/cluster-settings-witness.png" alt-text="pantalla de testigo del clúster" lightbox="media/manage-cluster/cluster-settings-witness.png":::

## <a name="change-cluster-hyper-v-settings"></a>Cambio de la configuración de Hyper-V del clúster

Hay cinco valores del host de Hyper-V que se pueden aplicar al clúster.

1. En Windows Admin Center, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Herramientas**, seleccione **Configuración**.
1. Seleccione **General** y, a continuación, use los siguientes valores:

   - **Ruta de acceso de discos duros virtuales**: especifique la carpeta predeterminada para almacenar los archivos del disco duro virtual.

   - **Ruta de acceso de las máquinas virtuales**: especifique la carpeta predeterminada para almacenar los archivos de configuración de máquina virtual.

   - **Tipo de programador del hipervisor**: seleccione **Core Scheduler** (Programador principal) o **Classic Scheduler** (Programador clásico). Esto determina cómo programa el hipervisor los procesos virtuales para que se ejecuten en los procesadores físicos que usan la tecnología Simultaneous multi-threading (también conocida como SMT o hyper-threading).

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="pantalla de configuración general de Hyper-V del clúster" lightbox="media/manage-cluster/cluster-settings-hyperv.png":::

1. Para permitir la redirección de dispositivos y recursos locales desde máquinas virtuales, seleccione **Modo de sesión mejorada**. Tenga en cuenta que las conexiones del modo de sesión mejorada requieren un sistema operativo invitado compatible.

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="pantalla Modo de sesión mejorada de Hyper-V del clúster" lightbox="media/manage-cluster/cluster-settings-session.png":::

1. Para permitir que las máquinas virtuales expandan los nodos NUMA físicos, seleccione **Expansión de NUMA**. La expansión de la arquitectura de memoria no uniforme (NUMA) puede proporcionar una máquina virtual con más memoria de la que está disponible en un solo nodo NUMA.

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="pantalla Expansión de NUMA del clúster" lightbox="media/manage-cluster/cluster-settings-numa.png":::

1. Para especificar el número de máquinas virtuales que pueden moverse simultáneamente durante la ejecución (migración en vivo), seleccione **Migración en vivo**, seleccione un número y, a continuación, especifique lo siguiente:

   - En **Protocolo de autenticación**, seleccione **CredSSP** o **Kerberos**.

   - En **Opción de rendimiento**, seleccione **Compresión** o **SMB**. Los datos comprimidos se envían mediante una conexión TCP/IP.

   - Seleccione la casilla **Use any network** (Usar cualquier red) para usar cualquier red disponible de un nodo para realizar la migración.

        :::image type="content" source="media/manage-cluster/cluster-settings-liv-migration.png" alt-text="pantalla Migración en vivo del clúster" lightbox="media/manage-cluster/cluster-settings-liv-migration.png":::

1. Para especificar el número de migraciones de almacenamiento que se pueden realizar al mismo tiempo, seleccione **Storage Migration** (Migración de almacenamiento) y, a continuación, seleccione un número.

    :::image type="content" source="media/manage-cluster/cluster-settings-sto-migration.png" alt-text="pantalla de migración de almacenamiento del clúster" lightbox="media/manage-cluster/cluster-settings-sto-migration.png":::

## <a name="next-steps"></a>Pasos siguientes

- Para supervisar el clúster, consulte [Supervisión de Azure Stack HCI con Azure Monitor](azure-monitor.md).
