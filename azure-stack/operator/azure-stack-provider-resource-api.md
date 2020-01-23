---
title: API de uso de recursos de proveedor de Azure Stack Hub | Microsoft Docs
description: Referencia de la API de uso de recursos, que recupera información de uso de Azure Stack Hub.
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
ms.date: 01/07/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/25/2018
ms.openlocfilehash: e1d2e350079ab3d58802c8490da30a4c9555885d
ms.sourcegitcommit: d62400454b583249ba5074a5fc375ace0999c412
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/15/2020
ms.locfileid: "76023261"
---
# <a name="provider-resource-usage-api"></a>API de uso de recursos de proveedor

El término *proveedor* se aplica al administrador de servicios y a los proveedores delegados. Los operadores de Azure Stack Hub y los proveedores delegados pueden usar la API de uso de proveedores para ver el uso de sus inquilinos directos. Por ejemplo, como se muestra en el diagrama siguiente, P0 puede llamar a la API de proveedor para obtener información del uso directo de P1 y P2, y P1 puede llamar para obtener información de uso sobre P3 y P4.

![Modelo conceptual de la jerarquía de proveedores](media/azure-stack-provider-resource-api/image1.png)

## <a name="api-call-reference"></a>Referencia de llamadas a la API

### <a name="request"></a>Solicitud

La solicitud obtiene detalles de consumo para las suscripciones y el período de tiempo solicitados. No hay ningún cuerpo de solicitud.

Esta API de uso es una API de proveedor, por lo que se debe asignar al autor de llamada el rol **Propietario**, **Colaborador** o **Lector** en la suscripción del proveedor.

| Método | URI de solicitud |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={reportedStartTime}&reportedEndTime={reportedEndTime}&aggregationGranularity={granularity}&subscriberId={sub1.1}&api-version=2015-06-01-preview&continuationToken={token-value}` |

### <a name="arguments"></a>Argumentos

| Argumento | Descripción |
| --- | --- |
| `armendpoint` |Punto de conexión de Azure Resource Manager del entorno de Azure Stack Hub. La norma en Azure Stack Hub es que el nombre del punto de conexión de Azure Resource Manager esté en el formato `https://adminmanagement.{domain-name}`. Por ejemplo, si en el Kit de desarrollo de Azure Stack (ASDK) el nombre de dominio es *local.azurestack.external*, el punto de conexión de Resource Manager será `https://adminmanagement.local.azurestack.external`. |
| `subId` |Identificador de suscripción del usuario que realiza la llamada. |
| `reportedStartTime` |Hora de inicio de la consulta. El valor de `DateTime` debe estar en formato Hora universal coordinada (UTC) y al principio de la hora, por ejemplo, 13:00. Para la agregación diaria, establezca este valor en la medianoche de la hora UTC. El formato se cambia a ISO 8601; por ejemplo, `2015-06-16T18%3a53%3a11%2b00%3a00Z`, donde los dos puntos se cambian por `%3a` y el signo más se cambia por `%2b` para que sea un URI descriptivo. |
| `reportedEndTime` |Hora de finalización de la consulta. Las restricciones que se aplican a `reportedStartTime` también se aplican a este argumento. El valor de `reportedEndTime` no puede ser una fecha futura ni la actual. Si es así, el resultado se establece en "el procesamiento no está completo". |
| `aggregationGranularity` |Parámetro opcional que tiene dos valores discretos posibles: **a diario** y **cada hora**. Como sugieren los valores, uno devuelve los datos con una especificidad diaria y el otro es una resolución por horas. La opción **diaria** es el valor predeterminado. |
| `subscriberId` |Id. de suscripción. Para obtener los datos filtrados, es necesario el identificador de suscripción de un inquilino directo del proveedor. Si no se especifica ningún parámetro de identificador de suscripción, la llamada devuelve los datos de uso para todos los inquilinos directos del proveedor. |
| `api-version` |Versión del protocolo que se usa para realizar esta solicitud. Este valor se establece en `2015-06-01-preview`. |
| `continuationToken` |Token recuperado en la última llamada al proveedor de la API de uso. Este token es necesario cuando una respuesta supera las 1000 líneas. Actúa como un marcador para el progreso. Si el token no está presente, los datos se recuperan desde el principio del día o de la hora, según la especificidad pasada. |

### <a name="response"></a>Response

```http
GET
/subscriptions/sub1/providers/Microsoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime=reportedStartTime=2014-05-01T00%3a00%3a00%2b00%3a00&reportedEndTime=2015-06-01T00%3a00%3a00%2b00%3a00&aggregationGranularity=Daily&subscriberId=sub1.1&api-version=1.0
```

