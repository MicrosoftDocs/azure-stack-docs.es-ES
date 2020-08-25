---
title: Supervisión de clústeres de Azure Stack HCI
description: Supervisión de clústeres, servidores, máquinas virtuales, unidades y volúmenes de Azure Stack HCI mediante Windows Admin Center y PowerShell.
author: khdownie
ms.author: v-kedow
ms.topic: how-to
ms.service: azure-stack
ms.subservice: azure-stack-hci
ms.date: 08/12/2020
ms.openlocfilehash: 9ce1dc258243e9e17458c1f70e5a852a0b56996a
ms.sourcegitcommit: a3e042c782a38ecf3baf7a64b1d492a655972f9a
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2020
ms.locfileid: "88202048"
---
# <a name="monitor-azure-stack-hci-clusters"></a>Supervisión de clústeres de Azure Stack HCI

> Se aplica a: Azure Stack HCI, versión 20H2; Windows Server 2019

Hay tres maneras de supervisar los clústeres de Azure Stack HCI y sus componentes subyacentes: Windows Admin Center, [Azure Monitor](azure-monitor.md) y PowerShell.

## <a name="monitor-using-windows-admin-center-dashboard"></a>Supervisión mediante el panel de Windows Admin Center

[Instale Windows Admin Center](/windows-server/manage/windows-admin-center/deploy/install) en un servidor o un equipo de administración y, a continuación, agregue y conéctese al clúster de Azure Stack HCI que desea supervisar. Las alertas críticas se muestran de forma destacada en la parte superior del panel de Windows Admin Center en cuanto se inicia sesión. Por ejemplo, la captura de pantalla siguiente indica que es necesario instalar actualizaciones y que el clúster tiene un error de unidad crítico:

:::image type="content" source="media/monitor-cluster/dashboard-alert.png" alt-text="Ejemplo de alertas del panel de Windows Admin Center":::

## <a name="monitor-virtual-machines"></a>Supervisión de máquinas virtuales

Es importante entender el estado de las máquinas virtuales en las que se ejecutan las aplicaciones y bases de datos. Si no se asigna suficiente CPU o memoria a una máquina virtual para las cargas de trabajo que se ejecutan en ella, puede que el rendimiento sea lento o que la aplicación deje de estar disponible. Si una máquina virtual responde a menos de tres latidos durante un período de cinco minutos o más, puede que haya un problema.

Para supervisar máquinas virtuales en Windows Admin Center, haga clic en **Máquinas virtuales** en el menú **Herramientas** de la izquierda. Para ver un inventario completo de las máquinas virtuales que se ejecutan en el clúster, haga clic en **Inventario** en la parte superior de la página. Verá una tabla con información sobre cada máquina virtual, que incluye:

- **Nombre:** el nombre de la máquina virtual.
- **Estado:** indica si la máquina virtual se está ejecutando o está detenida.
- **Servidor de host:** indica en qué servidor del clúster se está ejecutando la máquina virtual.
- **Uso de CPU:** el porcentaje del total de recursos de CPU del clúster que usa la máquina virtual.
- **Presión de memoria:** el porcentaje de los recursos de memoria disponibles que usa la máquina virtual.
- **Demanda de memoria:** la cantidad de memoria asignada (GB o MB) que usa la máquina virtual.
- **Memoria asignada:** la cantidad total de memoria asignada a la máquina virtual.
- **Tiempo de actividad:** cuánto tiempo se ha estado ejecutando la máquina virtual en días:horas:minutos:segundos.
- **Latido:** indica si el clúster puede comunicarse con la máquina virtual.
- **Estado de la recuperación ante desastres:** muestra si la máquina virtual ha iniciado sesión en Azure Site Recovery.

## <a name="monitor-servers"></a>Supervisar servidores

