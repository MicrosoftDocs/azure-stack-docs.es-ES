---
title: Introducción a los planes, ofertas, cuotas y suscripciones de Azure Stack | Microsoft Docs
description: Como operador de nube, es importante que comprenda los planes, ofertas, cuotas y suscripciones de Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 3dc92e5c-c004-49db-9a94-783f1f798b98
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/26/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 10/12/2018
ms.openlocfilehash: 8aa75e3a66fbec2494a653fb62405fe6c9134d7d
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985208"
---
# <a name="plan-offer-quota-and-subscription-overview"></a>Introducción a los planes, ofertas, cuotas y suscripciones

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

[Azure Stack](azure-stack-overview.md) le permite proporcionar una amplia variedad de servicios, como máquinas virtuales, bases de datos SQL Server, SharePoint, Exchange e incluso [elementos de Azure Marketplace](azure-stack-marketplace-azure-items.md). Como operador de Azure Stack, debe configurar y entregar esos servicios en Azure Stack por medio de planes, ofertas y cuotas.

Las ofertas contienen uno o varios planes y cada plan incluye uno o varios servicios. Al crear planes y combinarlos en diferentes ofertas, puede administrar:

- Los servicios y los recursos a los que pueden acceder los usuarios.
- La cantidad de recursos que los usuarios pueden consumir.
- Qué regiones tienen acceso a los recursos.

Al ofrecer un servicio, siga estos pasos de alto nivel:

1. Agregar un servicio que quiere proporcionar a los usuarios.
2. Cree un plan que tenga uno o varios servicios. Al crear un plan, seleccione o cree cuotas que definan los límites de recursos de cada servicio en el plan.
3. Cree una oferta que contenga uno o varios planes. La oferta puede incluir planes base y planes complementarios opcionales.

Después de haber creado la oferta, los usuarios pueden suscribirse a ella para acceder a los servicios y recursos que la oferta proporciona. Los usuarios pueden suscribirse a todas las ofertas que quieran. En la siguiente ilustración se muestra un ejemplo sencillo de un usuario que se ha suscrito a dos ofertas. Cada oferta tiene un plan o dos, y cada plan le proporciona acceso a los servicios.

![Suscripción de inquilinos con ofertas y planes](media/azure-stack-key-features/image4.png)

## <a name="plans"></a>Planes

Los planes son agrupaciones de uno o varios servicios. Como operador de Azure Stack, [creará planes](azure-stack-create-plan.md) para ofrecer a sus usuarios. A su vez, los usuarios se suscriben a las ofertas para usar los planes y los servicios que incluyen. Cuando cree los planes, asegúrese de establecer las cuotas, definir los planes base y considerar la inclusión de planes complementarios opcionales.

### <a name="quotas"></a>Cuotas

Para ayudarle a administrar la capacidad de la nube, puede usar *cuotas* configuradas previamente o crear una nueva para cada servicio de un plan. Las cuotas definen los límites de recursos superiores que puede aprovisionar o consumir una suscripción de usuario. Por ejemplo, una cuota podría permitir que un usuario creara hasta cinco máquinas virtuales.

> [!IMPORTANT]
> Pueden pasar hasta dos horas hasta que las nuevas cuotas estén disponibles en el portal del usuario o antes de que se aplique una cuota modificada.

Puede configurar cuotas por región. Por ejemplo, un plan que proporciona servicios de proceso para la región A puede tener una cuota de dos máquinas virtuales.

>[!NOTE]
>En el Kit de desarrollo de Azure Stack, solo hay una región (denominada *local*) disponible.

Obtenga más información sobre los [tipos de cuota en Azure Stack](azure-stack-quota-types.md).

### <a name="base-plan"></a>Plan base

Al crear una oferta, el administrador de servicios puede incluir un plan base. Estos planes se incluyen de forma predeterminada cuando un usuario se suscribe a la oferta. Cuando un usuario se suscribe, tiene acceso a todos los proveedores de recursos especificados en dichos planes base (con las cuotas correspondientes).

### <a name="add-on-plans"></a>Planes complementarios

Los planes complementarios son planes opcionales que puede agregar a una oferta. Dichos planes no se incluyen de forma predeterminada en la suscripción. Los planes complementarios son planes adicionales (cuotas) disponibles en una oferta que un suscriptor puede agregar a sus suscripciones. Por ejemplo, puede ofrecer un plan base con recursos limitados para una versión de prueba y un plan complementario con recursos más abundantes para los clientes que decidan adoptar el servicio.

## <a name="offers"></a>Ofertas

Las ofertas son grupos de uno o varios planes que se crean para que los usuarios puedan suscribirse a ellas. Por ejemplo, la Oferta Alfa puede incluir el Plan A, que proporciona un conjunto de servicios Compute y el Plan B, que contiene un conjunto de servicios Storage y Network.

Cuando se [crea una oferta](azure-stack-create-offer.md), debe incluir al menos un plan base, pero también puede crear planes complementarios que los usuarios pueden agregar a su suscripción.

## <a name="subscriptions"></a>Suscripciones

Una suscripción es la forma en que los inquilinos acceden a las ofertas. Si es un operador de Azure Stack de un proveedor de servicios, los usuarios (inquilinos) le compran sus servicios mediante la suscripción a sus ofertas. Si es un operador de Azure Stack de una organización, los usuarios (empleados) pueden suscribirse a los servicios que les ofrece sin pagar.

Cada combinación de un usuario con una oferta es una suscripción única. Un usuario puede tener suscripciones a varias ofertas, pero cada suscripción solo se aplica a una oferta. Los planes, las ofertas y las cuotas solo se aplican a una suscripción, no se pueden compartir entre varias. Cada recurso que crea un usuario está asociado a una suscripción.

### <a name="default-provider-subscription"></a>Suscripción de proveedor predeterminada

La suscripción de proveedor predeterminada se crea automáticamente al implementar el Kit de desarrollo de Azure Stack. Esta suscripción puede utilizarse para administrar Azure Stack, implementar proveedores de recursos adicionales y crear ofertas y planes para los usuarios. Por motivos de seguridad y concesión de licencias, no debe usarse para ejecutar aplicaciones y cargas de trabajo de cliente.

## <a name="next-steps"></a>Pasos siguientes

Para más información acerca de los planes y las ofertas, consulte el artículo de [Creación de un plan](azure-stack-create-plan.md).
