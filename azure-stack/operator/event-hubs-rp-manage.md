---
title: Cómo administrar Event Hubs en Azure Stack Hub
description: Aprenda a administrar el proveedor de recursos de Event Hubs en Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: e9f500da4cab68688a6dd33374cfbc1cc166828d
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/31/2020
ms.locfileid: "80424641"
---
# <a name="how-to-manage-event-hubs-on-azure-stack-hub"></a>Cómo administrar Event Hubs en Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

La experiencia de administración de Event Hubs permite controlar el servicio y visualizar su estado y sus alertas. 

## <a name="overview"></a>Información general

Siga estos pasos para acceder a la página de administración de Event Hubs:

1. Inicie sesión en el Portal de administración de Azure Stack Hub.
2. En el panel de la izquierda, seleccione **Todos los servicios**.
3. Busque "Event Hubs" y seleccione el servicio. Si no encuentra el servicio de Event Hubs, se debe [instalar](event-hubs-rp-install.md) primero el proveedor de recursos.
4. Se muestra la página de información general de administración de Event Hubs. Encontrará cuatro secciones en el panel izquierdo:
   - **Información general**: proporciona una vista general y acceso a áreas de administración específicas.
   - **Alertas**: muestra todas las alertas críticas y de advertencia de Event Hubs. Consulte la sección [Alertas](#alerts) para más información.
   - **Cuotas**: permite crear, actualizar y eliminar cuotas. Consulte la sección [Cuotas](#quotas) para más información.
   - **Clústeres de Event Hubs**: muestra una lista de todos los clústeres configurados. Para más información, consulte la sección [Clústeres de Event Hubs](#event-hubs-clusters).

   [![Administración de Event Hubs](media/event-hubs-rp-manage/1-manage-event-hubs.png)](media/event-hubs-rp-manage/1-manage-event-hubs.png#lightbox)

## <a name="quotas"></a>Cuotas

Al seleccionar **Cuotas** en la página principal, se muestra la lista de cuotas en uso, incluidos los planes asociados que especifican las cuotas. 
 
[![Administración de Event Hubs: cuotas](media/event-hubs-rp-manage/3-quotas.png)](media/event-hubs-rp-manage/3-quotas.png#lightbox)

Para más información sobre los tipos de cuota definidos para Event Hubs, consulte [Tipos de cuotas](azure-stack-quota-types.md#event-hubs-quota-types)

## <a name="alerts"></a>Alertas

El proveedor de recursos de Event Hubs admite las siguientes alertas:
   
| Category | Alerta | Tipo | Condición |
|----------|-------|------|-----------|
| Rendimiento | | | |
| | EventHub-CpuUsage | Advertencia | El promedio de % de uso de CPU del clúster de Event Hubs en las últimas seis horas es superior al 50 %. |
| | EventHub-MemoryUsage | Advertencia | El promedio de % de uso de disco de datos (E:) del clúster de Event Hubs en las últimas seis horas es mayor del 50 %. |
| | EventHub-DiskUsage | Advertencia | El promedio de % de espacio libre en memoria del clúster de Event Hubs en las últimas seis horas es inferior al 50 %. |
| Uso o cuota | | | |
| | EventHub-QuotaExceeded | Advertencia | Se ha producido un error de cuota superada en las últimas seis horas. |
| | EventHub-NamespaceCreditUsage | Advertencia | La suma de los usos de los créditos del espacio de nombres en las últimas seis horas es mayor que 10 000. |
| Servicio degradado | | | |
| | EventHub-InternalServerError | Advertencia | Se ha producido un error interno del servidor en el servicio en las últimas seis horas. |
| | EventHub-ServerBusy | Advertencia | Se ha producido un error de servidor ocupado en las últimas seis horas. |
| Remoto | | | |
| | EventHub-ClientError | Advertencia | Se ha producido un error de cliente en las últimas seis horas. |
| Resource | | | |
| | EventHub-PendingDeletingResources | Advertencia | La suma de los recursos pendientes de eliminación en las últimas seis horas es mayor que 100. |
| | EventHub-ProvisioningQueueLength | Advertencia | El promedio de longitud de cola de aprovisionamiento en las últimas seis horas es mayor que 30. |

La selección de **Alertas** en la página principal muestra la lista de alertas emitidas:

[![Administración de Event Hubs: resumen de alertas](media/event-hubs-rp-manage/2-alerts-summary.png)](media/event-hubs-rp-manage/2-alerts-summary.png#lightbox)

Al seleccionar una alerta de la lista, se muestra el panel **Detalles de alertas** a la derecha:

[![Administración de Event hubs: detalles de alertas](media/event-hubs-rp-manage/2-alerts-detail.png)](media/event-hubs-rp-manage/2-alerts-detail.png#lightbox)

Para más información sobre la funcionalidad de supervisión de Azure Stack Hub, incluidas las alertas, consulte [Supervisión de estado y alertas](azure-stack-monitor-health.md). Para más información sobre la recopilación de registros, consulte [Introducción a la recopilación de registros de diagnóstico de Azure Stack Hub](azure-stack-diagnostic-log-collection-overview.md).

## <a name="event-hubs-clusters"></a>Clústeres de Event Hubs

Al seleccionar **Clústeres de Event Hubs** en la página principal, se muestra una lista de los clústeres de usuarios disponibles. La lista incluye lo siguiente para cada clúster:

- Información de configuración importante.
- Estado del servicio.
- Estado de copia de seguridad.

[![Administración de Event Hubs: recursos de usuario](media/event-hubs-rp-manage/4-user-resources.png)](media/event-hubs-rp-manage/4-user-resources.png#lightbox)

Al seleccionar un vínculo en **Estado** o en **Copia de seguridad**, se mostrará información detallada sobre el estado de Event Hubs y sobre el estado de la copia de seguridad, respectivamente. En el vínculo que aparece en **Nombre**, se muestra más información sobre clúster:
- Información de estado y configuración.
- Una lista de los límites de servicio del clúster.

[![Administración de Event Hubs: clústeres de usuario](media/event-hubs-rp-manage/4-user-clusters.png)](media/event-hubs-rp-manage/4-user-clusters.png#lightbox)

Los límites de servicio son parámetros de configuración que definen los límites operativos de Event Hubs. Los límites de servicio disponibles son similares a los que se ofrecen en Azure Event Hubs dedicado. Al seleccionar los vínculos en **Config value** (Valor de configuración), puede cambiar el valor asignado.

> [!IMPORTANT]
> Debe dedicar tiempo a analizar todas las implicaciones antes de cambiar los límites de servicio. Los cambios en el límite de servicio pueden afectar al comportamiento de la solución que consume y genera eventos. Los cambios también pueden afectar al consumo de recursos de la capacidad de Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre:

- Los tipos de cuota definidos para Event Hubs, consulte [Tipos de cuotas](azure-stack-quota-types.md#event-hubs-quota-types).
- Las funcionalidades de supervisión de Azure Stack Hub, incluidas las alertas, consulte [Supervisión de estado y alertas](azure-stack-monitor-health.md). 
- La colección de registros de Azure Stack Hub, consulte [Introducción a la recopilación de registros de diagnóstico de Azure Stack Hub](azure-stack-diagnostic-log-collection-overview.md).













