---
title: Uso de perfiles de la versión de la API con Java en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo usar los perfiles de la versión de la API con Java en Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 7d2a98656671a5e6f59d46054e074e45bb9d3c89
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282897"
---
# <a name="use-api-version-profiles-with-java-in-azure-stack"></a>Uso de los perfiles de la versión de la API con Java en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

El SDK de Java para Resource Manager de Azure Stack proporciona herramientas que le ayudarán a crear y administrar su infraestructura. Los proveedores de recursos del SDK incluyen los servicios Compute, Redes, Storage, App Services y [Key Vault](/azure/key-vault/key-vault-whatis).

El SDK de Java incorpora perfiles de API mediante la inclusión de dependencias en el archivo **Pom.xml** que carga los módulos correctos en el archivo **.java**. Sin embargo, puede agregar varios perfiles como dependencias, como **2019-03-01-hybrid** o **latest** como el perfil de Azure. El uso de estas dependencias carga el módulo correcto para que cuando cree su tipo de recurso, pueda seleccionar qué versión de la API de esos perfiles quiere utilizar. Esto le permite usar las últimas versiones de Azure, mientras desarrolla en las versiones más actuales de la API para Azure Stack.

El uso del SDK de Java permite una verdadera experiencia de desarrollador de nube híbrida. Los perfiles de API en el SDK de Java habilitan el desarrollo en la nube híbrida ayudándole a cambiar entre los recursos de Azure globales y los recursos de Azure Stack.

## <a name="java-and-api-version-profiles"></a>Perfiles de la versión de API y Java

Un perfil de API es una combinación de los proveedores de recursos y las versiones de la API. Use un perfil de API para obtener la versión más reciente y más estable de cada tipo de recurso de un paquete de proveedor de recursos.

- Para usar las últimas versiones de todos los servicios, use el perfil **más reciente** como la dependencia.

  - Para usar el perfil más reciente, la dependencia es **com.microsoft.azure**.

  - Para utilizar los servicios compatibles más recientes disponibles en Azure Stack, utilice el perfil **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**.

    - El perfil se especifica en el archivo **Pom.xml** como una dependencia, que carga los módulos automáticamente si elige la clase correcta de la lista desplegable (como lo haría con .NET).

  - Las dependencias aparecerán como sigue:

     ```xml
     <dependency>
     <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
     <artifactId>azure</artifactId>
     <version>1.0.0-beta</version>
     </dependency>
     ```

  - Para utilizar versiones específicas de API para un tipo de recurso en un proveedor de recursos específico, utilice las versiones específicas de API definidas mediante Intellisense.

Puede combinar todas las opciones en la misma aplicación.

## <a name="install-the-azure-java-sdk"></a>Instalación del SDK de Java de Azure

Para instalar el SDK de Java, siga estos pasos:

