---
title: Uso de la API de Azure Stack | Microsoft Docs
description: Aprenda a recuperar una autenticación de Azure para realizar solicitudes de API a Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 1773083a5d02f3bb988ac3e5cef6528a5d49b94e
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64311145"
---
<!--  cblackuk and charliejllewellyn. This is a community contribution by cblackuk-->

# <a name="use-the-azure-stack-api"></a>Uso de la API de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar la interfaz de programación de aplicaciones (API) para automatizar operaciones como la incorporación de una máquina virtual a la nube de Azure Stack.

La API requiere que el cliente se autentique en el punto de conexión de inicio de sesión de Microsoft Azure. Este punto de conexión devuelve un token en el encabezado de cada solicitud enviada a la API de Azure Stack. Microsoft Azure usa Oauth 2.0.

En este artículo se proporcionan ejemplos que usan la utilidad **cURL** para crear solicitudes de Azure Stack. La aplicación, cURL, es una herramienta de línea de comandos con una biblioteca para transferir datos. Estos ejemplos lo guían por el proceso de recuperación de un token de acceso a la API de Azure Stack. La mayoría de los lenguajes de programación proporcionan bibliotecas Oauth 2.0, que presentan una buena administración de los tokens y controlan tareas tales como la actualización del token.

Examine todo el proceso de utilización de la API REST de Azure Stack con un cliente REST genérico, como **cURL**, para que le ayude a comprender las solicitudes subyacentes y lo que puede esperar recibir en la carga de respuesta.

