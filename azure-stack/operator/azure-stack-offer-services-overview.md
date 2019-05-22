---
title: Ofertas de servicios en Azure Stack | Microsoft Docs
description: Como operador de nube, puede ofrecer servicios a los usuarios.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 3282464bf1df7fcbbdb532735c8d4b1dabfa0401
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985039"
---
# <a name="overview-of-offering-services-in-azure-stack"></a>Introducción a la oferta de servicios en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

[Microsoft Azure Stack](azure-stack-overview.md) es una nueva plataforma en la nube híbrida que le permite proporcionar servicios desde el centro de datos. Como proveedor de servicios, puede ofrecer servicios a los inquilinos. Dentro de una empresa u organismo gubernamental, puede ofrecer servicios locales a los empleados. 

Puede ofrecer servicios de [Infraestructura como servicio](https://azure.microsoft.com/overview/what-is-iaas/) (IaaS) que permiten a los usuarios crear una infraestructura informática a petición, aprovisionada y administrada desde el portal de usuarios de Azure Stack.

También puede implementar servicios de [Plataforma como servicio](https://azure.microsoft.com/overview/what-is-paas/) (PaaS) para Azure Stack de Microsoft y otros proveedores. Los servicios que puede entregar incluyen, entre otros:

- [Incorporación de un proveedor de recursos de App Service a Azure Stack](azure-stack-app-service-overview.md)

- [Incorporación de un proveedor de recursos de SQL Server a Azure Stack](azure-stack-sql-resource-provider-deploy.md)

- [Incorporación de un proveedor de recursos de MySQL Server a Azure Stack](azure-stack-mysql-resource-provider-deploy.md)


Incluso puede combinar los servicios para integrar y compilar soluciones complejas para distintos usuarios.

Para ofrecer estos servicios a los usuarios, primero debe crear [planes, ofertas y cuotas](azure-stack-plan-offer-quota-overview.md). Luego, los usuarios pueden suscribirse a las ofertas para usar los servicios.

## <a name="plan-your-service-offers"></a>Planificar las ofertas de servicios

Cuando planifique las ofertas, tenga en cuenta los puntos siguientes:

**Ofertas de prueba**: puede usar ofertas de prueba para atraer a usuarios nuevos, que luego pueden actualizar a servicios adicionales. Para crear una oferta de prueba, cree un pequeño [plan base](azure-stack-plan-offer-quota-overview.md#base-plan) con un plan de complementos opcional mayor.

**Planificación de capacidad**: es posible que le preocupe que los usuarios acaparen grandes cantidades de recursos y atasquen el sistema para todos los usuarios. Para mejorar el rendimiento, puede [configurar sus planes con cuotas](azure-stack-plan-offer-quota-overview.md#plans) para limitar el uso.

**Proveedores delegados**: puede conceder a otros usuarios la capacidad de crear ofertas en su entorno. Por ejemplo, si es proveedor de servicios, puede [delegar](azure-stack-delegated-provider.md) esta funcionalidad en sus distribuidores. O bien, si es una organización, puede delegar en otras divisiones o subsidiarias.

## <a name="next-steps"></a>Pasos siguientes

[Creación de una oferta en Azure Stack](azure-stack-create-offer.md)
