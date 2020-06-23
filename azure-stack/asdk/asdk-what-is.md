---
title: ¿Qué es el Kit de desarrollo de Azure Stack? | Microsoft Docs
description: Más información sobre el Kit de desarrollo de Azure Stack y cómo se usa para evaluar Azure Stack Hub.
author: justinha
ms.topic: overview
ms.date: 11/27/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 11/27/2019
ms.openlocfilehash: 197d1b47b3127b21f3b843a77a57642894270a71
ms.sourcegitcommit: 5f4f0ee043ff994efaad44129ce49be43c64d5dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/16/2020
ms.locfileid: "84819240"
---
# <a name="what-is-the-azure-stack-development-kit-asdk"></a>¿Qué es el Kit de desarrollo de Azure Stack?
El tamaño de los [sistemas integrados de Microsoft Azure Stack Hub](../operator/azure-stack-overview.md) va de los 4 a los 16 nodos y recibe soporte técnico conjunto de un asociado de hardware y Microsoft. Use los sistemas integrados de Azure Stack Hub para habilitar nuevos escenarios para las cargas de trabajo de producción. Si es un operador de Azure Stack Hub que administra la infraestructura de sistemas integrados y ofrece servicios, consulte la [documentación para operadores](/azure-stack/operator).

El ASDK es una implementación de un único nodo de Azure Stack Hub que puede descargar y usar de forma **gratuita**. Todos los componentes de ASDK se instalan en máquinas virtuales que se ejecutan en un equipo de un solo host, que debe cumplir o superar los [requisitos mínimos de hardware](asdk-deploy-considerations.md#hardware). El ASDK está pensado para proporcionar un entorno en el que pueda evaluar Azure Stack Hub y desarrollar aplicaciones actuales con API y herramientas coherentes con Azure en un entorno *que no sea de producción*. 

> [!IMPORTANT]
> El ASDK no está diseñado para ejecutarse en un entorno de producción.

Como todos los componentes del ASDK se implementan en un equipo de un solo host, hay recursos físicos limitados disponibles. Con las implementaciones del ASDK, las máquinas virtuales de la infraestructura de Azure Stack Hub y las máquinas virtuales de inquilino coexisten en el mismo equipo servidor. Esta configuración no está pensada para la evaluación del rendimiento o el escalado.

El ASDK está diseñado para proporcionar una experiencia de nube híbrida coherente con Azure para:
- **Administradores** (operadores de Azure Stack Hub): El ASDK es un excelente recurso para evaluar los servicios disponibles de Azure Stack Hub y obtener información sobre ellos.
- **Desarrolladores**: El ASDK puede usarse para desarrollar aplicaciones modernas o híbridas en entornos locales (entornos de desarrollo y pruebas). Esta flexibilidad permite repetir la experiencia de desarrollo antes de las implementaciones de producción de Azure Stack Hub, o durante ellas.

Vea este breve vídeo para obtener más información sobre el ASDK:

> [!VIDEO https://www.youtube.com/embed/dbVWDrl00MM]


## <a name="asdk-and-multi-node-azure-stack-hub-differences"></a>Diferencias entre el ASDK y Azure Stack Hub multinodo
Las implementaciones del ASDK de un solo nodo difieren de las implementaciones de Azure Stack Hub multinodo en algunos aspectos importantes:

|Descripción|ASDK|Azure Stack Hub multinodo|
|-----|-----|-----|
|**Escala**|Todos los componentes se instalan en un equipo servidor de un solo nodo.|Puede oscilar entre 4 y 16 nodos.|
|**Resistencia**|La configuración de un solo nodo no proporciona alta disponibilidad|Se admiten funcionalidades de alta disponibilidad.|
|**Redes**|El host de ASDK enruta todo el tráfico de red de ASDK. No hay ningún requisito de conmutación adicional.|En implementaciones de varios nodos se necesita una [infraestructura de enrutamiento de red](../operator/azure-stack-network.md#network-infrastructure) más compleja, que incluya conmutadores de la parte superior del bastidor (TOR), de controlador de administración de placa base (BMC) y perimetrales (red de centro de datos).|
|**Proceso de revisión y actualización**|Para cambiar a una nueva versión del ASDK, debe volver a implementar el ASDK en el equipo host de ASDK.|Se utiliza un proceso de [revisión y actualización](../operator/azure-stack-updates.md) para actualizar la versión instalada de Azure Stack Hub.|
|**Soporte técnico**|Foro de Azure Stack en MSDN. Soporte técnico de Microsoft *no* tiene ningún soporte técnico disponible para entornos que no sean de producción.|[Foro de Azure Stack en MSDN](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStack) y soporte técnico completo.|
| | |

## <a name="learn-about-available-services"></a>Información sobre los servicios disponibles
Como operador de Azure Stack Hub, debe saber qué servicios puede poner a disposición de los usuarios. Azure Stack Hub admite un subconjunto de los servicios de Azure, y la lista de servicios admitidos continuará evolucionando con el tiempo.

### <a name="foundational-services"></a>Servicios fundamentales
De forma predeterminada, Azure Stack Hub incluye los siguientes "servicios fundamentales" cuando se implementa el ASDK:
- Proceso
- Storage
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
Azure Stack Hub continuará incorporando compatibilidad con otros servicios de Azure. Para más información acerca de las próximas novedades de Azure Stack Hub, consulte el [mapa de ruta de Azure Stack](https://azure.microsoft.com/updates/?query=azure%20stack%20hub).


## <a name="next-steps"></a>Pasos siguientes
Para empezar a evaluar Azure Stack Hub, en primer lugar hay que [descargar el ASDK más reciente](asdk-download.md) y preparar el equipo host de ASDK. A continuación, instale ASDK e inicie sesión en los portales del administrador y de usuarios para comenzar a usar Azure Stack Hub.