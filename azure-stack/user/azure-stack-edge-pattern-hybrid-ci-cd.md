---
title: Patrón DevOps para la inteligencia perimetral con Azure Stack | Microsoft Docs
description: Obtenga información sobre el patrón DevOps para la inteligencia perimetral con Azure Stack
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
ms.openlocfilehash: 04eff0f095f14d88443fc4b221799e63f523c82c
ms.sourcegitcommit: a6d47164c13f651c54ea0986d825e637e1f77018
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72277010"
---
# <a name="devops-pattern"></a>Patrón DevOps

Programe desde una única ubicación y realice la implementación en varios destinos en entornos de desarrollo, pruebas y producción que pueden estar ubicados en el centro de datos local, en nubes privadas o en la nube pública.

## <a name="context-and-problem"></a>Contexto y problema

La continuidad, la seguridad y la confiabilidad de la implementación de las aplicaciones resultan fundamentales para las organizaciones y vitales para los equipos de desarrollo.

A menudo, las aplicaciones requieren la refactorización de código para ejecutarse en cada entorno de destino. Esto significa que la aplicación no es totalmente portátil. Debe actualizarse, probarse y validarse a medida que se traslada a cada entorno. Por ejemplo, el código escrito en un entorno de desarrollo debe volver a escribirse para que funcione en un entorno de pruebas y debe volver a escribirse cuando finalmente llega a un entorno de producción. Además, este código está vinculado específicamente al host, lo que aumenta el costo y la complejidad del mantenimiento de la aplicación. Cada versión de la aplicación está vinculada a cada entorno. Esta mayor complejidad y la duplicación aumentan los riesgos relacionados con la seguridad y la calidad del código. Además, el código no se puede volver a implementar fácilmente cuando se eliminan los hosts con error de restauración o se implementan hosts adicionales para administrar los aumentos de la demanda.

## <a name="solution"></a>Solución

El patrón DevOps le permite compilar, probar e implementar una aplicación que se ejecuta en varias nubes. Este patrón une las prácticas de integración continua y de entrega continua. Con la integración continua, el código se compila y se prueba cada vez que un miembro del equipo confirma un cambio en el control de versiones. La entrega continua automatiza cada paso de una compilación a un entorno de producción. Juntos, estos procesos crean un proceso de versión que admite la implementación en diversos entornos. Con este patrón, puede preparar el código y, a continuación, implementarlo en un entorno local, en nubes privadas diferentes y en nubes públicas. Las diferencias en el entorno requieren realizar un cambio en un archivo de configuración en lugar de cambios en el código.

![Patrón DevOps](media/azure-stack-edge-pattern-hybrid-ci-cd/hybrid-ci-cd.png)

Con un conjunto coherente de herramientas de desarrollo en entornos locales, de nube privada y de nube pública, puede implementar una práctica de integración continua y entrega continua. Las aplicaciones y los servicios implementados con el patrón DevOps son intercambiables y pueden ejecutarse en cualquiera de estas ubicaciones, aprovechando las características y funcionalidades de los entornos local y de nube pública.

El uso de una canalización de versión de DevOps le ayuda a realizar lo siguiente:

-   Iniciar una nueva compilación basada en confirmaciones de código en un único repositorio.

-   Implementar automáticamente el código recién compilado en la nube pública para llevar a cabo las pruebas de aceptación de usuario.

-   Realizar la implementación automáticamente a una nube privada una vez que el código ha pasado las pruebas.

## <a name="issues-and-considerations"></a>Problemas y consideraciones

El patrón DevOps está pensado para garantizar la coherencia entre las implementaciones, independientemente del entorno de destino. Sin embargo, las funcionalidades varían en los entornos local y de nube. Tenga en cuenta lo siguiente.

-   ¿Las funciones, los puntos de conexión, los servicios y los demás recursos de la implementación están disponibles en las ubicaciones de implementación de destino?

-   ¿Los artefactos de configuración se almacenan en ubicaciones a las que se puede acceder a través de nubes?

-   ¿Los parámetros de implementación funcionarán en todos los entornos de destino?

-   ¿Las propiedades específicas de los recursos están disponibles en todas las nubes de destino?

