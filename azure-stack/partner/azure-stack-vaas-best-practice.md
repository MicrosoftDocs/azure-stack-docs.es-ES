---
title: Procedimientos recomendados para la validación de Azure Stack. | Microsoft Docs
description: En este artículo se incluyen procedimientos recomendados para la validación como servicio.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 03/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 6b4e74cce10522fc241c7662ed381793bd264093
ms.sourcegitcommit: b95983e6e954e772ca5267304cfe6a0dab1cfcab
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/23/2019
ms.locfileid: "68418568"
---
# <a name="best-practices-for-validation-as-a-service"></a>Procedimientos recomendados de la validación como servicio

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

En este artículo se abordan los procedimientos recomendados para administrar recursos en la validación como servicio (VaaS). Para obtener información general de los recursos de VaaS, consulte [Conceptos clave de la validación como servicio](azure-stack-vaas-key-concepts.md).

## <a name="solution-management"></a>Administración de soluciones

### <a name="naming-convention-for-vaas-solutions"></a>Convención de nomenclatura para las soluciones VaaS

Use una convención de nomenclatura coherente para todas las soluciones registradas en VaaS. Por ejemplo, el nombre de la solución se puede construir a partir de las propiedades de hardware siguientes, como se muestra a continuación:

|Nombre de producto | Elemento de hardware único 1 | Elemento de hardware único 2 | Nombre de la solución
|---|---|---|---|
My Solution XYZ |  All Flash | My Switch X01 | MySolutionXYZ_AllFlash_MySwitchX01

### <a name="when-to-create-a-new-vaas-solution"></a>Cuándo se debe crear una nueva solución VaaS

Use la misma solución VaaS al ejecutar los flujos de trabajo en la misma SKU de hardware. Debe crearse una nueva solución VaaS solo cuando haya un cambio en la SKU de hardware.

## <a name="workflow-management"></a>Administración de flujos de trabajo

### <a name="naming-convention-for-vaas-workflows"></a>Convención de nomenclatura para los flujos de trabajo VaaS

Use una convención de nomenclatura coherente para todas las ejecuciones de flujos de trabajo VaaS. Por ejemplo, construya un nombre de flujo de trabajo a partir de las siguientes propiedades de compilación como se indica a continuación:

|Húmero de compilación (mayor) | Date | Tamaño de la solución | Nombre del flujo de trabajo
|---|---|---| ---|
1808 | 081518 | 4NODE | 1808_081518_4NODE

## <a name="next-steps"></a>Pasos siguientes

- Más información sobre [Conceptos clave de la validación como servicio](azure-stack-vaas-key-concepts.md)
