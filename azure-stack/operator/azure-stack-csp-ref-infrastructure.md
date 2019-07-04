---
title: Infraestructura de informes de uso para proveedores de servicios en la nube | Microsoft Docs
description: Azure Stack incluye la infraestructura necesaria para realizar el seguimiento del uso para inquilinos a los que proporciona servicio un proveedor de servicios en la nube (CSP) cuando se produce y lo desvía a Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 0b4ff1e6c76bedc4618bfa527b0045d7bfce41af
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419472"
---
# <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Infraestructura de informes de uso para proveedores de servicios en la nube

Azure Stack incluye la infraestructura necesaria para realizar el seguimiento del uso cuando se produce y lo desvía a Azure. En Azure, Azure Commerce procesa los datos de uso y cobra el uso a las suscripciones apropiadas de Azure. Esto sucede de la misma manera que se supervisa el seguimiento del uso que tiene lugar en la nube global de Azure.

Hay determinados conceptos que son coherentes entre Azure global y Azure Stack. Azure Stack tiene suscripciones locales, que cumplen un rol similar al de una suscripción de Azure. Las suscripciones locales solo son válidas localmente. Las suscripciones locales se asignan a las suscripciones de Azure cuando uso se desvía a Azure.

Azure Stack tiene medidores de uso local. El uso local se asigna a los medidores utilizados en Azure Commerce. Sin embargo, los identificadores de los medidores son diferentes. Hay más medidores disponibles de forma local que el que usa Microsoft para facturación.

Hay algunas diferencias entre cómo los servicios están valorados en Azure Stack y Azure. Por ejemplo, en Azure Stack, el cargo por las máquinas virtuales solo se basa en núcleo virtual/horas, con la misma tasa para todas las series de máquinas virtuales, a diferencia de Azure. El motivo es que en Azure global los distintos precios reflejan hardware diferente. En Azure Stack, el cliente proporciona el hardware, así que no hay ninguna razón para cobrar distintas tasas para diferentes clases de máquinas virtuales.

Puede informarse sobre los medidores de Azure Stack utilizados en Commerce y sus precios en el centro de partners, de la misma manera que lo haría para los servicios de Azure:

1. En el Centro de partners, vaya al **menú del panel**, seleccione **Vender** y, a continuación, seleccione **Precios y ofertas**.
2. En **Usage-based services** (Servicios basados en uso), seleccione **Current** (Actual).
3. Abra la hoja de cálculo **Azure in Global CSP price list** (Lista de precios de Azure en CSP global).
4. Filtre por **Region = Azure Stack** (Región = Azure Stack).

## <a name="terms-used-for-billing-and-usage"></a>Términos que se usan para la facturación y el uso

Los siguientes términos y conceptos se usan para el uso y la facturación en Azure Stack:

| Término | Definición |
| --- | --- |
| Asociado de CSP directo | Un asociado del proveedor de servicios en la nube (CSP) directo recibe una factura directamente de Microsoft por el uso de Azure y de Azure Stack, y factura directamente a los clientes. |
| CSP indirecto | Los revendedores indirectos trabajan con un proveedor indirecto (también conocido como distribuidor). Los revendedores reclutan clientes finales; el proveedor indirecto mantiene la relación de facturación con Microsoft, administra la facturación de clientes y proporciona servicios adicionales como soporte técnico de productos. |
| Cliente final | Los clientes finales son las empresas y los organismos gubernamentales que poseen las aplicaciones y otras cargas de trabajo que se ejecutan en Azure Stack. |

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca del programa CSP, consulte [Cloud Solutions](https://partner.microsoft.com/solutions/microsoft-cloud-solutions) (Soluciones en la nube).
- Para más información sobre cómo recuperar la información de utilización de recursos de Azure Stack, consulte [Uso y facturación en Azure Stack](azure-stack-billing-and-chargeback.md).
