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
ms.date: 12/23/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: c889ebc0bc7b4074b375ebb4e64d1d38c7fcd177
ms.sourcegitcommit: 9ecf9c58fbcc4bc42c1fdc688f370c643c761a29
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/04/2020
ms.locfileid: "93330295"
---
# <a name="billing-model-overview"></a>Introducción al modelo de facturación

Como usuario del Centro de datos modular o de Azure Stack Hub resistente, Microsoft se encarga de su facturación en función del tiempo de uso de cada dispositivo. El cargo es por período de tiempo e incluye el derecho a usar los servicios básicos de proceso, almacenamiento y red, App Service, IoT Hub y sus requisitos previos, y servicios auxiliares como Key Vault. También se incluye el uso de Windows Server como sistema operativo invitado.

El uso real no se mide y no se envía a Microsoft.

La facturación comienza 14 días después de enviar un pedido para un dispositivo. En los contenedores de Azure Stack Hub, el período de facturación inicial es de un año. Después de un año, el pedido se puede ampliar durante un año adicional y se facturará otro año de uso. Si el pedido no se amplía y no se devuelve el dispositivo, la facturación continuará mensualmente. En el caso de Azure Stack Hub resistente, la facturación siempre es mensual.

Si el dispositivo se devuelve a Microsoft, la facturación se detiene en cuanto se recibe el dispositivo en el almacén de datos de Microsoft.

## <a name="next-steps"></a>Pasos siguientes

[Referencia de la API Usage](analyze-usage-tzl.md)
