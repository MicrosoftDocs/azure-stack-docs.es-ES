---
title: Introducción al Kit de desarrollo de Azure Stack (ASDK) | Microsoft Docs
description: Describe qué es ASDK y los casos de uso comunes para evaluar Microsoft Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc
ms.date: 02/08/2019
ms.author: mabrigg
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 08f5d51eb4ded47d1e52ec53d4c75903d13c8815
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617680"
---
# <a name="what-is-the-azure-stack-development-kit"></a>¿Qué es el Kit de desarrollo de Azure Stack?
El tamaño de los [sistemas integrados de Microsoft Azure Stack](../operator/azure-stack-overview.md) va de los 4 a los 16 nodos y tiene un soporte técnico conjunto por parte de un asociado de hardware y Microsoft. Use los sistemas integrados de Azure Stack para permitir nuevos escenarios para las cargas de trabajo de producción. Si es un operador de Azure Stack que administra la infraestructura de sistemas integrados y ofrece servicios, consulte la [documentación del operador](/azure-stack/operator).

El Kit de desarrollo de Azure Stack (ASDK) es una implementación de un único nodo de Azure Stack que puede descargar y usar de **forma gratuita**. Todos los componentes de ASDK se instalan en máquinas virtuales que se ejecutan en un equipo servidor de un solo host, que debe cumplir o superar los [requisitos mínimos de hardware](asdk-deploy-considerations.md#hardware). El Kit de desarrollo de Azure Stack está pensado para proporcionar un entorno en el que pueda evaluar Azure Stack y desarrollar aplicaciones actuales con API y herramientas coherentes con Azure en un entorno *que no sea de producción*. 

> [!IMPORTANT]
> El ASDK no está diseñado para ejecutarse en un entorno de producción.

Como todos los componentes del ASDK se implementan en un único equipo host del kit de desarrollo, hay recursos físicos limitados disponibles. Con las implementaciones del ASDK, las máquinas virtuales de infraestructura de Azure Stack y las máquinas virtuales de inquilino coexisten en el mismo equipo servidor. Esta configuración no está pensada para la evaluación del rendimiento o la escala.

El ASDK está diseñado para proporcionar una experiencia de nube híbrida coherente con Azure para:
- **Administradores** (operadores de Azure Stack). El ASDK es un excelente recurso para evaluar los servicios disponibles de Azure Stack, y obtener información sobre ellos.
- **Desarrolladores**. El ASDK puede usarse para desarrollar aplicaciones modernas o híbridas en entornos locales (entornos de desarrollo y pruebas). Esto permite repetir la experiencia de desarrollo antes o al mismo tiempo que las implementaciones de producción de Azure Stack. 

Vea este breve vídeo para obtener más información sobre el ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-differences"></a>El ASDK y las diferencias con Azure Stack multinodo
Las implementaciones del ASDK en un solo nodo difieren de las implementaciones de Azure Stack multinodo en algunos aspectos importantes que hay que tener en cuenta.

|DESCRIPCIÓN|ASDK|Azure Stack multinodo|
|-----|-----|-----|
|**Escala**|Todos los componentes se instalan en un equipo servidor de un solo nodo.|Puede oscilar entre 4 y 16 nodos.|
|**Resistencia**|La configuración de un solo nodo no proporciona alta disponibilidad|Se admiten funcionalidades de [alta disponibilidad](../operator/azure-stack-overview.md#providing-high-availability).|
|**Redes**|El host de ASDK enruta todo el tráfico de red de ASDK. No hay ningún requisito de conmutación adicional.|En implementaciones de varios nodos se necesita una [infraestructura de enrutamiento de red](../operator/azure-stack-network.md#network-infrastructure) más compleja, que incluya conmutadores de la parte superior del bastidor (TOR), de controlador de administración de placa base (BMC) y perimetrales (red de centro de datos).|
|**Proceso de revisión y actualización**|Para cambiar a una nueva versión del ASDK, debe volver a implementar el ASDK en el equipo host del kit de desarrollo.|El proceso de [revisión y actualización](../operator/azure-stack-updates.md) se utiliza para actualizar la versión instalada de Azure Stack.|
|**Soporte técnico**|Foro de Azure Stack en MSDN. El Servicio de atención al cliente y soporte técnico de Microsoft (CSS) *no* tiene ningún soporte técnico disponible para entornos que no sean de producción.|[Foro de Azure Stack en MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) y soporte técnico completo de CSS.|
| | |

## <a name="learn-about-available-services"></a>Información sobre los servicios disponibles
Como operador de Azure Stack, debe saber qué servicios puede poner a disposición de los usuarios. Azure Stack admite un subconjunto de los servicios de Azure y la lista de servicios admitidos continuará evolucionando con el tiempo.

### <a name="foundational-services"></a>Servicios fundamentales
De forma predeterminada, Azure Stack incluye los siguientes “servicios fundamentales” cuando se implementa el ASDK:
- Proceso
- Almacenamiento
- Redes
- Key Vault

Con estos servicios fundamentales, puede ofrecer la infraestructura como servicio (IaaS) a los usuarios con una configuración mínima.

### <a name="additional-services"></a>Servicios adicionales
Actualmente, se admiten los siguientes servicios de plataforma como servicio (PaaS) adicionales:
- App Service
- Azure Functions
- Bases de datos SQL y MySQL

> [!NOTE]
> Estos servicios requieren una configuración adicional para que pueda ponerlos a disposición de los usuarios y no están disponibles de forma predeterminada al instalar el ASDK.

## <a name="service-roadmap"></a>Mapa de ruta de los servicios
Azure Stack continuará agregando compatibilidad con otros servicios de Azure. Para más información acerca de las próximas novedades de Azure Stack, consulte el [mapa de ruta de Azure Stack](https://azure.microsoft.com/roadmap/?tag=azure-stack). 


## <a name="next-steps"></a>Pasos siguientes
Para empezar a evaluar Azure Stack, en primer lugar hay que [descargar el ASDK más reciente](asdk-download.md) y preparar el equipo host de ASDK. Después de preparar el host del kit de desarrollo, puede instalar el ASDK e iniciar sesión en los portales de administrador y usuario para empezar a usar Azure Stack.