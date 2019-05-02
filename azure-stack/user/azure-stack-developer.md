---
title: Desarrollo de aplicaciones para Azure Stack | Microsoft Docs
description: Consideraciones a la hora de desarrollar prototipos de aplicaciones en Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/05/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 2f0d311851abe1c0fb01ec08dc82626805b35eb1
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64310353"
---
# <a name="develop-for-azure-stack"></a>Desarrollo para Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede empezar a desarrollar aplicaciones hoy mismo, incluso si no tiene acceso a un entorno de Azure Stack. Dado que Azure Stack proporciona servicios de Microsoft Azure que se ejecutan en el centro de datos, puede usar herramientas y procesos similares para desarrollar con Azure Stack tal como lo haría con Azure.

## <a name="development-considerations"></a>Consideraciones de desarrollo

Con un poco de preparación y la orientación que le proporcionarán los siguientes temas, puede usar Azure para emular un entorno de Azure Stack.

* En Azure, puede crear plantillas de Azure Resource Manager que pueden implementarse en Azure Stack. Consulte el artículo sobre [consideraciones de las plantillas](azure-stack-develop-templates.md) para obtener ayuda acerca de cómo desarrollar las plantillas para garantizar la portabilidad.
* Existen diferencias en la disponibilidad y la versión del servicio entre Azure y Azure Stack. Puede usar el [módulo de directivas de Azure Stack](azure-stack-policy-module.md) para restringir los tipos de recursos y la disponibilidad del servicio de Azure a fin de adaptarlos a la disponibilidad de Azure Stack. La restricción de los servicios permitirá que las aplicaciones se basen en los servicios disponibles de Azure Stack.
* Las [plantillas de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) son ejemplos comunes de escenarios que muestran cómo desarrollar plantillas que puedan implementarse en Azure y Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos para obtener más información sobre el desarrollo de Azure Stack:

* [Azure Resource Manager template best practices](azure-stack-develop-templates.md) (Procedimientos recomendados de plantillas de Azure Resource Manager)
* [Plantillas de inicio rápido de Azure Stack en GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates)