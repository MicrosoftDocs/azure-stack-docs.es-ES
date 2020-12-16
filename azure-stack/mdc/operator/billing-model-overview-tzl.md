---
title: Introducción al modelo de facturación de clientes en Azure Stack | Microsoft Docs
description: Conozca cómo se factura a los usuarios de Azure Stack por el uso de los recursos.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/04/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: bd20dee177ac1f061b954eb0285b3103747675d1
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96934852"
---
# <a name="billing-model-overview"></a>Introducción al modelo de facturación

Como usuario del Centro de datos modular o de Azure Stack Hub resistente, Microsoft se encarga de su facturación en función del tiempo de uso de cada dispositivo. El cargo es por período e incluye el derecho a usar los servicios básicos de proceso, almacenamiento y redes. Se le cobrará en función del uso de App Service, Event Hubs y cualquier otro servicio PaaS, así como por las máquinas virtuales con pago por uso de Windows Server que se ejecutan en Azure Stack Hub Ruggedized y MDC. Si está totalmente sin conexión y no puede notificar los datos de uso, debe obtener licencias de capacidad para los servicios PaaS y traer sus propias licencias para las máquinas virtuales Windows.

La facturación comienza 14 días después de enviar un pedido para un dispositivo. En los contenedores de Azure Stack Hub, el período de facturación inicial es de un año. Después de un año, el pedido se puede ampliar durante un año adicional y se facturará otro año de uso. Si el pedido no se amplía y no se devuelve el dispositivo, la facturación continuará mensualmente. En el caso de Azure Stack Hub resistente, la facturación siempre es mensual.

Si el dispositivo se devuelve a Microsoft, la facturación se detiene en cuanto se recibe el dispositivo en el almacén de datos de Microsoft.

## <a name="next-steps"></a>Pasos siguientes

[Referencia de la API Usage](analyze-usage-tzl.md)
