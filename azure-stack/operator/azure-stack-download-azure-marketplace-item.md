---
title: Descarga de elementos de Marketplace desde Azure | Microsoft Docs
description: El operador en la nube puede descargar elementos de Marketplace desde Azure a una implementación de Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/10/2018
ms.openlocfilehash: 5b90bc5014d0f8ebfaed8532f62b7c4f3aee8667
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691860"
---
# <a name="download-marketplace-items-from-azure-to-azure-stack"></a>Descarga de elementos de Marketplace desde Azure a Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Los operadores en la nube pueden descargar elementos de Azure Marketplace y hacer que estén disponibles en Azure Stack. Dichos elementos se pueden elegir en una lista de elementos de Azure Marketplace que se han probados con anterioridad y se sabe que son compatibles con Azure Stack. Con frecuencia se agregan nuevos elementos a esta lista, así que es aconsejable consultar periódicamente si tiene contenido nuevo. 

Hay dos escenarios para conectarse a Azure Marketplace: 

- **Un escenario conectado**: requiere que el entorno de Azure Stack esté conectado a Internet. El portal de Azure Stack se usa para buscar y descargar elementos. 
- **Un escenario sin conexión o con conexión parcial**: requiere que se acceda a Internet mediante la herramienta de redifusión de Marketplace para descargar los elementos de este. Después, los elementos descargados se transfieren a la instalación desconectada de Azure Stack. Este escenario usa PowerShell.

En [Elementos de Azure Marketplace disponibles para Azure Stack](azure-stack-marketplace-azure-items.md) encontrará una lista de los elementos de Marketplace que se pueden descargar.

## <a name="connected-scenario"></a>Escenario conectado

Si Azure Stack se conecta a internet, se puede usar el Portal de administración para descargar los elementos de Marketplace.

### <a name="prerequisites"></a>Requisitos previos

La implementación de Azure Stack debe tener conectividad a Internet y estar [registrada en Azure](azure-stack-registration.md ).

### <a name="use-the-portal-to-download-marketplace-items"></a>Uso del portal para descargar elementos de Marketplace
  
1. Inicie sesión en el Portal de administración de Azure Stack.

