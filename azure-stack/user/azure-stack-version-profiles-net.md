---
title: Uso de los perfiles de la versión de API con .NET en Azure Stack Hub
description: Más información sobre cómo usar los perfiles de la versión de API con .NET en Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 05/05/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: bb13b13f48aa9ccab231e255e0dcb5547b61615a
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/05/2020
ms.locfileid: "82835924"
---
# <a name="use-api-version-profiles-with-net-in-azure-stack-hub"></a>Uso de los perfiles de la versión de API con .NET en Azure Stack Hub

El SDK de .NET para Resource Manager de Azure Stack Hub proporciona herramientas que facilitan la creación y administración de infraestructuras. Los proveedores de recursos del SDK incluyen los servicios Compute, Redes, Storage, App Services y [Key Vault](/azure/key-vault/key-vault-whatis). El SDK de .NET incluye 14 paquetes NuGet, Debe descargar estos paquetes en la solución cada vez que compile el proyecto. Sin embargo, puede descargar de forma específica el proveedor de recursos que utilizará para las versiones **2019-03-01-hybrid** o **2018-03-01-hybrid** con el fin de optimizar la memoria para la aplicación. Cada paquete consta de un proveedor de recursos, la versión correspondiente de la API y el perfil de la API a la que pertenece. Los perfiles de API en el SDK de .NET habilitan el desarrollo en la nube híbrida ayudándole a cambiar entre los recursos de Azure globales y los recursos de Azure Stack Hub.

## <a name="net-and-api-version-profiles"></a>Perfiles de la versión de API y .NET

Un perfil de API es una combinación de los proveedores de recursos y las versiones de la API. Use un perfil de API para obtener la versión más reciente y más estable de cada tipo de recurso de un paquete de proveedor de recursos.

- Para usar las últimas versiones de todos los servicios, use el perfil **más reciente** de los paquetes. Este perfil es parte del paquete NuGet **Microsoft.Azure.Management**.

- Para usar los servicios compatibles con Azure Stack Hub, utilice uno de los siguientes paquetes:
  - **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**
  - **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

  Asegúrese de que la parte **ResourceProvider** del paquete NuGet anterior se cambia al proveedor correcto.

- Para usar la última versión de API de un servicio, utilice el perfil **latest** del paquete NuGet específico. Por ejemplo, si quiere usar solo la **última versión de la API** del servicio Compute, utilice el perfil **latest** del paquete **compute**. El perfil **latest** forma parte del paquete NuGet **Microsoft.Azure.Management**.

- Para utilizar versiones específicas de API para un tipo de recurso en un proveedor de recursos específico, utilice las versiones específicas de API definidas dentro del paquete.

Puede combinar todas las opciones en la misma aplicación.

## <a name="install-the-azure-net-sdk"></a>Instalación del SDK de .NET para Azure

- Instale Git. Para instrucciones, consulte [Introducción: instalación de Git][].

- Para instalar los paquetes NuGet correctos, consulte [Búsqueda e instalación de un paquete][].

- Los paquetes que se deben instalar dependen de la versión del perfil que quiera utilizar. Los nombres de paquete para las versiones de perfiles son:

  - **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

  - **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**

- Para instalar los paquetes NuGet correctos para Visual Studio Code, consulte el siguiente vínculo para descargar las [instrucciones del administrador de paquetes NuGet][].

- Si no está disponible, cree una suscripción y guarde su identificador para usarlo más adelante. Para más información acerca de la creación de una suscripción, consulte [Creación de suscripciones para ofertas en Azure Stack Hub][].

- Cree una entidad de servicio y guarde el identificador y el secreto de cliente. Para más información sobre la creación de una entidad de servicio para Azure Stack Hub, consulte [Proporcionar a las aplicaciones acceso a Azure Stack Hub][]. El identificador de cliente también se conoce como el identificador de aplicación al crear una entidad de servicio.

- Asegúrese de que la entidad de servicio tenga rol de colaborador o propietario en la suscripción. Para más información sobre cómo asignar roles a la entidad de servicio, consulte [Proporcionar a las aplicaciones acceso a Azure Stack Hub][].

## <a name="prerequisites"></a>Prerrequisitos

Para usar el SDK de Azure para .NET con Azure Stack Hub, debe especificar los siguientes valores y, después, establecer valores con variables de entorno. Para establecer las variables de entorno, consulte las instrucciones bajo la tabla relativas a su sistema operativo específico.

| Value                     | Variables de entorno   | Descripción                                                                                                             |
|---------------------------|-------------------------|-------------------------------------------------------------------------------------------------------------------------|
| Id. de inquilino                 | `AZURE_TENANT_ID `      | El valor de su [*identificador de inquilino*][] de Azure Stack Hub.                                                                          |
| Id. de cliente                 | `AZURE_CLIENT_ID `      | Id. de la aplicación de la entidad de servicio que guardó al crear dicha entidad de servicio en la sección anterior de este artículo. |
| Id. de suscripción           | `AZURE_SUBSCRIPTION_ID` | El [*identificador de suscripción*][] es su forma de acceder a las ofertas de Azure Stack Hub.                                                      |
| Secreto del cliente             | `AZURE_CLIENT_SECRET`   | Secreto de aplicación de la entidad de servicio que guardó al crear dicha entidad de servicio.                                      |
| Punto de conexión de Resource Manager | `ARM_ENDPOINT`          | Consulte [*Punto de conexión de Resource Manager de Azure Stack Hub*][].                                                                    |
| Location                  | `RESOURCE_LOCATION`     | Ubicación de Azure Stack Hub.

Para buscar el identificador de inquilino de Azure Stack Hub, siga las instrucciones de [este artículo](../operator/azure-stack-csp-ref-operations.md). Para establecer las variables de entorno, haga lo siguiente:

### <a name="windows"></a>Windows

Para establecer las variables de entorno, en el símbolo del sistema de Windows, use el siguiente formato:

