---
title: Introducción y preguntas frecuentes sobre la facturación de Azure App Service en Azure Stack | Microsoft Docs
description: Más información sobre cómo se calculan los gastos de Azure App Service en Azure Stack y cómo se cobran.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/23/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/23/2019
ms.openlocfilehash: 7b11f00c98c6433ad035b6190789276ea5aa6fa7
ms.sourcegitcommit: be5382f715a9c1c18c660b630d8fcd823f13aae3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/24/2019
ms.locfileid: "66213051"
---
# <a name="azure-app-service-on-azure-stack-billing-overview-and-faq"></a>Introducción y preguntas frecuentes sobre la facturación de Azure App Service en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Las instrucciones de este documento muestran cómo se factura a los operadores de nube por ofrecer Azure App Service en Azure Stack y cómo pueden ellos facturar a su vez a sus inquilinos por el uso del servicio.

## <a name="billing-overview"></a>Introducción a la facturación

Los operadores de nube de Azure Stack pueden elegir implementar Azure App Service en la marca de Azure Stack para ofrecer funcionalidades de inquilino de Azure App Service y Azure Functions a sus clientes.  El proveedor de recursos de Azure App Service consta de varios tipos de roles que se pueden dividir entre los niveles de trabajo y de infraestructura.

Los roles de infraestructura no se facturan ya que son necesarios para el funcionamiento básico del servicio.  Los roles de infraestructura se pueden escalar horizontalmente según sea necesario para satisfacer las demandas de los inquilinos del operador de nube.  Los roles de infraestructura son los siguientes:

- Controladores
- Roles de administración
- Publicadores
- Front-ends

Los niveles de trabajo constan de dos tipos principales: compartidos y dedicados. El uso del trabajo se factura al operador de nube según los siguientes criterios.

## <a name="shared-workers"></a>Trabajos compartidos

Los trabajos compartidos son multiinquilino, no tienen host, son planes compartidos de App Service y funciones basadas en el consumo de Azure Functions para muchos inquilinos. Los trabajos compartidos emiten medidores de uso cuando se marcan como listos en el proveedor de recursos de Azure App Service.

## <a name="dedicated-workers"></a>Trabajos dedicados

Los trabajos dedicados están vinculados a planes de App Service que crean los inquilinos. Por ejemplo, en la SKU S1, los inquilinos pueden escalar a 10 instancias de forma predeterminada. Por tanto, cuando un inquilino crea un plan de App Service S1, Azure App Service asigna una de las instancias del conjunto de escalado del nivel de trabajo inferior al plan de App Service de ese inquilino. Posteriormente, el trabajo asignado ya no está disponible para asignarse a los demás inquilinos.  Si el inquilino elige escalar el plan de App Service a 10 instancias, los otros 9 trabajos se eliminan del grupo de disponibles y se asignan al plan de App Service del inquilino.

Los medidores se emiten para los trabajos dedicados cuando:

- Se marcan como "listos" en el proveedor de recursos de Azure App Service.
- Se asignan a un plan de App Service.

Por tanto, este modelo de facturación permite a los operadores de nube aprovisionar un grupo de trabajos dedicados listos para que los clientes los usen sin tener que pagar por ellos hasta que el plan de App Service del inquilino los reserva de manera efectiva. Por ejemplo, si tiene 20 trabajos en el nivel de trabajo inferior y tiene 5 clientes que crean cada uno dos planes de App Service S1, y que escalan cada plan de App Service a dos instancias, se quedará sin trabajos disponibles. Como resultado, no tendrá capacidad para que los clientes, nuevos o ya existentes, puedan escalar horizontalmente o crear nuevos planes de App Service. Los operadores de nube pueden ver el número actual de trabajos disponibles por cada nivel echando un vistazo a los niveles de trabajo de la configuración de Azure App Service en la administración de Azure Stack.

![Niveles de trabajo de App Service][1]

## <a name="see-customer-usage-using-the-azure-stack-usage-service"></a>Consulte el uso del cliente mediante el servicio de uso de Azure Stack.

Los operadores de nube pueden consultar la [API de uso de inquilino de Azure Stack](azure-stack-tenant-resource-usage-api.md) para recuperar la información de uso de los clientes y puede encontrar todos los medidores individuales que App Service emite para describir el uso del inquilino en la sección [Preguntas más frecuentes sobre uso](azure-stack-usage-related-faq.md). Estos medidores se pueden utilizar para calcular el uso por suscripción de cliente y esto, a su vez, puede utilizarse para calcular los cargos.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-pre-requisites"></a>¿Cómo puedo activar la licencia de la infraestructura de SQL Server y del servidor de archivos que se indica en la sección de requisitos previos?

La activación de las licencias para la infraestructura de SQL Server y del servidor de archivos que requiere el proveedor de recursos de Azure App Service, se describe en el artículo Azure App Service en Azure Stack: [Antes de comenzar](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server).

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>La sección de preguntas más frecuentes sobre el uso enumera los medidores del inquilino pero no los precios para tales medidores, ¿donde puedo encontrarlos?

Los operadores de nube tienen libertad para aplicar sus propios modelos de precios a sus clientes finales. El servicio proporciona la medición del uso y el operador de nube debe utilizar la cantidad que indica el medidor para cobrar a sus clientes según el modelo de precios que el operador determine. Esta capacidad de establecer los precios permite a los operadores diferenciarse de otros operadores de Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

- [API de uso de inquilino de Azure Stack](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png