---
title: Tareas de evaluación avanzadas de ASDK
description: Obtenga información sobre las tareas de evaluación avanzadas del Kit de desarrollo de Azure Stack (ASDK).
author: justinha
ms.topic: article
ms.date: 02/12/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 2c4a83ea024f83c0181cfe77c598519746deaeed
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77695366"
---
# <a name="advanced-asdk-evaluation-tasks"></a>Tareas de evaluación avanzadas de ASDK
Una vez que se haya familiarizado con las características y funcionalidades básicas del Kit de desarrollo de Azure Stack (ASDK), puede ampliar sus conocimientos acerca de Azure Stack mediante el examen de escenarios más avanzados. Estas tareas de evaluación más avanzadas se documentan de forma muy completa en la documentación del operador de Azure Stack.

> [!NOTE]
> Aunque muchas de las tareas del operador se admiten en las implementaciones de ASDK, de producción y multinodo de Azure Stack, no todos los escenarios de uso son compatibles con las implementaciones de ASDK. Para obtener más información, consulte [El ASDK y las diferencias con Azure Stack multinodo](asdk-what-is.md#asdk-and-multi-node-azure-stack-hub-differences).

## <a name="delegate-offers-in-azure-stack"></a>Delegación de ofertas en Azure Stack
Como operador de Azure Stack, es posible que a menudo desee delegar en otras personas las tareas de creación de ofertas y registro de usuarios. Por ejemplo, si es un proveedor de servicios puede desear que los distribuidores registren a los clientes y los administren en su nombre. Si forma parte de un grupo central de TI en una empresa, puede que quiera que las subsidiarias registren usuarios sin su intervención.

[La delegación de ofertas en Azure Stack](../operator/azure-stack-delegated-provider.md) le ayuda con estas tareas, ya que le permite acceder y administrar mas usuarios de los que podría hacer de forma directa.

## <a name="make-sql-databases-available-to-your-azure-stack-users"></a>Bases de datos SQL disponibles para los usuarios de Azure Stack
Los operadores de Azure Stack pueden crear ofertas que permitan a los usuarios (inquilinos) crear bases de datos SQL que puedan usar con sus aplicaciones nativas de la nube, sitios webs y cargas de trabajo. Si proporciona a los usuarios estas bases de datos personalizadas en la nube y a petición, puede ahorrarles tiempo y recursos.

Use el adaptador del proveedor de recursos de SQL Server para que [los usuarios de Azure Stack puedan disponer de las bases de datos SQL](../operator/azure-stack-tutorial-sql-server.md) como un servicio de Azure Stack. Después de instalar el proveedor de recursos, conéctelo a una o varias instancias de SQL Server.

## <a name="make-web-and-api-apps-available-to-your-azure-stack-users"></a>Aplicaciones web y aplicaciones de API disponibles para los usuarios de Azure Stack
Los operadores de Azure Stack pueden crear ofertas que permitan a los usuarios (inquilinos) crear aplicaciones web y de API, así como instancias de Azure Functions. Si proporciona a los usuarios acceso a estas aplicaciones en la nube y a petición, puede ahorrarles tiempo y recursos.

Implemente el proveedor de recursos de App Service para que tanto las [aplicaciones web como las aplicaciones de API estén disponibles para los usuarios de Azure Stack](../operator/azure-stack-tutorial-app-service.md).

## <a name="next-steps"></a>Pasos siguientes

[Más información acerca de la oferta de servicios con los sistemas integrados de Azure Stack](../operator/service-plan-offer-subscription-overview.md)
