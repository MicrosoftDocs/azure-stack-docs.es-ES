---
title: Administración de máquinas virtuales con Windows Admin Center
description: Aprenda a crear y administrar máquinas virtuales en un clúster en Azure Stack HCl mediante Windows Admin Center.
author: v-dasis
ms.topic: how-to
ms.date: 11/06/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: 73d705bf5b36509b3aed31afb09105f2da91862f
ms.sourcegitcommit: 08ef9545316798c9a21c2f9bc1da8c15cb648982
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/07/2020
ms.locfileid: "94360356"
---
# <a name="manage-vms-with-windows-admin-center"></a>Administración de máquinas virtuales con Windows Admin Center

> Se aplica a Azure Stack HCI, versión 20H2; Windows Server 2019

Windows Admin Center se puede usar para crear y administrar máquinas virtuales en Azure Stack HCI.

## <a name="create-a-new-vm"></a>Creación de una máquina virtual nueva

Puede crear fácilmente una nueva máquina virtual mediante Windows Admin Center.

:::image type="content" source="media/manage-vm/new-vm.png" alt-text="Pantalla de nueva máquina virtual" lightbox="media/manage-vm/new-vm.png":::

1. En la página principal de Windows Admin Center, en **Todas las conexiones**, seleccione el servidor o clúster en el que desea crear la máquina virtual.
1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. En **Máquinas virtuales**, seleccione la pestaña **Inventario** y, a continuación, seleccione **Añadir** y **Nuevo**.
1. En **Nueva máquina virtual**, escriba un nombre para la máquina virtual.
1. Seleccione **Generación 2 (recomendado)** .
1. En **Host**, seleccione el servidor en el que desea que resida la máquina virtual.
1. En **Ruta de acceso**, seleccione una ruta de acceso de archivo preasignada en la lista desplegable o haga clic en **Examinar** para elegir la carpeta en la que desea guardar la configuración de la máquina virtual y los archivos del disco duro virtual (VHD). Para buscar cualquier recurso compartido de SMB disponible en la red, escriba la ruta de acceso como *\\servidor\recurso compartido*.

