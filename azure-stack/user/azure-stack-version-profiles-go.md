---
title: Uso de perfiles de la versión de la API con GO en Azure Stack | Microsoft Docs
description: Información acerca de los perfiles de la versión de la API con GO en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/26/2019
ms.openlocfilehash: 33fc05de4bf0107c8090badb77872082790aa087
ms.sourcegitcommit: 889fd09e0ab51ad0e43552a800bbe39dc9429579
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/16/2019
ms.locfileid: "65782582"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack"></a>Uso de los perfiles de la versión de la API con GO en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

## <a name="go-and-version-profiles"></a>GO y perfiles de la versión

Un perfil es una combinación de diferentes tipos de recursos con distintas versiones de diferentes servicios. Los perfiles le ayudan a mezclar y combinar diferentes tipos de recursos y le ofrecen las siguientes ventajas:

- Estabilidad para la aplicación mediante el bloqueo de versión específicas de la API.
- Compatibilidad para la aplicación con Azure Stack y centros de datos de Azure regionales.

En Azure SDK para Go, los perfiles están disponibles en la ruta de acceso de profiles, con la versión en formato **AAAA-MM-DD**. En este momento, la versión más reciente del perfil de API de Azure Stack es la **2017-03-09**. Para importar un servicio determinado de un perfil, importe su módulo correspondiente del perfil. Por ejemplo, para importar el servicio **Compute** del perfil **2017-03-09**, use el siguiente código:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/compute/mgmt/compute"
```

## <a name="install-azure-sdk-for-go"></a>Instalación del SDK de Azure para GO

1. Instale Git. Para instrucciones, consulte [Introducción: instalación de Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Instale el [lenguaje de programación de GO](https://golang.org/dl). Los perfiles de la API para Azure requieren la versión 1.9 de Go, o cualquier otra más reciente.
3. Instale el SDK de Azure para GO y sus dependencias mediante la ejecución del siguiente comando de Bash:

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>SDK para Go

En los siguientes vínculos puede encontrar más información acerca de Azure SDK para GO:

- SDK de Azure para GO en [Instalación del SDK de Azure para Go](/go/azure/azure-sdk-go-install).
- Azure SDK para GO está disponible públicamente en GitHub, en el repositorio [azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Dependencias de Go-AutoRest

SDK para GO depende de los módulos de Azure **Go-AutoRest** para enviar solicitudes REST a los puntos de conexión de Azure Resource Manager. Debe importar las dependencias de los módulos de Azure **Go-AutoRest** de [Go-AutoRest de Azure en GitHub](https://github.com/Azure/go-autorest). Puede encontrar los comandos de Bash de instalación en la sección **Instalación**.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack"></a>Uso de perfiles de SDK para Go en Azure Stack

Para ejecutar un ejemplo de código de Go en Azure Stack:

1. Instale el SDK de Azure para GO y sus dependencias. Para obtener instrucciones, consulte la sección anterior, [Instalación de Azure SDK para Go](#install-azure-sdk-for-go).
2. Obtenga la información de metadatos del punto de conexión de Resource Manager. El punto de conexión devuelve un archivo JSON con la información necesaria para ejecutar el código de GO.

   > [!NOTE]  
   > El valor de **ResourceManagerUrl** del Kit de desarrollo de Azure Stack (ASDK) es: `https://management.local.azurestack.external/`  
   > El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<region>.<fqdn>/`  
   > Para recuperar los metadatos necesarios: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
   Archivo JSON de ejemplo:

   ```json
   { "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
     "graphEndpoint": "https://graph.windows.net/",  
     "portal Endpoint": "https://portal.local.azurestack.external/",
     "authentication": {
       "loginEndpoint": "https://login.windows.net/",
       "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
     }
   }
   ```

3. Si no está disponible, cree una suscripción y guarde su identificador para usarlo más adelante. Para obtener información acerca la creación de una suscripción, consulte [Creación de suscripciones a ofertas en Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).

4. Cree una entidad de servicio con el ámbito **Suscripción** y el rol **Propietario**. Guarde el identificador y el secreto de la entidad de servicio. Para obtener información acerca de la creación de una entidad de servicio para Azure Stack, consulte [Creación de una entidad de servicio](azure-stack-create-service-principals.md). El entorno de Azure Stack ya está configurado.

5. Importe en el código un módulo de servicio del perfil de SDK para GO. La versión actual del perfil de Azure Stack es **2017-03-09**. Por ejemplo, para importar un módulo de red del tipo de perfil **2017-03-09**, use este código:

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   ```

6. En la función, cree y autentique un cliente con una llamada de función de cliente **New**. Para crear un cliente de red virtual, puede usar el siguiente código:  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   Establezca `<baseURI>` en el valor de **ResourceManagerUrl** que utilizó en el paso 2. Establezca `<subscriptionID>` en el valor de **SubscriptionID** que guardó en el paso 3.

   Para crear el token, consulte la sección siguiente.  

7. Invoque métodos de API con el cliente que creó en el paso anterior. Por ejemplo, para crear una red virtual mediante el cliente del paso anterior, vea el siguiente ejemplo:

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

Para ver un ejemplo completo de creación de una red virtual en Azure Stack mediante el perfil de SDK para GO, consulte el [ejemplo](#example).

## <a name="authentication"></a>Authentication

Para obtener la propiedad **Authorizer** de Azure Active Directory mediante el SDK para GO, instale los módulos de **Go-AutoRest**. Dichos módulos se deberían haber instalado con la instalación del "SDK para GO"; si no ha sido así, instale el [paquete de autenticación de GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Authorizer se debe establecer como autorizador del cliente de recursos. Hay distintas formas de obtener tokens de autorizador en Azure Stack mediante credenciales de cliente:

1. Si hay una entidad de servicio con el rol de propietario en la suscripción disponible, omita este paso. De lo contrario, cree una [entidad de servicio](azure-stack-create-service-principals.md) y asígnele un rol de "propietario" cuyo [ámbito sea su suscripción](azure-stack-create-service-principals.md#assign-the-service-principal-to-a-role). Guarde el identificador y el secreto de la aplicación de la entidad de servicio.

2. Importe el paquete **adal** de Go-AutoRest en el código.

   ```go
   package main
   import "github.com/Azure/go-autorest/autorest/adal"
   ```

3. Cree **oauthConfig** mediante el método NewOAuthConfig del módulo **adal**.

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/ada1"

   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
   }
   ```

   Establezca `<activeDirectoryEndpoint>` en el valor de la propiedad `loginEndpoint` de los metadatos de `ResourceManagerUrl` recuperados en la sección anterior de este documento. El valor de `<tenantID>` debe ser el identificador de su inquilino de Azure Stack.

4. Por último, cree un token de entidad de servicio mediante el método `NewServicePrincipalToken` del módulo **adal**:

   ```go
   package main

   import "github.com/Azure/go-autorest/autorest/adal"

   func CreateToken() (adal.OAuthTokenProvider, error) {
       var token adal.OAuthTokenProvider
       oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
       token, err = adal.NewServicePrincipalToken(
           *oauthConfig,
           clientID,
           clientSecret,
           activeDirectoryResourceID)
       return token, err
   ```

    Establezca `<activeDirectoryResourceID>` en uno de los valores de la lista "audience" de los metadatos de **ResourceManagerUrl** recuperados en la sección anterior de este artículo.
    Establezca `<clientID>` en el identificador de la aplicación de la entidad de servicio que guardó al crear esta última en la sección anterior de este artículo.
    Establezca `<clientSecret>` en el secreto de la aplicación de la entidad de servicio que guardó al crear esta última en la sección anterior de este artículo.

## <a name="example"></a>Ejemplo

En este ejemplo se muestra un ejemplo de código de Go que crea una red virtual en Azure Stack. Para ver ejemplos completos de SDK para GO, consulte el [repositorio de ejemplos de Azure SDK para GO](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Hay ejemplos de Azure Stack disponibles en la ruta de acceso de hybrid dentro de las carpetas de servicios del repositorio.

> [!NOTE]  
> Para ejecutar el código de este ejemplo, compruebe que la suscripción utilizada tiene el proveedor de recursos **Red** como **Registrado**. Para comprobarlo, busque la suscripción en el portal de Azure Stack y seleccione **Proveedores de recursos**.

1. Importe los paquetes necesarios en el código. Use el perfil más reciente disponible en Azure Stack para importar el módulo de red:

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
       "github.com/Azure/go-autorest/autorest"
       "github.com/Azure/go-autorest/autorest/adal"
       "github.com/Azure/go-autorest/autorest/to"
   )
   ```

2. Defina las variables del entorno. Para crear una red virtual, debe tener un grupo de recursos.

   ```go
   var (
       activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
       tenantID = "yourAzureStackTenantID"
       clientID = "yourServicePrincipalApplicationID"
       clientSecret = "yourServicePrincipalSecret"
       activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
       subscriptionID = "yourSubscriptionID"
       baseURI = "yourResourceManagerURL"
       resourceGroupName = "existingResourceGroupName"
   )
   ```

3. Ahora que ha definido las variables del entorno, agregue un método para crear un token de autenticación mediante el paquete **adal**. Para más información acerca de la autenticación, consulte la sección anterior.

   ```go
   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }
   ```

4. Agregue el método `main`. El método `main` primero obtiene un token mediante el método que se ha definido en el paso anterior. Luego, crea a un cliente mediante el uso del módulo de red del perfil. Por último, crea una red virtual.

   ```go
   package main

   import (
      "context"
      "fmt"
      "github.com/Azure/azure-sdk-for-go/profiles/2017-03-09/network/mgmt/network"
      "github.com/Azure/go-autorest/autorest"
      "github.com/Azure/go-autorest/autorest/adal"
      "github.com/Azure/go-autorest/autorest/to"
   )

   var (
      activeDirectoryEndpoint = "yourLoginEndpointFromResourceManagerUrlMetadata"
      tenantID = "yourAzureStackTenantID"
      clientID = "yourServicePrincipalApplicationID"
      clientSecret = "yourServicePrincipalSecret"
      activeDirectoryResourceID = "yourAudienceFromResourceManagerUrlMetadata"
     subscriptionID = "yourSubscriptionID"
     baseURI = "yourResourceManagerURL"
     resourceGroupName = "existingResourceGroupName"
   )

   //CreateToken creates a service principal token
   func CreateToken() (adal.OAuthTokenProvider, error) {
      var token adal.OAuthTokenProvider
      oauthConfig, err := adal.NewOAuthConfig(activeDirectoryEndpoint, tenantID)
      token, err = adal.NewServicePrincipalToken(
          *oauthConfig,
          clientID,
          clientSecret,
          activeDirectoryResourceID)
      return token, err
   }

   func main() {
      token, _ := CreateToken()
      vnetClient := network.NewVirtualNetworksClientWithBaseURI(baseURI, subscriptionID)
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
      future, _ := vnetClient.CreateOrUpdate(
          context.Background(),
          resourceGroupName,
          "sampleVnetName",
          network.VirtualNetwork{
              Location: to.StringPtr("local"),
              VirtualNetworkPropertiesFormat: &network.VirtualNetworkPropertiesFormat{
                  AddressSpace: &network.AddressSpace{
                      AddressPrefixes: &[]string{"10.0.0.0/8"},
                  },
                  Subnets: &[]network.Subnet{
                      {
                          Name: to.StringPtr("subnetName"),
                          SubnetPropertiesFormat: &network.SubnetPropertiesFormat{
                              AddressPrefix: to.StringPtr("10.0.0.0/16"),
                          },
                      },
                  },
              },
          })
      err := future.WaitForCompletion(context.Background(), vnetClient.Client)
      if err != nil {
          fmt.Printf(err.Error())
          return
      }
   }
   ```

## <a name="next-steps"></a>Pasos siguientes

- [Install PowerShell for Azure Stack](../operator/azure-stack-powershell-install.md) (Instalación de PowerShell para Azure Stack)
- [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md)  
