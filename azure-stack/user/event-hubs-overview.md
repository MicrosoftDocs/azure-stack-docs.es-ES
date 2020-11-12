---
title: Introducción a Event Hubs en Azure Stack Hub
description: Obtenga mas información sobre Event Hubs en Azure Stack Hub. Consulte cómo crear soluciones híbridas. Compare las características de Azure Event Hubs y Event Hubs en Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/27/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: adb00ebbe3f5ea3cf233ebaa485913d48ea63e23
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94546963"
---
# <a name="overview-of-event-hubs-on-azure-stack-hub"></a>Introducción a Event Hubs en Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Event Hubs en Azure Stack Hub permite desarrollar escenarios de nube híbrida. Se admiten las soluciones de streaming y basadas en eventos, tanto en el procesamiento local como en la nube de Azure. Tanto si el escenario es híbrido (conectado) como desconectado, la solución puede admitir el procesamiento de eventos o secuencias a gran escala. El escenario solo está limitado por el tamaño del clúster de Event Hubs, que puede aprovisionar según sus necesidades. 

## <a name="run-event-processing-tasks-and-build-event-driven-applications-on-site"></a>Ejecución de tareas de procesamiento de eventos y creación de aplicaciones controladas por eventos en el sitio

Con Event Hubs en Azure Stack Hub, puede implementar escenarios empresariales como los siguientes:

- Cargas de trabajo de inteligencia artificial y aprendizaje automático en las que Event Hubs es el motor de streaming de eventos.
- Implemente arquitecturas basadas en eventos en sus propios sitios fuera de los centros de datos de Azure.
- Análisis de clics para aplicaciones web implementadas de forma local.
- Análisis de datos de telemetría de dispositivos.
- Procesamiento de flujos con marcos de trabajo de código abierto que usan Apache Kafka, como Apache Spark, Flink, Storm y Samza.
- [Consumo de eventos y métricas del sistema operativo invitado de proceso](azure-stack-metrics-monitor.md).

## <a name="build-hybrid-solutions"></a>Creación de soluciones híbridas

Cree soluciones híbridas que ingieren y procesan datos perimetrales localmente en Azure Stack Hub. Envíe datos agregados a Azure para su posterior procesamiento, visualización y almacenamiento. Si es necesario, aproveche la informática sin servidor en Azure.

[![Diagrama de soluciones híbridas](media/event-hubs-overview/hybrid-architecture-ehoash.png)](media/event-hubs-overview/hybrid-architecture-ehoash.png#lightbox)

## <a name="features"></a>Características 

Las ediciones de Event Hubs (en Azure Stack Hub y en Azure) ofrecen un alto grado de paridad de características. Esta paridad significa que los SDK, los ejemplos, PowerShell, la CLI y los portales ofrecen una experiencia similar, con algunas diferencias. En la tabla siguiente se resumen las diferencias más importantes de la disponibilidad de características que existe entre las ediciones.  

| Característica | Event Hubs en Azure Stack Hub | Azure Event Hubs |
|-|-|-|-|
| Experiencia de administrador de operadores | ✔ | ✘ |
| Compatibilidad con Kafka | ✔ | ✔ |
| Mismo conjunto de SDK | ✔ | ✔ |
| Autorización del acceso a Event Hubs mediante Azure Active Directory | ✘ | ✔ |
| Característica de captura | ✘ | ✔ |
| Recuperación ante desastres geográfica | ✘ | ✔ |
| Azure Monitor | ✔ | ✔ |
| Característica de inflado automático | ✘ | ✔ |

Las operaciones de administración de recursos de Azure también se pueden realizar mediante plantillas de Resource Manager, [PowerShell](/powershell/module/Az.eventhub/) y la [CLI de Azure](/cli/azure/eventhubs/eventhub/). Actualmente, no se admiten las operaciones de administración de operadores en PowerShell ni en la CLI de Azure.

## <a name="feature-documentation"></a>Documentación de características

La [documentación de Azure Event Hubs](/azure/event-hubs/) se aplica a las dos ediciones de Event Hubs: Event Hubs en Azure Stack Hub y Azure Event Hubs. Esta documentación trata temas sobre el uso de Event Hubs y actividades como:

- Detalles sobre los [conceptos de Event Hubs](/azure/event-hubs/event-hubs-features)
- [Creación de un clúster y un espacio de nombres de Event Hubs](event-hubs-quickstart-cluster-portal.md)
- [Creación de un centro de eventos](/azure/event-hubs/event-hubs-create#create-an-event-hub)
- [Transmisión mediante el protocolo Kafka](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)

### <a name="operator-documentation"></a>Documentación del operador 
 
Para más información acerca de Event Hubs en la experiencia de operador de Azure Stack Hub, consulte la [documentación del operador de Event Hubs](../operator/event-hubs-rp-overview.md). En esta documentación se proporciona información sobre actividades como las siguientes:

- Instalar Event Hubs.
- Poner Event Hubs a disposición de los usuarios.
- Obtener información sobre el estado del servicio.
- Recopilar registros.


## <a name="next-steps"></a>Pasos siguientes

Si Event Hubs no está disponible en su suscripción, colabore con el administrador para [instalar Event Hubs en el proveedor de recursos de Azure Stack Hub](../operator/event-hubs-rp-overview.md).

Si tiene Event Hubs instalado y está preparado para usarlo, consulte la [documentación de Event Hubs](/azure/event-hubs/event-hubs-about) para obtener más detalles sobre el servicio.
