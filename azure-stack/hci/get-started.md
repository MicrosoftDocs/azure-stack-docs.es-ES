---
title: Introducción a Azure Stack HCI y Windows Admin Center
description: Conéctese rápidamente a un clúster de Azure Stack HCI existente y use Windows Admin Center para supervisar el rendimiento del clúster y del almacenamiento.
author: khdownie
ms.author: v-kedow
ms.topic: overview
ms.date: 04/08/2020
ms.openlocfilehash: dd97ee55779d1c3c35976005a880a0dd510e29af
ms.sourcegitcommit: 76af742a42e807c400474a337e29d088ede8a60d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/22/2020
ms.locfileid: "85196892"
---
# <a name="get-started-with-azure-stack-hci-and-windows-admin-center"></a>Introducción a Azure Stack HCI y Windows Admin Center

> Se aplica a: Windows Server 2019

En este tema se proporcionan instrucciones para instalar Windows Admin Center, conectarse a un clúster de Azure Stack HCI y supervisar el rendimiento del clúster y del almacenamiento.

## <a name="install-windows-admin-center"></a>Instalación de Windows Admin Center

La manera más sencilla de instalar Windows Admin Center es en un equipo con Windows 10 local, aunque debe ser miembro del grupo de administradores locales para hacerlo.