2.  Antes de empezar a descargar elementos de Marketplace vea el espacio de almacenamiento que tiene disponible. Después, cuando seleccione los elementos que desea descargar, puede comparar el tamaño de la descarga con la capacidad de almacenamiento disponible. Si hay un límite de capacidad, considere distintas opciones para [administrar el espacio disponible](azure-stack-manage-storage-shares.md#manage-available-space). 

    Para examinar el espacio disponible, en **Region management** (Administración de regiones), seleccione la región que desea explorar y vaya a **Resource Providers** > **Storage** (Proveedores de recursos > Almacenamiento):

    ![Examinar espacio de almacenamiento](media/azure-stack-download-azure-marketplace-item/storage.png)

3. Abra Azure Stack Marketplace y conéctese a Azure. Para ello, seleccione **Elementos de Marketplace** y **Add from Azure** (Agregar desde Azure):

    ![Adición desde Azure](media/azure-stack-download-azure-marketplace-item/marketplace.png)

    El portal muestra la lista de elementos que se pueden descargar desde Azure Marketplace. Puede filtrar los productos por nombre, publicador o tipo de producto. También puede hacer clic en cada elemento para ver su descripción, así como información adicional, incluido su tamaño de descarga:

    ![Lista de Marketplace](media/azure-stack-download-azure-marketplace-item/image03.PNG)

4. Seleccione el elemento que desee y, después, seleccione **Download** (Descargar). Los tiempos de descarga varían.

    ![Mensaje de descarga](media/azure-stack-download-azure-marketplace-item/image04.png)

    Una vez finalizada la descarga, el nuevo elemento de Marketplace se puede implementar como usuario u operador de Azure Stack.

5. Para implementar el elemento descargado, seleccione **+Crear un recurso** y busque el nuevo elemento de Marketplace entre las distintas categorías. Luego, seleccione el elemento para comenzar el proceso de implementación. El proceso no es el mismo para todos los artículos de Marketplace. 

## <a name="disconnected-or-a-partially-connected-scenario"></a>Escenario sin conexión o con conexión parcial

Si Azure Stack está en modo sin conexión y sin conectividad a internet, use PowerShell y la *herramienta de redifusión de Marketplace* para descargar los elementos de Marketplace en un equipo con conectividad a Internet. Luego, transfiera elementos al entorno de Azure Stack. En un entorno sin conexión, no se pueden descargar elementos de Marketplace desde el portal Azure Stack. 

La herramienta de redifusión de Marketplace también se puede usar en un escenario con conexión. 

Este escenario tiene dos partes:
- **Parte 1:** descarga desde Azure Marketplace. En el equipo con acceso a Internet, configure PowerShell, descargue la herramienta de redifusión y, después, descargue los elementos desde Azure Marketplace.  
- **Parte 2:** carga y publicación en Azure Stack Marketplace. Mueva los archivos que ha descargado a su entorno de Azure Stack, impórtelos en Azure Stack y, después, publíquelos en Azure Stack Marketplace.  


### <a name="prerequisites"></a>Requisitos previos
- La implementación de Azure Stack se debe [registrar en Azure](azure-stack-registration.md ).  

- El equipo que tenga conectividad a Internet debe tener la **versión 1.2.11 del paquete Azure Stack PowerShell Module**, o cualquier versión superior. Si no están, [instale los módulos de PowerShell específicos de Azure Stack](azure-stack-powershell-install.md).  

- Para habilitar la importación de un elemento de Marketplace descargado, debe estar configurado el [entorno de PowerShell para el operador de Azure Stack](azure-stack-powershell-configure-admin.md).  

- Debe tener una [cuenta de almacenamiento](azure-stack-manage-storage-accounts.md) en Azure Stack que tenga un contenedor al que se pueda acceder de forma pública (que es un blob de almacenamiento). Utilice el contenedor como almacenamiento temporal para los archivos de la galería de elementos de Marketplace. Si no está familiarizado con las cuentas de almacenamiento y los contenedores, consulte [Carga, descarga y enumeración de blobs mediante Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal) en la documentación de Azure.

- La herramienta de redifusión de Marketplace se descarga en el primer procedimiento. 

- Puede instalar [AzCopy](/azure/storage/common/storage-use-azcopy) para que la descarga se realice de forma óptima, pero no es necesario.

### <a name="use-the-marketplace-syndication-tool-to-download-marketplace-items"></a>Uso de la herramienta de redifusión de Marketplace para descargar elementos de Marketplace

1. En un equipo con conexión a Internet, abra una consola de PowerShell como administrador.

2. Agregue la cuenta de Azure que ha usado para registrar Azure Stack. Para agregar la cuenta, en PowerShell ejecute `Add-AzureRmAccount` sin parámetros. Se le solicita que escriba las credenciales de su cuenta de Azure y puede que tenga que utilizar la autenticación en dos fases en función de la configuración de la cuenta.

3. Si tiene varias suscripciones, ejecute el siguiente comando para seleccionar la que ha usado para el registro:  

   ```powershell  
   Get-AzureRmSubscription -SubscriptionID '<Your Azure Subscription GUID>' | Select-AzureRmSubscription
   $AzureContext = Get-AzureRmContext
   ```

4. Descargue la versión más reciente de la herramienta de redifusión de Marketplace mediante el siguiente script:  

   ```powershell
   # Download the tools archive.
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12 
   invoke-webrequest https://github.com/Azure/AzureStack-Tools/archive/master.zip `
     -OutFile master.zip

   # Expand the downloaded files.
   expand-archive master.zip `
     -DestinationPath . `
     -Force

   # Change to the tools directory.
   cd .\AzureStack-Tools-master

   ```

5. Importe el módulo de redifusión e inicie la herramienta mediante la ejecución de los comandos siguientes. Reemplace `Destination folder path` por la ubicación en la que se almacenan los archivos que descargue desde Azure Marketplace.   

   ```powershell  
   Import-Module .\Syndication\AzureStack.MarketplaceSyndication.psm1

   Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes" 
   ```

   Tenga en cuenta que `Export-AzSOfflineMarketplaceItem` tiene una marca `-cloud` adicional que especifica el entorno de nube, de manera predeterminada, **azurecloud**.

6. Cuando la herramienta se ejecuta, debe ver una pantalla similar a la de la siguiente imagen, con la lista de elementos disponibles en Marketplace:

   [![Elementos emergentes de Azure Marketplace](media/azure-stack-download-azure-marketplace-item/image05.png "Azure Marketplace items")](media/azure-stack-download-azure-marketplace-item/image05.png#lightbox)

7. Seleccione el elemento que desea descargar y tome nota de la *versión* Puede mantener presionada la tecla *Ctrl* para seleccionar varias imágenes. Hará referencia a la *versión* cuando importe el elemento en el siguiente procedimiento. 
   
   También puede filtrar la lista de imágenes mediante la opción **Agregar criterios**.

8. Seleccione **Aceptar**, lea los términos legales y acéptelos. 

9. El tiempo que tarda la descarga depende del tamaño del elemento. Una vez que se completa la descarga, el elemento está disponible en la carpeta que se especificó en el script. La descarga incluye un archivo VHD (para máquinas virtuales) o un archivo .zip (para extensiones de máquina virtual). También puede incluir un paquete de galería en el formato *.azpkg*, que es simplemente un archivo .zip.

10. Si se produce un error en la descarga, puede intentarlo de nuevo volviendo a ejecutar el siguiente cmdlet de PowerShell:

    ```powershell
    Export-AzSOfflineMarketplaceItem -Destination "Destination folder path in quotes"
    ```

    Antes de volver a intentarlo, quite la carpeta de producto en la que se produjo el error de descarga. Por ejemplo, si se produce un error en el script de descarga al descargar en `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1`, quite la carpeta `D:\downloadFolder\microsoft.customscriptextension-arm-1.9.1` y, a continuación, vuelva a ejecutar el cmdlet.
 
### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1811-and-higher"></a>Importación de la descarga y su publicación en Azure Stack Marketplace (1811 y versiones posteriores)

1. Debe mover los archivos que [descargó anteriormente](#use-the-marketplace-syndication-tool-to-download-marketplace-items) de forma local para que estén disponibles en su entorno de Azure Stack. La herramienta de redifusión de Marketplace debe estar disponible también en su entorno de Azure Stack, ya que necesita utilizar la herramienta para realizar la operación de importación.

   En la siguiente imagen muestra un ejemplo de estructura de carpeta. `D:\downloadfolder` contiene todos los elementos de Marketplace descargados. Cada subcarpeta es un elemento de Marketplace (por ejemplo, `microsoft.custom-script-linux-arm-2.0.3`) y tienen como nombre el identificador del producto. Dentro de cada subcarpeta se encuentra el contenido descargado del elemento de Marketplace.

   [![Estructura de directorio de descarga de Marketplace](media/azure-stack-download-azure-marketplace-item/mp1sm.png "Marketplace download directory structure")](media/azure-stack-download-azure-marketplace-item/mp1.png#lightbox)

2. Siga las instrucciones de [este artículo](azure-stack-powershell-configure-admin.md) para configurar la sesión de PowerShell para un operador de Azure Stack. 

3. Importe el módulo de redifusión e inicie la herramienta de redifusión de Marketplace mediante la ejecución del script siguiente:

   ```powershell
   $credential = Get-Credential -Message "Enter the azure stack operator credential:"
   Import-AzSOfflineMarketplaceItem -origin "marketplace content folder" -AzsCredential $credential
   ```

   El parámetro `-origin` especifica la carpeta de nivel superior que contiene todos los productos descargados; por ejemplo, `"D:\downloadfolder"`.

   El `-AzsCredential` es opcional. Sirve para renovar el token de acceso, si ha expirado. Si el parámetro `-AzsCredential` no se especifica y el token expira, se le pide que escriba las credenciales del operador.

    > [!Note]  
    > AD FS solo admite la autenticación interactiva con identidades de usuario. Si se requiere un objeto de credencial, debe usar una entidad de servicio (SPN). Para más información acerca de cómo configurar una entidad de servicio con Azure Stack y AD FS como servicio de administración de identidades, consulte [Administración de una entidad de servicio para AD FS](azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

4. Después de que el script se complete correctamente, el elemento debe estar disponible en Azure Stack Marketplace.

### <a name="import-the-download-and-publish-to-azure-stack-marketplace-1809-and-lower"></a>Importación de la descarga y su publicación en Azure Stack Marketplace (1809 y versiones posteriores)

1. Los archivos de las imágenes de máquina virtual o plantillas de solución que haya [descargado con anterioridad](#use-the-marketplace-syndication-tool-to-download-marketplace-items) deben estar disponibles localmente para su entorno de Azure Stack.  

2. Use el Portal de administración para cargar el paquete de elementos de Marketplace (el archivo .azpkg) y la imagen del disco duro virtual (el archivo .vhd) en el almacenamiento de blobs de Azure Stack. La carga de los archivos del paquete y del disco hace que estén disponible para Azure Stack, con el fin de que más adelante el elemento se pueda publicar en Azure Stack Marketplace.

   Para poder realizar la carga es preciso tener una cuenta de almacenamiento con un contenedor con acceso público (consulte los requisitos previos para este escenario).  
   1. En el Portal de administración de Azure Stack, vaya a **Todos los servicios** y en la categoría **DATOS Y ALMACENAMIENTO**, seleccione **Cuentas de almacenamiento**.  
   
   2. Seleccione una cuenta de almacenamiento en la suscripción y, después, en **BLOB SERVICE**, seleccione **Contenedores**.  
      [![Blob service](media/azure-stack-download-azure-marketplace-item/blob-service.png "Blob service")](media/azure-stack-download-azure-marketplace-item/blob-service.png#lightbox)  
   
   3. Seleccione el contenedor que desee usar y, después, seleccione **Cargar**, para abrir el panel **Cargar blob**.  
      [![Contenedor](media/azure-stack-download-azure-marketplace-item/container.png "Container")](media/azure-stack-download-azure-marketplace-item/container.png#lightbox)  
   
   4. En el panel Cargar blob, vaya a los archivos de paquete y de disco que se cargan en el almacenamiento y seleccione **Cargar**: [![Cargar](media/azure-stack-download-azure-marketplace-item/uploadsm.png "Upload")](media/azure-stack-download-azure-marketplace-item/upload.png#lightbox)  

   5. Los archivos que se cargan aparecen en el panel del contenedor. Seleccione un archivo y, después, copie la dirección URL del panel **Propiedades del blob**. Esta dirección URL la usará en el paso siguiente, cuando importe el elemento de Marketplace en Azure Stack.  En la siguiente imagen, el contenedor es *blob-test-storage* y el archivo es *Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg*.  La dirección URL del archivo es *https://testblobstorage1.blob.local.azurestack.external/blob-test-storage/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg* .  
      [![Propiedades de blob](media/azure-stack-download-azure-marketplace-item/blob-storagesm.png "Blob properties")](media/azure-stack-download-azure-marketplace-item/blob-storage.png#lightbox)  

3. Importe la imagen de disco duro virtual en Azure Stack mediante el cmdlet **Add-AzsPlatformimage**. Cuando use este cmdlet, reemplace los valores *publisher*, *offer* y otros parámetros por los valores de la imagen que va a importar. 

   Los valores *publisher*, *offer* y *sku* de la imagen se pueden obtener en el archivo de texto que se descarga junto con el archivo AZPKG. El archivo de texto se almacena en la ubicación de destino. El valor de *versión* es el valor que anotó al descargar el elemento de Azure en el procedimiento anterior. 
 
   En el siguiente script de ejemplo, se usan los valores de la máquina virtual Windows Server 2016 Datacenter - Server Core. El valor de *-Osuri* es una ruta de acceso de ejemplo a la ubicación de almacenamiento de blobs del elemento.

   Como alternativa a este script, puede usar el [procedimiento descrito en este artículo](azure-stack-add-vm-image.md#add-a-vm-image-through-the-portal) para importar la imagen .VHD mediante Azure Portal.

   ```powershell  
   Add-AzsPlatformimage `
    -publisher "MicrosoftWindowsServer" `
    -offer "WindowsServer" `
    -sku "2016-Datacenter-Server-Core" `
    -osType Windows `
    -Version "2016.127.20171215" `
    -OsUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.vhd"  
   ```

   **Sobre las plantillas de solución:** algunas plantillas pueden incluir un pequeño archivo .VHD de 3 MB llamado **fixed3.vhd**. Dicho archivo no es preciso que se importe en Azure Stack. Fixed3.vhd.  Este archivo se incluye con algunas plantillas de solución para cumplir requisitos de publicación de Azure Marketplace.

   Vea la descripción de las plantillas y descargue e importe los requisitos adicionales, como los discos duros virtuales, necesarios para trabajar con la plantilla de solución.  
   
   **Sobre las extensiones:** cuando trabaje con las extensiones de imágenes de máquinas virtuales, use los parámetros siguientes:
   - *Publicador*
   - *Tipo*
   - *Versión*  

   No use *Offer* para las extensiones.   


4.  Use PowerShell para publicar el elemento de Marketplace en Azure Stack mediante el cmdlet **Add-AzsGalleryItem**. Por ejemplo:  
    ```powershell  
    Add-AzsGalleryItem `
     -GalleryItemUri "https://mystorageaccount.blob.local.azurestack.external/cont1/Microsoft.WindowsServer2016DatacenterServerCore-ARM.1.0.801.azpkg" `
     -Verbose
    ```
5. Después de publicar un elemento de la galería está disponible para su uso. Para confirmar que el elemento se ha publicado, vaya a **Todos los servicios** y en la categoría **GENERAL**, seleccione **Marketplace**.  Si la descarga es una plantilla de solución, asegúrese de agregar todas las imágenes de discos duros virtuales de dicha plantilla.  
  [![Ver Marketplace](media/azure-stack-download-azure-marketplace-item/view-marketplacesm.png "View marketplace")](media/azure-stack-download-azure-marketplace-item/view-marketplace.png#lightbox)  

Con el lanzamiento de Azure Stack PowerShell 1.3.0 se pueden agregar extensiones de máquina virtual. Por ejemplo:

```powershell
Add-AzsVMExtension -Publisher "Microsoft" -Type "MicroExtension" -Version "0.1.0" -ComputeRole "IaaS" -SourceBlob "https://github.com/Microsoft/PowerShell-DSC-for-Linux/archive/v1.1.1-294.zip" -SupportMultipleExtensions -VmOsType "Linux"
```

## <a name="next-steps"></a>Pasos siguientes

[Creación y publicación de un producto en Marketplace](azure-stack-create-and-publish-marketplace-item.md)