```json
{
"value": [
{

"id":
"/subscriptions/sub1.1/providers/Microsoft.Commerce.Admin/UsageAggregate/sub1.1-

meterID1",
"name": "sub1.1-meterID1",
"type": "Microsoft.Commerce.Admin/UsageAggregate",

"properties": {
"subscriptionId":"sub1.1",
"usageStartTime": "2015-03-03T00:00:00+00:00",
"usageEndTime": "2015-03-04T00:00:00+00:00",
"instanceData":"{\"Microsoft.Resources\":{\"resourceUri\":\"resourceUri1\",\"location\":\"Alaska\",\"tags\":null,\"additionalInfo\":null}}",
"quantity":2.4000000000,
"meterId":"meterID1"

}
},

. . .
```

### <a name="response-details"></a>Detalles de la respuesta

| Argumento | Descripción |
| --- | --- |
|`id` |Identificador único del agregado de uso. |
|`name` |Nombre del agregado de uso. |
|`type` |Definición de recursos. |
|`subscriptionId` |Identificador de la suscripción del usuario de Azure Stack Hub. |
|`usageStartTime`|Hora de inicio UTC del depósito de uso al que pertenece este agregado de uso.|
|`usageEndTime`|Hora de finalización UTC del depósito de uso al que pertenece este agregado de uso. |
|`instanceData` |Pares de clave y valor de los detalles de la instancia (con un formato nuevo):<br> `resourceUri`: identificador de recurso completo, incluidos los grupos de recursos y el nombre de instancia. <br> `location`: región en la que se ejecutó este servicio. <br> `tags`: etiquetas del recurso especificadas por el usuario. <br> `additionalInfo`: más detalles sobre el recurso que se consumió; por ejemplo, el tipo de imagen o versión de sistema operativo. |
|`quantity`|Cantidad de consumo de recursos que se produjo en este período de tiempo. |
|`meterId` |Identificador único del recurso que se consumió (también denominado `ResourceID`). |

## <a name="retrieve-usage-information"></a>Recuperar información de utilización

### <a name="powershell"></a>PowerShell

Para generar los datos de uso, es preciso tener recursos en ejecución y que utilicen activamente el sistema; por ejemplo, una máquina virtual activa o una cuenta de almacenamiento que contiene algunos datos. Si no está seguro de si tiene algún recurso que se ejecute en Marketplace de Azure Stack Hub, implemente una máquina virtual y compruebe la hoja de supervisión de la máquina virtual para asegurarse de que se está ejecutando. Use los siguientes cmdlets de PowerShell para ver los datos de utilización:

1. [Instale PowerShell para Azure Stack Hub](azure-stack-powershell-install.md).
2. [Configure el entorno de PowerShell del usuario de Azure Stack Hub](../user/azure-stack-powershell-configure-user.md) o del [operador de Azure Stack Hub](azure-stack-powershell-configure-admin.md).
3. Para recuperar los datos de uso, llame al cmdlet [Get-AzsSubscriberUsage](/powershell/module/azs.commerce.admin/get-azssubscriberusage) de PowerShell:

   ```powershell
   Get-AzsSubscriberUsage -ReportedStartTime "2017-09-06T00:00:00Z" -ReportedEndTime "2017-09-07T00:00:00Z"
   ```

### <a name="rest-api"></a>API DE REST

Puede recopilar información de uso de las suscripciones eliminadas mediante la llamada al servicio **Microsoft.Commerce.Admin**.

#### <a name="return-all-tenant-usage-for-deleted-for-active-users"></a>Devolución de todo el uso de inquilino para usuarios activos o eliminados

| Método | URI de solicitud |
| --- | --- |
| GET | `https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&api-version=2015-06-01-preview` |

#### <a name="return-usage-for-deleted-or-active-tenant"></a>Devolución del uso para el inquilino eliminado o activo

| Método | URI de solicitud |
| --- | --- |
| GET |`https://{armendpoint}/subscriptions/{subId}/providersMicrosoft.Commerce.Admin/subscriberUsageAggregates?reportedStartTime={start-time}&reportedEndTime={end-endtime}&aggregationGranularity=Hourly&subscriberId={subscriber-id}&api-version=2015-06-01-preview` |

## <a name="next-steps"></a>Pasos siguientes

- [Referencia de la API de uso de recursos de inquilino](azure-stack-tenant-resource-usage-api.md)
- [Usage-related FAQ](azure-stack-usage-related-faq.md) (P+F relacionadas con la utilización)