1. Siga las instrucciones oficiales para instalar Git. Consulte [Introducción: Instalación de Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Siga las instrucciones para instalar el [SDK de Java](https://zulu.org/download/) y [Maven](https://maven.apache.org/). La versión correcta es la versión 8 de Java Developer Kit. La versión correcta de Apache Maven es la versión 3.0 o posterior. La variable de entorno `JAVA_HOME` se debe establecer en la ubicación de instalación de Java Development Kit para completar este inicio rápido. Para obtener más información, consulte [Creación de la primera función con Java y Maven](/azure/azure-functions/functions-create-first-java-maven).

3. Para instalar los paquetes de dependencias correctos, abra el archivo **Pom.xml** en la aplicación de Java. Agregue una dependencia como se muestra en el código siguiente:

   ```xml  
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <version>1.0.0-beta</version>
   </dependency>
   ```

4. El conjunto de paquetes que se deben instalar dependerán de la versión del perfil que desee utilizar. Los nombres de paquete para las versiones de perfiles son:

   - **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**
   - **com.microsoft.azure**
     - **más reciente**

5. Si no está disponible, cree una suscripción y guarde el identificador de la suscripción para usarlo más adelante. Para obtener instrucciones para crear una suscripción, consulte [Creación de suscripciones para ofertas en Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).

6. Cree una entidad de servicio y guarde el identificador y el secreto de cliente. Para obtener instrucciones para crear una entidad de servicio para Azure Stack, consulte [Proporcionar a las aplicaciones acceso a Azure Stack](../operator/azure-stack-create-service-principals.md). El identificador de cliente también se conoce como el identificador de aplicación al crear una entidad de servicio.

7. Asegúrese de que la entidad de servicio tenga rol de colaborador o propietario en la suscripción. Para obtener instrucciones sobre cómo asignar roles a la entidad de servicio, consulte [Proporcionar a las aplicaciones acceso a Azure Stack](../operator/azure-stack-create-service-principals.md).

## <a name="prerequisites"></a>Requisitos previos

Para usar el SDK de Azure de Java con Azure Stack, debe proporcionar los siguientes valores y, a continuación, establecer valores con variables de entorno. Para establecer las variables de entorno, consulte las instrucciones bajo la tabla para su sistema operativo.

| Valor                     | Variables de entorno | DESCRIPCIÓN                                                                                                                                                                                                          |
| ------------------------- | --------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| Id. de inquilino                 | `AZURE_TENANT_ID`            | El [identificador de inquilino](../operator/azure-stack-identity-overview.md) de su instancia de Azure Stack.                                                          |
| Id. de cliente                 | `AZURE_CLIENT_ID`             | El identificador de aplicación de la entidad de servicio que guardó al crear esta última en la sección anterior.                                                                                              |
| Id. de suscripción           | `AZURE_SUBSCRIPTION_ID`      | Puede usar el [identificador de suscripción](../operator/service-plan-offer-subscription-overview.md#subscriptions) para acceder a las ofertas de Azure Stack.                |
| Secreto del cliente             | `AZURE_CLIENT_SECRET`        | El secreto de aplicación de la entidad de servicio que guardó al crear esta última.                                                                                                                                   |
| Punto de conexión de Resource Manager | `ARM_ENDPOINT`              | Consulte el artículo [Punto de conexión de Resource Manager de Azure Stack](../user/azure-stack-version-profiles-ruby.md#the-azure-stack-resource-manager-endpoint). |
| Location                  | `RESOURCE_LOCATION`    | **Local** para Azure Stack.                                                                                                                                                                                                |

Para buscar el identificador de inquilino de Azure Stack, siga las instrucciones que se encuentran [aquí](../operator/azure-stack-csp-ref-operations.md). Para establecer las variables de entorno, use los procedimientos de las siguientes secciones:

### <a name="microsoft-windows"></a>Microsoft Windows

Para establecer las variables de entorno, en el símbolo del sistema de Windows, use el siguiente formato:

```shell
Set AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="macos-linux-and-unix-based-systems"></a>Sistemas basados en MacOS, Linux y Unix

En los sistemas basados en Unix, use el comando siguiente:

```shell
Export AZURE_TENANT_ID=<Your_Tenant_ID>
```

### <a name="trust-the-azure-stack-ca-root-certificate"></a>Confiar en el certificado de raíz de CA de Azure Stack

Si usa el Kit de desarrollo de Azure Stack (ASDK), debe confiar en el certificado raíz de la entidad de certificación de la máquina remota. No es necesario que confíe en el certificado raíz de la entidad de certificación con los sistemas integrados de Azure Stack.

#### <a name="windows"></a>Windows

1. Exporte el certificado autofirmado de Azure Stack al escritorio.

1. En un símbolo del sistema, cambie el directorio a `%JAVA_HOME%\bin`.

1. Ejecute el siguiente comando:

   ```shell
   .\keytool.exe -importcert -noprompt -file <location of the exported certificate here> -alias root -keystore %JAVA_HOME%\lib\security\cacerts -trustcacerts -storepass changeit
   ```

### <a name="the-azure-stack-resource-manager-endpoint"></a>Punto de conexión de Resource Manager de Azure Stack

Azure Resource Manager es una plataforma de administración que permite a sus administradores implementar, administrar y supervisar recursos de Azure. Azure Resource Manager puede controlar estas tareas como grupo, en vez de individualmente, en una sola operación.

Puede obtener la información de metadatos en el punto de conexión de Resource Manager. El punto de conexión devuelve un archivo JSON con la información necesaria para ejecutar el código.

Tenga en cuenta las siguientes consideraciones:

- El valor de **ResourceManagerUrl** del ASDK es `https://management.local.azurestack.external/`.

- El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`.

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
      "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
      }
}
```

## <a name="existing-api-profiles"></a>Perfiles de API existentes

- **com.microsoft.azure.profile\_2019\_03\_01\_hybrid**: último perfil creado para Azure Stack. Use este perfil de servicios para obtener una mayor compatibilidad con Azure Stack, siempre y cuando tenga una versión 1904 o posterior.

- **com.microsoft.azure.profile\_2018\_03\_01\_hybrid**: Perfil creado para Azure Stack. Use este perfil para que los servicios sean compatibles con Azure Stack para las versiones 1808 o posteriores.

- **com.microsoft.azure**: este perfil contiene las versiones más recientes de todos los servicios. Use las versiones más recientes de todos los servicios.

Para más información sobre los perfiles de API y Azure Stack, consulte [Resumen de perfiles de API](../user/azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-java-sdk-api-profile-usage"></a>Uso de perfil de API del SDK de Java de Azure

El código siguiente autentica la entidad de servicio en Azure Stack. Crea un token mediante el identificador de inquilino y la base de autenticación, que es específica de Azure Stack:

```java
AzureTokenCredentials credentials = new ApplicationTokenCredentials(client, tenant, key, AZURE_STACK)
                    .withDefaultSubscriptionID(subscriptionID);
Azure azureStack = Azure.configure()
                    .withLogLevel(com.microsoft.rest.LogLevel.BASIC)
                    .authenticate(credentials, credentials.defaultSubscriptionID());
```

Este código le permite usar las dependencias del perfil de API para implementar la aplicación correctamente en Azure Stack.

## <a name="define-azure-stack-environment-setting-functions"></a>Definición de las funciones de configuración del entorno de Azure Stack

Para registrar la nube de Azure Stack con los puntos de conexión correctos, utilice el siguiente código:

```java
// Get Azure Stack cloud endpoints
final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);

AzureEnvironment AZURE_STACK = new AzureEnvironment(new HashMap<String, String>() {
                {
                    put("managementEndpointUrl", settings.get("audience"));
                    put("resourceManagerEndpointUrl", armEndpoint);
                    put("galleryEndpointUrl", settings.get("galleryEndpoint"));
                    put("activeDirectoryEndpointUrl", settings.get("login_endpoint"));
                    put("activeDirectoryResourceID", settings.get("audience"));
                    put("activeDirectoryGraphResourceID", settings.get("graphEndpoint"));
                    put("storageEndpointSuffix", armEndpoint.substring(armEndpoint.indexOf('.')));
                    put("keyVaultDnsSuffix", ".vault" + armEndpoint.substring(armEndpoint.indexOf('.')));
                }
            });
```

La llamada `getActiveDirectorySettings` en el código anterior recupera los puntos de conexión de los puntos de conexión de los metadatos. Indica las variables de entorno de la llamada que se realiza:

```java
public static HashMap<String, String> getActiveDirectorySettings(String armEndpoint) {

    HashMap<String, String> adSettings = new HashMap<String, String>();
    try {

        // create HTTP Client
        HttpClient httpClient = HttpClientBuilder.create().build();

        // Create new getRequest with below mentioned URL
        HttpGet getRequest = new HttpGet(String.format("%s/metadata/endpoints?api-version=1.0",
                             armEndpoint));

        // Add additional header to getRequest which accepts application/xml data
        getRequest.addHeader("accept", "application/xml");

        // Execute request and catch response
        HttpResponse response = httpClient.execute(getRequest);

        // Check for HTTP response code: 200 = success
        if (response.getStatusLine().getStatusCode() != 200) {
            throw new RuntimeException("Failed : HTTP error code : " + response.getStatusLine().getStatusCode());
        }

        String responseStr = EntityUtils.toString(response.getEntity());
        JSONObject responseJson = new JSONObject(responseStr);
        adSettings.put("galleryEndpoint", responseJson.getString("galleryEndpoint"));
        JSONObject authentication = (JSONObject) responseJson.get("authentication");
        String audience = authentication.get("audiences").toString().split("\"")[1];
        adSettings.put("login_endpoint", authentication.getString("loginEndpoint"));
        adSettings.put("audience", audience);
        adSettings.put("graphEndpoint", responseJson.getString("graphEndpoint"));

    } catch (ClientProtocolException cpe) {
        cpe.printStackTrace();
        throw new RuntimeException(cpe);
    } catch (IOException ioe) {
        ioe.printStackTrace();
        throw new RuntimeException(ioe);
    }
    return adSettings;
}
```

## <a name="samples-using-api-profiles"></a>Ejemplos donde se usan perfiles de API

Use los ejemplos siguientes de GitHub como referencia para crear soluciones con perfiles de API de Azure Stack y .NET:

- [Administración de grupos de recursos](https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group)

- [Administrar cuentas de almacenamiento](https://github.com/Azure-Samples/hybrid-storage-java-manage-storage-accounts)

- [Administración de una máquina virtual](https://github.com/Azure-Samples/hybrid-compute-java-manage-vm) (actualizado con el perfil 2019-03-01-hybrid).

### <a name="sample-unit-test-project"></a>Proyecto de prueba unitaria de ejemplo

1. Clone el repositorio con el siguiente comando:

   ```shell
   git clone https://github.com/Azure-Samples/Hybrid-resources-java-manage-resource-group.git`
   ```

2. Cree una entidad de servicio de Azure y asigne un rol para acceder a la suscripción. Para obtener instrucciones sobre cómo crear una entidad de servicio, consulte [Uso de Azure PowerShell para crear una entidad de servicio con un certificado](../operator/azure-stack-create-service-principals.md).

3. Recupere las siguientes variables de entorno necesarias:

   - `AZURE_TENANT_ID`
   - `AZURE_CLIENT_ID`
   - `AZURE_CLIENT_SECRET`
   - `AZURE_SUBSCRIPTION_ID`
   - `ARM_ENDPOINT`
   - `RESOURCE_LOCATION`

4. Establezca las siguientes variables de entorno con la información que recuperó de la entidad de servicio que creó con el símbolo del sistema:

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Resource Manager URL}`
   - `export RESOURCE_LOCATION={location of Azure Stack}`

   En Windows, use **set** en lugar de **export**.

5. Use la función `getActiveDirectorySettings` para recuperar los puntos de conexión de metadatos de Azure Resource Manager.

    ```java
    // Get Azure Stack cloud endpoints
    final HashMap<String, String> settings = getActiveDirectorySettings(armEndpoint);
    ```

6. En el archivo **Pom.xml**, agregue la dependencia siguiente para utilizar el perfil **2019-03-01-hybrid** para Azure Stack. Esta dependencia instala los módulos asociados a este perfil para los proveedores de recursos de los servicios Compute, Redes, Storage, Key Vault y App Services:

   ```xml
   <dependency>
   <groupId>com.microsoft.azure.profile_2019_03_01_hybrid</groupId>
   <artifactId>azure</artifactId>
   <vers1s.0.0-beta</version>
   </dependency>
   ```

7. En el símbolo del sistema que estaba abierto para establecer las variables de entorno, escriba el siguiente comando:

   ```shell
   mvn clean compile exec:java
   ```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los perfiles de API, consulte:

- [Administración de versiones de perfiles de Azure Stack](azure-stack-version-profiles.md)
- [Versiones de API del proveedor de recursos compatibles con perfiles](azure-stack-profiles-azure-resource-manager-versions.md)
