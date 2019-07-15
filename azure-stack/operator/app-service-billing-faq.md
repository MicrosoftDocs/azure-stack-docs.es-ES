---
title: Introducción y preguntas frecuentes sobre la facturación de Azure App Service en Azure Stack | Microsoft Docs
description: Más información sobre cómo se calculan los gastos de Azure App Service en Azure Stack y cómo se facturan.
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
ms.openlocfilehash: 8b17020419183067793309055a6487f31da51246
ms.sourcegitcommit: d67ae2877e3b049bf70b2567b917e55394fb8984
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/10/2019
ms.locfileid: "67712675"
---
# <a name="azure-app-service-on-azure-stack-billing-overview-and-faq"></a>Introducción y preguntas frecuentes sobre la facturación de Azure App Service en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En este artículo se muestra cómo se factura a los operadores en la nube la oferta de Azure App Service en Azure Stack y cómo estos operadores pueden facturar a sus inquilinos por el uso del servicio.

## <a name="billing-overview"></a>Introducción a la facturación

Los operadores en la nube de Azure Stack pueden elegir si desean implementar Azure App Service en la marca de Azure Stack para ofrecer funcionalidades de inquilino de Azure App Service y Azure Functions a sus clientes. El proveedor de recursos de Azure App Service consta de varios tipos de roles que se pueden dividir entre los niveles de trabajo y de infraestructura.

Los roles de infraestructura no se facturan ya que son necesarios para el funcionamiento básico del servicio. Los roles de infraestructura se pueden escalar horizontalmente según sea necesario para satisfacer las demandas de los inquilinos del operador de nube. Los roles de infraestructura son los siguientes:

- Controladores
- Roles de administración
- Publicadores
- Front-ends

Los niveles de trabajo constan de dos tipos principales: compartidos y dedicados. El uso del trabajo se factura al operador de nube según los siguientes criterios.

## <a name="shared-workers"></a>Trabajos compartidos

Los trabajos compartidos son multiinquilino, no tienen host, son planes compartidos de App Service y funciones basadas en el consumo de Azure Functions para muchos inquilinos. Los trabajos compartidos emiten medidores de uso cuando se marcan como listos en el proveedor de recursos de Azure App Service.

## <a name="dedicated-workers"></a>Trabajos dedicados

Los trabajos dedicados están vinculados a planes de App Service que crean los inquilinos. Por ejemplo, en la SKU S1, los inquilinos pueden escalar a 10 instancias de forma predeterminada. Cuando un inquilino crea un plan de App Service S1, Azure App Service asigna una de las instancias del conjunto de escalado del nivel de trabajo inferior al plan de App Service de ese inquilino. De este modo, el trabajo asignado ya no está disponible para asignarse a los demás inquilinos. Si el inquilino elige escalar el plan de App Service a 10 instancias, se eliminan 9 trabajos más del grupo de disponibles y se asignan al plan de App Service del inquilino.

Los medidores se emiten para los trabajos dedicados cuando:

- Se marcan como "listos" en el proveedor de recursos de Azure App Service.
- Se asignan a un plan de App Service.

Este modelo de facturación permite a los operadores en la nube aprovisionar un grupo de trabajos dedicados listos para que los clientes los usen sin tener que pagar por ellos hasta que el plan de App Service del inquilino los reserve de manera efectiva. 

Por ejemplo, supongamos que tiene 20 trabajos en el nivel de trabajo pequeño. Entonces, si tiene cinco clientes que crean dos planes S1 de App Service, y cada uno de ellos escala el plan de App Service hasta dos instancias, no tiene ningún trabajo disponible. Como resultado, no tiene capacidad para que los clientes (nuevos o existentes) puedan escalar horizontalmente o crear nuevos planes de App Service. 

Los operadores de nube pueden ver el número actual de trabajos disponibles por cada nivel echando un vistazo a los niveles de trabajo de la configuración de Azure App Service en la administración de Azure Stack.

![Pantalla App Service: Niveles de trabajo][1]

## <a name="see-customer-usage-by-using-the-azure-stack-usage-service"></a>Consultar el uso del cliente mediante el servicio de uso de Azure Stack

Los operadores en la nube pueden consultar la [API de uso de recursos de inquilino de Azure Stack](azure-stack-tenant-resource-usage-api.md) para recuperar información de uso de los clientes. Puede encontrar todos los medidores individuales que App Service emite para describir el uso de los inquilinos en la sección de [preguntas más frecuentes de uso](azure-stack-usage-related-faq.md). Estos medidores se utilizan para calcular el uso por suscripción de cliente y esto, a su vez, se utiliza para calcular los cargos.