Puede supervisar los servidores host que componen un clúster de Azure Stack HCI directamente desde Windows Admin Center. Si los servidores host no están configurados con suficiente CPU o memoria para proporcionar los recursos que requieren las máquinas virtuales, se puede producir un cuello de botella de rendimiento. 

Para supervisar los servidores en Windows Admin Center, haga clic en **Servidores** en el menú **Herramientas** de la izquierda. Para ver un inventario completo de los servidores que se ejecutan en el clúster, haga clic en **Inventario** en la parte superior de la página. Verá una tabla con información sobre cada servidor, que incluye:

- **Nombre:** el nombre del servidor host en el clúster.
- **Estado:** indica si el servidor está activo o inactivo.
- **Tiempo de actividad:** cuánto tiempo ha estado conectado el servidor.
- **Fabricante:** el fabricante del hardware del servidor.
- **Modelo:** el modelo del servidor.
- **Número de serie:** el número de serie del servidor.
- **Uso de CPU:** el porcentaje de la CPU del servidor host que se está usando. Ningún servidor del clúster debe usar más del 85 por ciento de la CPU durante más de 10 minutos. 
- **Uso de memoria:** el porcentaje de memoria del servidor host que se está usando. Si un servidor tiene menos de 100 MB de memoria disponible durante 10 minutos o más, considere la posibilidad de agregar memoria.

## <a name="monitor-volumes"></a>Supervisión de volúmenes

Los volúmenes de almacenamiento se pueden llenar rápidamente, por lo que es importante supervisarlos periódicamente para evitar cualquier impacto en la aplicación. Para supervisar los volúmenes en Windows Admin Center, haga clic en **Volúmenes** en el menú **Herramientas** de la izquierda. Para ver un inventario completo de los volúmenes de almacenamiento que se ejecutan en el clúster, haga clic en **Inventario** en la parte superior de la página. Verá una tabla con información sobre cada volumen, que incluye:

- **Nombre:** El nombre del objeto visual.
- **Estado:** "OK" indica que el volumen es correcto; de lo contrario, se notificará una advertencia o un error.
- **Sistema de archivos:** sistema de archivos en el volumen (ReFS, CSVFS).
- **Resistencia:** indica si el volumen es un reflejo bidireccional, un reflejo triple o una paridad con aceleración de reflejo.
- **Size:** el tamaño del volumen (TB o GB)
- **Grupo de almacenamiento:** el grupo de almacenamiento al que pertenece el volumen.
- **Uso del almacenamiento:** el porcentaje de la capacidad de almacenamiento de volumen que se está usando.
- **IOPS:** número de operaciones de entrada/salida por segundo.

## <a name="monitor-drives"></a>Supervisión de unidades

Azure Stack HCI virtualiza el almacenamiento de forma que la pérdida de una unidad individual no afecta significativamente al clúster. Sin embargo, las unidades con errores [tienen que reemplazarse](replace-drives.md) y las unidades pueden afectar al rendimiento al llenarse o generar latencia. Si el sistema operativo no se puede comunicar con una unidad, es posible que la unidad esté floja o desconectada, puede que exista algún problema en su conector o que se haya producido un error en la propia unidad. Windows retira automáticamente las unidades después de 15 minutos de pérdida de comunicación. 

Para supervisar las unidades en Windows Admin Center, haga clic en **Unidades** en el menú **Herramientas** de la izquierda. Para ver un inventario completo de las unidades del clúster, haga clic en **Inventario** en la parte superior de la página. Verá una tabla con información sobre cada unidad, que incluye:

- **Número de serie:** el número de serie de la unidad.
- **Estado:** "OK" indica que el estado de la unidad es correcto; de lo contrario, se notificará una advertencia o un error.
- **Modelo:** el modelo de la unidad.
- **Size:** la capacidad total de la unidad (TB o GB).
- **Tipo:** el tipo de unidad (SSD, HDD).
- **Usado para:** indica si la unidad se utiliza para el almacenamiento en caché o para capacidad.
- **Ubicación:** el adaptador de almacenamiento y el puerto al que está conectada la unidad.
- **Server:** el nombre del servidor al que está conectada la unidad.
- **Grupo de almacenamiento:** el grupo de almacenamiento al que pertenece la unidad.
- **Uso del almacenamiento:** el porcentaje de la capacidad de almacenamiento de la unidad que se está usando.

