---
title: Adición de una imagen de máquina virtual a Azure Stack | Microsoft Docs
description: Aprenda a agregar o quitar una imagen de máquina virtual en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 8fec1b3702aa7c8c55f1a90167b1ac13f0ac8847
ms.sourcegitcommit: e2f6205e6469b39c2395ee09424bb7632cb94c40
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70271752"
---
# <a name="add-a-vm-image-to-azure-stack"></a>Adición de una imagen de máquina virtual en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En Azure Stack, puede agregar una imagen de máquina virtual (VM) al Marketplace para que esté disponible para los usuarios. Las imágenes se agregan mediante plantillas de Azure Resource Manager para Azure Stack. También puede agregar estas imágenes a la interfaz de usuario de Azure Marketplace como un elemento de Marketplace mediante el portal de administración o con Windows PowerShell. Use una imagen de Azure Marketplace global o su propia imagen de VM personalizada.

## <a name="add-a-vm-image-through-the-portal"></a>Agregación de una imagen de máquina virtual a través del portal

> [!NOTE]  
> Con este método, debe crear el elemento de Marketplace por separado.

Debe poderse hacer referencia a las imágenes mediante un identificador URI de Blob Storage. Prepare una imagen de sistema operativo Windows o Linux en formato VHD (no VHDX) y, a continuación, cargue la imagen en una cuenta de almacenamiento en Azure o Azure Stack. Si la imagen ya se ha cargado en Blob Storage en Azure o Azure Stack, puede omitir el paso 1.

