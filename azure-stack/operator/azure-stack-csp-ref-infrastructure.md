---
title: Infraestructura de informes de uso para proveedores de soluciones en la nube para Azure Stack Hub
description: Más información sobre la infraestructura de informes de uso que se utiliza para realizar un seguimiento de los inquilinos que atiende un proveedor de soluciones en la nube (CSP).
author: sethmanheim
ms.topic: article
ms.date: 08/27/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: f3d5ba6cfe8f8006de7dd11c03194ebf29ca698c
ms.sourcegitcommit: 28894abb31423046f6775aadef490c467f1b1820
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/29/2020
ms.locfileid: "89089684"
---
# <a name="usage-reporting-infrastructure-for-cloud-solution-providers"></a>Infraestructura de informes de uso para proveedores de soluciones en la nube

Azure Stack Hub incluye la infraestructura necesaria para realizar el seguimiento del uso cuando se produce y lo desvía a Azure. En Azure, Azure Commerce procesa los [datos de uso y cobra el uso](azure-stack-billing-and-chargeback.md) a las suscripciones apropiadas de Azure. Este proceso funciona de la misma manera que el seguimiento del uso que tiene lugar en la nube global de Azure.

Hay algunos conceptos que coinciden en el entorno global de Azure y Azure Stack Hub. Azure Stack Hub tiene suscripciones locales, que cumplen un rol similar al de una suscripción de Azure. Las suscripciones locales solo son válidas localmente. Las suscripciones locales se asignan a las suscripciones de Azure cuando uso se desvía a Azure.

Azure Stack Hub tiene medidores de uso local. El uso local se asigna a los medidores utilizados en Azure Commerce. Sin embargo, los identificadores de los medidores son diferentes. Hay más medidores disponibles de forma local que el que usa Microsoft para facturación.

Hay algunas diferencias entre cómo se tarifan los servicios en Azure Stack Hub y en Azure. Por ejemplo, en Azure Stack Hub, a diferencia de Azure, el cargo por las máquinas virtuales se basa solo en núcleo virtual/horas, con la misma tasa para todas las series de máquinas virtuales. El motivo es que en Azure global los distintos precios reflejan hardware diferente. En Azure Stack Hub, el cliente proporciona el hardware, así que no hay ninguna razón para cobrar distintas tasas para diferentes clases de máquinas virtuales.

Puede informarse sobre los medidores de Azure Stack Hub utilizados en Commerce y sus precios en el Centro de partners. El proceso es el mismo que para los servicios de Azure:

1. En el Centro de partners, vaya al **menú del panel**, seleccione **Vender** y, a continuación, seleccione **Precios y ofertas**.
2. En **Usage-based services** (Servicios basados en uso), seleccione **Current** (Actual).
3. Abra la hoja de cálculo **Azure in Global CSP price list** (Lista de precios de Azure en CSP global).
4. Filtre por **Región = Azure Stack Hub**.

## <a name="terms-used-for-billing-and-usage"></a>Términos que se usan para la facturación y el uso

Los siguientes términos y conceptos se utilizan para el uso y la facturación en Azure Stack Hub:

| Término | Definición |
| --- | --- |
| Asociado de CSP directo | Un asociado de CSP directo recibe una factura directamente de Microsoft por el uso de Azure y de Azure Stack Hub, y factura directamente a los clientes. |
| CSP indirecto | Los revendedores indirectos trabajan con un proveedor indirecto (también conocido como distribuidor). Los revendedores reclutan clientes finales; el proveedor indirecto mantiene la relación de facturación con Microsoft, administra la facturación de clientes y proporciona servicios adicionales como soporte técnico de productos. |
| Cliente final | Los clientes finales son las empresas y los organismos gubernamentales que poseen las aplicaciones y otras cargas de trabajo que se ejecutan en Azure Stack Hub. |

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca del programa CSP, consulte [Cloud Solutions](https://partner.microsoft.com/solutions/microsoft-cloud-solutions) (Soluciones en la nube).
- Para más información sobre cómo recuperar la información del uso de recursos de Azure Stack Hub, consulte [Uso y facturación en Azure Stack Hub](azure-stack-billing-and-chargeback.md).
