---
title: Uso de perfiles de la versión de la API con Ruby en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo usar los perfiles de la versión de la API con Ruby en Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: B82E4979-FB78-4522-B9A1-84222D4F854B
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/01/2019
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: d9ef8ab09031db59311317693f72433b63737c34
ms.sourcegitcommit: 3d14ae30ce3ee44729e5419728cce14b3000e968
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2019
ms.locfileid: "71814462"
---
# <a name="use-api-version-profiles-with-ruby-in-azure-stack"></a>Uso de los perfiles de la versión de la API con Ruby en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

## <a name="ruby-and-api-version-profiles"></a>Perfiles de la versión de API y Ruby

El SDK de Ruby para Resource Manager de Azure Stack proporciona herramientas que le ayudarán a crear y administrar su infraestructura. Los proveedores de recursos del SDK incluyen los servicios Compute, Virtual Network y Storage con el lenguaje Ruby. Los perfiles de API en el SDK de Ruby habilitan el desarrollo en la nube híbrida ayudándole a cambiar entre los recursos de Azure globales y los recursos de Azure Stack.

Un perfil de API es una combinación de los proveedores de recursos y las versiones del servicio. Puede usar un perfil de API para combinar los diferentes tipos de recursos.

- Para usar las últimas versiones de todos los servicios, use el perfil **más reciente** de la gema de acumulación del SDK de Azure.
- Para usar los servicios compatibles con Azure Stack, utilice el perfil **V2019_03_01_Hybrid** o **V2018_03_01** de la gema de acumulación del SDK de Azure.
- Para usar la última versión de **API de un servicio**, utilice el perfil **más reciente** de la gema específica. Por ejemplo, para usar solo el último valor de **api-version** del servicio de proceso, utilice el perfil **más reciente** de la gema **Compute**.
- Para usar la **versión de API** de un servicio, utilice las versiones de API específicas definidas dentro de la gema.

> [!NOTE]
> Puede combinar todas las opciones en la misma aplicación.

## <a name="install-the-azure-ruby-sdk"></a>Instalación del SDK de Ruby de Azure