## <a name="frequently-asked-questions"></a>Preguntas más frecuentes

### <a name="how-do-i-license-the-sql-server-and-file-server-infrastructure-required-in-the-prerequisites"></a>¿Cómo puedo activar las licencias de la infraestructura de SQL Server y del servidor de archivos que se indica en la sección de requisitos previos?

La activación de las licencias de la infraestructura de SQL Server y del servidor de archivos que requiere el proveedor de recursos de Azure App Service se describe en el artículo sobre Azure App Service en Azure Stack denominado [Antes de comenzar](azure-stack-app-service-before-you-get-started.md#licensing-concerns-for-required-file-server-and-sql-server).

### <a name="the-usage-faq-lists-the-tenant-meters-but-not-the-prices-for-those-meters-where-can-i-find-them"></a>En la sección de preguntas más frecuentes sobre el uso se enumeran los medidores de los inquilinos, pero no se mencionan los precios para tales medidores. ¿Dónde puedo encontrarlos?

Como operador en la nube, tiene la libertad de aplicar su propio modelo de precios a los clientes. El servicio de uso proporciona la medición del uso. Por tanto, puede usar la cantidad de medidor que va a cobrar a los clientes según el modelo de precios que usted determine. La posibilidad de establecer los precios permite a los operadores diferenciarse de otros operadores de Azure Stack.

### <a name="as-a-csp-how-can-i-offer-free-and-shared-skus-for-customers-to-try-out-the-service"></a>Como CSP, ¿cómo puedo ofrecer SKU gratis y compartidas para que los clientes prueben el servicio?

Como operador en la nube, incurre en costos para ofrecer SKU gratis y compartidas, ya que se hospedan en trabajos compartidos. Para minimizar ese costo, puede optar por reducir verticalmente el nivel de trabajo compartido a un mínimo. 

Por ejemplo, para ofrecer SKU gratis y compartidas de planes de App Service y para ofrecer funciones basadas en el consumo, es necesario tener disponible un mínimo de una instancia A1. Los trabajos compartidos son multiinquilinos, por lo que pueden hospedar varias aplicaciones de clientes, cada una de ellas aislada y protegida por el espacio aislado de App Service. Al escalar el nivel de trabajo compartido de esta manera, se puede limitar el desembolso al costo de una vCPU por mes.

Por tanto, puede optar por crear una cuota, para usarla en un plan, que solo ofrezca SKU gratis y compartidas, y que limite el número de planes de App Service gratis y compartidos que el cliente puede crear.

## <a name="sample-scripts-to-assist-with-billing"></a>Scripts de ejemplo para ayudar con la facturación

El equipo de Azure App Service creó scripts de PowerShell de ejemplo para ayudar a consultar el servicio de uso de Azure Stack. Los operadores en la nube pueden usar estos scripts de ejemplo para preparar su propia facturación para los inquilinos. Los scripts de ejemplo se encuentran en el [repositorio de herramientas de Azure Stack](https://github.com/Azure/AzureStack-tools) en GitHub. Los scripts de App Service se encuentran en la [carpeta AppService, en Usage (Uso)](https://github.com/Azure/AzureStack-Tools/tree/master/Usage/AppService).

Los scripts de ejemplo disponibles son los siguientes:

- [Get-AppServiceBillingRecords](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceBillingRecords.ps1): en este ejemplo se recuperan los cambios Azure App Service en los registros de facturación de Azure Stack desde la API de uso de Azure Stack.
- [Get-AppServiceSubscriptionUsage](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Get-AppServiceSubscriptionUsage.ps1): en este ejemplo se calculan las cantidades de uso de Azure App Service en Azure Stack por suscripción. Este script calcula las cantidades de uso tanto en función de los datos de la API de uso como de los precios por medidor proporcionados por el operador en la nube.
- [Suspend-UserSubscriptions](https://github.com/Azure/AzureStack-Tools/blob/master/Usage/AppService/Suspend-UserSubscriptions.ps1): en este ejemplo se suspenden o se habilitan las suscripciones según los límites de uso especificados por el operador en la nube.

## <a name="next-steps"></a>Pasos siguientes

- [API de uso de recursos de inquilino de Azure Stack](azure-stack-tenant-resource-usage-api.md)

<!--Image references-->
[1]: ./media/app-service-billing-faq/app-service-worker-tiers.png
