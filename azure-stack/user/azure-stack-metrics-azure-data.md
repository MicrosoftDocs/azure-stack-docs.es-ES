---
title: Uso de Azure Monitor en Azure Stack Hub
description: Obtenga información sobre cómo usar Azure Monitor en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 5/27/2020
ms.author: mabrigg
ms.lastreviewed: 12/01/2019
ms.openlocfilehash: bd8a7ee4082f446c9d175a9b3b69fa0e5a460b24
ms.sourcegitcommit: 0aa5f7f20690839661c8bb3bfdbe32f82bec0c64
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/21/2020
ms.locfileid: "86567423"
---
# <a name="use-azure-monitor-on-azure-stack-hub"></a>Uso de Azure Monitor en Azure Stack Hub

En este artículo se proporciona información general sobre el servicio Azure Monitor en Azure Stack Hub. En él, se explica el funcionamiento de Azure Monitor y se proporciona información adicional sobre su uso en Azure Stack Hub.

Si desea ver una introducción a Azure Monitor, consulte el artículo sobre Azure global [Introducción a Azure Monitor en Azure Stack Hub](/azure/monitoring-and-diagnostics/monitoring-get-started).

![Hoja de Monitor en Azure Stack Hub](./media/azure-stack-metrics-azure-data/azs-monitor.png)

Azure Monitor es un servicio de plataforma que proporciona un único origen para la supervisión de recursos de Azure. Azure Monitor le permite visualizar, consultar, enrutar y archivar las métricas y los registros procedentes de los recursos de Azure, así como tomar otras medidas relacionadas. Puede trabajar con estos datos mediante el portal del administrador de Azure Stack Hub, los cmdlets de Monitor en PowerShell, la CLI multiplataforma o las API REST de Azure Monitor. Para más información sobre la conectividad específica admitida en Azure Stack Hub, consulte [Consumo de datos de supervisión de Azure Stack Hub](azure-stack-metrics-monitor.md).

> [!Note]
> Las métricas y los registros de diagnóstico no están disponibles en el Kit de desarrollo de Azure Stack.

## <a name="prerequisites-for-azure-monitor-on-azure-stack-hub"></a>Requisitos previos de Azure Monitor en Azure Stack Hub

Registre el proveedor de recursos **Microsoft.insights** en la configuración de los proveedores de recursos ofertados en la suscripción. Puede comprobar si el proveedor de recursos está disponible en la oferta asociada a su suscripción:

1. Abra el portal de usuarios de Azure Stack Hub.
2. Seleccione **Suscripciones**.
3. Seleccione la suscripción que quiere registrar.
4. En **Configuración**, seleccione **Proveedores de recursos**. 
5. Busque **Microsoft.Insights** en la lista y compruebe que el estado es **Registrado**.

## <a name="overview-of-azure-monitor-on-azure-stack-hub"></a>Introducción a Azure Monitor en Azure Stack Hub

Al igual que ocurre con Azure Monitor en Azure, Azure Monitor en Azure Stack Hub dispone de registros y métricas de infraestructura de nivel básico de la mayoría de los servicios.

## <a name="azure-monitor-sources-compute-subset"></a>Orígenes de Azure Monitor: subconjunto de Compute

