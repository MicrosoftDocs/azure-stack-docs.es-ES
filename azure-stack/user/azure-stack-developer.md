---
title: Desarrollo de aplicaciones para Azure Stack Hub | Microsoft Docs
description: Consideraciones a la hora de desarrollar prototipos de aplicaciones en Azure Stack Hub mediante los servicios de Azure.
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
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: ba5aefa61db489f5f7063ebc4785785ba2f26f4c
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2020
ms.locfileid: "75883735"
---
# <a name="develop-for-azure-stack-hub"></a>Desarrollo para Azure Stack Hub

Puede empezar a desarrollar aplicaciones hoy mismo aunque no tenga acceso a un entorno de Azure Stack Hub. Azure Stack Hub proporciona los servicios de Microsoft Azure que se ejecutan en su centro de datos, lo que significa que puede usar las mismas herramientas y procesos de Azure para desarrollar en Azure Stack Hub.

## <a name="development-considerations"></a>Consideraciones de desarrollo

Con un poco de preparación y la guía que le proporcionarán los siguientes temas, puede usar Azure para emular un entorno de Azure Stack Hub.

* En Azure, puede crear plantillas de Azure Resource Manager que pueden implementarse en Azure Stack Hub. Consulte el artículo sobre [consideraciones de las plantillas](azure-stack-develop-templates.md) para obtener ayuda acerca de cómo desarrollar las plantillas para garantizar la portabilidad.
* Existen diferencias en cuento a disponibilidad y versión del servicio entre Azure y Azure Stack Hub. Puede usar el [módulo de directivas de Azure Stack Hub](azure-stack-policy-module.md) para restringir los tipos de recursos y la disponibilidad del servicio de Azure para adaptarlos a la disponibilidad de Azure Stack Hub. La restricción de los servicios garantiza que las aplicaciones usan los servicios disponibles para Azure Stack Hub.
* Las [plantillas de inicio rápido de Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates) son ejemplos comunes de escenarios que muestran cómo desarrollar plantillas que puedan implementarse en Azure y Azure Stack Hub.

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el desarrollo de Azure Stack Hub, consulte los siguientes artículos:

* [Azure Resource Manager template best practices](azure-stack-develop-templates.md) (Procedimientos recomendados de plantillas de Azure Resource Manager)
* [Plantillas de inicio rápido de Azure Stack Hub en GitHub](https://github.com/Azure/AzureStack-QuickStart-Templates)
