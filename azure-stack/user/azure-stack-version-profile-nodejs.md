---
title: Uso de perfiles de la versión de API con Node.js en Azure Stack Hub
description: Aprenda a usar los perfiles de la versión de API con Node.js en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 04/30/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/30/2020
ms.openlocfilehash: 92f05840d8a2a8f58f70abd10e2860224f706d2b
ms.sourcegitcommit: 0aa5f7f20690839661c8bb3bfdbe32f82bec0c64
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/21/2020
ms.locfileid: "86566505"
---
# <a name="use-api-version-profiles-with-nodejs-software-development-kit-sdk-in-azure-stack-hub"></a>Uso de perfiles de la versión de API con el kit de desarrollo de software (SDK) de Node.js en Azure Stack Hub

## <a name="nodejs-and-api-version-profiles"></a>Perfiles de la versión de API y Node.js

Puede usar el SDK de Node.js para ayudar a crear y administrar la infraestructura de las aplicaciones. Los perfiles de API del SDK de Node.js sirven de ayuda con las soluciones de nube híbrida, ya que permiten cambiar entre los recursos globales de Azure y los recursos de Azure Stack Hub. Puede codificar una vez y, después, seleccionar como destino tanto Azure global como Azure Stack Hub. 

En este artículo, puede usar [Visual Studio Code](https://code.visualstudio.com/) como herramienta de desarrollo. Visual Studio Code puede depurar el SDK de Node.js y permite ejecutar la aplicación e instalarla en la instancia de Azure Stack Hub. Puede depurar desde Visual Studio Code o a través de una ventana de terminal que ejecute el comando `node <nodefile.js>`.

## <a name="the-nodejs-sdk"></a>El SDK de Node.js

El SDK de Node.js proporciona las herramientas de Resource Manager de Azure Stack Hub. Los proveedores de recursos del SDK incluyen servicios de proceso, redes, almacenamiento, aplicaciones y KeyVault. Hay 10 bibliotecas cliente de proveedores de recursos que se pueden instalar en la aplicación Node.js. También puede especificar el proveedor de recursos que utilizará para **2018-03-01-hybrid** o **2019-03-01-profile** con el fin de optimizar la memoria para la aplicación. Cada módulo consta de un proveedor de recursos, la versión correspondiente de API y el perfil de API. 

Un perfil de API es una combinación de los proveedores de recursos y las versiones de la API. Puede usar un perfil de API para obtener la versión más reciente y más estable de cada tipo de recurso de un paquete de proveedor de recursos.

  -   Para usar las últimas versiones de todos los servicios, use el perfil **más reciente** de los paquetes.

  -   Para utilizar los servicios compatibles con Azure Stack Hub, use **\@azure/arm-resources-profile-hybrid-2019-03-01** o **\@azure/arm-storage-profile-2019-03-01-hybrid**

### <a name="packages-in-npm"></a>Paquetes en npm

Cada proveedor de recursos tiene su propio paquete. Puede obtener el paquete desde el [registro npm](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid).

Puede encontrar los siguientes paquetes:

| Proveedor de recursos | Paquete |
| --- | --- |
| [App Service](https://www.npmjs.com/package/@azure/arm-appservice-profile-2019-03-01-hybrid) | @azure/arm-appservice-profile-2019-03-01-hybrid |
| [Suscripciones a Azure Resource Manager](https://www.npmjs.com/package/@azure/arm-subscriptions-profile-hybrid-2019-03-01) | @azure/arm-subscriptions-profile-hybrid-2019-03-01  |
| [Directiva de Azure Resource Manager](https://www.npmjs.com/package/@azure/arm-policy-profile-hybrid-2019-03-01) | @azure/arm-policy-profile-hybrid-2019-03-01
| [DNS de Azure Resource Manager](https://www.npmjs.com/package/@azure/arm-dns-profile-2019-03-01-hybrid) | @azure/arm-dns-profile-2019-03-01-hybrid  |
| [Autorización](https://www.npmjs.com/package/@azure/arm-authorization-profile-2019-03-01-hybrid) | @azure/arm-authorization-profile-2019-03-01-hybrid  |
| [Proceso](https://www.npmjs.com/package/@azure/arm-compute-profile-2019-03-01-hybrid) | @azure/arm-compute-profile-2019-03-01-hybrid |
| [Storage](https://www.npmjs.com/package/@azure/arm-storage-profile-2019-03-01-hybrid) | @azure/arm-storage-profile-2019-03-01-hybrid |
| [Network](https://www.npmjs.com/package/@azure/arm-network-profile-2019-03-01-hybrid) | @azure/arm-network-profile-2019-03-01-hybrid |
| [Recursos](https://www.npmjs.com/package/@azure/arm-resources-profile-hybrid-2019-03-01) | @azure/arm-resources-profile-hybrid-2019-03-01 |
 | [KeyVault](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid) | @azure/arm-keyvault-profile-2019-03-01-hybrid |

Para usar la versión más reciente de la API de un servicio, utilice el perfil **Más reciente** de la biblioteca cliente específica. Por ejemplo, si desea usar solo la última versión de API del servicio de recursos, utilice el perfil `azure-arm-resource` del paquete de la **biblioteca cliente de Resource Management**. paquete.

Utilice las versiones específicas de API definidas dentro del paquete para usar las versiones de API de un proveedor de recursos.

  > [!Note]  
  > Puede combinar todas las opciones en la misma aplicación.

## <a name="install-the-nodejs-sdk"></a>Instalación del SDK de Node.js

1. Instale Git. Para instrucciones, consulte [Introducción: instalación de Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).

2. Instale o actualice a la versión actual de [Node.js](https://nodejs.org/en/download/). Node.js también incluye el administrador de paquetes [npm](https://www.npmjs.com/) de JavaScript.

3. Instale o actualice [Visual Studio Code](https://code.visualstudio.com/) e instale la [extensión de Node.js](https://code.visualstudio.com/docs/nodejs/nodejs-debugging) para Visual Studio Code.

2.  Instale los paquetes de cliente para Resource Manager de Azure Stack Hub. Para obtener más información, consulte el artículo de [instalación de las bibliotecas cliente](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid).

3.  Los paquetes que se deben instalar dependerán de la versión del perfil que le gustaría utilizar. Puede encontrar una lista de proveedores de recursos en la sección [Paquetes en npm](#packages-in-npm).

4. Instale la biblioteca cliente del proveedor de recursos usando npm. En la línea de comandos, ejecute `npm install <package-name>`. Por ejemplo, puede ejecutar `npm install @azure/arm-authorization-profile-2019-03-01-hybrid` para instalar la biblioteca del proveedor de recursos de autorización.

5.  Cree una suscripción y tome nota del identificador de suscripción al usar el SDK. Para ver las instrucciones necesarias para hacerlo, consulte [Creación de suscripciones para ofertas en Azure Stack Hub](/azure/azure-stack/azure-stack-subscribe-plan-provision-vm).

6.  Cree una entidad de servicio y guarde el identificador y el secreto de cliente. El identificador de cliente también se conoce como el identificador de aplicación al crear una entidad de servicio. Para más información, consulte [Proporcionar a las aplicaciones acceso a Azure Stack Hub](../operator/azure-stack-create-service-principals.md).

7.  Asegúrese de que la entidad de servicio tenga rol de colaborador o propietario en la suscripción. Para obtener instrucciones sobre cómo asignar roles a entidades de servicio, consulte [Proporcionar a las aplicaciones acceso a Azure Stack Hub](../operator/azure-stack-create-service-principals.md).

### <a name="nodejs-prerequisites"></a>Requisitos previos de Node.js 

Para usar el SDK de Azure de Node.js con Azure Stack Hub, debe proporcionar los siguientes valores y, después, establecer valores con variables de entorno. Para establecer las variables de entorno, consulte las instrucciones bajo la tabla para su sistema operativo.

| Value | Variables de entorno | Descripción |
| --- | --- | --- |
| Id. de inquilino | TENANT\_ID | El valor de su [identificador de inquilino](/azure/azure-stack/azure-stack-identity-overview) de Azure Stack Hub. |
| Id. de cliente | CLIENT\_ID | El identificador de aplicación de la entidad de servicio que guardó al crear esta última en la sección anterior de este documento.  |
| Id. de suscripción | AZURE\_SUBSCRIPTION\_ID   Para acceder a las ofertas de Azure Stack Hub, es imprescindible el [identificador de suscripción](../operator/service-plan-offer-subscription-overview.md#subscriptions).  |
| Secreto del cliente | APPLICATION\_SECRET | El secreto de aplicación de la entidad de servicio que guardó al crear esta última. |
| Punto de conexión de Resource Manager | ARM\_ENDPOINT | Consulte [Punto de conexión de Resource Manager de Azure Stack Hub](/azure/azure-stack/user/azure-stack-version-profiles-ruby#the-azure-stack-hub-resource-manager-endpoint). |

#### <a name="set-your-environmental-variables-for-nodejs"></a>Establecimiento las variables de entorno para Node.js

Para establecer las variables de entorno, haga lo siguiente:

  - **Microsoft Windows**  

    Para establecer las variables de entorno, en el símbolo del sistema de Windows, use el siguiente formato:
      
      `set Tenant_ID=<Your_Tenant_ID>`

  - **Sistemas basados en macOS, Linux y Unix**  

    Para establecer las variables de entorno, en el símbolo del sistema de Bash, use el siguiente formato:

    `export Azure_Tenant_ID=<Your_Tenant_ID>`

**Punto de conexión de Resource Manager de Azure Stack Hub**

Microsoft Azure Resource Manager es una plataforma de administración que permite a los administradores implementar, administrar y supervisar recursos de Azure. Azure Resource Manager puede controlar estas tareas como grupo, en vez de individualmente, en una sola operación.

Puede obtener la información de metadatos del punto de conexión de Resource Manager. El punto de conexión devuelve un archivo JSON con la información necesaria para ejecutar el código.

> [!Note]  
> El valor de **ResourceManagerUrl** del Kit de desarrollo de Azure Stack (ASDK) es: `https://management.local.azurestack.external` La dirección **ResourceManagerUrl** en un sistema integrado es: `https://management.region.<fqdn>/`, donde `<fqdn>` es el nombre de dominio completo.
Para recuperar los metadatos necesarios: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

Archivo JSON de ejemplo:

```JSON  
{
    "galleryEndpoint": "https://portal.local.azurestack.external/",

    "graphEndpoint": "https://graph.windowsNode.js/",

    "portal Endpoint": "https://portal.local.azurestack.external/",

    "authentication": {

        "loginEndpoint": "https://login.windowsNode.js/",

        "audiences": ["https://management.<yourtenant>.onmicrosoft.com/"]

    }

}
```

### <a name="existing-api-profiles"></a>Perfiles de API existentes

-  **\@azure/arm-resourceprovider-profile-2019-03-01-hybrid**

    Último perfil creado para Azure Stack Hub. Use este perfil para que los servicios sean lo más compatibles posible con Azure Stack Hub, siempre y cuando tenga una marca de versión 1808, o superior.

-  **\@azure-arm-resource**

    El perfil consta de las versiones más recientes de todos los servicios. Use las versiones más recientes de todos los servicios de Azure.

Para más información sobre los perfiles de API y Azure Stack Hub, consulte [Resumen de perfiles de API](/azure/azure-stack/user/azure-stack-version-profiles#summary-of-api-profiles).

### <a name="azure-nodejs-sdk-api-profile-usage"></a>Uso de perfil de API del SDK de Node.js de Azure

Las siguientes líneas se deben usar para crear una instancia de un cliente del perfil. Este parámetro solo es necesario para Azure Stack Hub u otras nubes privadas. Azure global ya tiene esta configuración de forma predeterminada con @azure-arm-resource o @azure-arm-storage.

```Node.js  
var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;

var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
````

Se necesita el código siguiente para autenticar la entidad de servicio en Azure Stack Hub. Crea un token mediante el identificador de inquilino y la base de autenticación, que es específica de Azure Stack Hub,

```Node.js  
var clientId = process.env['AZURE_CLIENT_ID'];
var tenantId = process.env['AZURE_TENANT_ID']; //"adfs"
var secret = process.env['AZURE_CLIENT_SECRET'];
var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
var base_url = process.env['ARM_ENDPOINT'];
var resourceClient, storageClient;
```

lo que le permitirá usar la biblioteca cliente del perfil de API para implementar aplicaciones correctamente en Azure Stack Hub.

En el fragmento de código siguiente se usa el punto de conexión de Azure Resource Manager que se especifica para la instancia de Azure Stack Hub y se recopilan los datos mostrados anteriormente, como el punto de conexión de la galería, el punto de conexión de Graph, el público y el punto de conexión del portal.

```Node.js  
var map = {};
const fetchUrl = base_url + 'metadata/endpoints?api-version=1.0'
```

## <a name="environment-settings"></a>Configuración del entorno

Para autenticar la entidad de servicio en el entorno de Azure Stack Hub, use el siguiente código: El uso de este código y la configuración de las variables de entorno en el símbolo del sistema genera automáticamente esta asignación para el desarrollador.

```Node.js  
function main() {
  var initializePromise = initialize();
  initializePromise.then(function (result) {
    var userDetails = result;
    console.log("Initialized user details");
    // Use user details from here
    console.log(userDetails)
    map["name"] = "AzureStack"
    map["portalUrl"] = userDetails.portalEndpoint 
    map["resourceManagerEndpointUrl"] = base_url 
    map["galleryEndpointUrl"] = userDetails.galleryEndpoint 
    map["activeDirectoryEndpointUrl"] = userDetails.authentication.loginEndpoint.slice(0, userDetails.authentication.loginEndpoint.lastIndexOf("/") + 1) 
    map["activeDirectoryResourceId"] = userDetails.authentication.audiences[0] 
    map["activeDirectoryGraphResourceId"] = userDetails.graphEndpoint 
    map["storageEndpointSuffix"] = "." + base_url.substring(base_url.indexOf('.'))  
    map["keyVaultDnsSuffix"] = ".vault" + base_url.substring(base_url.indexOf('.')) 
    map["managementEndpointUrl"] = userDetails.authentication.audiences[0] 
    map["validateAuthority"] = "false"
    Environment.Environment.add(map);
```

## <a name="samples-using-api-profiles"></a>Ejemplos donde se usan perfiles de API

Puede usar los ejemplos siguientes como referencia para crear soluciones con perfiles de API de Azure Stack Hub y Node.js. Puede obtener los ejemplos de GitHub en los repositorios siguientes:

- [Introducción al proveedor de recursos de nodos de almacenamiento](https://github.com/Azure-Samples/hybrid-storage-nodejs-create-storageaccount)
- [Administración del nodo de ejecución](https://github.com/Azure-Samples/Hybrid-compute-nodejs-create-vm)
- [Grupos y recursos de nodos de Resource Manager](https://github.com/Azure-Samples/Hybrid-resourcegroups-nodejs-manageresources)

### <a name="sample-create-storage-account"></a>Ejemplo de creación de cuenta de almacenamiento 

1.  Clone el repositorio.

    ```bash  
    git clone https://github.com/sijuman/storage-node-resource-provider-getting-started.git
    ```

2.  Cree una entidad de servicio de Azure y asigne un rol para acceder a la suscripción. Para ver las instrucciones, consulte [Uso de Azure PowerShell para crear una entidad de servicio con un certificado](/azure/azure-stack/azure-stack-create-service-principals).

3.  Recupere los siguientes valores obligatorios:
    - Id. de inquilino
    - Id. de cliente
    - Secreto del cliente
    - Identificador de suscripción de Azure
    - Punto de conexión de Resource Manager de Azure Stack Hub

4.  Establezca las siguientes variables de entorno con la información que recuperó de la entidad de servicio que creó con el símbolo del sistema:

    ```bash  
    export TENANT_ID=<your tenant id>
    export CLIENT_ID=<your client id>
    export APPLICATION_SECRET=<your client secret>K
    export AZURE_SUBSCRIPTION_ID=<your subscription id>
    export ARM_ENDPOINT=<your Azure Stack Hub Resource manager URL>
    ```

    > [!Note]  
    > En Windows, use **set** en lugar de **export**.

5.  Abra el archivo `index.js` de la aplicación de ejemplo.

6.  Como variable de ubicación seleccione su ubicación de Azure Stack Hub. Por ejemplo, `LOCAL = "local"`.

7.  Establezca las credenciales que le permitirán autenticarse en Azure Stack Hub. Esta parte del código se incluye en este ejemplo en el archivo index.js.

    ```Node.js  
    var clientId = process.env['CLIENT_ID'];
    var tenantId = process.env['TENANT_ID']; //"adfs"
    var secret = process.env['APPLICATION_SECRET'];
    var subscriptionId = process.env['AZURE_SUBSCRIPTION_ID'];
    var base_url = process.env['ARM_ENDPOINT'];
    var resourceClient, storageClient;
    ```

8.  Comprueba que ha establecido las bibliotecas de cliente correctas mediante una combinación de las bibliotecas cliente estipuladas anteriormente. En este ejemplo hemos usado lo siguiente:

    ```Node.js
    var ResourceManagementClient = require('@azure/arm-resources-profile-hybrid-2019-03-01').ResourceManagementClient;
    var StorageManagementClient = require('@azure/arm-storage-profile-2019-03-01-hybrid').StorageManagementClient;
    ```

9.  Mediante la [búsqueda de módulos npm](https://www.npmjs.com/package/@azure/arm-keyvault-profile-2019-03-01-hybrid), busque **2019-03-01-hybrid** e instale los paquetes asociados a este perfil para los proveedores de recursos de proceso, redes, almacenamiento, KeyVault y App Services.

    Para ello, puede abrir el símbolo del sistema, redirigirlo a la carpeta raíz del repositorio y ejecutar `npm install @azure/arm-keyvault-profile-2019-03-01-hybrid` en cada proveedor de recursos utilizado.

10.  En el símbolo del sistema, ejecute el comando `npm install` para instalar todos los módulos node.js.

11.  Ejecute el ejemplo.

        ```Node.js  
        node index.js
        ```

12.  Para realizar la limpieza después de index.js, ejecute el script de limpieza.

        ```Node.js  
        Node cleanup.js <resourceGroupName> <storageAccountName>
        ```

13.  El ejemplo se ha completado correctamente. Para obtener más información del ejemplo, siga leyendo.

### <a name="what-does-indexjs-do"></a>¿Qué hace index.js?

El ejemplo crea una nueva cuenta de almacenamiento, enumeran las cuentas de almacenamiento de la suscripción o el grupo de recursos, muestra las claves de la cuenta de almacenamiento, vuelve a generar las claves de la cuenta de almacenamiento, actualiza las propiedades de la cuenta de almacenamiento y comprueba disponibilidad del nombre de la cuenta de almacenamiento.

El ejemplo comienza con el inicio de sesión mediante la entidad de servicio y la creación de los objetos **ResourceManagementClient** y **StorageManagementClient** con las credenciales y el identificador de suscripción.

A continuación, el ejemplo configura un grupo de recursos en el que creará la nueva cuenta de almacenamiento.

```Node.js  
function createResourceGroup(callback) {
  var groupParameters = { location: location, tags: { sampletag: 'sampleValue' } };
  console.log('\nCreating resource group: ' + resourceGroupName);
  return resourceClient.resourceGroups.createOrUpdate(resourceGroupName, groupParameters, callback);
}
```

### <a name="create-a-new-storage-account"></a>Creación de una cuenta de almacenamiento nueva

Después, el ejemplo crea una cuenta de almacenamiento asociada al grupo de recursos creado en el paso anterior.

En este caso, el nombre de la cuenta de almacenamiento se genera aleatoriamente para garantizar la exclusividad. Sin embargo, la llamada para crear una nueva cuenta de almacenamiento se realizará correctamente si ya existe una cuenta con el mismo nombre en la suscripción.

```Node.js  
var createParameters = {
    location: location,
    sku: {
        name: accType,
    },
    kind: 'Storage',
    tags: {
        tag1: 'val1',
        tag2: 'val2'
    }
};


console.log('\\n--&gt;Creating storage account: ' + storageAccountName + ' with parameters:\\n' + util.inspect(createParameters));

return storageClient.storageAccounts.create(resourceGroupName, storageAccountName, createParameters, callback);
```

### <a name="list-storage-accounts-in-the-subscription-or-resource-group"></a>Enumeración de las cuentas de almacenamiento de la suscripción o el grupo de recursos

El ejemplo enumera todas las cuentas de almacenamiento de una suscripción determinada:

```Node.js  
console.log('\\n--&gt;Listing storage accounts in the current subscription.');

return storageClient.storageAccounts.list(callback);

It also lists storage accounts in the resource group:

    console.log('\\n--&gt;Listing storage accounts in the resourceGroup : ' + resourceGroupName);

return storageClient.storageAccounts.listByResourceGroup(resourceGroupName, callback);
```

### <a name="read-and-regenerate-storage-account-keys"></a>Lectura y regeneración de las claves de cuentas de almacenamiento

El ejemplo enumera las claves de cuenta de almacenamiento de la cuenta de almacenamiento y el grupo de recursos recién creados:

```Node.js  
console.log('\\n--&gt;Listing storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.listKeys(resourceGroupName, storageAccountName, callback);
```

También vuelve a generar las claves de acceso de la cuenta:

```Node.js  
console.log('\\n--&gt;Regenerating storage account keys for account: ' + storageAccountName);

return storageClient.storageAccounts.regenerateKey(resourceGroupName, storageAccountName, 'key1', callback);
```

### <a name="get-storage-account-properties"></a>Obtención de las propiedades de la cuenta de almacenamiento

El ejemplo lee las propiedades de la cuenta de almacenamiento:

```Node.js  
console.log('\\n--&gt;Getting info of storage account: ' + storageAccountName);

return storageClient.storageAccounts.getProperties(resourceGroupName, storageAccountName, callback);
```

### <a name="check-storage-account-name-availability"></a>Comprobación de la disponibilidad de una cuenta de almacenamiento

El ejemplo comprueba si un nombre de cuenta de almacenamiento determinado está disponible en Azure:

```Node.js  
console.log('\\n--&gt;Checking if the storage account name : ' + storageAccountName + ' is available.');

return storageClient.storageAccounts.checkNameAvailability(storageAccountName, callback);
```

### <a name="delete-the-storage-account-and-resource-group"></a>Eliminación de la cuenta de almacenamiento y el grupo de recursos

Al ejecutar cleanup.js, se elimina la cuenta de almacenamiento que creó el ejemplo:

```Node.js  
console.log('\\nDeleting storage account : ' + storageAccountName);
return storageClient.storageAccounts.deleteMethod(resourceGroupName, storageAccountName, callback);
```

También se elimina el grupo de recursos que creó el ejemplo:

```Node.js  
console.log('\\nDeleting resource group: ' + resourceGroupName);

return resourceClient.resourceGroups.deleteMethod(resourceGroupName, callback);
```

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre los perfiles de API, consulte:

- [Administración de perfiles de la versión de API en Azure Stack Hub](azure-stack-version-profiles.md)
- [Versiones de API del proveedor de recursos compatibles con perfiles](azure-stack-profiles-azure-resource-manager-versions.md)