![Orígenes de Azure Monitor en Azure Stack Hub: subconjunto de proceso](media//azure-stack-metrics-azure-data/azs-monitor-computersubset.svg)

El proveedor de recursos **Microsoft.Compute** de Azure Stack Hub contiene:
 - Máquinas virtuales 
 - Conjuntos de escalado de máquinas virtuales

### <a name="application---diagnostics-logs-app-logs-and-metrics"></a>Aplicación: registros de diagnóstico, registros de aplicación y métricas

Las aplicaciones pueden ejecutarse en el sistema operativo de una máquina virtual en ejecución con el proveedor de recursos **Microsoft.Compute**. Estas aplicaciones y máquinas virtuales emiten su propio conjunto de registros y métricas. Azure Monitor se basa en la extensión Diagnósticos de Azure (Windows o Linux) para recopilar la mayoría de los registros y las métricas a nivel de aplicación.

Los tipos de medidas incluyen:
 - Contadores de rendimiento
 - Registros de aplicación
 - Registros de eventos de Windows
 - Origen de eventos de .NET
 - Registros IIS
 - ETW basado en manifiesto
 - Volcados de memoria
 - Registros de errores personalizados

> [!Note]  
> No se admite la extensión de Linux Diagnostics en Azure Stack Hub.

### <a name="host-and-guest-vm-metrics"></a>Métricas de máquinas virtuales host e invitadas

Los recursos de Compute enumerados anteriormente tienen una máquina virtual host dedicada y un sistema operativo invitado. La máquina virtual host y el sistema operativo invitado son los equivalentes a la máquina virtual raíz y a la máquina virtual invitada en el hipervisor de Hyper-V. Puede recopilar métricas tanto de la máquina virtual host como del sistema operativo invitado. También puede recopilar registros de diagnóstico para el SO invitado. En [Métricas de Azure Stack Hub compatibles con Azure Monitor](azure-stack-metrics-supported.md), hay una lista con las métricas de la máquina virtual host y la máquina virtual invitada de Azure Stack Hub que se pueden recopilar. 

### <a name="activity-log"></a>Registro de actividades

Puede buscar los registros de actividad para obtener información sobre los recursos de proceso tal y como se ven en la infraestructura de Azure Stack Hub. El registro contiene información tal como el número de veces que se crean o se destruye recursos. Los registros de actividad de Azure Stack Hub son coherentes con Azure. Para más información, consulte la descripción de [Introducción al registro de actividad de Azure](/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs). 


## <a name="azure-monitor-sources-everything-else"></a>Orígenes de Azure Monitor: todos los demás

![Orígenes de Azure Monitor en Azure Stack Hub: todo lo demás](media//azure-stack-metrics-azure-data/azs-monitor-othersubset.svg)

### <a name="resources---metrics-and-diagnostics-logs"></a>Recursos: métricas y registros de diagnóstico

Las métricas y los registros de diagnóstico que se pueden recopilar varían según el tipo de recurso. Entre las métricas admitidas, se encuentran las métricas de cada recurso de Azure Stack Hub que se pueden recopilar. Para más información, consulte [Métricas de Azure Stack Hub compatibles con Azure Monitor](azure-stack-metrics-supported.md).

### <a name="activity-log"></a>Registro de actividades

El registro de actividad es el mismo que en los recursos de Compute. 

### <a name="uses-for-monitoring-data"></a>Usos de los datos de supervisión

**Almacenamiento y archivado**  

Algunos datos de supervisión ya están almacenados y disponibles en Azure Monitor durante un período de tiempo. 
 - Las métricas se almacenan durante 90 días. 
 - Las entradas de registros de actividad se almacenan durante 90 días. 
 - Los registros de diagnóstico no se almacenan.
 - Archive los datos en una cuenta de almacenamiento para conservarlos más tiempo.

**Consultar**  

Puede usar la API REST de Azure Monitor, los comandos de la interfaz de la línea de comandos (CLI) multiplataforma, los cmdlets de PowerShell o .NET SDK para acceder a los datos del sistema o los datos almacenados en Azure. 

**Visualización**

Ver los datos de supervisión en gráficos y diagramas ayuda a encontrar tendencias más rápidamente que si se buscan en los propios datos. 

Algunos métodos de visualización incluyen:
 - Uso del portal de usuarios y del administrador de Azure Stack Hub
 - Enrutamiento de datos a Microsoft Power BI
 - Enrutamiento de datos a una herramienta de visualización de terceros mediante streaming en vivo o haciendo que la herramienta lea un archivo de almacenamiento de Azure

## <a name="methods-of-accessing-azure-monitor-on-azure-stack-hub"></a>Métodos para acceder a Azure Monitor en Azure Stack Hub

Puede manipular el seguimiento, el enrutamiento y la recuperación de los datos mediante uno de los métodos siguientes. No todos los métodos están disponibles para todas las acciones o tipos de datos. 

 - [Portal de usuarios de Azure Stack Hub](azure-stack-use-portal.md)
 - [PowerShell](/azure/monitoring-and-diagnostics/insights-powershell-samples)
 - [Interfaz de la línea de comandos (CLI) multiplataforma](/azure/monitoring-and-diagnostics/insights-cli-samples)
 - [REST API](/rest/api/monitor)
 - [SDK de .NET](https://www.nuget.org/packages/Microsoft.Azure.Management.Monitor)

> [!Important]  
> Si encuentra un error de **recurso no encontrado** al ver el gráfico de rendimiento de una máquina virtual, asegúrese de que ha registrado Microsoft.Insights en la suscripción asociada a la máquina virtual.

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca del consumo de datos de supervisión de Azure Stack Hub, consulte el artículo [Consumo de datos de supervisión de Azure Stack Hub](azure-stack-metrics-monitor.md).
