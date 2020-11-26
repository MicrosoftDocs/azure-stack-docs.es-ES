---
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: include
ms.date: 10/10/2020
ms.reviewer: bryanla
ms.lastreviewed: 10/20/2020
ms.openlocfilehash: 0df920ef0c8063332a290ca5e95e1c01d755e548
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2020
ms.locfileid: "95517570"
---
Por último, determine las propiedades de implementación más recientes del proveedor de recursos y úselas para completar el proceso de rotación de secretos.

### <a name="determine-deployment-properties"></a>Determinación de las propiedades de implementación

Los proveedores de recursos se implementan en el entorno de Azure Stack Hub como un paquete de producto con versión. A los paquetes se les asigna un identificador de paquete único, con el formato `'<product-id>.<installed-version>'`. Donde `<product-id>` es una cadena única que representa el proveedor de recursos y `<installed-version>` representa una versión específica. Los secretos asociados a cada paquete se almacenan en el servicio Key Vault de Azure Stack Hub. 

Abra una consola de PowerShell con privilegios elevados y complete los pasos siguientes para determinar las propiedades necesarias para rotar los secretos del proveedor de recursos:

1. Inicie sesión en el entorno de Azure Stack Hub con sus credenciales de operador. Consulte [Conexión a Azure Stack Hub con PowerShell](../operator/azure-stack-powershell-configure-admin.md) para el script de inicio de sesión de PowerShell. Asegúrese de usar los cmdlets Az de PowerShell (en lugar de AzureRM) y reemplazar todos los valores de marcador de posición, como las direcciones URL del punto de conexión y el nombre de inquilino del directorio.

2. Ejecute el cmdlet `Get-AzsProductDeployment` para recuperar una lista de las implementaciones más recientes del proveedor de recursos. La colección `"value"` devuelta contiene un elemento para cada proveedor de recursos implementado. Busque el proveedor de recursos de interés y tome nota de los valores de estas propiedades:
   - `"name"`: contiene el identificador de producto del proveedor de recursos en el segundo segmento del valor. 
   - `"properties"."deployment"."version"`: contiene el número de versión implementado actualmente. 

   En el ejemplo siguiente, observe la implementación del proveedor de recursos de Event Hubs en el primer elemento de la colección, que tiene el identificador de producto `"microsoft.eventhub"` y la versión `"1.2003.0.0"`:

   ```powershell
   PS C:\WINDOWS\system32> Get-AzsProductDeployment -AsJson
   VERBOSE: GET https://adminmanagement.myregion.mycompany.com/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productDeployments?api-version=2019-01-01 with 0-char payload
   VERBOSE: Received 2656-char response, StatusCode = OK
   {
       "value":  [
                     {
                         "id":  "/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productDeployments/microsoft.eventhub",
                         "name":  "global/microsoft.eventhub",
                         "type":  "Microsoft.Deployment.Admin/locations/productDeployments",
                         "properties":  {
                                            "status":  "DeploymentSucceeded",
                                            "subscriptionId":  "b37ae55a-a6c6-4474-ba97-81519412adf5",
                                            "deployment":  {
                                                               "version":  "1.2003.0.0",
                                                               "actionPlanInstanceResourceId":"/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/actionplans/abcdfcd3-fef0-z1a3-z85d-z6ceb0f31e36",
                                                               "parameters":  {
   
                                                                              }
                                                           },
                                            "lastSuccessfulDeployment":  {
                                                                             "version":  "1.2003.0.0",
                                                                             "actionPlanInstanceResourceId":"/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/actionplans/abcdfcd3-fef0-z1a3-z85d-z6ceb0f31e36",
                                                                             "parameters":  {
   
                                                                                            }
                                                                         },
                                            "provisioningState":  "Succeeded"
                                        }
                     },
                     {
                     ...
                     }
                 ]
   }
   ```

3. Construya el identificador de paquete del proveedor de recursos mediante la concatenación del identificador de producto y la versión del proveedor de recursos. Por ejemplo, con los valores que se derivan del paso anterior, el identificador de paquete del proveedor de recursos de Event Hubs es `microsoft.eventhub.1.2003.0.0`. 