## <a name="add-performance-counters"></a>Incorporación de contadores de rendimiento

Use la herramienta Monitor de rendimiento de Windows Admin Center para ver y comparar los contadores de rendimiento de Windows, las aplicaciones o los dispositivos en tiempo real.

1. Seleccione **Monitor de rendimiento** en el menú **Herramientas** de la izquierda.
1. Haga clic en **área de trabajo en blanco** para iniciar una nueva área de trabajo o en **restaurar la anterior** para restaurar un área de trabajo anterior.
1. Si va a crear una nueva área de trabajo, haga clic en el botón **Agregar contador** y seleccione uno o más servidores de origen para supervisar, o seleccione todo el clúster.
1. Seleccione el objeto y la instancia que desea supervisar, así como el tipo de contador y gráfico para ver la información de rendimiento dinámica.
1. Para guardar el área de trabajo, seleccione **Guardar > Guardar como** en el menú de la parte superior.
 
Por ejemplo, la captura de pantalla siguiente muestra un contador de rendimiento denominado "Uso de memoria" que muestra información sobre la memoria en un clúster de dos nodos.

:::image type="content" source="media/monitor-cluster/performance-monitor.png" alt-text="Ejemplo de un contador de rendimiento en tiempo real de Windows Admin Center":::

## <a name="query-and-process-performance-history-with-powershell"></a>Historial de rendimiento de consultas y procesos con PowerShell

También puede supervisar los clústeres de Azure Stack HCI con cmdlets de PowerShell que devuelven información sobre el clúster y sus componentes. Consulte [Historial de rendimiento de Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/performance-history).

## <a name="use-the-health-service-feature"></a>Uso de la característica Servicio de mantenimiento

Se debe investigar cualquier error de Servicio de mantenimiento en el clúster. Consulte [Servicio de mantenimiento en Windows Server](/windows-server/failover-clustering/health-service-overview) para aprender a ejecutar informes e identificar errores.

## <a name="troubleshoot-health-and-operational-states"></a>Solución de problemas y estados operativos

Para conocer el estado del mantenimiento y los estados operativos de los grupos de almacenamiento, discos virtuales y unidades, consulte la [solución de problemas y estados operativos de Espacios de almacenamiento y Espacios de almacenamiento directo](/windows-server/storage/storage-spaces/storage-spaces-states).

## <a name="monitor-performance-using-storage-qos"></a>Supervisión del rendimiento mediante Calidad de servicio del almacenamiento

Calidad de servicio del almacenamiento (QoS) proporciona una manera de supervisar y administrar de forma centralizada las entradas y salidas de almacenamiento de las máquinas virtuales a fin de mitigar los problemas de vecinos ruidosos y proporcionar un rendimiento coherente. Consulte [Calidad de servicio del almacenamiento](/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="set-up-alerts-in-azure-monitor"></a>Configuración de alertas en Azure Monitor

Azure Stack HCI se integra con Azure Monitor para permitir que los usuarios [configuren alertas](azure-monitor.md#setting-up-alerts-using-windows-admin-center) y reciban notificaciones si se superan los umbrales de uso de memoria, CPU, capacidad de disco y uso de memoria, si no se devuelven los latidos de la máquina virtual, o si hay un error crítico del sistema o un error del servicio de mantenimiento.

## <a name="next-steps"></a>Pasos siguientes

Para obtener información relacionada, consulte:

- [Introducción a Azure Stack HCI y Windows Admin Center](../get-started.md)
- [Supervisión de Azure Stack HCI con Azure Monitor](azure-monitor.md)