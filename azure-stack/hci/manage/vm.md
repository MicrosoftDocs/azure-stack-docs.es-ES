---
title: Administración de máquinas virtuales en Azure Stack HCI mediante Windows Admin Center
description: Aprenda a crear y administrar máquinas virtuales en un clúster en Azure Stack HCl mediante Windows Admin Center.
author: v-dasis
ms.topic: article
ms.date: 05/20/2020
ms.author: v-dasis
ms.reviewer: JasonGerend
ms.openlocfilehash: f3ba9415cd5d91260067a1648bdb38c196be206a
ms.sourcegitcommit: d69eacbf48c06309b00d17c82ebe0ce2bc6552df
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/22/2020
ms.locfileid: "83794411"
---
# <a name="manage-vms-on-azure-stack-hci-using-windows-admin-center"></a>Administración de máquinas virtuales en Azure Stack HCI mediante Windows Admin Center

> Se aplica solo a Windows Server 2019

Windows Admin Center se puede usar para crear y administrar máquinas virtuales en Azure Stack HCI.

## <a name="get-a-list-of-vms"></a>Obtención de una lista de máquinas virtuales

Puede ver fácilmente todas las máquinas virtuales de un servidor o del clúster.

:::image type="content" source="media/manage-vm/vm-inventory.png" alt-text="Pantalla Máquinas virtuales":::

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

## <a name="view-vm-metrics"></a>Visualización de las métricas de la máquina virtual

Puede ver información detallada y los gráficos de rendimiento de una máquina virtual específica desde su página dedicada.

:::image type="content" source="media/manage-vm/vm-details.png" alt-text="Pantalla de información detallada de las máquinas virtuales":::

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. Haga clic en la pestaña **Inventario** de la derecha y, a continuación, seleccione la máquina virtual. En la siguiente página, puede hacer esto:

   - Ver gráficos de líneas de datos actuales y pasados de CPU, memoria, red, IOPS y rendimiento de E/S (los datos históricos solo están disponibles para los clústeres hiperconvergidos)
   - Ver, crear, aplicar, cambiar el nombre y eliminar puntos de control.
   - Ver los detalles de archivos de discos duros virtuales (.vhd), adaptadores de red y servidores host.
   - Ver el estado de la máquina virtual.
   - Guardar la máquina virtual, eliminar un estado guardado o crear un punto de control.
   - Cambiar la configuración de la máquina virtual.
   - Conectarse a la consola de la máquina virtual mediante VMConnect a través del host de Hyper-V.
   - Replicar la máquina virtual con Azure Site Recovery.

## <a name="view-aggregate-vm-metrics"></a>Visualización de métricas de máquina virtual agregadas

Puede ver las métricas de rendimiento y uso de los recursos de todas las máquinas virtuales del clúster.

:::image type="content" source="media/manage-vm/host-metrics.png" alt-text="Pantalla de métricas del host":::

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

        :::image type="content" source="media/manage-vm/vm-settings-general.png" alt-text="Pantalla de configuración general de una máquina virtual":::

1. Seleccione **Memoria** para cambiar la memoria de inicio de la máquina virtual, el intervalo de memoria dinámica, el porcentaje de búfer de memoria y el peso de la memoria.

    :::image type="content" source="media/manage-vm/vm-settings-memory.png" alt-text="Pantalla de cambio de la configuración de memoria de una máquina virtual":::

1. Seleccione **Procesadores** para cambiar el número de procesadores virtuales, habilitar la virtualización anidada o habilitar multithreading simultáneo (SMT).

    :::image type="content" source="media/manage-vm/vm-settings-processor.png" alt-text="Pantalla de cambio de la configuración del procesador de una máquina virtual":::

1. Para agregar un nuevo disco virtual, seleccione **Discos** y, a continuación, seleccione si desea crear un disco virtual vacío o usar un disco duro existente o un archivo de imagen ISO (.iso). Haga clic en **Examinar** y seleccione la ruta de acceso al disco virtual o al archivo de imagen.

    :::image type="content" source="media/manage-vm/vm-settings-disk.png" alt-text="Pantalla de cambio de la configuración de disco de una máquina virtual":::

1. Para agregar, quitar o cambiar la configuración del adaptador de red, seleccione **Redes** y haga lo siguiente:
    - Especifique el conmutador virtual que desea usar y si desea habilitar la identificación de LAN virtual (también debe especificar el identificador de VLAN)
    - Para cambiar la configuración adicional de un adaptador de red, haga clic en **Avanzado** para poder:
        - Seleccionar entre tipo un de dirección MAC dinámica o estática
        - Permitir la suplantación de direcciones MAC
        - Habilitar la administración de ancho de banda y especificar el intervalo máximo y mínimo

        :::image type="content" source="media/manage-vm/vm-settings-network.png" alt-text="Pantalla de cambio de la configuración de la red de VM":::