En este artículo no se exploran todas las opciones disponibles para recuperar tokens, como el inicio de sesión interactivo o la creación de identificadores de aplicación dedicados. Para obtener información acerca de estos temas, consulte [Referencia de API REST de Azure](https://docs.microsoft.com/rest/api/).

## <a name="get-a-token-from-azure"></a>Obtención de un token de Azure

Cree el cuerpo de la solicitud con el formato de tipo de contenido x-www-form-urlencoded para obtener un token de acceso. PUBLIQUE la solicitud en el punto de conexión de autenticación e inicio de sesión de REST de Azure.

### <a name="uri"></a>URI

```bash  
POST https://login.microsoftonline.com/{tenant id}/oauth2/token
```

El **identificador de inquilino** es:

 - El dominio del inquilino, como `fabrikam.onmicrosoft.com`
 - El identificador del inquilino, como `8eaed023-2b34-4da1-9baa-8bc8c9d6a491`
 - Valor predeterminado de las claves independientes del inquilino: `common`

### <a name="post-body"></a>Cuerpo de POST

```bash  
grant_type=password
&client_id=1950a258-227b-4e31-a9cf-717495945fc2
&resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
&username=admin@fabrikam.onmicrosoft.com
&password=Password123
&scope=openid
```

Para cada valor:

- **grant_type**  
   El tipo de esquema de autenticación que se va a usar. En este ejemplo, el valor es `password`

- **resource**  
   El recurso al que accede al token. Para encontrar el recurso, consulte el punto de conexión de metadatos de administración de Azure Stack. Examine la sección **audiences**.

- **Punto de conexión de administración de Azure Stack**  
   ```
   https://management.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-01
   ```

  > [!NOTE]  
  > Si es un administrador que trata de acceder a la API de inquilino, debe asegurarse de usar el punto de conexión de inquilino, por ejemplo: `https://adminmanagement.{region}.{Azure Stack domain}/metadata/endpoints?api-version=2015-01-011`  

  Por ejemplo, con el Kit de desarrollo de Azure Stack como punto de conexión:

   ```bash
   curl 'https://management.local.azurestack.external/metadata/endpoints?api-version=2015-01-01'
   ```

  Respuesta:

  ```
  {
  "galleryEndpoint":"https://adminportal.local.azurestack.external:30015/",
  "graphEndpoint":"https://graph.windows.net/",
  "portalEndpoint":"https://adminportal.local.azurestack.external/",
  "authentication":{
     "loginEndpoint":"https://login.windows.net/",
     "audiences":["https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"]
     }
  }
  ```

### <a name="example"></a>Ejemplo

  ```
  https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155
  ```

  **client_id**

  Este valor está codificado en un valor predeterminado:

  ```
  1950a258-227b-4e31-a9cf-717495945fc2
  ```

  Existen opciones alternativas disponibles para escenarios concretos:

  
  | Application | ApplicationID |
  | --------------------------------------- |:-------------------------------------------------------------:|
  | LegacyPowerShell | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417 |
  | PowerShell | 1950a258-227b-4e31-a9cf-717495945fc2 |
  | WindowsAzureActiveDirectory | 00000002-0000-0000-c000-000000000000 |
  | VisualStudio | 872cd9fa-d31f-45e0-9eab-6e460a02d1f1 |
  | AzureCLI | 04b07795-8ddb-461a-bbee-02f9e1bf7b46 |

  **username**

  Por ejemplo, la cuenta de AAD de Azure Stack:

  ```
  azurestackadmin@fabrikam.onmicrosoft.com
  ```

  **password**

  La contraseña de administrador de AAD de Azure Stack.

### <a name="example"></a>Ejemplo

Solicitud:

```
curl -X "POST" "https://login.windows.net/fabrikam.onmicrosoft.com/oauth2/token" \
-H "Content-Type: application/x-www-form-urlencoded" \
--data-urlencode "client_id=1950a258-227b-4e31-a9cf-717495945fc2" \
--data-urlencode "grant_type=password" \
--data-urlencode "username=admin@fabrikam.onmicrosoft.com" \
--data-urlencode 'password=Password12345' \
--data-urlencode "resource=https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155"
```

Respuesta:

```
{
  "token_type": "Bearer",
  "scope": "user_impersonation",
  "expires_in": "3599",
  "ext_expires_in": "0",
  "expires_on": "1512574780",
  "not_before": "1512570880",
  "resource": "https://contoso.onmicrosoft.com/4de154de-f8a8-4017-af41-df619da68155",
  "access_token": "eyJ0eXAiOi...truncated for readability..."
}
```

## <a name="api-queries"></a>Consultas de la API

Una vez que obtenga el token de acceso, debe agregarlo como encabezado a todas las solicitudes de API. Para ello, debe crear un encabezado **authorization** con el valor: `Bearer <access token>`. Por ejemplo:

Solicitud:

```bash  
curl -H "Authorization: Bearer eyJ0eXAiOi...truncated for readability..." 'https://adminmanagement.local.azurestack.external/subscriptions?api-version=2016-05-01'
```

Respuesta:

```bash  
offerId : /delegatedProviders/default/offers/92F30E5D-F163-4C58-8F02-F31CFE66C21B
id : /subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8
subscriptionId : 800c4168-3eb1-406b-a4ca-919fe7ee42e8
tenantId : 9fea4606-7c07-4518-9f3f-8de9c52ab628
displayName : Default Provider Subscription
state : Enabled
subscriptionPolicies : @{locationPlacementId=AzureStack}
```

### <a name="url-structure-and-query-syntax"></a>Sintaxis de estructura y consulta de URL

La dirección URL genérica de solicitud consta de: {URI-scheme} :// {URI-host} / {resource-path} ? {cadena de consulta}

- **Esquema de URI**:  
El URI indica el protocolo usado para enviar la solicitud. Por ejemplo, `http` o `https`.
- **Host del URI**:  
El host especifica el nombre de dominio o la dirección IP del servidor donde se hospeda el punto de conexión de servicio REST, como `graph.microsoft.com` o `adminmanagement.local.azurestack.external`.
- **Ruta de acceso del recurso**:  
La ruta de acceso especifica el recurso o la colección de recursos, que pueden incluir varios segmentos usados por el servicio para determinar la selección de esos recursos. Por ejemplo: `beta/applications/00003f25-7e1f-4278-9488-efc7bac53c4a/owners` puede usarse para consultar en la lista los propietarios de una aplicación específica dentro de la colección de aplicaciones.
- **Cadena de consulta**:  
La cadena proporciona parámetros simples adicionales, como los criterios de selección de versión o recurso de API.

## <a name="azure-stack-request-uri-construct"></a>Construcción de URI de solicitud de Azure Stack

```
{URI-scheme} :// {URI-host} / {subscription id} / {resource group} / {provider} / {resource-path} ? {OPTIONAL: filter-expression} {MANDATORY: api-version}
```

### <a name="uri-syntax"></a>Sintaxis de URI

```
https://adminmanagement.local.azurestack.external/{subscription id}/resourcegroups/{resource group}/providers/{provider}/{resource-path}?{api-version}
```

### <a name="query-uri-example"></a>Ejemplo de consulta de URI

```
https://adminmanagement.local.azurestack.external/subscriptions/800c4168-3eb1-406b-a4ca-919fe7ee42e8/resourcegroups/system.local/providers/microsoft.infrastructureinsights.admin/regionhealths/local/Alerts?$filter=(Properties/State eq 'Active') and (Properties/Severity eq 'Critical')&$orderby=Properties/CreatedTimestamp desc&api-version=2016-05-01"
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre el uso de los puntos de conexión de RESTful de Azure, consulte la [referencia de la API REST de Azure](https://docs.microsoft.com/rest/api/).
