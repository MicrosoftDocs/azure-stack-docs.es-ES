---
title: Patrón de aplicación distribuida geográficamente en Azure Stack Hub
description: Conozca el patrón de aplicación distribuida geográficamente para la inteligencia perimetral mediante Azure y Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod2019
ms.openlocfilehash: 1f6243927390c7a520c2607c722664b2d31fc07f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "80891049"
---
# <a name="geo-distributed-app-pattern"></a>Patrón de aplicación distribuida geográficamente

Sepa cómo proporcionar puntos de conexión de aplicación entre varias regiones y enrutar el tráfico de los usuarios en función de las necesidades de ubicación y cumplimiento.

## <a name="context-and-problem"></a>Contexto y problema

Las organizaciones con zonas geográficas de gran alcance intentan distribuir y permitir el acceso a los datos de forma segura y precisa, a la vez que garantizan los niveles necesarios de seguridad, cumplimiento y rendimiento por usuario, ubicación y dispositivo en todos los límites.

## <a name="solution"></a>Solución

El patrón de enrutamiento del tráfico geográfico de Azure Stack Hub o las aplicaciones distribuidas geográficamente, permiten que el tráfico se dirija a puntos de conexión específicos en función de varias métricas. La creación de una instancia de Traffic Manager con la configuración de puntos de conexión y enrutamiento geográfico enruta el tráfico a puntos de conexión en función de los requisitos regionales, la legislación internacional y corporativa y su necesidad en cuanto a los datos.

![Patrón distribuido geográficamente](media/pattern-geo-distributed/geo-distribution.png)

## <a name="components"></a>Componentes

### <a name="outside-the-cloud"></a>Fuera de la nube

#### <a name="traffic-manager"></a>Traffic Manager

En el diagrama, Traffic Manager se encuentra fuera de la nube pública, pero tiene que ser capaz de coordinar el tráfico tanto en el centro de datos local como en la nube pública. El equilibrador enruta el tráfico a las ubicaciones geográficas.

#### <a name="domain-name-system-dns"></a>Sistema de nombres de dominio (DNS)

El sistema de nombres de dominio, o DNS, es responsable de traducir (o resolver) el nombre del sitio web o del servicio en su dirección IP.

### <a name="public-cloud"></a>Nube pública

#### <a name="cloud-endpoint"></a>Punto de conexión de nube

Las direcciones IP públicas se usan para enrutar el tráfico entrante mediante Traffic Manager al punto de conexión de recursos de aplicación de la nube pública.  

### <a name="local-clouds"></a>Nubes locales

#### <a name="local-endpoint"></a>Punto de conexión local

Las direcciones IP públicas se usan para enrutar el tráfico entrante mediante Traffic Manager al punto de conexión de recursos de aplicación de la nube pública.

## <a name="issues-and-considerations"></a>Problemas y consideraciones

Tenga en cuenta los puntos siguientes al decidir cómo implementar este patrón:

### <a name="scalability"></a>Escalabilidad

El patrón controla el enrutamiento del tráfico geográfico en lugar de escalar para satisfacer los aumentos del tráfico. Sin embargo, puede combinar este patrón con otras soluciones locales y de Azure. Por ejemplo, este patrón se puede usar con el patrón de escalado de toda la nube.

### <a name="availability"></a>Disponibilidad

Asegúrese de que las aplicaciones implementadas localmente están configuradas para una alta disponibilidad mediante la configuración del hardware local y la implementación de software.

### <a name="manageability"></a>Facilidad de uso

El patrón garantiza una administración sin problemas y una interfaz familiar entre entornos.

## <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón

- Mi organización tiene oficinas internacionales que requieren una seguridad regional personalizada y directivas de distribución.
- Cada una de las oficinas de mi organización extrae datos de empleados, negocio e instalaciones, que requieren informes de actividad para cada normativa local y zona horaria.
- Se pueden cumplir los requisitos de gran escala mediante el escalado horizontal de las aplicaciones, con la realización de varias implementaciones de la aplicación en una única región, así como entre regiones, para afrontar los requisitos extremos de carga.
- Las aplicaciones deben tener alta disponibilidad y responder a las solicitudes de los clientes incluso en caso de una interrupción en una sola región.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los temas presentados en este artículo:

- Consulte la [información general sobre Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) para más información sobre cómo funciona este equilibrador de carga de tráfico basado en DNS.
- Consulte [Consideraciones sobre el diseño de aplicaciones híbridas](overview-app-design-considerations.md) para más información sobre los procedimientos recomendados y para obtener respuestas a preguntas adicionales.
- Consulte la información relativa a la [familia de productos y soluciones de Azure Stack](/azure-stack) para más información sobre toda la gama de productos y soluciones.

Cuando esté listo para probar la solución de ejemplo, continúe con la [guía de implementación de la solución de aplicaciones distribuidas geográficamente](solution-deployment-guide-geo-distributed.md). La guía de implementación proporciona instrucciones paso a paso para implementar y probar sus componentes. Aprenda a dirigir el tráfico a puntos de conexión específicos en función de varias métricas con el patrón de aplicaciones distribuidas geográficamente. La creación de un perfil de Traffic Manager con la configuración de puntos de conexión y enrutamiento geográfico garantiza que la información se enruta a puntos de conexión en función de los requisitos regionales, la legislación internacional y corporativa, y su necesidad en cuanto a los datos.
