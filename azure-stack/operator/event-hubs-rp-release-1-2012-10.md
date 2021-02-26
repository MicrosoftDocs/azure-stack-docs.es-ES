---
title: Notas de la versión de Event Hubs en Azure Stack Hub 1.2012.1.0
description: Obtenga información sobre la versión 1.2012.1.0 de Event Hubs en Azure Stack Hub, incluidas las correcciones de errores, las características y cómo instalar la actualización.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: article
ms.date: 02/15/2021
ms.reviewer: kalkeea
ms.lastreviewed: 02/15/2021
ms.openlocfilehash: ef28447b87c0647b65c996112c5739bf7c6caccf
ms.sourcegitcommit: 34babe5abf1bceee718011b5c5c25f75e1b03b0c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/17/2021
ms.locfileid: "100563533"
---
# <a name="event-hubs-on-azure-stack-hub-1201210-release-notes"></a>Notas de la versión de Event Hubs en Azure Stack Hub 1.2012.1.0

En estas notas de la versión se describen las mejoras y correcciones en la versión 1.2012.1.0 de Event Hubs en Azure Stack Hub y los problemas conocidos. 

[!INCLUDE [Azure Stack Hub update reminder](../includes/event-hubs-hub-update-banner.md)]

## <a name="updates-in-this-release"></a>Actualizaciones de esta versión

En esta versión se incluyen las siguientes actualizaciones:

- Para los desarrolladores del SDK de Azure Portal, ahora se admite la versión del portal 5.0.303.7361.
- Mejoras del registro interno para clústeres de Event Hubs.

## <a name="issues-fixed-in-this-release"></a>Problemas corregidos en esta versión

En esta versión se incluyen las siguientes correcciones:

- Corrección del orden de actualización de los clústeres de Event Hubs para solucionar un problema de actualización.
- La comprobación del estado del clúster y de las copias de seguridad de los clústeres de Event Hubs no se estaba ejecutando cuando los clústeres presentaban el estado "Actualizando" o "Error de actualización". El problema se ha corregido en esta versión.
- Se ha corregido un error que hacía que los registros de uso contuvieran la cantidad equivocada. En lugar de núcleos, se estaban emitiendo unidades de capacidad. Anteriormente, un clúster de 1 CU mostraba 1 núcleo en el uso por horas. Los usuarios ahora verán la cantidad correcta de 10 núcleos para un clúster de 1 CU en su uso por horas.

## <a name="known-issues"></a>Problemas conocidos 

No hay ningún problema conocido en esta versión.

## <a name="next-steps"></a>Pasos siguientes

- Para más información, consulte [Información general sobre el operador de Event Hubs en Azure Stack Hub](event-hubs-rp-overview.md).

