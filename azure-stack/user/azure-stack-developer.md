---
title: Desarrollo de aplicaciones para Azure Stack | Microsoft Docs
description: Consideraciones a la hora de desarrollar prototipos de aplicaciones en Azure Stack mediante los servicios de Azure.
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
ms.date: 05/21/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: 059e7961cae5c6d6faa8d79d05dbc08a05a13893
ms.sourcegitcommit: b3dac698f2e1834491c2f9af56a80e95654f11f3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/30/2019
ms.locfileid: "68658564"
---
# <a name="develop-for-azure-stack"></a>Desarrollo para Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede empezar a desarrollar aplicaciones hoy mismo, incluso si no tiene acceso a un entorno de Azure Stack. Azure Stack proporciona los servicios de Microsoft Azure que se ejecutan en su centro de datos, lo que significa que puede usar las mismas herramientas y procesos de Azure para desarrollar en Azure Stack.

## <a name="development-considerations"></a>Consideraciones de desarrollo

Con un poco de preparación y la orientación que le proporcionarán los siguientes temas, puede usar Azure para emular un entorno de Azure Stack.

* En Azure, puede crear plantillas de Azure Resource Manager que pueden implementarse en Azure Stack. Consulte el artículo sobre [consideraciones de las plantillas](azure-stack-develop-templates.md) para obtener ayuda acerca de cómo desarrollar las plantillas para garantizar la portabilidad.
* Existen diferencias en la disponibilidad y la versión del servicio entre Azure y Azure Stack. Puede usar el [módulo de directivas de Azure Stack](azure-stack-policy-module.md) para restringir los tipos de recursos y la disponibilidad del servicio de Azure a fin de adaptarlos a la disponibilidad de Azure Stack. La restricción de los servicios permitirá que las aplicaciones se basen en los servicios disponibles de Azure Stack.
* Las [plantillas de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) son ejemplos comunes de escenarios que muestran cómo desarrollar plantillas que puedan implementarse en Azure y Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

Consulte los siguientes artículos para obtener más información sobre el desarrollo de Azure Stack:

* [Azure Resource Manager template best practices](azure-stack-develop-templates.md) (Procedimientos recomendados de plantillas de Azure Resource Manager)
* [Plantillas de inicio rápido de Azure Stack en GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates)