---
title: Implementación de un clúster protegido de Service Fabric en Azure Stack | Microsoft Docs
description: Aprenda a implementar un clúster protegido de Service Fabric en Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: shnatara
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: 8a323bf5d767db288cd2d876f38119b61f577afa
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909669"
---
# <a name="deploy-a-service-fabric-cluster-in-azure-stack"></a>Implementación de un clúster de Service Fabric en Azure Stack

Use el elemento **Clúster de Service Fabric** en Azure Marketplace para implementar un clúster protegido de Service Fabric en Azure Stack. 

Para más información sobre cómo trabajar con Service Fabric, consulte [Información general de Azure Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-overview) y [Escenarios de seguridad de los clústeres de Service Fabric](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) en la documentación de Azure.

El clúster de Service Fabric en Azure Stack no utiliza el proveedor de recursos Microsoft.ServiceFabric. En su lugar, en Azure Stack, el clúster de Service Fabric es un conjunto de escalado de máquinas virtuales con un conjunto de software preinstalado con [Desired State Configuration (DSC)](https://docs.microsoft.com/powershell/dsc/overview/overview).

## <a name="prerequisites"></a>Requisitos previos

Se necesitan los siguientes requisitos para implementar el clúster de Service Fabric:
1. **Certificado de clúster**  
   Este es el certificado de servidor X.509 que va a agregar a KeyVault al implementar Service Fabric. 
   - El **CN** de este certificado debe coincidir con el nombre de dominio completo (FQDN) del clúster de Service Fabric que cree. Para más información sobre FQDN, consulte [Certificados necesarios para una implementación de producción en Azure Stack de Azure App Service](../operator/azure-stack-app-service-before-you-get-started.md#certificates-required-for-azure-stack-production-deployment-of-azure-app-service).
   - El formato del certificado debe ser PFX, ya que se requieren las claves públicas y privadas. 
     Consulte los [requisitos](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) para crear este certificado del lado servidor.

     > [!NOTE]  
     > Puede usar un certificado autofirmado en lugar del certificado de servidor X.509 para fines de prueba. No es necesario que los certificados autofirmados coincidan con el nombre de dominio completo del clúster.

1. **Certificado de cliente de administración**  
   Este es el certificado que usa el cliente para autenticarse en el clúster de Service Fabric. Puede ser autofirmado. Consulte los [requisitos](https://docs.microsoft.com/azure/service-fabric/service-fabric-cluster-security) para crear este certificado de cliente.

1. **Los siguientes elementos deben estar disponibles en Marketplace de Azure Stack:**
    - **Windows Server 2016**: la plantilla usa la imagen de Windows Server 2016 para crear el clúster.  
    - **Extensión de script personalizada**: extensión de máquina virtual de Microsoft.  
    - **Configuración de estado deseado de PowerShell**: extensión de máquina virtual de Microsoft.


## <a name="add-a-secret-to-key-vault"></a>Incorporación de un secreto a Key Vault
Para implementar un clúster de Service Fabric, debe especificar el *identificador secreto* correcto de Key Vault o la dirección URL del clúster de Service Fabric. La plantilla de Azure Resource Manager, toma una instancia de Key Vault como entrada. Después, la plantilla recupera el certificado del clúster al instalar el clúster de Service Fabric.

> [!IMPORTANT]  
> Debe usar PowerShell para agregar un secreto a Key Vault para su uso con Service Fabric. No use el portal.  

Use el siguiente script para crear el identificador de Key Vault y agregar el *certificado de clúster* a él. (Consulte los [requisitos previos](#prerequisites)). Antes de ejecutar el script, revise el script de ejemplo y actualice los parámetros indicados para adecuarse a su entorno. Este script también ofrecerá los valores que debe proporcionar a la plantilla de Azure Resource Manager. 

> [!TIP]  
> Antes de ejecutar correctamente el script, debe haber una oferta pública que incluya los servicios para Compute, Network, Storage y Key Vault. 

  ```powershell
    function Get-ThumbprintFromPfx($PfxFilePath, $Password) 
        {
            return New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($PfxFilePath, $Password)
        }
    
    function Publish-SecretToKeyVault ($PfxFilePath, $Password, $KeyVaultName)
       {
            $keyVaultSecretName = "ClusterCertificate"
            $certContentInBytes = [io.file]::ReadAllBytes($PfxFilePath)
            $pfxAsBase64EncodedString = [System.Convert]::ToBase64String($certContentInBytes)
    
            $jsonObject = ConvertTo-Json -Depth 10 ([pscustomobject]@{
                data     = $pfxAsBase64EncodedString
                dataType = 'pfx'
                password = $Password
            })
    
            $jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
            $jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)
            $secret = ConvertTo-SecureString -String $jsonEncoded -AsPlainText -Force
            $keyVaultSecret = Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName -SecretValue $secret
            
            $pfxCertObject = Get-ThumbprintFromPfx -PfxFilePath $PfxFilePath -Password $Password
    
            Write-Host "KeyVault id: " -ForegroundColor Green
            (Get-AzureRmKeyVault -VaultName $KeyVaultName).ResourceId
            
            Write-Host "Secret Id: " -ForegroundColor Green
            (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $keyVaultSecretName).id
    
            Write-Host "Cluster Certificate Thumbprint: " -ForegroundColor Green
            $pfxCertObject.Thumbprint
       }
    
    #========================== CHANGE THESE VALUES ===============================
    $armEndpoint = "https://management.local.azurestack.external"
    $tenantId = "your_tenant_ID"
    $location = "local"
    $clusterCertPfxPath = "Your_path_to_ClusterCert.pfx"
    $clusterCertPfxPassword = "Your_password_for_ClusterCert.pfx"
    #==============================================================================
    
    Add-AzureRmEnvironment -Name AzureStack -ARMEndpoint $armEndpoint
    Login-AzureRmAccount -Environment AzureStack -TenantId $tenantId
    
    $rgName = "sfvaultrg"
    Write-Host "Creating Resource Group..." -ForegroundColor Yellow
    New-AzureRmResourceGroup -Name $rgName -Location $location
    
    Write-Host "Creating Key Vault..." -ForegroundColor Yellow
    $Vault = New-AzureRmKeyVault -VaultName sfvault -ResourceGroupName $rgName -Location $location -EnabledForTemplateDeployment -EnabledForDeployment -EnabledForDiskEncryption
    
    Write-Host "Publishing certificate to Vault..." -ForegroundColor Yellow
    Publish-SecretToKeyVault -PfxFilePath $clusterCertPfxPath -Password $clusterCertPfxPassword -KeyVaultName $vault.VaultName
   ``` 


Para más información, consulte [Administrar Key Vault en Azure Stack mediante PowerShell](azure-stack-key-vault-manage-powershell.md).

## <a name="deploy-the-marketplace-item"></a>Implementación de un elemento de Marketplace

1. En el portal de usuarios, vaya a **+ Crear un recurso** > **Proceso** > **Clúster de Service Fabric**. 

   ![Seleccione el clúster de Service Fabric](./media/azure-stack-solution-template-service-fabric-cluster/image2.png)

2. Para cada página como, por ejemplo, *Aspectos básicos*, rellene el formulario de implementación. Use los valores predeterminados si no está seguro de un valor.

    En caso de implementaciones en una instancia desconectada de Azure Stack o para implementar otra versión de Service Fabric, descargue el paquete de implementación de Service Fabric y el correspondiente paquete del entorno de ejecución, y hospédelo en un blob de Azure Stack. Proporcione estos valores para los campos **Service Fabric deployment package URL** (URL del paquete de implementación de Service Fabric) y **Service Fabric runtime package URL** (URL del paquete del entorno de ejecución de Service Fabric).
    > [!NOTE]  
    > Hay problemas de compatibilidad entre la última versión de Service Fabric y su SDK correspondiente. Hasta que se solucione ese problema, proporcione los siguientes parámetros para la dirección URL del paquete de implementación y la del paquete del entorno de ejecución. De lo contrario, se producirá un error en las implementaciones.
    > - Dirección URL del paquete de implementación de Service Fabric: <https://download.microsoft.com/download/8/3/6/836E3E99-A300-4714-8278-96BC3E8B5528/6.5.641.9590/Microsoft.Azure.ServiceFabric.WindowsServer.6.5.641.9590.zip>
    > - Dirección URL del paquete del entorno de ejecución de Service Fabric: <https://download.microsoft.com/download/B/0/B/B0BCCAC5-65AA-4BE3-AB13-D5FF5890F4B5/6.5.641.9590/MicrosoftAzureServiceFabric.6.5.641.9590.cab>
    >
    > En el caso de las implementaciones desconectadas, descargue estos paquetes de la ubicación especificada y hospédelos localmente en un blob de Azure Stack.

   ![Aspectos básicos](media/azure-stack-solution-template-service-fabric-cluster/image3.png)

    
3. En la página *Configuración de red*, puede especificar los puertos específicos que debe abrir para las aplicaciones:

   ![Configuración de red](media/azure-stack-solution-template-service-fabric-cluster/image4.png)

4. En la página *Security* (Seguridad), agregue los valores que obtuvo al [crear la instancia de Azure Key Vault](#add-a-secret-to-key-vault) y cargar el secreto.

   En *Huella digital del certificado de cliente de administración*, especifique la huella digital del *certificado de cliente de administración*. (Consulte los [requisitos previos](#prerequisites)).
   
   - Source Key Vault (Almacén de claves de origen):  especifique toda la cadena de `keyVault id` a partir de los resultados del script. 
   - Cluster Certificate URL (URL del certificado de clúster): especifique la dirección URL completa de `Secret Id` a partir de los resultados del script. 
   - Cluster Certificate thumbprint (Huella digital del certificado de clúster): especifique la *huella digital del certificado de clúster* a partir de los resultados del script.
   - Dirección URL de un certificado de servidor: Si desea usar un certificado independiente del certificado del clúster, cargue el certificado en un almacén de claves y proporcione la dirección URL completa al secreto. 
   - Huella digital de certificado de servidor: Especificación de la huella digital del certificado de servidor
   - Admin Client Certificate Thumbprints (Huella digital de certificado de cliente de administración): especifique la *huella digital del certificado de cliente de administración* que ha creado en los requisitos previos. 

   ![Salida del script](media/azure-stack-solution-template-service-fabric-cluster/image5.png)

   ![Seguridad](media/azure-stack-solution-template-service-fabric-cluster/image6.png)

5. Complete el asistente y, a continuación, seleccione **Crear** para implementar el clúster de Service Fabric.



## <a name="access-the-service-fabric-cluster"></a>Acceda al clúster de Service Fabric
Puede acceder al clúster de Service Fabric mediante el uso de Service Fabric Explorer o Service Fabric PowerShell.


### <a name="use-service-fabric-explorer"></a>Uso de Service Fabric Explorer
1.  Asegúrese de que el explorador tiene acceso al certificado de cliente de administración y puede autenticarse en el clúster de Service Fabric.  

    a. Abra Internet Explorer y vaya a **Opciones de Internet** > **Contenido** > **Certificados**.
  
    b. En Certificados, seleccione **Importar** para iniciar el *Asistente para importación de certificados*y, a continuación, haga clic en **Siguiente**. En la página *Archivo para importar* haga clic en **Examinar**y seleccione el **certificado de cliente de administración** que proporcionó a la plantilla de Azure Resource Manager.
        
       > [!NOTE]  
       > Este certificado no es el certificado de clúster que se agregó previamente a la instancia de Key Vault.  

    c. Asegúrese de que tiene la opción "Intercambio de información personal" seleccionada en la lista desplegable de extensión de la ventana del explorador de archivos.  

       ![Intercambio de información personal](media/azure-stack-solution-template-service-fabric-cluster/image8.png)  

    d. En la página *Almacén de certificados*, seleccione **Personal** y complete el asistente.  
       ![Almacén de certificados](media/azure-stack-solution-template-service-fabric-cluster/image9.png)  
1. Para buscar el nombre de dominio completo del clúster de Service Fabric:  

    a. Vaya al grupo de recursos que está asociado con el clúster de Service Fabric y busque el recurso *Dirección IP pública*. Seleccione el objeto asociado a la dirección IP pública para abrir la hoja *Dirección IP pública*.  

      ![Dirección IP pública](media/azure-stack-solution-template-service-fabric-cluster/image10.png)   

    b. En la hoja Dirección IP pública aparece el nombre de dominio completo como *Nombre DNS*.  

      ![Nombre DNS](media/azure-stack-solution-template-service-fabric-cluster/image11.png)  

1. Para buscar la dirección URL de Service Fabric Explorer y el punto de conexión de cliente, revise los resultados de la implementación de la plantilla.

1. En el explorador, vaya a <https://*FQDN*:19080>. Sustituya *FQDN* por el FQDN del clúster de Service Fabric del paso 2.   
   Si ha usado un certificado autofirmado, recibirá una advertencia de que la conexión no es segura. Para continuar con el sitio web, seleccione **More information** (Más información) y, a continuación, **Go on to the webpage** (Acceder a la página web). 

1. Para autenticar el sitio debe seleccionar el certificado que va a usar. Seleccione **Más opciones**, seleccione el certificado adecuado y, finalmente, haga clic en **Aceptar** para conectarse a Service Fabric Explorer. 

   ![Autenticar](media/azure-stack-solution-template-service-fabric-cluster/image14.png)



### <a name="use-service-fabric-powershell"></a>Use Service Fabric PowerShell

1. Instale el *SDK de Microsoft Azure Service Fabric* desde [Preparación del entorno de desarrollo en Windows](https://docs.microsoft.com/azure/service-fabric/service-fabric-get-started#install-the-sdk-and-tools) en la documentación de Azure Service Fabric.  

1. Una vez completada la instalación, configure las variables de entorno del sistema para asegurarse de que se puede acceder a los cmdlets de Service Fabric desde PowerShell.  
    
    a. Vaya a **Panel de control** > **Sistema y seguridad** > **Sistema** y, finalmente, seleccione **Configuración avanzada del sistema**.  
    
      ![Panel de control](media/azure-stack-solution-template-service-fabric-cluster/image15.png) 

    b. En la pestaña **Avanzado** en *Propiedades del sistema*, seleccione **Variables de entorno**.  

    c. En *Variable del sistema*, edite **Ruta de acceso** y asegúrese de que **C:\\Archivos de programa\\Microsoft Service Fabric\\bin\\Fabric\\Fabric.Code** está en la parte superior de la lista de variables de entorno.  

      ![Lista de variables de entorno](media/azure-stack-solution-template-service-fabric-cluster/image16.png)

1. Después de cambiar el orden de las variables de entorno, reinicie PowerShell y, a continuación, ejecute el siguiente script de PowerShell para acceder al clúster de Service Fabric:

   ```powershell  
    Connect-ServiceFabricCluster -ConnectionEndpoint "\[Service Fabric
    CLUSTER FQDN\]:19000" \`

    -X509Credential -ServerCertThumbprint
    761A0D17B030723A37AA2E08225CD7EA8BE9F86A \`

    -FindType FindByThumbprint -FindValue
    0272251171BA32CEC7938A65B8A6A553AA2D3283 \`

    -StoreLocation CurrentUser -StoreName My -Verbose
   ```
   
   > [!NOTE]  
   > No hay ningún *https://* antes del nombre del clúster en el script. Se requiere el puerto 19000.

## <a name="next-steps"></a>Pasos siguientes

[Implementación de Kubernetes en Azure Stack](azure-stack-solution-template-kubernetes-deploy.md).
