---
title: Administración de clústeres de Azure Stack HCI mediante Windows Admin Center
description: Aprenda a administrar clústeres de Azure Stack HCI mediante Windows Admin Center.
ms.topic: how-to
author: v-dasis
ms.author: v-dasis
ms.reviewer: jgerend
ms.date: 01/22/2021
ms.openlocfilehash: 4b6e7537a1111ed2e38d1783acf7197a4249b2ac
ms.sourcegitcommit: e772df8ac78c86d834a68d1a8be83b7f738019b7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/26/2021
ms.locfileid: "98772105"
---
# <a name="manage-azure-stack-hci-clusters-using-windows-admin-center"></a>Administración de clústeres de Azure Stack HCI mediante Windows Admin Center

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

Windows Admin Center se puede utilizar para administrar los clústeres de Azure Stack HCI. Concretamente, utilizará la característica Administrador de clústeres de Windows Admin Center para administrar los clústeres.

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

## <a name="change-storage-settings"></a>Cambio de la configuración d almacenamiento:

Puede seleccionar usar la memoria del servidor para almacenar en caché las lecturas frecuentes y especificar la memoria máxima que se usará por servidor. Para más información, consulte [Descripción de la caché en Azure Stack HCI](../concepts/cache.md).

1. En Windows Admin Center, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Herramientas**, seleccione **Configuración** en la parte inferior.
1. Seleccione **Caché en memoria** y escriba el nombre nuevo.

    :::image type="content" source="media/manage-cluster/cluster-settings-memory.png" alt-text="pantalla de caché en memoria del clúster" lightbox="media/manage-cluster/cluster-settings-memory.png":::

1. Puede cambiar el nombre del bloque de almacenamiento que Espacios de almacenamiento directo usa. Seleccione **Bloques de almacenamiento** y escriba el nuevo nombre. Esto es aplicable a los clústeres extendidos.

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-pools.png" alt-text="Pantalla de bloque de almacenamiento de clúster" lightbox="media/manage-cluster/cluster-settings-storage-pools.png":::

1. Puede cambiar la configuración de Espacios de almacenamiento directo. Seleccione **Espacios de almacenamiento directo** y cambie las siguientes opciones de configuración según sea necesario:

    - **Persistent cache** (Caché persistente): habilite o deshabilite la caché persistente.
    - **Cache mode for HDD** (Modo de caché para HDD): cambie el modo de caché para las unidades HDD.
    - **Modo de caché para HDD** (Modo de caché para SSD): cambie la memoria caché de las unidades SSD.

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-spaces-direct.png" alt-text="pantalla de Espacios de almacenamiento directo del clúster" lightbox="media/manage-cluster/cluster-settings-storage-spaces-direct.png":::

## <a name="change-cluster-settings"></a>Cambio de la configuración del clúster

Hay varias opciones de configuración general que se pueden aplicar al clúster. Aquí puede establecer y administrar puntos de acceso, el comportamiento de apagado del nodo, el cifrado de tráfico, el equilibrio de carga de máquinas virtuales y el testigo del clúster.

1. En Windows Admin Center, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Herramientas**, seleccione **Configuración**.
1. Para cambiar el nombre del clúster, seleccione **Punto de acceso** y escriba el nuevo nombre.

    :::image type="content" source="media/manage-cluster/cluster-settings-access.png" alt-text="Escenario del clúster extendido activo/activo" lightbox="media/manage-cluster/cluster-settings-access.png":::

1. Para controlar el comportamiento de cierre del nodo, seleccione **Node shutdown behavior** (Comportamiento de apagado del nodo) y asegúrese de que la casilla está habilitada. Esto mueve todas las máquinas virtuales del nodo primero para permitir el cierre correcto del nodo.

    :::image type="content" source="media/manage-cluster/cluster-settings-shutdown.png" alt-text="pantalla de comportamiento de apagado del nodo del clúster" lightbox="media/manage-cluster/cluster-settings-shutdown.png":::

1. Para cifrar las conexiones SMB usadas para enviar datos entre los nodos del clúster, seleccione **Cluster traffic encryption** (Cifrado del tráfico del clúster) y, a continuación, seleccione **Cifrar** en los cuadros desplegables para lo siguiente:

   - **Tráfico principal**: cifra el tráfico enviado a través de NetFT (adaptador virtual de clúster) en el puerto 3343

   - **Storage traffic** (Tráfico de almacenamiento): cifra el tráfico del volumen compartido de clúster (CSV) y la capa de bus de almacenamiento (SBL).

        :::image type="content" source="media/manage-cluster/cluster-settings-encryption.png" alt-text="pantalla de cifrado del tráfico del clúster" lightbox="media/manage-cluster/cluster-settings-encryption.png":::

1. Para equilibrar la carga de las máquinas virtuales del clúster de forma automática, seleccione **Virtual machine load balancing** (Equilibrio de carga de la máquina virtual) y haga lo siguiente:

   - En **Balance virtual machines** (Equilibrar máquinas virtuales), seleccione la acción adecuada.
   - En **Aggressiveness** (Intensidad), seleccione el comportamiento adecuado.

     Para más información sobre cómo funciona esto, consulte [Introducción al equilibrio de carga de las máquinas virtuales](/windows-server/failover-clustering/vm-load-balancing-overview).

        :::image type="content" source="media/manage-cluster/cluster-settings-vm-load.png" alt-text="pantalla de equilibrio de carga de la máquina virtual del clúster" lightbox="media/manage-cluster/cluster-settings-vm-load.png":::