1. Seleccione **Orden de arranque** para agregar dispositivos de arranque o cambiar la secuencia de arranque de la máquina virtual.

    :::image type="content" source="media/manage-vm/vm-settings-boot.png" alt-text="Pantalla de cambio del orden de arranque de la máquina virtual":::

1. Seleccione **Puntos de comprobación** para habilitar los puntos de comprobación de la máquina virtual, el tipo de punto de comprobación y la ubicación.

    > [!NOTE]
    > Se recomienda utilizar el valor del punto de comprobación **Producción** ya que usa tecnología de copia de seguridad en el sistema operativo invitado para crear puntos de comprobación coherentes con los datos. El valor **Estándar** utiliza instantáneas de VHD para crear puntos de comprobación con el estado de aplicaciones y servicios.

     :::image type="content" source="media/manage-vm/vm-settings-checkpoint.png" alt-text="Pantalla de cambio de puntos de comprobación de máquina virtual":::

1. Para cambiar la configuración de seguridad de la máquina virtual, seleccione **Seguridad** y haga lo siguiente:
    - Seleccione **Habilitar arranque seguro** para ayudar a evitar que se ejecute código no autorizado en el arranque (recomendado). Seleccione también una plantilla de Microsoft o de código abierto en el cuadro desplegable.

    - En **Compatibilidad con cifrado**, puede:

        - Seleccionar **Habilitar módulo de plataforma segura** para usar un módulo del servicio criptográfico del hardware.

        - Habilitar el cifrado de estado y el tráfico de migración de máquina virtual

        > [!NOTE]
        > La compatibilidad con el cifrado requiere un protector de clave para la máquina virtual. Si aún no está presente, al seleccionar una de estas opciones se generará un protector de clave que permite ejecutar la máquina virtual en este host.

    - En **Directiva de seguridad**, seleccione **Habilitar blindaje** para ver opciones de protección adicionales para la máquina virtual.

        :::image type="content" source="media/manage-vm/vm-settings-security.png" alt-text="Pantalla de cambio de la configuración de seguridad de una máquina virtual":::

## <a name="create-a-new-vm"></a>Creación de una máquina virtual nueva

Puede crear fácilmente una nueva máquina virtual mediante Windows Admin Center.

:::image type="content" source="media/manage-vm/new-vm.png" alt-text="Pantalla de nueva máquina virtual":::

1. En la página principal de Windows Admin Center, en **Todas las conexiones**, seleccione el servidor o clúster en el que desea crear la máquina virtual.
1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. En **Máquinas virtuales**, seleccione la pestaña **Inventario** y, a continuación, seleccione **Nuevo**.
1. En **Nueva máquina virtual**, escriba un nombre para la máquina virtual.
1. Seleccione **Generación 2 (recomendado)** .
1. Seleccione una ruta de acceso de archivo preasignada en la lista desplegable o haga clic en **Examinar** para elegir la carpeta en la que desea guardar la configuración de la máquina virtual y los archivos del disco duro virtual (VHD). Para buscar cualquier recurso compartido de SMB disponible en la red, escriba la ruta de acceso como *\\servidor\recurso compartido*.

