---
title: Introducción a Event Hubs en Azure Stack Hub
description: Obtenga más información sobre el proveedor de recursos de Event Hubs en Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/27/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: b0a4e18afdc8ce68ffa212bcc7bc78506420c235
ms.sourcegitcommit: dd53af1b0fc2390de162d41e3d59545d1baad1a7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/31/2020
ms.locfileid: "80424743"
---
# <a name="event-hubs-on-azure-stack-hub-overview"></a>Introducción a Event Hubs en Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

Event Hubs en Azure Stack Hub permite desarrollar escenarios de nube híbrida. Se admiten las soluciones de streaming y basadas en eventos, tanto en el procesamiento local como en la nube de Azure. Tanto si el escenario es híbrido (con conexión) como sin conexión, la solución puede admitir el procesamiento de eventos o secuencias a gran escala. El escenario solo está limitado por el tamaño del clúster de Event Hubs, que puede aprovisionar según sus necesidades. 

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

Además, las operaciones de Azure Resource Manager se pueden realizar mediante sus plantillas, [PowerShell](/powershell/module/azurerm.eventhub/) y la [CLI de Azure](/cli/azure/eventhubs/eventhub/). Actualmente, no se admiten las operaciones de administración de operadores en PowerShell ni en la CLI de Azure.

## <a name="feature-documentation"></a>Documentación de características

La [documentación de Azure Event Hubs](/azure/event-hubs/) se aplica a las dos ediciones de Event Hubs: Event Hubs en Azure Stack Hub y Azure Event Hubs. Esta documentación contiene temas sobre el uso de Event Hubs y actividades como:

- Detalles sobre los [conceptos de Event Hubs](/azure/event-hubs/event-hubs-features)
- [Creación de un clúster y un espacio de nombres de Event Hubs](event-hubs-quickstart-cluster-portal.md)
- Creación de un [centro de eventos](/azure/event-hubs/event-hubs-create#create-an-event-hub)
- La transmisión [mediante el protocolo Kafka](/azure/event-hubs/event-hubs-quickstart-kafka-enabled-event-hubs)

### <a name="operator-documentation"></a>Documentación del operador 
 
Para más información acerca de Event Hubs en la experiencia de operador de Azure Stack Hub, consulte la [documentación del operador de Event Hubs](/azure-stack/operator/event-hubs-rp-overview). En esta documentación se proporciona información sobre actividades como las siguientes:

- Instalar Event Hubs.
- Poner Event Hubs a disposición de los usuarios.
- Obtener información sobre el estado del servicio.
- Recopilar registros.


## <a name="next-steps"></a>Pasos siguientes

Si Event Hubs no está disponible en su suscripción, trabaje con el administrador para [instalar el proveedor de recursos de Event Hubs en Azure Event Hubs](../operator/event-hubs-rp-overview.md).

Si tiene Event Hubs instalado y está preparado para usarlo, consulte la [documentación de Event Hubs](/azure/event-hubs/event-hubs-about) para obtener más detalles sobre el servicio.
