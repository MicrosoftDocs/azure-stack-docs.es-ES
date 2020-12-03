---
title: Uso de los perfiles de la versión de la API con GO en Azure Stack Hub
description: Aprenda a usar los perfiles de la versión de API con GO en Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 12/2/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: 9a179649753addab0f92f99291a2a54a05e5b8fd
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525870"
---
# <a name="use-api-version-profiles-with-go-in-azure-stack-hub"></a>Uso de los perfiles de la versión de la API con GO en Azure Stack Hub

## <a name="go-and-version-profiles"></a>GO y perfiles de la versión

Un perfil es una combinación de diferentes tipos de recursos con distintas versiones de diferentes servicios. Los perfiles le ayudan a mezclar y combinar diferentes tipos de recursos y le ofrecen las siguientes ventajas:

- Estabilidad para la aplicación mediante el bloqueo de versión específicas de API.
- Compatibilidad de la aplicación tanto con Azure Stack Hub como con los centros de datos de Azure regionales.

En el SDK de Go, los perfiles están disponibles en la ruta de acceso de perfiles. Los números de versión de los perfiles se etiquetan con el formato **AAAA-MM-DD**. La versión más reciente del perfil de API de Azure Stack Hub es **2019-03-01** para la versión 1904 de Azure Stack Hub, o las versiones posteriores. Para importar un servicio determinado de un perfil, importe su módulo correspondiente del perfil. Por ejemplo, para importar el servicio **Compute** del perfil **2019-03-01**, use el siguiente código:

```go
import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/compute/mgmt/compute"
```

## <a name="install-the-azure-sdk-for-go"></a>Instalación del SDK de Azure para Go

1. Instale Git. Para instrucciones, consulte [Introducción: instalación de Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
2. Instale el [lenguaje de programación de Go](https://golang.org/dl). Los perfiles de la API para Azure requieren la versión 1.9 de Go, o cualquier otra más reciente.
3. Instale el SDK de Azure para Go y sus dependencias mediante la ejecución del siguiente comando de Bash:

   ```bash
   go get -u -d github.com/Azure/azure-sdk-for-go/...
   ```

### <a name="the-go-sdk"></a>SDK para Go

En los siguientes vínculos puede encontrar más información acerca de Azure SDK para Go:

- SDK de Azure para GO en [Instalación del SDK de Azure para Go](/go/azure/azure-sdk-go-install).
- Azure SDK para Go está disponible públicamente en GitHub, en el repositorio [azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go).

### <a name="go-autorest-dependencies"></a>Dependencias de Go-AutoRest

SDK para GO depende de los módulos de Azure **Go-AutoRest** para enviar solicitudes REST a los puntos de conexión de Azure Resource Manager. Debe importar las dependencias de los módulos de Azure **Go-AutoRest** de [Go-AutoRest de Azure en GitHub](https://github.com/Azure/go-autorest). Puede encontrar los comandos de Bash de instalación en la sección **Instalación**.

## <a name="how-to-use-go-sdk-profiles-on-azure-stack-hub"></a>Uso de perfiles del SDK de Go en Azure Stack Hub

Para ejecutar un ejemplo de código de Go en Azure Stack Hub, siga estos pasos:

1. Instale Azure SDK para Go y sus dependencias. Para obtener instrucciones, consulte la sección anterior, [Instalación de Azure SDK para Go](#install-the-azure-sdk-for-go).
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

3. Si no está disponible, cree una suscripción y guarde su identificador para usarlo más adelante. Para obtener información sobre la creación de una suscripción, consulte [Creación de suscripciones a ofertas en Azure Stack Hub](../operator/azure-stack-subscribe-plan-provision-vm.md).

4. Cree una entidad de servicio que utilice un secreto de cliente, con el ámbito **Suscripción** y el rol **Propietario**. Guarde el identificador y el secreto de la entidad de servicio. Para obtener información acerca de la creación de una entidad de servicio para Azure Stack Hub, consulte [Uso de una identidad de aplicación para acceder a recursos](../operator/azure-stack-create-service-principals.md). El entorno de Azure Stack Hub ya está configurado.

5. Importe en el código un módulo de servicio del perfil de SDK para GO. La versión actual del perfil de Azure Stack Hub es **2019-03-01**. Por ejemplo, para importar un módulo de red del tipo de perfil **2019-03-01**, use este código:

   ```go
   package main
    import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   ```

6. En la función, cree y autentique un cliente con una llamada de función de cliente **New**. Para crear un cliente de red virtual, use el siguiente código:  

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"

   func main() {
      vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
      vnetClient.Authorizer = autorest.NewBearerAuthorizer(token)
   ```

   Establezca `<baseURI>` en el valor de **ResourceManagerUrl** que utilizó en el paso 2. Establezca `<subscriptionID>` en el valor de **SubscriptionID** que guardó en el paso 3.

   Para crear el token, consulte la sección siguiente.  

7. Invoque métodos de API con el cliente que creó en el paso anterior. Por ejemplo, para crear una red virtual mediante el cliente del paso anterior, vea el siguiente ejemplo:

   ```go
   package main

   import "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
   func main() {
   vnetClient := network.NewVirtualNetworksClientWithBaseURI("<baseURI>", "(subscriptionID>")
   vnetClient .Authorizer = autorest.NewBearerAuthorizer(token)

   vnetClient .CreateOrUpdate( )
   ```

Para ver un ejemplo completo de creación de una red virtual en Azure Stack Hub mediante el perfil del SDK de GO, vea el [ejemplo](#example).

## <a name="authentication"></a>Authentication

Para obtener la propiedad **Authorizer** de Azure Active Directory mediante el SDK para GO, instale los módulos de **Go-AutoRest**. Estos módulos deben haberse instalado ya con la instalación de "Go SDK". En caso contrario, instale el [paquete de autenticación desde GitHub](https://github.com/Azure/go-autorest/tree/master/autorest/adal).

Authorizer se debe establecer como autorizador del cliente de recursos. Hay distintas formas de obtener tokens de autorizador en Azure Stack Hub mediante credenciales de cliente:

1. Si hay una entidad de servicio con el rol de propietario en la suscripción disponible, omita este paso. En caso contrario, consulte [Uso de una identidad de aplicación para acceder a recursos](../operator/azure-stack-create-service-principals.md) para obtener instrucciones sobre cómo crear una entidad de servicio que use un secreto de cliente y asignarle un rol de "propietario" destinado a su suscripción. Asegúrese de capturar el identificador y el secreto de la aplicación de la entidad de servicio.

2. Importe el paquete **adal** de **Go-AutoRest** en el código.

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

   Establezca `<activeDirectoryEndpoint>` en el valor de la propiedad `loginEndpoint` de los metadatos de `ResourceManagerUrl` recuperados en la sección anterior de este documento. El valor de `<tenantID>` debe ser el identificador de su inquilino de Azure Stack Hub.

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
    Establezca `<clientID>` en el identificador de aplicación de la entidad de servicio que guardó al crear esta última en la sección anterior de este artículo.
    Establezca `<clientSecret>` en el secreto de la aplicación de la entidad de servicio que guardó al crear esta última en la sección anterior de este artículo.

## <a name="example"></a>Ejemplo

En este ejemplo se muestra un ejemplo de código de Go que crea una red virtual en Azure Stack Hub. Para ver ejemplos completos de SDK para GO, consulte el [repositorio de ejemplos de Azure SDK para GO](https://github.com/Azure-Samples/azure-sdk-for-go-samples). Hay ejemplos de Azure Stack Hub disponibles en la ruta de acceso de hybrid dentro de las carpetas de servicios del repositorio.

> [!NOTE]  
> Para ejecutar el código de este ejemplo, compruebe que la suscripción utilizada tiene el proveedor de recursos **Red** como **Registrado**. Para comprobarlo, busque la suscripción en el portal de Azure Stack Hub y seleccione **Proveedores de recursos**.

1. Importe los paquetes necesarios en el código. Use el perfil más reciente disponible en Azure Stack Hub para importar el módulo de red:

   ```go
   package main

   import (
       "context"
       "fmt"
       "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
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

3. Ahora que ha definido las variables del entorno, agregue un método para crear un token de autenticación mediante el paquete **adal**. Para obtener más información acerca de la autenticación, consulte la sección anterior.

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
      "github.com/Azure/azure-sdk-for-go/profiles/2019-03-01/network/mgmt/network"
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
      err := future.WaitForCompletionRef(context.Background(), vnetClient.Client)
      if err != nil {
          fmt.Printf(err.Error())
          return
      }
   }
   ```

Algunos de los ejemplos de código disponibles para Azure Stack Hub que usan el SDK de Go son:

- [Creación de la máquina virtual](https://github.com/Azure-Samples/Hybrid-Compute-Go-Create-VM)
- [Almacenamiento Dataplane](https://github.com/Azure-Samples/Hybrid-Storage-Go-Dataplane)
- [Uso de Managed Disks](https://github.com/Azure-Samples/Hybrid-Compute-Go-ManagedDisks) (ejemplo que usa el perfil 2019-03-01, cuyo destino son las versiones más recientes de la API compatibles con Azure Stack Hub)

## <a name="next-steps"></a>Pasos siguientes

- [Instalación de PowerShell para Azure Stack Hub](../operator/powershell-install-az-module.md)
- [Configuración del entorno de PowerShell del usuario de Azure Stack Hub](azure-stack-powershell-configure-user.md)