1. En **Procesadores virtuales**, seleccione el número de procesadores virtuales y si desea habilitar la virtualización anidada.
1. En **Memoria**, seleccione la cantidad de memoria de inicio (se recomienda un mínimo de 4 GB) y un intervalo mínimo y máximo de memoria dinámica, según corresponda, para asignarla a la máquina virtual.
1. En **Red**, seleccione un adaptador de red en la lista desplegable.
1. En **Almacenamiento**, haga clic en **Agregar** y seleccione si desea crear un nuevo disco duro virtual o usar uno ya existente. Si va a utilizar uno ya existente, haga clic en **Examinar** y seleccione la ruta de acceso del archivo correspondiente.  
1. En **Sistema operativo**, realice una de las acciones siguientes:
   - Seleccione **Instalar un sistema operativo más adelante** si desea instalar un sistema operativo para la máquina virtual más adelante.
   - Seleccione **Install an operating system from an image file (*.iso)** (Instalar un sistema operativo a partir de un archivo de imagen (*.iso), haga clic en **Examinar** y, a continuación, seleccione el archivo de imagen .iso correspondiente que se va a usar.
   - Seleccione **Instalar un sistema operativo desde un servidor de instalación en red** si desea instalar un sistema operativo en la máquina virtual más adelante con este método. Asegúrese de haber seleccionado previamente un adaptador de red o, de lo contrario, no funcionará.
1. Cuando haya finalizado, haga clic en **Crear** para crear la máquina virtual.
1. Para iniciar la máquina virtual, en la lista de **Máquinas virtuales**, mantenga el mouse sobre la nueva máquina virtual, active la casilla correspondiente a la izquierda y seleccione **Iniciar**.
1. En **Estado**, compruebe que el estado de la máquina virtual es **En ejecución**.

## <a name="move-a-vm-to-another-server-in-the-cluster"></a>Traslado de una máquina virtual a otro servidor del clúster

Puede trasladar fácilmente una máquina virtual a otro servidor del clúster de la siguiente manera:

:::image type="content" source="media/manage-vm/vm-more-move.png" alt-text="Pantalla de traslado de máquina virtual":::

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. Seleccione la pestaña **Inventario** de la derecha. Elija una máquina virtual de la lista y seleccione **Más > Mover**.
1. Elija un servidor de la lista y seleccione **Mover**.
1. En **Mover máquina virtual**, seleccione **Clúster de conmutación por error** y, a continuación, escriba el nombre del clúster y el nodo del clúster al que va a trasladar la máquina virtual.
1. Después de un traslado correcto, verá el nombre actualizado en la lista en **Servidor host**.

## <a name="import-or-export-a-vm"></a>Importación o exportación de una máquina virtual

Puede importar o exportar fácilmente una máquina virtual. En el siguiente procedimiento se describe el proceso de importación.

:::image type="content" source="media/manage-vm/vm-more-import.png" alt-text="Pantalla de importación de máquina virtual":::

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. Seleccione la pestaña **Inventario** de la derecha. Elija una máquina virtual en clúster de la lista y seleccione **Más > Importar**.
1. Escriba el nombre de la carpeta que contiene la máquina virtual o haga clic en **Examinar** y seleccione una carpeta.
1. Seleccione la máquina virtual que desea importar.
1. Cree un identificador único para la máquina virtual si es necesario.
1. Cuando termine, seleccione **Importar**.

Para exportar una máquina virtual, el proceso es muy parecido. Solo tiene que seleccionar **Más > Exportar** en su lugar.

## <a name="view-vm-event-logs"></a>Visualización de registros de eventos de la máquina virtual

Puede ver los registros de eventos de la máquina virtual de la siguiente manera:

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. En la pestaña **Resumen** de la derecha, seleccione **Ver todos los eventos**.
1. Seleccione una categoría de eventos y expanda la vista.

## <a name="connect-to-a-vm-by-using-remote-desktop"></a>Conexión a una máquina virtual mediante Escritorio remoto

En lugar de usar Windows Admin Center, también puede administrar las máquinas virtuales mediante un host de Hyper-V con una conexión de Protocolo de escritorio remoto (RDP).

1. En **Herramientas**, desplácese hacia abajo y seleccione **Máquinas virtuales**.
1. Seleccione la pestaña **Inventario** de la derecha. Elija una máquina virtual de la lista y seleccione la opción **Más > Conectar** o **Más > Descargar archivo RDP**. Ambas opciones usan la herramienta VMConnect para conectarse a la máquina virtual invitada mediante el host de Hyper-V y requieren que escriba las credenciales de nombre de usuario y contraseña de administrador de este host.

    - La opción **Conectar** se conecta a la máquina virtual mediante Escritorio remoto en el explorador web.

    - La opción **Descargar archivo RDP** descarga un archivo .rdp que puede abrir para conectarse con la aplicación Conexión a Escritorio remoto (mstsc.exe).

## <a name="protect-vms-with-azure-site-recovery"></a>Protección de máquinas virtuales con Azure Site Recovery

Puede usar Windows Admin Center para configurar Azure Site Recovery y replicar las máquinas virtuales locales en Azure. Es un servicio de valor agregado. Para empezar, consulte [Protección de máquinas virtuales con Azure Site Recovery](azure-site-recovery.md).

:::image type="content" source="media/manage-vm/vm-more-azure.png" alt-text="Pantalla de configuración de Azure Site Recovery":::

## <a name="next-steps"></a>Pasos siguientes

También puede crear y administrar máquinas virtuales con los cmdlets de Hyper-V para Windows PowerShell. Para más información, consulte [Hyper-V](https://docs.microsoft.com/powershell/module/hyper-v/?view=win10-ps).