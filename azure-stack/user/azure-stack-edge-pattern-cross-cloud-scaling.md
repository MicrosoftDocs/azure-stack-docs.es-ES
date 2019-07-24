---
title: Patrón de escalado de toda la nube para la inteligencia perimetral con Azure Stack | Microsoft Docs
description: Obtenga información sobre el patrón de escalado de toda la nube para la inteligencia perimetral con Azure Stack
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
ms.date: 07/11/2019
ms.author: mabrigg
ms.reviewer: anajod
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 2b97be7ecfffe83e560d32fbaac6480c8c17bb88
ms.sourcegitcommit: 51ec68b5e6dbf437aaca19a9f35ba07d2c402892
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/12/2019
ms.locfileid: "67856305"
---
# <a name="cross-cloud-scaling-pattern"></a>Patrón de escalado de toda la nube

Agregue recursos automáticamente a una aplicación existente para dar cabida a un aumento de la carga.

## <a name="context-and-problem"></a>Contexto y problema

La aplicación no puede aumentar la capacidad para satisfacer un aumento inesperado de la demanda. Esto hace que los usuarios no puedan acceder a la aplicación durante los momentos de uso máximo. La aplicación puede atender a un número fijo de usuarios.

Las empresas globales requieren aplicaciones basadas en la nube seguras, confiables y disponibles. Por este motivo, es vital satisfacer los aumentos de la demanda y usar la infraestructura adecuada para admitir esa demanda. Las empresas luchan por equilibrar los costos y el mantenimiento con la seguridad, el almacenamiento y la disponibilidad en tiempo real de los datos empresariales.

Es posible que no pueda ejecutar la aplicación en la nube pública. Sin embargo, puede que no sea económicamente viable para la empresa mantener la capacidad necesaria en el entorno local para controlar los picos en la demanda de la aplicación. Con este patrón, puede usar la elasticidad de la nube pública con la solución local.

## <a name="solution"></a>Solución

El patrón de escalado de toda la nube amplía una aplicación ubicada en una nube local con los recursos de nube pública. El patrón se desencadena por un aumento o una disminución de la demanda y, respectivamente, agrega recursos a la nube o los quita de esta. Estos recursos proporcionan redundancia, disponibilidad rápida y enrutamiento compatible con las geoáreas.

![Patrón de escalado de toda la nube](media/azure-stack-edge-pattern-cross-cloud-scaling/cross-cloud-scaling.png)

> Nota:  
> Este patrón solo se aplica a las aplicaciones sin estado.

El patrón de escalado de toda la nube consta de los siguientes componentes.

**Traffic Manager**  
** En el diagrama esto se encuentra fuera del grupo de la nube pública, pero debería ser capaz de coordinar el tráfico tanto en el centro de datos local como en la nube pública. El equilibrador ofrece alta disponibilidad para la aplicación mediante la supervisión de puntos de conexión y la redistribución de la conmutación por error cuando es necesario.

**Sistema de nombres de dominio (DNS)**  
El sistema de nombres de dominio, o DNS, es responsable de traducir (o resolver) el nombre del sitio web o del servicio en su dirección IP.

### <a name="cloud"></a>Nube

**Servidor de compilación hospedado**  
Un entorno para hospedar la canalización de compilación.

**Recursos de aplicación**  
Los recursos de la aplicación deben ser capaces de reducirse y escalarse horizontalmente, como los conjuntos de escalado de máquinas virtuales y los contenedores.

**Nombre de dominio personalizado**  
Use un nombre de dominio personalizado para el enrutamiento global de las solicitudes.

**Direcciones IP públicas**  
Las direcciones IP públicas se usan para enrutar el tráfico entrante a través del administrador de tráfico al punto de conexión de recursos de aplicación de la nube pública.  

### <a name="local-cloud"></a>Nube local

**Servidor de compilación hospedado**  
Un entorno para hospedar la canalización de compilación.

**Recursos de aplicación**  
Los recursos de la aplicación deben ser capaces de reducirse y escalarse horizontalmente, como los conjuntos de escalado de máquinas virtuales y los contenedores.

**Nombre de dominio personalizado**  
Use un nombre de dominio personalizado para el enrutamiento global de las solicitudes.

**Direcciones IP públicas**  
Las direcciones IP públicas se usan para enrutar el tráfico entrante a través del administrador de tráfico al punto de conexión de recursos de aplicación de la nube pública. 

## <a name="issues-and-considerations"></a>Problemas y consideraciones


Tenga en cuenta los puntos siguientes al decidir cómo implementar este patrón:

### <a name="scalability-considerations"></a>Consideraciones sobre escalabilidad

El componente clave del escalado de toda la nube es la posibilidad de proporcionar un escalado a petición entre la infraestructura en la nube pública y local, que ofrezca unos servicios coherentes y confiables según la demanda.

### <a name="availability-considerations"></a>Consideraciones sobre disponibilidad

Asegúrese de que las aplicaciones implementadas localmente están configuradas para una alta disponibilidad mediante la configuración del hardware local y la implementación de software.

### <a name="manageability-considerations"></a>Consideraciones sobre la manejabilidad

El patrón de toda la nube garantiza una administración sin problemas y una interfaz familiar entre entornos.

#### <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón

Use este patrón:

-   Cuando necesite aumentar la capacidad de la aplicación con demandas inesperadas o peticiones periódicas en la demanda.

-   Si no desea invertir en recursos que solo se utilizarán durante los picos. Pague por lo que usa.

No se recomienda este patrón si:

-   La solución requiere que los usuarios se conecten mediante Internet.

-   Su empresa tiene normativas locales que requieren que la conexión de origen proceda de una llamada in situ.

-   La red experimenta cuellos de botella regulares que restringirían el rendimiento del escalado.

-   Su entorno está desconectado de Internet y no puede acceder a la nube pública.

## <a name="example"></a>Ejemplo

Aprenda a crear una solución para toda la nube que proporcione un proceso desencadenado manualmente para cambiar de una aplicación web hospedada en Azure Stack a una aplicación web hospedada en Azure con escalado automático mediante Traffic Manager, con la garantía de una utilidad en la nube flexible y escalable cuando hay poca carga.

[Creación de soluciones de escalado de toda la nube con Azure](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-cloud-burst)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre los [Modelos de diseño de nube híbrida para Azure Stack](azure-stack-edge-pattern-overview.md)
