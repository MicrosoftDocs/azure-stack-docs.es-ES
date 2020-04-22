---
title: Ejemplos de soluciones y patrones híbridos de Azure y Azure Stack Hub
description: Información general sobre ejemplos de soluciones y patrones híbridos para aprender y compilar soluciones híbridas en Azure y Azure Stack Hub.
author: BryanLa
ms.topic: overview
ms.date: 11/05/2019
ms.author: bryanla
ms.reviewer: anajod
ms.lastreviewed: 11/05/2019
ms.openlocfilehash: d56957c139728898117eab5f8a3a35c98722c0d2
ms.sourcegitcommit: 4138a2a15f78e7db38b3a29acc963a71937146fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/15/2020
ms.locfileid: "80812312"
---
# <a name="hybrid-patterns-and-solution-examples-for-azure-and-azure-stack"></a>Ejemplos de soluciones y patrones híbridos de Azure y Azure Stack

Microsoft proporciona soluciones y productos de Azure y Azure Stack como un ecosistema coherente de Azure. La familia de productos de Microsoft Azure Stack es una extensión de Azure.

## <a name="the-hybrid-cloud-and-hybrid-apps"></a>Nube híbrida y aplicaciones híbridas

Azure Stack lleva la agilidad de la informática en la nube a su entorno local y al perímetro mediante una *nube híbrida*. Azure Stack Hub, Azure Stack HCI y Azure Stack Edge llevan Azure desde la nube a sus centros de datos soberanos, sucursales, centros de trabajo, etc. Con este conjunto diverso de funcionalidades, puede:

- Reutilizar el código y ejecutar aplicaciones nativas en la nube de forma coherente entre Azure y los entornos locales.
- Ejecutar cargas de trabajo virtualizadas tradicionales con conexiones opcionales a los servicios de Azure.
- Transferir datos a la nube o mantenerlos en su centro de datos soberano para mantener el cumplimiento.
- Ejecutar cargas de trabajo virtualizadas o en contenedores, de aprendizaje automático y aceleradas mediante hardware, y todo ello con la inteligencia perimetral.

Las aplicaciones que abarcan las nubes también se conocen como *aplicaciones híbridas*. Puede compilar las aplicaciones en la nube híbrida en Azure e implementarlas en su centro de datos conectado o desconectado ubicado en cualquier lugar.

Los escenarios de aplicaciones híbridas varían significativamente según los recursos que haya disponibles para el desarrollo. También abarcan aspectos como la región geográfica, la seguridad, el acceso a Internet, etc. Aunque tal vez las soluciones y los patrones que se describen aquí no den respuesta a todos los requisitos, proporcionan instrucciones y ejemplos que se pueden explorar y reutilizar durante la implementación de soluciones híbridas.

## <a name="design-patterns"></a>Patrones de diseño

Los patrones de diseño ofrecen guías de diseño repetibles y generalizadas a partir de escenarios y experiencias de clientes reales. Un patrón es abstracto, lo que permite que se pueda aplicar a distintos tipos de escenarios o sectores verticales. Cada patrón documenta el contexto y el problema, y proporciona información general sobre una solución de ejemplo. La solución de ejemplo está pensada como una posible implementación del patrón.

Hay dos tipos de artículos sobre patrones:

- Patrón único: proporciona una guía de diseño para un único escenario de uso general.
- Varios patrones: proporciona una guía de diseño en la que se usa la aplicación de varios patrones. Este patrón suele ser necesario para resolver escenarios más complejos o problemas específicos del sector.

## <a name="solution-deployment-guides"></a>Guías de implementación de soluciones

Las guías de implementación paso a paso ayudan en la implementación de una solución de ejemplo. La guía también puede hacer referencia a un ejemplo de código complementario que está almacenado en el [repositorio de soluciones de ejemplo](https://github.com/Azure-Samples/azure-intelligent-edge-patterns) de GitHub.

## <a name="next-steps"></a>Pasos siguientes

- Consulte la información relativa a la [familia de productos y soluciones de Azure Stack](/azure-stack) para más información sobre toda la gama de productos y soluciones.
- Explore las secciones sobre patrones y guías de implementación de soluciones de la tabla de contenido para más información sobre cada una de ellas.
- Lea el artículo sobre [Consideraciones de diseño para aplicaciones híbridas](overview-app-design-considerations.md) para revisar los pilares de la calidad del software para diseñar, implementar y usar aplicaciones híbridas.
- [Configure un entorno de desarrollo en Azure Stack](../user/azure-stack-dev-start.md) e [implemente su primera aplicación](../user/azure-stack-dev-start-deploy-app.md) en Azure Stack.