Para obtener más información, consulte [Desarrollo de plantillas de Azure Resource Manager para mantener la coherencia en la nube](https://docs.microsoft.com/azure/azure-resource-manager/templates-cloud-consistency).

Asimismo, a la hora de decidir cómo implementar este patrón, debe tener en cuenta los siguientes puntos:

### <a name="scalability-considerations"></a>Consideraciones sobre escalabilidad

Los sistemas de automatización de la implementación son el punto de control clave en los patrones de DevOps. Las implementaciones pueden variar. La elección del tamaño correcto del servidor depende del tamaño de la carga de trabajo esperada. Las máquinas virtuales son más difíciles de escalar que los contenedores. Sin embargo, para utilizar contenedores para el escalado, el proceso de compilación debe ejecutarse con contenedores.

### <a name="availability-considerations"></a>Consideraciones sobre disponibilidad

La disponibilidad en el contexto de DevPattern significa poder recuperar cualquier información de estado asociada al flujo de trabajo como, por ejemplo, los resultados de las pruebas, las dependencias de código u otros artefactos. Para evaluar los requisitos de disponibilidad, tenga en cuenta dos métricas comunes:

-   Objetivo de tiempo de recuperación (RTO) especifica cuánto tiempo se puede estar sin un sistema.

-   Objetivo de punto de recuperación (RPO) indica la cantidad de datos que puede permitirse perder si una interrupción del servicio afecta al sistema.

En la práctica, las métricas RTO y el RPO implican redundancia y copia de seguridad. En la nube global de Azure, la disponibilidad no es una cuestión de recuperación de hardware, que forma parte de Azure, sino más bien de garantizar que mantenga el estado de sus sistemas DevOps. En Azure Stack, es posible que deba tenerse en cuenta la recuperación de hardware.

Otro factor importante a tener en cuenta a la hora de diseñar el sistema que se usa para la automatización de la implementación es el control de acceso y la administración adecuada de los derechos necesarios para implementar servicios en entornos de nube. ¿Qué derechos son necesarios para crear, eliminar o modificar implementaciones? Por ejemplo, normalmente se necesita un conjunto de derechos para crear un grupo de recursos en Azure y otro para implementar servicios en el grupo de recursos.

### <a name="manageability-considerations"></a>Consideraciones sobre la manejabilidad

El diseño de cualquier sistema basado en el patrón DevOps debe tener en cuenta la automatización, el registro y las alertas de cada servicio de la cartera. Use servicios compartidos, un equipo de aplicaciones o ambos, y realice un seguimiento de las directivas de seguridad y de la gobernanza.

Implemente entornos de producción y entornos de desarrollo y pruebas en grupos de recursos independientes en Azure o Azure Stack. De este modo, puede supervisar los recursos de cada entorno y acumular los costos de facturación por grupo de recursos. También se pueden eliminar recursos en conjunto, lo que resulta útil cuando se realizan implementaciones de prueba.

## <a name="when-to-use-this-pattern"></a>Cuándo usar este patrón

Use este patrón:

-   Puede desarrollar código en un entorno que satisfaga las necesidades de los desarrolladores e implementarlo en un entorno específico de la solución en el que puede que sea difícil desarrollar código nuevo.

-   Puede usar el código y las herramientas que los desarrolladores quieran, siempre y cuando puedan seguir el proceso de integración continua y entrega continua en el patrón DevOps.

No se recomienda este patrón:

-   Si no puede automatizar la infraestructura, el aprovisionamiento de recursos, la configuración, la identidad y las tareas de seguridad.

-   Si los equipos no tienen acceso a recursos de nube híbrida para implementar un enfoque de integración continua/desarrollo continuo (CI/CD).

## <a name="example"></a>Ejemplo

Obtenga información sobre cómo implementar una aplicación en Azure y Azure Stack mediante una canalización híbrida de integración continua y entrega continua (CI/CD).

[Implementar aplicaciones en Azure y Azure Stack](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-solution-pipeline)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre los [Modelos de diseño de nube híbrida para Azure Stack](azure-stack-edge-pattern-overview.md)