- Siga las instrucciones oficiales para instalar [Git](https://git-scm.com/book/en/v2/Getting-Started-Installing-Git).
- Siga las instrucciones oficiales para instalar [Ruby](https://www.ruby-lang.org/en/documentation/installation/).
  - Durante la instalación, elija la opción para **agregar Ruby a la variable PATH**.
  - Instale el kit de desarrollo durante la instalación de Ruby cuando se le pida.
  - A continuación, instale el software que instala varios programas con el siguiente comando: 

       ```Ruby
       Gem install bundler
       ```

- Si no está disponible, cree una suscripción y guarde su identificador para usarlo más adelante. Las instrucciones para crear una suscripción se encuentran en el artículo [Creación de suscripciones para ofertas en Azure Stack](../operator/azure-stack-subscribe-plan-provision-vm.md).
- Cree una entidad de servicio y guarde su identificador y su secreto. Las instrucciones para crear una entidad de servicio para Azure Stack se encuentran en el artículo [Uso de una identidad de aplicación para acceder a recursos](../operator/azure-stack-create-service-principals.md).
- Asegúrese de que la entidad de servicio tenga rol de colaborador o propietario asignado en la suscripción. Las instrucciones sobre cómo asignar un rol a una entidad de servicio se encuentran en [Uso de una identidad de aplicación para acceder a recursos](../operator/azure-stack-create-service-principals.md).

## <a name="install-the-rubygem-packages"></a>Instalación de los paquetes RubyGem

Puede instalar los paquetes RubyGem de Azure directamente.

```Ruby  
gem install azure_mgmt_compute
gem install azure_mgmt_storage
gem install azure_mgmt_resources
gem install azure_mgmt_network
```

O bien, úselas en el archivo Gemfile.

```Ruby
gem 'azure_mgmt_storage'
gem 'azure_mgmt_compute'
gem 'azure_mgmt_resources'
gem 'azure_mgmt_network'
```

El SDK de Ruby para Azure Resource Manager está en versión preliminar y probablemente tendrá cambios de interfaz importantes en próximas versiones. Un número mayor en la versión secundaria puede indicar cambios importantes.

## <a name="use-the-azure_sdk-gem"></a>Uso de la gema azure_sdk

La gema **azure_sdk** es una acumulación de todas las gemas admitidas en el SDK de Ruby. Esta gema consta de un perfil  **más reciente** , que admite la última versión de todos los servicios. Incluye los perfiles de las versiones  **V2017_03_09** y **V2019_03_01_Hybrid**, que se compilan para Azure Stack.

Puede instalar la gema de acumulación azure_sdk con el siguiente comando:  

```Ruby  
gem install 'azure_sdk'
```

## <a name="prerequisites"></a>Requisitos previos

Para usar el SDK de Ruby de Azure con Azure Stack, debe proporcionar los siguientes valores y, a continuación, establecer valores con variables de entorno. Para establecer las variables de entorno, consulte las instrucciones bajo la tabla relativas a su sistema operativo específico.

| Valor | Variables de entorno | DESCRIPCIÓN |
| --- | --- | --- |
| Id. de inquilino | `AZURE_TENANT_ID` | El [identificador de inquilino](../operator/azure-stack-identity-overview.md) de su instancia de Azure Stack. |
| Id. de cliente | `AZURE_CLIENT_ID` | Id. de la aplicación de la entidad de servicio que guardó al crear dicha entidad de servicio en la sección anterior de este artículo.  |
| Id. de suscripción | `AZURE_SUBSCRIPTION_ID` | Puede usar el [identificador de suscripción](../operator/azure-stack-plan-offer-quota-overview.md#subscriptions) para acceder a las ofertas de Azure Stack. |
| Secreto del cliente | `AZURE_CLIENT_SECRET` | Secreto de aplicación de la entidad de servicio que guardó al crear dicha entidad de servicio. |
| Punto de conexión de Resource Manager | `ARM_ENDPOINT` | Consulte [Punto de conexión de Resource Manager de Azure Stack](#the-azure-stack-resource-manager-endpoint).  |

### <a name="the-azure-stack-resource-manager-endpoint"></a>Punto de conexión de Resource Manager de Azure Stack

Microsoft Azure Resource Manager es un marco de administración que permite a sus administradores implementar, administrar y supervisar recursos de Azure. Azure Resource Manager puede controlar estas tareas como grupo, en vez de individualmente, en una sola operación.

Puede obtener la información de metadatos en el punto de conexión de Resource Manager. El punto de conexión devuelve un archivo JSON con la información necesaria para ejecutar el código.

 > [!NOTE]  
 > El valor de **ResourceManagerUrl** del Kit de desarrollo de Azure Stack (ASDK) es: `https://management.local.azurestack.external/` El valor de **ResourceManagerUrl** en los sistemas integrados es: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`  
 > Para recuperar los metadatos necesarios: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`
  
 Archivo JSON de ejemplo:

 ```json
 {
   "galleryEndpoint": "https://portal.local.azurestack.external:30015/",  
   "graphEndpoint": "https://graph.windows.net/",  
   "portal Endpoint": "https://portal.local.azurestack.external/",
   "authentication": {
     "loginEndpoint": "https://login.windows.net/",
     "audiences": ["https://management.<yourtenant>.onmicrosoft.com/3cc5febd-e4b7-4a85-a2ed-1d730e2f5928"]
   }
 }
```

### <a name="set-environment-variables"></a>Establecimiento de variables de entorno

#### <a name="microsoft-windows"></a>Microsoft Windows

Para establecer las variables de entorno, en un símbolo del sistema de Windows, use el siguiente formato:

```shell
set AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

#### <a name="macos-linux-and-unix-based-systems"></a>Sistemas basados en MacOS, Linux y Unix

En los sistemas basados en Unix, use el comando siguiente:

```bash
export AZURE_TENANT_ID=<YOUR_TENANT_ID>
```

## <a name="existing-api-profiles"></a>Perfiles de API existentes

La gema de acumulación **Azure_sdk** tiene los 3 perfiles siguientes:

- **V2019_03_01_Hybrid**: Perfil creado para Azure Stack. Use este perfil para todas las versiones más recientes de los servicios disponibles en Azure Stack, versión 1904 o posterior.
- **V2017_03_09**: Perfil creado para Azure Stack. Use este perfil para que los servicios sean lo más compatibles posible con Azure Stack, versión 1808 o anterior.
- **Más reciente**: El perfil consta de las versiones más recientes de todos los servicios. Use las versiones más recientes de todos los servicios.

Para obtener más información sobre los perfiles de API y Azure Stack, consulte [Resumen de perfiles de API](azure-stack-version-profiles.md#summary-of-api-profiles).

## <a name="azure-ruby-sdk-api-profile-usage"></a>Uso de perfil de API del SDK de Ruby de Azure

Use el siguiente código para crear una instancia de un cliente del perfil. Este parámetro solo es necesario para Azure Stack u otras nubes privadas. Azure global ya tiene esta configuración de forma predeterminada.

```Ruby  
active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])

provider = MsRestAzure::ApplicationTokenProvider.new(
  ENV['AZURE_TENANT_ID'],
  ENV['AZURE_CLIENT_ID'],
  ENV['AZURE_CLIENT_SECRET'],
  active_directory_settings
)
credentials = MsRest::TokenCredentials.new(provider)
options = {
  credentials: credentials,
  subscription_id: subscription_id,
  active_directory_settings: active_directory_settings,
  base_url: ENV['ARM_ENDPOINT']
}

# Target profile built for Azure Stack
client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
```

El cliente del perfil se puede usar para acceder a proveedores de recursos individuales, como de proceso, almacenamiento y red:

```Ruby  
# To access the operations associated with Compute
profile_client.compute.virtual_machines.get 'RESOURCE_GROUP_NAME', 'VIRTUAL_MACHINE_NAME'

# Option 1: To access the models associated with Compute
purchase_plan_obj = profile_client.compute.model_classes.purchase_plan.new

# Option 2: To access the models associated with Compute
# Notice Namespace: Azure::Profiles::<Profile Name>::<Service Name>::Mgmt::Models::<Model Name>
purchase_plan_obj = Azure::Profiles::V2019_03_01_Hybrid::Compute::Mgmt::Models::PurchasePlan.new
```

## <a name="define-azure-stack-environment-setting-functions"></a>Definición de las funciones de configuración del entorno de Azure Stack

Para autenticar la entidad de servicio en el entorno de Azure Stack, defina los puntos de conexión con `get_active_directory_settings()`. Este método usa la variable de entorno **ARM_Endpoint** que estableció anteriormente:

```Ruby  
# Get Authentication endpoints using Arm Metadata Endpoints
def get_active_directory_settings(armEndpoint)
  settings = MsRestAzure::ActiveDirectoryServiceSettings.new
  response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
  status_code = response.code
  response_content = response.body
  unless status_code == "200"
    error_model = JSON.load(response_content)
    fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
  end
  result = JSON.load(response_content)
  settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
  settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
  settings
end
```

## <a name="samples-using-api-profiles"></a>Ejemplos donde se usan perfiles de API

Use los ejemplos siguientes de GitHub como referencia para crear soluciones con perfiles de API de Azure Stack y Ruby:

- [Administración de recursos y grupos de recursos de Azure con Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups)
- [Administración de máquinas virtuales mediante Ruby](https://github.com/Azure-Samples/Hybrid-Compute-Ruby-Manage-VM) (ejemplo que usa el perfil 2019-03-01-hybrid para tener como destino las versiones más recientes de API compatibles con Azure Stack).
- [Implementación de una máquina virtual habilitada para SSH con una plantilla en Ruby](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Template-Deployment)

### <a name="sample-resource-manager-and-groups"></a>Grupos y Resource Manager de ejemplo

Para ejecutar el ejemplo, asegúrese de haber instalado Ruby. Si usa Visual Studio Code, descargue también la extensión SDK de Ruby.

> [!NOTE]  
> El repositorio de ejemplo es [Hybrid-Resource-Manager-Ruby-Resources-And-Groups](https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups).

1. Clone el repositorio:

   ```bash
   git clone https://github.com/Azure-Samples/Hybrid-Resource-Manager-Ruby-Resources-And-Groups.git
   ```

2. Instale las dependencias con una agrupación de trabajos:

   ```Bash
   cd Hybrid-Resource-Manager-Ruby-Resources-And-Groups
   bundle install
   ```

3. Cree una entidad de servicio de Azure con PowerShell y recupere los valores necesarios.

   Para obtener instrucciones sobre cómo crear una entidad de servicio, consulte [Uso de Azure PowerShell para crear una entidad de servicio con un certificado](../operator/azure-stack-create-service-principals.md).

   Los valores necesarios son:

   - Id. de inquilino
   - Id. de cliente
   - Secreto del cliente
   - Id. de suscripción
   - Punto de conexión de Resource Manager

   Establezca las siguientes variables de entorno con la información que recuperó de la entidad de servicio que creó:

   - `export AZURE_TENANT_ID={your tenant ID}`
   - `export AZURE_CLIENT_ID={your client ID}`
   - `export AZURE_CLIENT_SECRET={your client secret}`
   - `export AZURE_SUBSCRIPTION_ID={your subscription ID}`
   - `export ARM_ENDPOINT={your Azure Stack Resource Manager URL}`

   > [!NOTE]  
   > En Windows, use `set` en lugar de `export`.

4. Asegúrese de que la variable de ubicación está establecida en su ubicación de Azure Stack; por ejemplo, `LOCAL="local"`.

5. Agregue la siguiente línea de código si usa Azure Stack u otras nubes privadas para dirigir los puntos de conexión de Active Directory adecuados:

   ```Ruby  
   active_directory_settings = get_active_directory_settings(ENV['ARM_ENDPOINT'])
   ```

6. En la variable `options`, agregue la configuración de Active Directory y la URL base para trabajar con Azure Stack:

   ```ruby  
   options = {
   credentials: credentials,
   subscription_id: subscription_id,
   active_directory_settings: active_directory_settings,
   base_url: ENV['ARM_ENDPOINT']
   }
   ```

7. Cree el cliente del perfil que tenga como destino el perfil de Azure Stack:

   ```ruby  
   client = Azure::Resources::Profiles::V2019_03_01_Hybrid::Mgmt::Client.new(options)
   ```

8. Para autenticar la entidad de servicio con Azure Stack, los puntos de conexión deben definirse con **get_active_directory_settings()** . Este método usa la variable de entorno **ARM_Endpoint** que estableció anteriormente:

   ```ruby  
   def get_active_directory_settings(armEndpoint)
     settings = MsRestAzure::ActiveDirectoryServiceSettings.new
     response = Net::HTTP.get_response(URI("#{armEndpoint}/metadata/endpoints?api-version=1.0"))
     status_code = response.code
     response_content = response.body
     unless status_code == "200"
       error_model = JSON.load(response_content)
       fail MsRestAzure::AzureOperationError.new("Getting Azure Stack Metadata Endpoints", response, error_model)
     end
     result = JSON.load(response_content)
     settings.authentication_endpoint = result['authentication']['loginEndpoint'] unless result['authentication']['loginEndpoint'].nil?
     settings.token_audience = result['authentication']['audiences'][0] unless result['authentication']['audiences'][0].nil?
     settings
   end
   ```

9. Ejecute el ejemplo.

   ```Ruby
   bundle exec ruby example.rb
   ```

## <a name="next-steps"></a>Pasos siguientes

- [Install PowerShell for Azure Stack](../operator/azure-stack-powershell-install.md) (Instalación de PowerShell para Azure Stack)
- [Configuración del entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md)  
