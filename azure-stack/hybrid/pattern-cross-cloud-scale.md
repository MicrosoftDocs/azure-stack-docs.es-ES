---
title: Patrón para compilar una aplicación que escale entre nubes, en Azure y Azure Stack Hub.
description: Aprenda a usar Azure y Azure Stack Hub para compilar una aplicación entre nubes escalable.
author: BryanLa
ms.service: azure-stack
ms.topic: article
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: 3bd29e8625fac818d05954720d84d572b684c92e
ms.sourcegitcommit: b96a0b151b9c0d3eea59e7c2d39119a913782624
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/07/2020
ms.locfileid: "75718018"
---
# <a name="cross-cloud-scaling-pattern"></a>Patrón de escalado de toda la nube

Agregue recursos automáticamente a una aplicación existente para dar cabida a un aumento de la carga.

## <a name="context-and-problem"></a>Contexto y problema

La aplicación no puede aumentar la capacidad para satisfacer un aumento inesperado de la demanda. Esta falta de escalabilidad hace que los usuarios no puedan acceder a la aplicación durante los momentos de uso máximo. La aplicación puede atender a un número fijo de usuarios.

Las empresas globales requieren aplicaciones basadas en la nube seguras, confiables y disponibles. Por este motivo, es vital satisfacer los aumentos de la demanda y usar la infraestructura adecuada para admitir esa demanda. Las empresas luchan por equilibrar los costos y el mantenimiento con la seguridad, el almacenamiento y la disponibilidad en tiempo real de los datos empresariales.

Es posible que no pueda ejecutar la aplicación en la nube pública. Sin embargo, puede que no sea económicamente viable para la empresa mantener la capacidad necesaria en el entorno local para controlar los picos en la demanda de la aplicación. Con este patrón, puede usar la elasticidad de la nube pública con la solución local.

## <a name="solution"></a>Solución

El patrón de escalado de toda la nube amplía una aplicación ubicada en una nube local con los recursos de nube pública. El patrón se desencadena por un aumento o una disminución de la demanda y, respectivamente, agrega recursos a la nube o los quita de esta. Estos recursos proporcionan redundancia, disponibilidad rápida y enrutamiento compatible con las geoáreas.

![Patrón de escalado de toda la nube](media/pattern-cross-cloud-scale/cross-cloud-scaling.png)

> [!NOTE]
> Este patrón solo se aplica a los componentes sin estado de su aplicación.

## <a name="components"></a>Componentes

El patrón de escalado entre nubes consta de los siguientes componentes.

**Traffic Manager**  

En el diagrama esto se encuentra fuera del grupo de la nube pública, pero debería ser capaz de coordinar el tráfico tanto en el centro de datos local como en la nube pública. El equilibrador ofrece alta disponibilidad para la aplicación mediante la supervisión de puntos de conexión y la redistribución de la conmutación por error cuando es necesario.

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
Los recursos de la aplicación necesitan la capacidad de reducirse y escalarse horizontalmente, como los conjuntos de escalado de máquinas virtuales y los contenedores.

**Nombre de dominio personalizado**  
Use un nombre de dominio personalizado para el enrutamiento global de las solicitudes.

**Direcciones IP públicas**  
Las direcciones IP públicas se usan para enrutar el tráfico entrante a través del administrador de tráfico al punto de conexión de recursos de aplicación de la nube pública. 

## <a name="issues-and-considerations"></a>Problemas y consideraciones

Tenga en cuenta los puntos siguientes al decidir cómo implementar este patrón:

### <a name="scalability"></a>Escalabilidad

El componente clave del escalado entre nubes es la capacidad de ofrecer escalado a petición. El escalado debe realizarse entre la infraestructura en la nube pública y local, así como ofrecer un servicio coherente y de confianza de acuerdo con la demanda.

### <a name="availability"></a>Disponibilidad

Asegúrese de que las aplicaciones implementadas localmente están configuradas para una alta disponibilidad mediante la configuración del hardware local y la implementación de software.

### <a name="manageability"></a>Facilidad de uso

El patrón de toda la nube garantiza una administración sin problemas y una interfaz familiar entre entornos.

## <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón

Use este patrón:

- Cuando necesite aumentar la capacidad de la aplicación con demandas inesperadas o peticiones periódicas en la demanda.
- Si no desea invertir en recursos que solo se utilizarán durante los picos. Pague por lo que usa.

No se recomienda este patrón si:

- La solución requiere que los usuarios se conecten mediante Internet.
- Su empresa tiene normativas locales que requieren que la conexión de origen proceda de una llamada in situ.
- La red experimenta cuellos de botella regulares que restringirían el rendimiento del escalado.
- Su entorno está desconectado de Internet y no puede acceder a la nube pública.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los temas presentados en este artículo:
- Consulte la [información general sobre Azure Traffic Manager](/azure/traffic-manager/traffic-manager-overview) para más información sobre cómo funciona este equilibrador de carga de tráfico basado en DNS.
- Consulte [Consideraciones sobre el diseño de aplicaciones híbridas](overview-app-design-considerations.md) para más información sobre los procedimientos recomendados y responder a preguntas adicionales.
- Consulte la información relativa a la [familia de productos y soluciones de Azure Stack](/azure-stack) para más información sobre toda la gama de productos y soluciones.

Cuando esté listo para probar la solución de ejemplo, continúe con la [guía de implementación de soluciones de escalado entre nubes](solution-deployment-guide-cross-cloud-scaling.md). La guía de implementación proporciona instrucciones paso a paso para implementar y probar sus componentes. Aprenderá a crear una solución entre nubes que proporcione un proceso desencadenado manualmente para cambiar de una aplicación web hospedada en Azure Stack Hub a una aplicación web hospedada en Azure. También aprenderá a usar el escalado automático a través de Traffic Manager, garantizando una utilidad en la nube flexible y escalable bajo carga.