1. [Cargue una imagen de máquina virtual Windows en Azure para implementaciones de Resource Manager](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/) o, si se trata de una imagen de Linux, siga las instrucciones descritas en [Implementación de máquinas virtuales Linux en Azure Stack](azure-stack-linux.md). Antes de cargar la imagen, es importante tener en cuenta los siguientes factores:

   - Azure Stack solo admite máquinas virtuales de primera generación (1) en el formato de disco duro virtual de disco fijo. El formato fijo estructura el disco lógico linealmente dentro del archivo, de manera que el desplazamiento del disco X se almacena en el desplazamiento del blob X. Un pequeño pie de página al final del blob describe las propiedades del disco duro virtual. Para confirmar la fijación del disco, use el comando [Get-VHD](https://docs.microsoft.com/powershell/module/hyper-v/get-vhd?view=win10-ps) de PowerShell.  

     > [!IMPORTANT]  
     >  Azure Stack no admite discos duros virtuales dinámicos. El cambio de tamaño de un disco dinámico conectado a una máquina virtual producirá un error de estado en esta. Para mitigar este problema, elimine la máquina virtual sin eliminar el disco de la máquina virtual, un blob de disco duro virtual de una cuenta de almacenamiento. A continuación, convierta el disco duro virtual dinámico a fijo y vuelva a crear la máquina virtual.

   - Es más eficaz cargar una imagen en Blob Storage de Azure Stack que en Azure Blob Storage porque se tarda menos tiempo en insertar la imagen en el repositorio de imágenes de Azure Stack.

   - Al cargar la [imagen de máquina virtual Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), asegúrese de sustituir el paso **Inicio de sesión en Azure** por el paso [Configuración del entorno de PowerShell del operador de Azure Stack](azure-stack-powershell-configure-admin.md).  

   - Anote el identificador URI donde cargará la imagen de Blob Storage. El identificador URI de Blob Storage tiene el siguiente formato: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* .vhd.

   - Para que sea posible acceder al blob de manera anónima, vaya al contenedor de blobs de la cuenta de almacenamiento donde se cargó el disco duro virtual de la imagen de máquina virtual. Seleccione **Blob** y, a continuación, seleccione **Directiva de acceso**. Opcionalmente, puede generar una firma de acceso compartido para el contenedor e incluirla como parte del identificador URI del blob. Este paso garantiza que el blob está disponible para su uso. Si no se puede acceder al blob de forma anónima, la imagen de la máquina virtual se creará con estado de error.

     ![Vaya a los blobs de la cuenta de almacenamiento.](./media/azure-stack-add-vm-image/image1.png)

     ![Establecimiento del acceso del blob en público](./media/azure-stack-add-vm-image/image2.png)

2. Inicie sesión como operador en Azure Stack. En el menú, seleccione **Todos los servicios** > **Imágenes** en **Proceso** > **Agregar**.

3. En **Crear imagen**, escriba los datos de nombre, suscripción, grupo de recursos, ubicación, disco del sistema operativo, tipo de sistema operativo, URI del blob de almacenamiento, tipo de cuenta y almacenamiento en caché de host. A continuación, seleccione **Crear** para empezar a crear la imagen de máquina virtual.

   ![Inicio de la creación de la imagen](./media/azure-stack-add-vm-image/image4.png)

   Cuando la imagen se crea correctamente, el estado de la imagen de máquina virtual cambia a **Correcto**.

4. Para que la imagen de máquina virtual esté disponible con más facilidad para su consumo por el usuario en la interfaz de usuario, es una buena idea [crear un elemento de Marketplace](azure-stack-create-and-publish-marketplace-item.md).

## <a name="remove-a-vm-image-through-the-portal"></a>Eliminación de una imagen de máquina virtual a través del portal

1. Abra el portal de administración en [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).

2. Seleccione **Administración de Marketplace** y luego seleccione la máquina virtual que desea eliminar.

3. Hacer clic en **Eliminar**.

## <a name="add-a-vm-image-to-the-marketplace-by-using-powershell"></a>Adición de una imagen de máquina virtual a Marketplace con PowerShell

> [!Note]  
> Cuando agregue una imagen, solo estará disponible para las plantillas basadas en Azure Resource Manager y las implementaciones de PowerShell. Para que una imagen esté disponible para los usuarios como un elemento de Marketplace, publique el elemento de Marketplace siguiendo los pasos de este artículo: [Creación y publicación de un producto en Marketplace](azure-stack-create-and-publish-marketplace-item.md)

1. [Instale PowerShell para Azure Stack](azure-stack-powershell-install.md).  

2. Inicie sesión como operador en Azure Stack. Para obtener instrucciones, consulte [Iniciar sesión como operador en Azure Stack](azure-stack-powershell-configure-admin.md).

3. Abra PowerShell con un símbolo del sistema con privilegios elevados y ejecute:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<offer>" `
      -sku "<sku>" `
      -version "<#.#.#>" `
      -OSType "<ostype>" `
      -OSUri "<osuri>"
   ```

   El cmdlet **Add-AzsPlatformimage** especifica valores usados por las plantillas de Azure Resource Manager para hacer referencia a la imagen de máquina virtual. Los valores incluyen:
   - **publisher**  
     Por ejemplo: `Canonical`  
     El segmento del nombre del publicador de la imagen de máquina virtual que usan los usuarios al implementar la imagen. Un ejemplo es **Microsoft**. No incluya un espacio ni otros caracteres especiales en este campo.  
   - **offer**  
     Por ejemplo: `UbuntuServer`  
     El segmento del nombre de la oferta de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Un ejemplo es **WindowsServer**. No incluya un espacio ni otros caracteres especiales en este campo.  
   - **sku**  
     Por ejemplo: `14.04.3-LTS`  
     El segmento del nombre de la SKU de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Un ejemplo es **Datacenter2016**. No incluya un espacio ni otros caracteres especiales en este campo.  
   - **version**  
     Por ejemplo: `1.0.0`  
     La versión de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Esta versión está en el formato *\#.\#.\#* . Un ejemplo es **1.0.0**. No incluya un espacio ni otros caracteres especiales en este campo.  
   - **osType**  
     Por ejemplo: `Linux`  
     El valor de osType de la imagen debe ser **Windows** o **Linux**.  
   - **OSUri**  
     Por ejemplo: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Puede especificar un URI de Blob Storage para `osDisk`.  

     Para más información, consulte la referencia de PowerShell para los cmdlets [Add-AzsPlatformimage](https://docs.microsoft.com/powershell/module/azs.compute.admin/add-azsplatformimage) y [New-DataDiskObject](https://docs.microsoft.com/powershell/module/Azs.Compute.Admin/New-DataDiskObject).

## <a name="add-a-custom-vm-image-to-the-marketplace-by-using-powershell"></a>Adición de una imagen de máquina virtual personalizada a Marketplace con PowerShell
 
1. [Instale PowerShell para Azure Stack](azure-stack-powershell-install.md).

   ```powershell
    # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    $TenantID = Get-AzsDirectoryTenantId `
      -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
      -EnvironmentName AzureStackAdmin

    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

2. Si usa **Servicios de federación de Active Directory (AD FS)** , use el siguiente cmdlet:

   ```powershell
   # For Azure Stack Development Kit, this value is set to https://adminmanagement.local.azurestack.external. To get this value for Azure Stack integrated systems, contact your service provider.
   $ArmEndpoint = "<Resource Manager endpoint for your environment>"

   # For Azure Stack Development Kit, this value is set to https://graph.local.azurestack.external/. To get this value for Azure Stack integrated systems, contact your service provider.
   $GraphAudience = "<GraphAudience endpoint for your environment>"

   # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external
    ```

3. Inicie sesión como operador en Azure Stack. Para obtener instrucciones, consulte [Iniciar sesión como operador en Azure Stack](azure-stack-powershell-configure-admin.md).

4. Cree una cuenta de almacenamiento en Azure global o Azure Stack para almacenar la imagen de máquina virtual personalizada. Para obtener instrucciones, consulte [Inicio rápido: Carga, descarga y enumeración de blobs mediante Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

5. Prepare una imagen de sistema operativo Windows o Linux en formato VHD (no VHDX), cargue la imagen en la cuenta de almacenamiento y obtenga el URI donde PowerShell puede recuperar la imagen de máquina virtual.  

   ```powershell
    Add-AzureRmAccount `
      -EnvironmentName "AzureStackAdmin" `
      -TenantId $TenantID
   ```

6. (Opcional) Puede cargar una matriz de discos de datos como parte de la imagen de máquina virtual. Cree los discos de datos mediante el cmdlet New-DataDiskObject. Abra PowerShell con un símbolo del sistema con privilegios elevados y ejecute:

   ```powershell
    New-DataDiskObject -Lun 2 `
    -Uri "https://storageaccount.blob.core.windows.net/vhds/Datadisk.vhd"
   ```

7. Abra PowerShell con un símbolo del sistema con privilegios elevados y ejecute:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" -offer "<offer>" -sku "<sku>" -version "<#.#.#>" -OSType "<ostype>" -OSUri "<osuri>"
   ```

    Para más información sobre los cmdlets Add-AzsPlatformimage y New-DataDiskObject, consulte la [documentación del módulo de operador de Azure Stack](https://docs.microsoft.com/powershell/module/) para Microsoft PowerShell.

## <a name="remove-a-vm-image-by-using-powershell"></a>Eliminación de una imagen de máquina virtual mediante PowerShell

Cuando ya no necesite la imagen de máquina virtual que ha cargado, puede eliminarla de Marketplace mediante el siguiente cmdlet:

1. [Instale PowerShell para Azure Stack](azure-stack-powershell-install.md).

2. Inicie sesión como operador en Azure Stack.

3. Abra PowerShell con un símbolo del sistema con privilegios elevados y ejecute:

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<publisher>" `
    -offer "<offer>" `
    -sku "<sku>" `
    -version "<version>" `
   ```
   El cmdlet **Remove-AzsPlatformImage** especifica valores usados por las plantillas de Azure Resource Manager para hacer referencia a la imagen de máquina virtual. Los valores incluyen:
   - **publisher**  
     Por ejemplo: `Canonical`  
     El segmento del nombre del publicador de la imagen de máquina virtual que usan los usuarios al implementar la imagen. Un ejemplo es **Microsoft**. No incluya un espacio ni otros caracteres especiales en este campo.  
   - **offer**  
     Por ejemplo: `UbuntuServer`  
     El segmento del nombre de la oferta de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Un ejemplo es **WindowsServer**. No incluya un espacio ni otros caracteres especiales en este campo.  
   - **sku**  
     Por ejemplo: `14.04.3-LTS`  
     El segmento del nombre de la SKU de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Un ejemplo es **Datacenter2016**. No incluya un espacio ni otros caracteres especiales en este campo.  
   - **version**  
     Por ejemplo: `1.0.0`  
     La versión de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Esta versión está en el formato *\#.\#.\#* . Un ejemplo es **1.0.0**. No incluya un espacio ni otros caracteres especiales en este campo.  
    
     Para más información sobre el cmdlet Remove-AzsPlatformImage, consulte la [documentación del módulo de operador de Azure Stack](https://docs.microsoft.com/powershell/module/) para Microsoft PowerShell.

## <a name="next-steps"></a>Pasos siguientes

[Aprovisionamiento de una máquina virtual](../user/azure-stack-create-vm-template.md)
