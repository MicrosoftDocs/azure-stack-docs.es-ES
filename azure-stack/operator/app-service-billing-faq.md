---
title: Introducción y preguntas frecuentes sobre la facturación de Azure App Service en Azure Stack | Microsoft Docs
description: Más información sobre cómo se calculan los gastos de Azure App Service en Azure Stack y cómo se cobran.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: fa3bc647d11bca915c58aa1bd948881628405776
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828363"
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

### <a name="as-a-csp-how-can-i-offer-free-and-shared-skus-for-customers-to-trial-the-service"></a>Como CSP, ¿cómo puedo ofrecer SKU gratis y compartidos para que los clientes prueben el servicio?

Como operador en la nube, incurrirá en costos por ofrecer SKU gratis y compartidos, como hacemos en Azure, ya que están hospedados en los trabajos compartidos.  Para minimizar ese costo, puede optar por reducir verticalmente el nivel de trabajo compartido a un mínimo.  Por ejemplo, para ofrecer SKU de planes de App Service gratis y compartidos y para ofrecer funciones basadas en el consumo, es necesario tener disponible un mínimo de una instancia A1.  Los trabajos compartidos son multiinquilinos, por lo que pueden hospedar varias aplicaciones de clientes, cada uno de ellos aislado y protegido por el espacio aislado de App Service.  Al escalar el nivel de trabajo compartido de esta manera, puede limitar el desembolso al costo de 1vCPU por mes.

Además, puede optar después por crear una cuota, para su uso en un plan, que solo ofrezca SKU gratis y compartidos y limite el número de planes de servicio de aplicaciones gratis y compartidos que el cliente puede crear.

## <a name="sample-scripts-to-assist-with-billing"></a>Scripts de ejemplo para ayudar con la facturación

El equipo de Azure App Service ha creado scripts de PowerShell de ejemplo para ayudar a consultar el servicio de uso de Azure Stack, para ayudar a los operadores de la nube a preparar su propia facturación para los inquilinos.  Los scripts de ejemplo se encuentran en el [repositorio de herramientas de Azure Stack](https://github.com/Azure/AzureStack-tools) de GitHub y los scripts de App Service se encuentran en la [capeta AppService bajo Usage](https://github.com/Azure/AzureStack-Tools/tree/master/Usage/AppService).

Los scripts de ejemplo disponibles son los siguientes:

- [Get-AppServiceBillingRecords](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceBillingRecords.ps1): en este ejemplo se recupera Azure App Service en los registros de facturación de API Usage de Azure Stack.
- [Get-AppServiceSubscriptionUsage](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceSubscriptionUsage.ps1): en este ejemplo se calcula Azure App Service en las cantidades de uso de Azure Stack por suscripción.  Este script calcula las cantidades de uso basándose en los datos de la API Usage y en los precios proporcionados por medidor por el operador de la nube.
- [Suspend-UserSubscriptions](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Suspend-UserSubscriptions.ps1): este ejemplo suspende o habilita la suscripción en función del límite de uso especificado por el operador de la nube.

## <a name="next-steps"></a>Pasos siguientes

- [API de uso de inquilino de Azure Stack](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png