4. Con el identificador de paquete derivado del paso anterior, ejecute `Get-AzsProductSecret -PackageId` para recuperar la lista de tipos de secreto utilizados por el proveedor de recursos. En la colección `value` devuelta, busque el elemento que contiene el valor `"Certificate"` para la propiedad `"properties"."secretKind"`. Este elemento contiene las propiedades del secreto del certificado del proveedor de recursos. Anote el nombre asignado a este secreto del certificado, que se identifica mediante el último segmento de la propiedad `"name"`, justo encima de `"properties"`. 

   En el ejemplo siguiente, la colección de secretos devuelta para el proveedor de recursos de Event Hubs contiene un secreto de tipo `"Certificate"` llamado `aseh-ssl-gateway-pfx`. 

    ```powershell
    PS C:\WINDOWS\system32> Get-AzsProductSecret -PackageId 'microsoft.eventhub.1.2003.0.0' -AsJson
    VERBOSE: GET
    https://adminmanagement.myregion.mycompany.com/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productPackages/microsoft.eventhub.1.2003.0.0/secrets?api-version=2019-01-01 with 0-char payload
    VERBOSE: Received 617-char response, StatusCode = OK
    {
        "value":  [
                        {
                            "id":  "/subscriptions/ze22ca96-z546-zbc6-z566-z35f68799816/providers/Microsoft.Deployment.Admin/locations/global/productPackages/microsoft.eventhub.1.2003.0.0/secrets/aseh-ssl-gateway-pfx",
                            "name":  "global/microsoft.eventhub.1.2003.0.0/aseh-ssl-gateway-pfx",
                            "type":  "Microsoft.Deployment.Admin/locations/productPackages/secrets",
                            "properties":  {
                                            "secretKind":  "Certificate",
                                            "description":  "Event Hubs gateway SSL certificate.",
                                            "expiresAfter":  "P730D",
                                            "secretDescriptor":  {
    
                                                                    },
                                            "secretState":  {
                                                                "status":  "Deployed",
                                                                "rotationStatus":  "None",
                                                                "expirationDate":  "2022-03-31T00:16:05.3068718Z"
                                                            },
                                            "provisioningState":  "Succeeded"
                                        }
                        },
                        ...
                    ]
    }
    ```

### <a name="rotate-the-secrets"></a>Rotación de los secretos

1. Use el cmdlet `Set-AzsProductSecret` para importar el nuevo certificado en Key Vault, que se usará en el proceso de rotación. Reemplace los valores de marcador de posición de las variables en consecuencia antes de ejecutar el script:

   | Marcador de posición | Descripción | Valor de ejemplo |
   | ----------- | ----------- | --------------|
   | `<product-id>` | Identificador de producto de la última implementación del proveedor de recursos. | `microsoft.eventhub` |
   | `<installed-version>` | Versión de la última implementación del proveedor de recursos. | `1.2003.0.0` |
   | `<cert-secret-name>` | Nombre con el que se almacena el secreto del certificado. | `aseh-ssl-gateway-pfx` |
   | `<cert-pfx-file-path>` | Ruta de acceso al archivo PFX del certificado. | `C:\dir\eh-cert-file.pfx` |
   | `<pfx-password>` | Contraseña asignada al archivo .PFX del certificado. | `strong@CertSecret6` |

   ```powershell
   $productId = '<product-id>'
   $packageId = $productId + '.' + '<installed-version>'
   $certSecretName = '<cert-secret-name>' 
   $pfxFilePath = '<cert-pfx-file-path>'
   $pfxPassword = ConvertTo-SecureString '<pfx-password>' -AsPlainText -Force   
   Set-AzsProductSecret -PackageId $packageId -SecretName $certSecretName -PfxFileName $pfxFilePath -PfxPassword $pfxPassword -Force
   ```

2. Por último, use el cmdlet `Invoke-AzsProductRotateSecretsAction` para rotar los secretos internos y externos:

   > [!NOTE]
   > El proceso de rotación tarda aproximadamente de 3,5 a 4 horas en completarse.

   ```powershell
   Invoke-AzsProductRotateSecretsAction -ProductId $productId
   ```
   
   Puede supervisar el progreso de la rotación de secretos en la consola de PowerShell o en el portal de administración; para ello, seleccione el proveedor de recursos en el servicio Marketplace:

   [![Progreso de rotación de secretos](media/resource-provider-va-rotate-secrets-rotate/secret-rotation-in-progress.png)](media/resource-provider-va-rotate-secrets-rotate/secret-rotation-in-progress.png#lightbox)

