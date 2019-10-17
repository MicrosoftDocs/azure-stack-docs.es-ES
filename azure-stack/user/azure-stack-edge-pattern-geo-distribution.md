---
title: Patrón DevOps para la inteligencia perimetral con Azure Stack | Microsoft Docs
description: Obtenga información sobre el patrón DevOps para la inteligencia perimetral con Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: deab520045d50acefb03691b9b127f99676061a0
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277715"
---
# <a name="geo-distributed-pattern"></a>Patrón distribuido geográficamente

Proporcione puntos de conexión de la aplicación en varias regiones. Enrute el tráfico de usuarios en función de las necesidades de cumplimiento y ubicación.

## <a name="context-and-problem"></a>Contexto y problema

Las organizaciones con zonas geográficas de gran alcance intentan distribuir y permitir el acceso a los datos de forma segura y precisa, a la vez que garantizan los niveles necesarios de seguridad, cumplimiento y rendimiento por usuario, ubicación y dispositivo en todos los límites.

## <a name="solution"></a>Solución

El patrón de enrutamiento de tráfico geográfico de Azure Stack, o las aplicaciones distribuidas geográficamente, permite que el tráfico se dirija a puntos de conexión específicos en función de varias métricas. La creación de una instancia de Traffic Manager con la configuración de puntos de conexión y enrutamiento geográfico enruta el tráfico a puntos de conexión en función de los requisitos regionales, la legislación internacional y corporativa y su necesidad en cuanto a los datos.

![Patrón distribuido geográficamente](media/azure-stack-edge-pattern-geo-distribution/geo-distribution.png)

**Traffic Manager**  
En el diagrama esto se encuentra fuera de la nube pública, pero debería ser capaz de coordinar el tráfico tanto en el centro de datos local como en la nube pública. El equilibrador enruta el tráfico a las ubicaciones geográficas.

**Sistema de nombres de dominio (DNS)**  
El sistema de nombres de dominio, o DNS, es responsable de traducir (o resolver) el nombre del sitio web o del servicio en su dirección IP.

### <a name="public-cloud"></a>Nube pública

**Punto de conexión en la nube**  
Las direcciones IP públicas se usan para enrutar el tráfico entrante a través del administrador de tráfico al punto de conexión de recursos de aplicación de la nube pública.  

### <a name="local-clouds"></a>Nubes locales

**Punto de conexión local**  
Las direcciones IP públicas se usan para enrutar el tráfico entrante a través del administrador de tráfico al punto de conexión de recursos de aplicación de la nube pública.

## <a name="issues-and-considerations"></a>Problemas y consideraciones

A la hora de decidir cómo implementar este patrón, debe considerar los siguientes puntos:

### <a name="scalability-considerations"></a>Consideraciones sobre escalabilidad

El patrón controla el enrutamiento del tráfico geográfico en lugar de escalar para satisfacer los aumentos del tráfico. Sin embargo, puede combinar este patrón con otras soluciones locales y de Azure. Por ejemplo, este patrón se puede usar con el patrón de escalado de toda la nube.

### <a name="availability-considerations"></a>Consideraciones sobre disponibilidad

Asegúrese de que las aplicaciones implementadas localmente están configuradas para una alta disponibilidad mediante la configuración del hardware local y la implementación de software.

### <a name="manageability-considerations"></a>Consideraciones sobre la manejabilidad

El patrón garantiza una administración sin problemas y una interfaz familiar entre entornos.

## <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón

-   Mi organización tiene oficinas internacionales que requieren una seguridad regional personalizada y directivas de distribución.

-   Cada una de las oficinas de mi organización extrae datos de los empleados, el negocio y las instalaciones, que requieren informes de actividad para cada normativa local y zona horaria.

-   Se pueden cumplir los requisitos de gran escala mediante el escalado horizontal de las aplicaciones, con la realización de varias implementaciones de la aplicación en una única región, así como entre regiones, para afrontar los requisitos extremos de carga.

-   Las aplicaciones deben tener alta disponibilidad y responder a las solicitudes de los clientes incluso en caso de una interrupción en una sola región.

## <a name="example"></a>Ejemplo

Aprenda a dirigir el tráfico a puntos de conexión específicos en función de varias métricas con el patrón de aplicaciones distribuidas geográficamente. La creación de un perfil de Traffic Manager con la configuración de puntos de conexión y enrutamiento geográfico garantiza que la información se enruta a puntos de conexión en función de los requisitos regionales, la legislación internacional y corporativa, y su necesidad en cuanto a los datos.

[Creación de una solución de aplicación distribuida geográficamente con Azure y Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-geo-distributed)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre los [Modelos de diseño de nube híbrida para Azure Stack](azure-stack-edge-pattern-overview.md)