1. Descargue [Windows Admin Center](https://www.microsoft.com/evalcenter/evaluate-windows-admin-center) desde Microsoft Evaluation Center. Aunque dice "Iniciar la evaluación", esta es la versión disponible con carácter general para su uso en producción, que se incluye como parte de la licencia de Windows Server.
2. Ejecute el archivo WindowsAdminCenter.msi para la instalación.
3. Al iniciar Windows Admin Center por primera vez, aparecerá un icono en el área de notificación del escritorio. Haga clic con el botón derecho en este icono y elija Abrir para abrir la herramienta en el explorador predeterminado. Asegúrese de seleccionar el certificado de cliente de Windows Admin Center cuando se le pida que seleccione un certificado.

## <a name="add-and-connect-to-an-azure-stack-hci-cluster"></a>Adición y conexión a un clúster de Azure Stack HCI

Después de haber completado la instalación de Windows Admin Center, puede agregar un clúster para administrarlo desde la página de información general principal.

1. Haga clic en **+ Agregar** en **Todas las conexiones**.

    :::image type="content" source="media/get-started/addcluster.png" alt-text="Captura de pantalla de Agregar clúster":::

2. Seleccione para agregar un clúster de Windows Server:

    :::image type="content" source="media/get-started/chooseconnectiontype.png" alt-text="Captura de pantalla de Elige el tipo de conexión":::

3. Escriba el nombre del clúster que desea administrar y haga clic en **Agregar**. El clúster se agregará a la lista de conexiones en la página de información general.

4. En **Todas las conexiones**, haga clic en el nombre del clúster que acaba de agregar. Windows Admin Center iniciará el **administrador de clústeres** y le llevará directamente al panel de Windows Admin Center para ese clúster.

## <a name="monitor-cluster-performance-with-the-windows-admin-center-dashboard"></a>Supervisión del rendimiento del clúster con el panel de Windows Admin Center

El panel de Windows Admin Center proporciona alertas e información de estado sobre servidores, unidades y volúmenes, así como detalles acerca del uso de la CPU, la memoria y el almacenamiento. La parte inferior del panel muestra información sobre el rendimiento del clúster, como IOPS y latencia por hora, día, semana, mes o año.

:::image type="content" source="media/get-started/dashboard.png" alt-text="Captura de pantalla del panel de Windows Admin Center":::

## <a name="monitor-performance-of-individual-components"></a>Supervisión del rendimiento de componentes individuales

El menú **Herramientas** a la izquierda del panel le permite explorar en profundidad cualquier componente del clúster para ver resúmenes e inventarios de máquinas virtuales, servidores, volúmenes y unidades.

### <a name="virtual-machines"></a>Máquinas virtuales

Para ver un resumen de las máquinas virtuales que se ejecutan en el clúster, haga clic en **Máquinas virtuales** en el menú **Herramientas** de la izquierda.

:::image type="content" source="media/get-started/vms-summary.png" alt-text="Resumen de la máquina virtual":::

Para un inventario completo de las máquinas virtuales que se ejecutan en el clúster junto con su estado, el servidor host, el uso de CPU, la presión de memoria, la demanda de memoria, la memoria asignada y el tiempo de actividad, haga clic en **Inventario** en la parte superior de la página.

:::image type="content" source="media/get-started/vms-inventory.png" alt-text="Inventario de máquina virtual":::

### <a name="servers"></a>Servidores

Para ver un resumen de los servidores del clúster, haga clic en **Servidores** en el menú **Herramientas** de la izquierda.

:::image type="content" source="media/get-started/servers-summary.png" alt-text="Resumen de servidores":::

Para ver un inventario completo de los servidores del clúster, incluido su estado, tiempo de actividad, fabricante, modelo y número de serie, haga clic en **Inventario** en la parte superior de la página.

:::image type="content" source="media/get-started/servers-inventory.png" alt-text="Inventario de servidores":::

### <a name="volumes"></a>Volúmenes

Para ver un resumen de los volúmenes del clúster, haga clic en **Volúmenes** en el menú **Herramientas** de la izquierda.

:::image type="content" source="media/get-started/volumes-summary.png" alt-text="Resumen de volúmenes":::

Para un inventario completo de los volúmenes del clúster, incluido su estado, sistema de archivos, resistencia, tamaño, uso de almacenamiento e IOPS, haga clic en **Inventario** en la parte superior de la página.

:::image type="content" source="media/get-started/volumes-inventory.png" alt-text="Inventario de volúmenes":::

### <a name="drives"></a>Unidades

Para ver un resumen de las unidades del clúster, haga clic en **Unidades** en el menú **Herramientas** de la izquierda.

:::image type="content" source="media/get-started/drives-summary.png" alt-text="Resumen de unidades":::

Para ver un inventario completo de las unidades del clúster junto con su número de serie, estado, modelo, tamaño, tipo, uso, ubicación, servidor y capacidad, haga clic en **Inventario** en la parte superior de la página.

:::image type="content" source="media/get-started/drives-inventory.png" alt-text="Inventario de unidades":::

### <a name="virtual-switches"></a>Conmutadores virtuales

Para ver la configuración de un conmutador virtual en el clúster, haga clic en **Conmutadores virtuales** en el menú **Herramientas** de la izquierda y, a continuación, haga clic en el nombre del conmutador virtual para el que desea mostrar la configuración. Windows Admin Center mostrará los adaptadores de red asociados al conmutador virtual, incluidas sus direcciones IP, el estado de la conexión, la velocidad del vínculo y la dirección MAC.

:::image type="content" source="media/get-started/virtual-switch-settings.png" alt-text="Configuración del conmutador virtual":::

## <a name="add-counters-with-the-performance-monitor-tool"></a>Adición de contadores con la herramienta Monitor de rendimiento

Use la herramienta Monitor de rendimiento para ver y comparar los contadores de rendimiento de Windows, las aplicaciones o los dispositivos en tiempo real.

1. Seleccione **Monitor de rendimiento** en el menú **Herramientas** de la izquierda.
2. Haga clic en **área de trabajo en blanco** para iniciar una nueva área de trabajo o en **restaurar la anterior** para restaurar un área de trabajo anterior.
    :::image type="content" source="media/get-started/performance-monitor.png" alt-text="Captura de pantalla de Monitor de rendimiento":::
3. Si va a crear una nueva área de trabajo, haga clic en el botón **Agregar contador** y seleccione uno o más servidores de origen para supervisar, o seleccione todo el clúster.
4. Seleccione el objeto y la instancia que desea supervisar, así como el tipo de contador y gráfico para ver la información de rendimiento dinámica.
    :::image type="content" source="media/get-started/example-counter.png" alt-text="Captura de pantalla de contador de ejemplo":::
5. Para guardar el área de trabajo, seleccione **Guardar > Guardar como** en el menú de la parte superior.

## <a name="use-azure-monitor-for-monitoring-and-alerts"></a>Uso de Azure Monitor para supervisión y alertas

También puede usar [Azure Monitor](/windows-server/manage/windows-admin-center/azure/azure-monitor) (requiere una suscripción de Azure) para recopilar eventos y contadores de rendimiento para el análisis y la generación de informes, tomar medidas cuando se detecta una condición determinada y recibir notificaciones por correo electrónico. Haga clic en **Azure Monitor** en el menú **Herramientas** para conectarse directamente a Azure desde Windows Admin Center.

## <a name="collect-diagnostics-information"></a>Recopilación de la información de diagnóstico

Seleccione **Diagnósticos** en el menú **Herramientas** para recopilar información para la solución de problemas con el clúster. Si llama a Soporte técnico de Microsoft, puede que le pidan esta información.

## <a name="next-steps"></a>Pasos siguientes

Para profundizar más en la supervisión del rendimiento, consulte también:

- [Historial de rendimiento de Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/performance-history)
- [Supervisión de Azure Stack HCI con Azure Monitor](manage/azure-monitor.md)