```shell
set Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="macos-linux-and-unix-based-systems"></a>Sistemas basados en MacOS, Linux y Unix

En los sistemas basados en Unix, use el comando siguiente:

```shell
export Azure_Tenant_ID=Your_Tenant_ID
```

### <a name="the-azure-stack-hub-resource-manager-endpoint"></a>Punto de conexión de Resource Manager de Azure Stack Hub

Azure Resource Manager es una plataforma de administración que permite a los administradores implementar, administrar y supervisar recursos de Azure. Azure Resource Manager puede controlar estas tareas como grupo, en vez de individualmente, en una sola operación.

Puede obtener la información de metadatos en el punto de conexión de Resource Manager. El punto de conexión devuelve un archivo JSON con la información necesaria para ejecutar el código.

Tenga en cuenta las siguientes consideraciones:

- El valor de **ResourceManagerUrl** del Kit de desarrollo de Azure Stack (ASDK) es: https://management.local.azurestack.external/.

- La dirección **ResourceManagerUrl** en un sistema integrado es: `https://management.region.<fqdn>/`, donde `<fqdn>` es el nombre de dominio completo.
Para recuperar los metadatos necesarios: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`.

Archivo JSON de ejemplo:

```json
{
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",
   "graphEndpoint": "https://graph.windows.net/",
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": 
      {
         "loginEndpoint": "https://login.windows.net/",
         "audiences": ["https://management.yourtenant.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Perfiles de API existentes

- **Microsoft.Azure.Management.Profiles.hybrid\_2019\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**: Último perfil creado para Azure Stack Hub. Use este perfil para que los servicios sean lo más compatibles posible con Azure Stack Hub, siempre y cuando tenga la versión 1904, o cualquier versión posterior.

- **Microsoft.Azure.Management.Profiles.hybrid\_2018\_03\_01.<*ResourceProvider*>.0.9.0-preview.nupkg**: Use este perfil para que los servicios sean compatibles con Azure Stack Hub para la versión 1808, o cualquier versión posterior.

- **Más reciente**: este perfil contiene las versiones más recientes de todos los servicios. Use las versiones más recientes de todos los servicios. Este perfil es parte del paquete NuGet **Microsoft.Azure.Management**.

Para más información sobre los perfiles de API y Azure Stack Hub, consulte [Resumen de perfiles de API][].

## <a name="azure-net-sdk-api-profile-usage"></a>Uso de perfil de API del SDK de .NET de Azure

Use el siguiente código para crear una instancia de un cliente de administración de recursos. Se puede usar un código similar para crear una instancia de otros clientes de proveedores de recursos (por ejemplo, Compute, Redes y Storage).

```csharp
var client = new ResourceManagementClient(armEndpoint, credentials)
{
    SubscriptionId = subscriptionId;
};
```

El parámetro `credentials` de este código es necesario para crear una instancia de un cliente. Mediante el código siguiente, el identificador del inquilino y la entidad de servicio generan un token de autenticación:

```csharp
var azureStackSettings = getActiveDirectoryServiceSettings(armEndpoint);
var credentials = ApplicationTokenProvider.LoginSilentAsync(tenantId, servicePrincipalId, servicePrincipalSecret, azureStackSettings).GetAwaiter().GetResult();
```

La llamada `getActiveDirectoryServiceSettings` del código recupera los puntos de conexión de Azure Stack Hub del punto de conexión de los metadatos. Indica las variables de entorno de la llamada que se realiza:

```csharp
public static ActiveDirectoryServiceSettings getActiveDirectoryServiceSettings(string armEndpoint)
{
    var settings = new ActiveDirectoryServiceSettings();
    try
    {
        var request = (HttpWebRequest)HttpWebRequest.Create(string.Format("{0}/metadata/endpoints?api-version=1.0", armEndpoint));
        request.Method = "GET";
        request.UserAgent = ComponentName;
        request.Accept = "application/xml";
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())
        {
            using (StreamReader sr = new StreamReader(response.GetResponseStream()))
            {
                var rawResponse = sr.ReadToEnd();
                var deserialized = JObject.Parse(rawResponse);
                var authenticationObj = deserialized.GetValue("authentication").Value<JObject>();
                var loginEndpoint = authenticationObj.GetValue("loginEndpoint").Value<string>();
                var audiencesObj = authenticationObj.GetValue("audiences").Value<JArray>();
                settings.AuthenticationEndpoint = new Uri(loginEndpoint);
                settings.TokenAudience = new Uri(audiencesObj[0].Value<string>());
                settings.ValidateAuthority = loginEndpoint.TrimEnd('/').EndsWith("/adfs", StringComparison.OrdinalIgnoreCase) ? false : true;
            }
        }
    }
    catch (Exception ex)
    {
        Console.WriteLine(String.Format("Could not get AD service settings. Exception: {0}", ex.Message));
    }
    return settings;
}
```

Estos pasos le permiten usar los paquetes NuGet de perfil de API para implementar la aplicación correctamente en Azure Stack Hub.

## <a name="samples-using-api-profiles"></a>Ejemplos donde se usan perfiles de API

Puede usar los ejemplos siguientes como referencia para crear soluciones con perfiles de API de Azure Stack Hub y .NET:

- [Administración de grupos de recursos](https://github.com/Azure-Samples/hybrid-resources-dotnet-manage-resource-group)
- [Administración de cuentas de almacenamiento](https://github.com/Azure-Samples/hybird-storage-dotnet-manage-storage-accounts)
- [Administración de una máquina virtual](https://github.com/Azure-Samples/hybrid-compute-dotnet-manage-vm): Este ejemplo usa el perfil **2019-03-01-hybrid**, es compatible con Azure Stack Hub.

## <a name="next-steps"></a>Pasos siguientes

Más información acerca de los perfiles de API:

- [Administración de perfiles de la versión de API en Azure Stack Hub](azure-stack-version-profiles.md)
- [Versiones de API del proveedor de recursos compatibles con perfiles](azure-stack-profiles-azure-resource-manager-versions.md)

  [Introducción: Instalación de Git]: https://git-scm.com/book/en/v2/Getting-Started-Installing-Git
  [Búsqueda e instalación de un paquete]: /nuget/tools/package-manager-ui
  [Instrucciones del administrador de paquetes NuGet]: https://marketplace.visualstudio.com/items?itemName=jmrog.vscode-nuget-package-manager
  [Creación de suscripciones para ofertas en Azure Stack Hub]: ../operator/azure-stack-subscribe-plan-provision-vm.md
  [Proporcionar a las aplicaciones acceso a Azure Stack Hub]: ../operator/azure-stack-create-service-principals.md
  [*Identificador de inquilino*]: ../operator/azure-stack-identity-overview.md
  [*Identificador de suscripción*]: ../operator/service-plan-offer-subscription-overview.md#subscriptions
  [*Punto de conexión de Resource Manager de Azure Stack Hub*]: ../user/azure-stack-version-profiles-ruby.md#the-azure-stack-hub-resource-manager-endpoint
  [Resumen de perfiles de API]: ../user/azure-stack-version-profiles.md#summary-of-api-profiles
  [Test Project to Virtual Machine, vNet, resource groups, and storage account]: https://github.com/seyadava/azure-sdk-for-net-samples/tree/master/TestProject
  [Use Azure PowerShell to create a service principal with a certificate]: ../operator/azure-stack-create-service-principals.md
  [Run unit tests with Test Explorer.]: /visualstudio/test/run-unit-tests-with-test-explorer?view=vs-2017
