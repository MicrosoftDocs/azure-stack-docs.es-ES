---
title: Facturación y contracargo a clientes en Azure Stack | Microsoft Docs
description: Aprenda cómo se factura a los usuarios de Azure Stack por el uso de los recursos y cómo se accede a la información de facturación para el análisis y el contracargo.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 03/21/2019
ms.openlocfilehash: bcdf43f7be95c76cfd4fc454d85e08ad197551a6
ms.sourcegitcommit: c196463492732218d2474d3a964f88e995272c80
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2019
ms.locfileid: "71094335"
---
# <a name="usage-and-billing-in-azure-stack"></a>Utilización y facturación en Azure Stack

En este artículo se describe cómo se factura a los usuarios de Azure Stack por el uso de los recursos y cómo se accede a la información de facturación para el análisis y el contracargo.

Azure Stack recopila y agrupa los datos de uso de los recursos utilizados y los reenvía a Azure Commerce. Azure Commerce factura el uso de Azure Stack de la misma forma que factura el uso de Azure.

También puede obtener datos de uso y exportarlos a su propio sistema de facturación y cargos al usuario con el uso de un adaptador de facturación, o bien exportarlos a una herramienta de inteligencia empresarial, como Microsoft Power BI.

## <a name="usage-pipeline"></a>Canalización de uso

Todos los proveedores de recursos de Azure Stack publican datos de uso en función de la utilización de los recursos. El servicio de uso agrega periódicamente (cada hora y cada día) los datos de uso y los almacena en la base de datos de uso. Los operadores y usuarios de Azure Stack pueden acceder a los datos de uso almacenados mediante las API de uso de recursos de Azure Stack.

Si ha [registrado su instancia de Azure Stack en Azure](azure-stack-registration.md), Azure Stack se configura para enviar los datos de uso a Azure Commerce. Una vez que los datos estén cargados en Azure, puede acceder a ellos a través del portal de facturación o mediante las API de uso de recursos de Azure. Para más información sobre el uso de datos que se notifica a Azure, consulte [Informes de datos de uso](azure-stack-usage-reporting.md).  

La siguiente imagen muestra los principales componentes de la canalización de uso:

![Canalización de uso](media/azure-stack-billing-and-chargeback/usagepipeline.png)

## <a name="what-usage-information-can-i-find-and-how"></a>¿Qué información de utilización se puede encontrar y cómo?

Los proveedores de recursos de Azure Stack como, por ejemplo, Compute, Storage y Network, generan datos de utilización a intervalos de horas para cada suscripción. Los datos de uso contienen información sobre los recursos consumidos, como el nombre del recurso, la suscripción y la cantidad utilizadas. Para información sobre los recursos de identificador de los medidores, consulte las [preguntas frecuentes de API de uso](azure-stack-usage-related-faq.md).

Después de que se hayan recopilado los datos de utilización, se [notifican a Azure](azure-stack-usage-reporting.md) para que genere una factura, que se puede ver en el portal de facturación de Azure.

> [!NOTE]  
> El informe de datos de uso no es necesario para el Kit de desarrollo de Azure Stack (ASDK) ni para los usuarios del sistema integrado de Azure Stack cuya licencia esté dentro del modelo de capacidad. Para más información sobre las licencias de Azure Stack, consulte la [hoja de datos de paquetes y precios](https://azure.microsoft.com/mediahandler/files/resourcefiles/5bc3f30c-cd57-4513-989e-056325eb95e1/Azure-Stack-packaging-and-pricing-datasheet.pdf).

El Portal de facturación de Azure muestra los datos de uso de los recursos facturables. Además de los recursos facturables, Azure Stack captura los datos de utilización de un conjunto más amplio de recursos, a los que se pueden obtener acceso en el entorno de Azure Stack a través de las API REST o cmdlets de PowerShell. Los operadores de Azure Stack pueden obtener los datos de uso de todas las suscripciones de usuario. Los usuarios individuales solo pueden obtener sus propios detalles de uso.

## <a name="usage-reporting-for-multi-tenant-cloud-service-providers"></a>Informes de uso de proveedores de servicios en la nube multiinquilino

Un proveedor de soluciones en la nube (CSP) multiinquilino que use Azure Stack podría querer notificar el uso de cada cliente por separado, para poder cargar el uso a distintas suscripciones de Azure.

La identidad de cada cliente se representa mediante un inquilino de Azure Active Directory (Azure AD) distinto. Azure Stack admite la asignación de una suscripción de CSP a cada inquilino de Azure AD. Puede agregar los inquilinos y sus suscripciones al registro de base de Azure Stack. El registro base se realiza para todas las instancias de Azure Stack. Si la suscripción de un inquilino no está registrada, el usuario puede seguir usando Azure Stack, y su uso se enviará a la suscripción utilizada para el registro de base.

## <a name="next-steps"></a>Pasos siguientes

- [Registro en Azure Stack](azure-stack-registration.md)
- [Report Azure Stack usage data to Azure](azure-stack-usage-reporting.md) (Notificar los datos de utilización de Azure Stack a Azure)
- [Provider Resource Usage API](azure-stack-provider-resource-api.md) (API de utilización de recursos de proveedor)
- [Tenant Resource Usage API](azure-stack-tenant-resource-usage-api.md) (API de utilización de recursos de inquilino)
- [Usage-related FAQ](azure-stack-usage-related-faq.md) (P+F relacionadas con la utilización)