1. Para seleccionar un tipo de testigo de cuórum, seleccione **Testigo** y, a continuación, en **Witness type** (Tipo de testigo), seleccione una de las opciones siguientes:

   - **Testigo de la nube**: para usar un recurso de la nube de Azure como testigo
   - **Testigo de disco**: para usar un recurso de disco como testigo (no se usa para clústeres extendidos).
   - **Testigo del recurso compartido de archivos**: para usar un recurso compartido de archivos como testigo

        Para obtener información detallada sobre cómo configurar un testigo, consulte [Configuración de un testigo del clúster](witness.md). Consulte también [Descripción del cuórum de clúster y de grupo en Azure Stack HCl](../concepts/quorum.md).

        :::image type="content" source="media/manage-cluster/cluster-settings-witness.png" alt-text="pantalla de testigo del clúster" lightbox="media/manage-cluster/cluster-settings-witness.png":::

1. Para usar las reglas de afinidad para controlar la selección de ubicación de máquinas virtuales en servidores y sitios de host, seleccione **Reglas de afinidad** y, a continuación, haga clic en **Crear regla**. Para obtener información detallada sobre la configuración de reglas, consulte [Creación de reglas de afinidad de sitio y servidor para máquinas virtuales](vm-affinity.md).

    :::image type="content" source="media/manage-cluster/affinity-rules.png" alt-text="Pantalla de reglas de afinidad de clúster" lightbox="media/manage-cluster/affinity-rules.png":::

1. Para seleccionar la cantidad de datos que se enviarán a Microsoft para el diagnóstico, seleccione **Datos de diagnóstico** y, a continuación, una de las siguientes opciones:

    - **Diagnostic data off (Security)** (Datos de diagnóstico desactivados [seguridad]): no se envían datos.
    - **Required (Basic)** (Requeridos [básicos]): datos mínimos enviados para mantener las cosas seguras y actualizadas.
    - **Optional (Full)** (Opcional [completos]): se envían todos los datos aplicables.

    :::image type="content" source="media/manage-cluster/cluster-diagnostic-data.png" alt-text="Pantalla de diagnóstico de datos del clúster" lightbox="media/manage-cluster/cluster-diagnostic-data.png":::

## <a name="change-hyper-v-settings"></a>Cambio de la configuración de Hyper-V

Existen varios valores del host de Hyper-V que se pueden aplicar al clúster.

1. En Windows Admin Center, seleccione **Administrador de clústeres** en la flecha desplegable de la parte superior.
1. En **Herramientas**, seleccione **Configuración**.
1. Seleccione **General** y, a continuación, use los siguientes valores:

   - **Ruta de acceso de discos duros virtuales**: especifique la carpeta predeterminada para almacenar los archivos del disco duro virtual.

   - **Ruta de acceso de las máquinas virtuales**: especifique la carpeta predeterminada para almacenar los archivos de configuración de máquina virtual.

        :::image type="content" source="media/manage-cluster/cluster-settings-hyperv.png" alt-text="pantalla de configuración general de Hyper-V del clúster" lightbox="media/manage-cluster/cluster-settings-hyperv.png":::

1. Para permitir la redirección de dispositivos y recursos locales desde máquinas virtuales, seleccione **Modo de sesión mejorada**. Tenga en cuenta que las conexiones del modo de sesión mejorada requieren un sistema operativo invitado compatible.

    :::image type="content" source="media/manage-cluster/cluster-settings-session.png" alt-text="pantalla Modo de sesión mejorada de Hyper-V del clúster" lightbox="media/manage-cluster/cluster-settings-session.png":::

1. Para permitir que las máquinas virtuales expandan los nodos NUMA físicos, seleccione **Expansión de NUMA**. La expansión de la arquitectura de memoria no uniforme (NUMA) puede proporcionar una máquina virtual con más memoria de la que está disponible en un solo nodo NUMA.

    :::image type="content" source="media/manage-cluster/cluster-settings-numa.png" alt-text="pantalla Expansión de NUMA del clúster" lightbox="media/manage-cluster/cluster-settings-numa.png":::

1. Para especificar el número de máquinas virtuales que pueden moverse simultáneamente durante la ejecución (migración en vivo), seleccione **Migración en vivo**, seleccione un número y, a continuación, especifique lo siguiente:

   - En **Protocolo de autenticación**, seleccione **CredSSP** o **Kerberos**.

   - En **Opciones de rendimiento**, seleccione **Compresión** o **SMB**. Los datos comprimidos se envían mediante una conexión TCP/IP.

   - Seleccione la casilla **Use any network** (Usar cualquier red) para usar cualquier red disponible de un nodo para realizar la migración.

        :::image type="content" source="media/manage-cluster/cluster-settings-live-migration.png" alt-text="pantalla Migración en vivo del clúster" lightbox="media/manage-cluster/cluster-settings-live-migration.png":::

1. Para especificar el número de migraciones de almacenamiento que se pueden realizar al mismo tiempo, seleccione **Storage Migration** (Migración de almacenamiento) y, a continuación, seleccione un número.

    :::image type="content" source="media/manage-cluster/cluster-settings-storage-migration.png" alt-text="pantalla de migración de almacenamiento del clúster" lightbox="media/manage-cluster/cluster-settings-storage-migration.png":::

## <a name="register-the-cluster-with-azure"></a>Registro del clúster con Azure.

Para registrar o anular el registro del clúster con Azure, seleccione **Azure Stack HCI registration** (Registro de Azure Stack HCI). Para obtener información detallada sobre cómo hacerlo, consulte [Conexión de Azure Stack HCI a Azure](../deploy/register-with-azure.md).

:::image type="content" source="media/manage-cluster/cluster-registration.png" alt-text="Pantalla de registro de Azure del clúster" lightbox="media/manage-cluster/cluster-registration.png":::

## <a name="next-steps"></a>Pasos siguientes

Para supervisar el clúster, consulte [Supervisión de Azure Stack HCI con Azure Monitor](azure-monitor.md).