1. En **Procesadores virtuales**, seleccione el número de procesadores virtuales y si desea habilitar la virtualización anidada para la máquina virtual.
1. En **Memoria**, seleccione la cantidad de memoria de inicio (se recomienda un mínimo de 4 GB) y un intervalo mínimo y máximo de memoria dinámica, según corresponda, para asignarla a la máquina virtual.
1. En **Red**, seleccione un adaptador de red en la lista desplegable.
1. En **Almacenamiento**, haga clic en **Agregar** y seleccione si desea crear un nuevo disco duro virtual o usar uno ya existente. Si va a utilizar uno ya existente, haga clic en **Examinar** y seleccione la ruta de acceso del archivo correspondiente.  
1. En **Sistema operativo**, realice una de las acciones siguientes:
   - Seleccione **Instalar un sistema operativo más adelante** si desea instalar un sistema operativo para la máquina virtual después de crearla.
   - Seleccione **Install an operating system from an image file (*.iso)** (Instalar un sistema operativo a partir de un archivo de imagen (*.iso), haga clic en **Examinar** y, a continuación, seleccione el archivo de imagen .iso correspondiente que se va a usar.
1. Cuando haya finalizado, haga clic en **Crear** para crear la máquina virtual.
1. Para iniciar la máquina virtual, en la lista de **Máquinas virtuales**, mantenga el mouse sobre la nueva máquina virtual, active la casilla correspondiente a la izquierda y seleccione **Iniciar**.
1. En **Estado**, compruebe que el estado de la máquina virtual es **En ejecución**.

## <a name="get-a-list-of-vms"></a>Obtención de una lista de máquinas virtuales

Puede ver fácilmente todas las máquinas virtuales de un servidor o del clúster.

:::image type="content" source="media/manage-vm/vm-inventory.png" alt-text="Pantalla Máquinas virtuales" lightbox="media/manage-vm/vm-inventory.png":::

1. En Windows Admin Center, en **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. En la pestaña **Inventario** situada a la derecha se enumeran todas las máquinas virtuales disponibles en el servidor actual o en el clúster, y se proporcionan comandos para administrar máquinas virtuales individuales. Puede:
    - Ver una lista de las máquinas virtuales en ejecución en el servidor o clúster actual.
    - Ver el estado de la máquina virtual y el servidor host si está viendo las máquinas virtuales de un clúster. Puede ver también el uso de la CPU y la memoria desde la perspectiva del host, incluida la presión de memoria, la demanda de memoria y la memoria asignada, y el tiempo de actividad de la máquina virtual, el estado del latido y el estado de protección (mediante Azure Site Recovery).
    - Cree una máquina virtual.
    - Eliminar, iniciar, desactivar, apagar, pausar, reanudar, restablecer o cambiar el nombre de una máquina virtual. También puede guardar la máquina virtual, eliminar un estado guardado o crear un punto de control.
    - Cambiar la configuración de una máquina virtual.
    - Conectarse a una consola de máquina virtual mediante el host de Hyper-V.
    - Replicar una máquina virtual con Azure Site Recovery.
    - En el caso de operaciones que se pueden ejecutar en varias máquinas virtuales como iniciar, apagar, guardar, pausar, eliminar o restablecer, puede seleccionar varias máquinas virtuales y ejecutar la operación una vez.

## <a name="view-vm-details"></a>Visualización de los detalles de la máquina virtual

Puede ver información detallada y los gráficos de rendimiento de una máquina virtual específica desde su página dedicada.

:::image type="content" source="media/manage-vm/vm-details.png" alt-text="Pantalla de información detallada de las máquinas virtuales" lightbox="media/manage-vm/vm-details.png":::

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. Haga clic en la pestaña **Inventario** de la derecha y, a continuación, seleccione la máquina virtual. En la siguiente página, puede hacer esto:

   - Ver gráficos de líneas de datos actuales y pasados de CPU, memoria, red, IOPS y rendimiento de E/S (los datos históricos solo están disponibles para los clústeres hiperconvergidos)
   - Ver, crear, aplicar, cambiar el nombre y eliminar puntos de control.
   - Ver los detalles de archivos de discos duros virtuales (.vhd), adaptadores de red y servidores host.
   - Ver el estado de la máquina virtual.
   - Guardar la máquina virtual, eliminar un estado guardado, exportar o clonar la máquina virtual.
   - Cambiar la configuración de la máquina virtual.
   - Conectarse a la consola de la máquina virtual mediante VMConnect a través del host de Hyper-V.
   - Replicar la máquina virtual con Azure Site Recovery.

## <a name="view-aggregate-vm-metrics"></a>Visualización de métricas de máquina virtual agregadas

Puede ver las métricas de rendimiento y uso de los recursos de todas las máquinas virtuales del clúster.

:::image type="content" source="media/manage-vm/host-metrics.png" alt-text="Pantalla de métricas del host" lightbox="media/manage-vm/host-metrics.png":::

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. En la pestaña **Resumen** de la derecha se proporciona una vista global de los recursos del host de Hyper-V y el rendimiento de un servidor o clúster seleccionado, incluido lo siguiente:
    - El número de máquinas virtuales que se ejecutan, detienen, pausan y guardan
    - Alertas de estado recientes o eventos del registro de eventos de Hyper-V para clústeres
    - Uso de la CPU y la memoria con una comparación entre el host y el invitado
    - Gráficos de líneas de datos actuales e históricos de IOPS y de rendimiento de E/S para clústeres

## <a name="change-vm-settings"></a>Cambio de la configuración de una máquina virtual

Hay varios valores de configuración que puede cambiar para una máquina virtual.

> [!NOTE]
> Algunos de ellos no se pueden cambiar en una máquina virtual en ejecución y, por lo tanto, primero deberá detener la máquina virtual.

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. Haga clic en la pestaña **Inventario** de la derecha, seleccione la máquina virtual y, a continuación, haga clic en **Configuración**.

1. Para cambiar las acciones de inicio y detención de la máquina virtual y la configuración general, seleccione **General** y haga lo siguiente:
    - Para cambiar el nombre de la máquina virtual, escríbalo en el campo **Nombre**.
    - Para cambiar las acciones predeterminadas de inicio y detención de la máquina virtual, seleccione la configuración adecuada en los cuadros desplegables.
    - Para cambiar los intervalos de tiempo para pausar o iniciar una máquina virtual, escriba los valores adecuados en los campos que se indican.

        :::image type="content" source="media/manage-vm/vm-settings-general.png" alt-text="Pantalla de configuración general de una máquina virtual" lightbox="media/manage-vm/vm-settings-general.png":::

1. Seleccione **Memoria** para cambiar la memoria de inicio de la máquina virtual, el intervalo de memoria dinámica, el porcentaje de búfer de memoria y el peso de la memoria.

    :::image type="content" source="media/manage-vm/vm-settings-memory.png" alt-text="Pantalla de cambio de la configuración de memoria de una máquina virtual" lightbox="media/manage-vm/vm-settings-memory.png":::

1. Seleccione **Procesadores** para cambiar el número de procesadores virtuales, habilitar la virtualización anidada o habilitar multithreading simultáneo (SMT).

    :::image type="content" source="media/manage-vm/vm-settings-processor.png" alt-text="Pantalla de cambio de la configuración del procesador de una máquina virtual" lightbox="media/manage-vm/vm-settings-processor.png":::

1. Para cambiar el tamaño de un disco existente, modifique el valor de **Tamaño (GB)** . Para agregar un nuevo disco virtual, seleccione **Discos** y, a continuación, seleccione si desea crear un disco virtual vacío o usar un disco duro existente o un archivo de imagen ISO (.iso). Haga clic en **Examinar** y seleccione la ruta de acceso al disco virtual o al archivo de imagen.

    :::image type="content" source="media/manage-vm/vm-settings-disk.png" alt-text="Pantalla de cambio de la configuración de disco de una máquina virtual" lightbox="media/manage-vm/vm-settings-disk.png":::

1. Para agregar, quitar o cambiar la configuración del adaptador de red, seleccione **Redes** y haga lo siguiente:
    - Especifique el conmutador virtual que desea usar y si desea habilitar la identificación de LAN virtual (también debe especificar el identificador de VLAN)
    - Para cambiar la configuración adicional de un adaptador de red, haga clic en **Avanzado** para poder:
        - Seleccionar entre tipo un de dirección MAC dinámica o estática
        - Permitir la suplantación de direcciones MAC
        - Habilitar la administración de ancho de banda y especificar el intervalo máximo y mínimo

        :::image type="content" source="media/manage-vm/vm-settings-network.png" alt-text="Pantalla de cambio de la configuración de la red de VM" lightbox="media/manage-vm/vm-settings-network.png":::

1. Seleccione **Orden de arranque** para agregar dispositivos de arranque o cambiar la secuencia de arranque de la máquina virtual.

    :::image type="content" source="media/manage-vm/vm-settings-boot.png" alt-text="Pantalla de cambio del orden de arranque de la máquina virtual" lightbox="media/manage-vm/vm-settings-boot.png":::

1. Seleccione **Puntos de control** para habilitar los puntos de control de la máquina virtual, seleccionar el tipo de punto de control y especificar la ubicación del archivo de punto de control.

    > [!NOTE]
    > Se recomienda utilizar el valor del punto de comprobación **Producción** ya que usa tecnología de copia de seguridad en el sistema operativo invitado para crear puntos de comprobación coherentes con los datos. El valor **Estándar** utiliza instantáneas de VHD para crear puntos de comprobación con el estado de aplicaciones y servicios.

     :::image type="content" source="media/manage-vm/vm-settings-checkpoint.png" alt-text="Pantalla de cambio de puntos de comprobación de máquina virtual" lightbox="media/manage-vm/vm-settings-checkpoint.png":::

1. Seleccione **Affinity rules** (Reglas de afinidad) a fin de crear una regla de afinidad para una máquina virtual. Para obtener más información sobre la creación de reglas de afinidad, consulte [Creación de reglas de afinidad de sitio y servidor para máquinas virtuales](vm-affinity.md).

    :::image type="content" source="media/manage-vm/vm-affinity.png" alt-text="Pantalla de reglas de afinidad de máquina virtual" lightbox="media/manage-vm/vm-affinity.png":::

1. Para cambiar la configuración de seguridad de la máquina virtual, seleccione **Seguridad** y haga lo siguiente:
    - Seleccione **Habilitar arranque seguro** para ayudar a evitar que se ejecute código no autorizado en el arranque (recomendado). Seleccione también una plantilla de Microsoft o de código abierto en el cuadro desplegable.
    - En **Plantilla**, seleccione la plantilla de seguridad que se va a usar.

    - En **Compatibilidad con cifrado**, puede:

        - Seleccionar **Habilitar módulo de plataforma segura** para usar un módulo del servicio criptográfico del hardware.

        - Habilitar el cifrado de estado y el tráfico de migración de máquina virtual

        > [!NOTE]
        > La compatibilidad con el cifrado requiere un protector de clave para la máquina virtual. Si aún no está presente, al seleccionar una de estas opciones se generará un protector de clave que permite ejecutar la máquina virtual en este host.

    - En **Directiva de seguridad**, seleccione **Habilitar blindaje** para ver opciones de protección adicionales para la máquina virtual.

        :::image type="content" source="media/manage-vm/vm-settings-security.png" alt-text="Pantalla de cambio de la configuración de seguridad de una máquina virtual" lightbox="media/manage-vm/vm-settings-security.png":::

## <a name="move-a-vm-to-another-server-or-cluster"></a>Traslado de una máquina virtual a otro servidor o clúster

Puede trasladar fácilmente una máquina virtual a otro servidor o a otro clúster de la siguiente manera:

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. En la pestaña **Inventario**, seleccione una máquina virtual de la lista y seleccione **Administrar > Mover**.
1. Elija un servidor de la lista y seleccione **Mover**.
1. Si quiere trasladar la máquina virtual y su almacenamiento, elija si desea moverla a otro clúster o a otro servidor del mismo clúster.

    :::image type="content" source="media/manage-vm/vm-more-move.png" alt-text="Pantalla de traslado de máquina virtual" lightbox="media/manage-vm/vm-more-move.png":::

1. Si desea trasladar solo el almacenamiento de la máquina virtual, seleccione moverlo a la misma ruta de acceso o seleccione diferentes rutas de acceso para la configuración, el punto de control o la paginación inteligente.

    :::image type="content" source="media/manage-vm/vm-move-storage.png" alt-text="Pantalla de traslado de almacenamiento de máquina virtual" lightbox="media/manage-vm/vm-move-storage.png":::

## <a name="join-a-vm-to-a-domain"></a>Unión de una máquina virtual a un dominio

Puede unir con facilidad una máquina virtual a un dominio de la manera siguiente:

:::image type="content" source="media/manage-vm/vm-domain-join.png" alt-text="Pantalla de unión de máquina virtual a un dominio" lightbox="media/manage-vm/vm-domain-join.png":::

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. En la pestaña **Inventario**, seleccione una máquina virtual de la lista y seleccione **Administrar > Unión a un dominio**.
1. Escriba el nombre del dominio al que desea unirse, junto con el nombre de usuario y la contraseña del dominio.
1. Escriba el nombre de usuario y la contraseña de la máquina virtual.
1. Cuando haya finalizado, haga clic en **Unirse**.

## <a name="clone-a-vm"></a>Clonación de una máquina virtual

Puede clonar fácilmente una máquina virtual como se indica a continuación:

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. Seleccione la pestaña **Inventario** de la derecha. Elija una máquina virtual de la lista y seleccione **Administrar > Clonar**.
1. Especifique un nombre y una ruta de acceso a la máquina virtual clonada.
1. Ejecute Sysprep en la máquina virtual si todavía no lo ha hecho.

:::image type="content" source="media/manage-vm/vm-clone.png" alt-text="Pantalla de clonación de máquina virtual" lightbox="media/manage-vm/vm-clone.png":::

## <a name="import-or-export-a-vm"></a>Importación o exportación de una máquina virtual

Puede importar o exportar fácilmente una máquina virtual. En el siguiente procedimiento se describe el proceso de importación.

:::image type="content" source="media/manage-vm/vm-more-import.png" alt-text="Pantalla de importación de máquina virtual" lightbox="media/manage-vm/vm-more-import.png":::

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. En la pestaña **Inventario**, seleccione **Agregar > Importar**.
1. Escriba el nombre de la carpeta que contiene la máquina virtual o haga clic en **Examinar** y seleccione una carpeta.
1. Seleccione la máquina virtual que desea importar.
1. Cree un identificador único para la máquina virtual si es necesario.
1. Cuando termine, seleccione **Importar**.

Para exportar una máquina virtual, el proceso es parecido:

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. En la pestaña **Inventario**, seleccione la máquina virtual que desea exportar en la lista.
1. Seleccione **Administrar > Exportar**.
1. Escriba la ruta de acceso a la que se va a exportar la máquina virtual.

:::image type="content" source="media/manage-vm/vm-export.png" alt-text="Pantalla de exportación de máquina virtual" lightbox="media/manage-vm/vm-export.png":::

## <a name="view-vm-event-logs"></a>Visualización de registros de eventos de la máquina virtual

Puede ver los registros de eventos de la máquina virtual de la siguiente manera:

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. En la pestaña **Resumen** de la derecha, seleccione **Ver todos los eventos**.
1. Seleccione una categoría de eventos y expanda la vista.

## <a name="connect-to-a-vm-by-using-remote-desktop"></a>Conexión a una máquina virtual mediante Escritorio remoto

En lugar de usar Windows Admin Center, también puede administrar las máquinas virtuales mediante un host de Hyper-V con una conexión de Protocolo de escritorio remoto (RDP).

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. En la pestaña **Inventario**, seleccione una máquina virtual de la lista y seleccione la opción **Conectar > Conectar** o **Conectar > Descargar archivo RDP**. Ambas opciones usan la herramienta VMConnect para conectarse a la máquina virtual invitada mediante el host de Hyper-V y requieren que escriba las credenciales de nombre de usuario y contraseña de administrador de este host.

    - La opción **Conectar** se conecta a la máquina virtual mediante Escritorio remoto en el explorador web.

    - La opción **Descargar archivo RDP** descarga un archivo .rdp que puede abrir para conectarse con la aplicación Conexión a Escritorio remoto (mstsc.exe).

## <a name="protect-vms-with-azure-site-recovery"></a>Protección de máquinas virtuales con Azure Site Recovery

Puede usar Windows Admin Center para configurar Azure Site Recovery y replicar las máquinas virtuales locales en Azure. Es un servicio de valor agregado. Para empezar, consulte [Protección de máquinas virtuales con Azure Site Recovery](azure-site-recovery.md).

:::image type="content" source="media/manage-vm/vm-more-azure.png" alt-text="Pantalla de configuración de Azure Site Recovery" lightbox="media/manage-vm/vm-more-azure.png":::

## <a name="next-steps"></a>Pasos siguientes

También puede crear y administrar las VM mediante Windows PowerShell. Para más información, consulte [Administración de máquinas virtuales en Azure Stack HCI mediante Windows PowerShell](vm-powershell.md).