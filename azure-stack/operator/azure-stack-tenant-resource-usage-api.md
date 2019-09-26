---
title: API de uso de recursos de inquilino| Microsoft Docs
description: Referencia de las API de uso de recursos, que recuperan información de uso de Azure Stack.
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
ms.date: 09/17/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 85bb518335c473a70ff97473d1b8b61654372cb8
ms.sourcegitcommit: 95f30e32e5441599790d39542ff02ba90e70f9d6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/17/2019
ms.locfileid: "71070108"
---
# <a name="tenant-resource-usage-api"></a>API de uso de recursos de inquilino

Un inquilino puede usar las API de inquilino para ver los datos propios de uso de recursos del inquilino. Estas API son coherentes con las API de uso de Azure.

Puede usar el cmdlet de Windows PowerShell [Get-UsageAggregates](/powershell/module/azurerm.usageaggregates/get-usageaggregates) para obtener datos de uso, de forma similar a Azure.

## <a name="api-call"></a>Llamada a la API

### <a name="request"></a>Solicitud

La solicitud obtiene detalles de consumo para las suscripciones y el período de tiempo solicitados. No hay ningún cuerpo de solicitud.

| **Método** | **URI de solicitud** |
| --- | --- |
| GET |https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce/usageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&api-version=2015-06-01-preview&continuationToken={token-value} |

### <a name="parameters"></a>Parámetros

| **Parámetro** | **Descripción** |
| --- | --- |
| Armendpoint |Punto de conexión de Azure Resource Manager del entorno de Azure Stack. La convención de Azure Stack es que el nombre del punto de conexión de Azure Resource Manager esté en el formato `https://management.{domain-name}`. Por ejemplo, si en el kit de desarrollo el nombre de dominio es local.azurestack.external, el punto de conexión de Resource Manager será `https://management.local.azurestack.external`. |
| subId |Identificador de suscripción del usuario que realiza la llamada. Puede usar esta API solo para consultar el uso de una única suscripción. Los proveedores pueden usar la API de uso de recursos de proveedor para consultar el uso de todos los inquilinos. |
| reportedStartTime |Hora de inicio de la consulta. El valor de *DateTime* debe estar en formato UTC y al principio de la hora, por ejemplo, 13:00. Para la agregación diaria, establezca este valor en la medianoche de la hora UTC. El formato es caracteres de escape ISO 8601, por ejemplo, **2015-06-16T18%3a53%3a11%2b00%3a00Z**, donde los dos puntos se cambian por %3a y el signo más se cambia por %2b para que sea un URI descriptivo. |
| reportedEndTime |Hora de finalización de la consulta. Las restricciones que se aplican a **reportedStartTime** también se aplican a este parámetro. El valor de **reportedEndTime** no puede ser un tiempo futuro. |
| aggregationGranularity |Parámetro opcional que tiene dos valores discretos posibles: **a diario** y **cada hora**. Como sugieren los valores, uno devuelve los datos con una especificidad diaria y el otro es una resolución por horas. La opción **diaria** es el valor predeterminado. |
| api-version |Versión del protocolo que se usa para realizar esta solicitud. Debe usar **2015-06-01-preview**. |
| continuationToken |Token recuperado en la última llamada al proveedor de la API de uso. Este token es necesario cuando una respuesta supera las 1000 líneas. Actúa como un marcador de progreso. Si no está presente, los datos se recuperan desde el principio del día o de la hora, según la especificidad pasada. |

### <a name="response"></a>Response

```html
GET
/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&api-version=1.0
```

```json
{
"value": [
{

"id":
"/subscriptions/sub1/providers/Microsoft.Commerce/UsageAggregate/sub1-meterID1",
"name": "sub1-meterID1",
"type": "Microsoft.Commerce/UsageAggregate",

"properties": {
"subscriptionId":"sub1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

...
```

### <a name="response-details"></a>Detalles de la respuesta

| **Parámetro** | **Descripción** |
| --- | --- |
| id |Identificador único del agregado de uso. |
| Nombre |Nombre del agregado de uso. |
| Tipo |Definición de recursos. |
| subscriptionId |Identificador de la suscripción del usuario de Azure. |
| usageStartTime |Hora de inicio UTC del depósito de uso al que pertenece este agregado de uso. |
| usageEndTime |Hora de finalización UTC del depósito de uso al que pertenece este agregado de uso. |
| instanceData |Pares de clave y valor de los detalles de la instancia (con un formato nuevo):<br>  *resourceUri*: Identificador de recurso completo, incluidos los grupos de recursos y el nombre de instancia. <br>  *location*: región en la que se ejecutó este servicio. <br>  *tags*: Etiquetas del recurso especificadas por el usuario. <br>  *additionalInfo*: Más detalles sobre el recurso que se consumió, por ejemplo, tipo de imagen o versión de sistema operativo. |
| quantity |Cantidad de consumo de recursos que se produjo en este período de tiempo. |
| meterId |Identificador único del recurso que se consumió (también denominado **ResourceID**). |

## <a name="next-steps"></a>Pasos siguientes

- [API de uso de recursos de proveedor](azure-stack-provider-resource-api.md)
- [Usage-related FAQ](azure-stack-usage-related-faq.md) (P+F relacionadas con la utilización)
