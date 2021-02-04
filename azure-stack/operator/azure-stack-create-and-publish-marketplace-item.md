---
title: Creación y publicación de un elemento de Marketplace en Azure Stack Hub
description: Aprenda a crear y publicar un elemento de Marketplace de Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 11/16/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 11/16/2020
ms.openlocfilehash: 1ca64f3d6084ca4f28967070c344b286e858cb9d
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/04/2021
ms.locfileid: "99534086"
---
# <a name="create-and-publish-a-custom-azure-stack-hub-marketplace-item"></a>Creación y publicación de un elemento personalizado de Marketplace de Azure Stack Hub

Cada elemento publicado en Marketplace de Azure Stack Hub usa el formato de paquete de galería de Azure (.azpkg). La herramienta *Azure Gallery Packager* permite crear un paquete de galería de Azure personalizado que puede cargar en Marketplace para Azure Stack Hub y que los usuarios pueden descargar. El proceso de implementación utiliza una plantilla de Azure Resource Manager.

## <a name="marketplace-items"></a>Elementos de Marketplace

En los ejemplos de este artículo se muestra cómo crear una oferta de Marketplace de una máquina virtuale, de tipo Windows o Linux.

### <a name="prerequisites"></a>Prerrequisitos

Antes de crear un elemento de Marketplace de máquina virtual, haga lo siguiente:

1. Cargue la imagen de máquina virtual personalizada en el portal de Azure Stack Hub; para ello, siga las instrucciones del artículo sobre la [incorporación de una imagen de máquina virtual en Azure Stack Hub](azure-stack-add-vm-image.md). 
2. Siga las instrucciones de este artículo para empaquetar la imagen (crear un archivo .azpkg) y cárguela en Marketplace en Azure Stack Hub.

## <a name="create-a-marketplace-item"></a>Creación de un elemento para Marketplace

Para crear un elemento de Marketplace personalizado, haga lo siguiente:

1. Descargue la [herramienta Azure Gallery Packager](https://aka.ms/azsmarketplaceitem) y el paquete de galería de Azure Stack Hub de ejemplo. Esta descarga incluye plantillas de máquina virtual personalizadas. Extraiga el archivo .zip y, en la carpeta **Custom VMs** (Máquinas virtuales personalizadas) podrá usar las plantillas de Windows o Linux que estén disponibles. Puede decidir volver a usar las plantillas predefinidas y modificar los parámetros correspondientes con los detalles del producto del elemento que se mostrará en el portal de Azure Stack Hub. O bien, simplemente puede volver a usar el archivo .azpkg disponible y omitir los pasos siguientes para personalizar su propio paquete de la galería.

2. Cree una plantilla de Azure Resource Manager o use nuestras plantillas de ejemplo para Windows o Linux. Estas plantillas de ejemplo se proporcionan en el archivo .zip de la Packager que descargó en el paso 1. Puede usar la plantilla y cambiar los campos de texto, o puede descargar una plantilla preconfigurada de GitHub. Para más información sobre las plantillas de Azure Resource Manager, consulte [Plantillas de Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates).

3. El paquete de galería debe contener la siguiente estructura:

   ![Captura de pantalla de la estructura del paquete de la galería](media/azure-stack-create-and-publish-marketplace-item/gallerypkg1.png)

   La estructura de los archivos de plantilla de implementación es la siguiente:

   ![Captura de pantalla de la estructura de las plantillas de implementación](media/azure-stack-create-and-publish-marketplace-item/gallerypkg2.png)

4. Reemplace los siguientes valores resaltados (con números) en la plantilla Manifest.json por el valor que proporcionó al [cargar la imagen personalizada](azure-stack-add-vm-image.md#add-a-platform-image).

   > [!NOTE]  
   > Nunca codifique de forma rígida los secretos, como las claves de producto, contraseñas o cualquier información de identificación del cliente, en la plantilla de Azure Resource Manager. Los archivos de plantilla de JSON son accesibles sin necesidad de autenticación una vez publicados en la galería. Almacene todos los secretos en [Key Vault](/azure/azure-resource-manager/resource-manager-keyvault-parameter) y llámelos desde dentro de la plantilla.

   Se recomienda que antes de publicar su propia plantilla personalizada, intente publicar el ejemplo tal cual y se asegure de que funciona en su entorno. Una vez que haya verificado que este paso funciona, elimine el ejemplo de la galería y realice cambios iterativos hasta que esté satisfecho con el resultado.

   La plantilla siguiente es un ejemplo del archivo Manifest.json:

    ```json
    {
       "$schema": "https://gallery.azure.com/schemas/2015-10-01/manifest.json#",
       "name": "Test", (1)
       "publisher": "<Publisher name>", (2)
       "version": "<Version number>", (3)
       "displayName": "ms-resource:displayName", (4)
       "publisherDisplayName": "ms-resource:publisherDisplayName", (5)
       "publisherLegalName": "ms-resource:publisherDisplayName", (6)
       "summary": "ms-resource:summary",
       "longSummary": "ms-resource:longSummary",
       "description": "ms-resource:description",
       "longDescription": "ms-resource:description",
       "links": [
        { "displayName": "ms-resource:documentationLink", "uri": "http://go.microsoft.com/fwlink/?LinkId=532898" }
        ],
       "artifacts": [
          {
             "isDefault": true
          }
       ],
       "images": [{
          "context": "ibiza",
          "items": [{
             "id": "small",
             "path": "icons\\Small.png", (7)
             "type": "icon"
             },
             {
                "id": "medium",
                "path": "icons\\Medium.png",
                "type": "icon"
             },
             {
                "id": "large",
                "path": "icons\\Large.png",
                "type": "icon"
             },
             {
                "id": "wide",
                "path": "icons\\Wide.png",
                "type": "icon"
             }]
        }]
    }
    ```

    En la lista siguiente se explican los valores numerados anteriores de la plantilla de ejemplo:

    - (1): nombre de la oferta.
    - (2): nombre del editor, sin espacio.
    - (3): versión de la plantilla, sin espacio.
    - (4): nombre que ven los clientes.
    - (5): nombre del editor que ven los clientes.
    - (6): nombre legal del editor.
    - (7): ruta de acceso y nombre de cada icono.

5. Para todos los campos que hacen referencia a **ms-resource**, tiene que cambiar los valores correspondientes en el archivo **strings/resources.json**:

    ```json
    {
    "displayName": "<OfferName.PublisherName.Version>",
    "publisherDisplayName": "<Publisher name>",
    "summary": "Create a simple VM",
    "longSummary": "Create a simple VM and use it",
    "description": "<p>This is just a sample of the type of description you could create for your gallery item!</p><p>This is a second paragraph.</p>",
    "documentationLink": "Documentation"
    }
    ```

6. Pruebe la plantilla con las [API de Azure Stack Hub](../user/azure-stack-profiles-azure-resource-manager-versions.md) para asegurarse de que el recurso puede implementarse correctamente.

7. Si la plantilla se basa en una imagen de máquina virtual, siga las instrucciones para [agregar una imagen de máquina virtual a Azure Stack Hub](azure-stack-add-vm-image.md).

8. Guarde la plantilla de Azure Resource Manager en la carpeta **/Contoso.TodoList/DeploymentTemplates/** .

9. Elija los iconos y el texto para el elemento de Marketplace. Agregue iconos a la carpeta **Iconos** y agregue texto al archivo **recursos** de la carpeta **Cadenas**. Use la convención de nomenclatura **small**, **medium**, **large** y **wide** para los iconos. Consulte la sección [Referencia de UI del elemento de Marketplace](#reference-marketplace-item-ui) para obtener una descripción detallada de estos tamaños.

    > [!NOTE]
    > Los tamaños de los cuatro iconos (pequeño, mediano, grande y ancho) son necesarios para compilar correctamente el elemento de Marketplace.

10. Para cualquier modificación adicional de Manifest.json, consulte la sección [Referencia: manifest.json del elemento de Marketplace](#reference-marketplace-item-manifestjson).

11. Cuando termine de modificar los archivos, conviértalos en archivos .azpkg. Para realizar la conversión, use la herramienta **AzureGallery.exe** y el paquete de galería de ejemplo que descargó anteriormente. Ejecute el siguiente comando:

    ```shell
    .\AzureGallery.exe package -m c:\<path>\<gallery package name>\manifest.json -o c:\Temp
    ```

    > [!NOTE]
    > La ruta de acceso de salida puede ser cualquier ruta de acceso que elija y no tiene que estar en la unidad C:. Sin embargo, debe existir la ruta de acceso completa al archivo manifest.json, así como al paquete de salida. Por ejemplo, si la ruta de acceso de salida es `C:\<path>\galleryPackageName.azpkg`, debe existir la carpeta `C:\<path>`.
    >
    >

## <a name="publish-a-marketplace-item"></a>Publicación de un elemento de Marketplace

### <a name="az-modules"></a>[Modules de Az](#tab/az)

1. Use PowerShell o el Explorador de Azure Storage para cargar el elemento de Marketplace (.azpkg) a Azure Blob Storage. Puede realizar la carga en el almacenamiento de Azure Stack Hub local o en Azure Storage, una ubicación temporal del paquete. Asegúrese de que el blob es accesible públicamente.

2. Para importar el paquete de galería en Azure Stack Hub, el primer paso es conectarse de forma remota (RDP) a la máquina virtual cliente para copiar el archivo que acaba de crear en Azure Stack Hub.

3. Agregue un contexto:

    ```powershell
    $ArmEndpoint = "https://adminmanagement.local.azurestack.external"
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint
    Connect-AzAccount -EnvironmentName "AzureStackAdmin"
    ```

4. Ejecute el siguiente script para importar el recurso en la galería:

    ```powershell
    Add-AzsGalleryItem -GalleryItemUri `
    https://sample.blob.core.windows.net/<temporary blob name>/<offerName.publisherName.version>.azpkg -Verbose
    ```

5. Compruebe que tiene una cuenta de almacenamiento válida que está disponible para almacenar el elemento. Puede obtener el valor de `GalleryItemURI` del portal del administrador de Azure Stack Hub. Seleccione **Storage account -> Blob Properties -> URL** (Cuenta de almacenamiento -> Propiedades de blob -> URL), con la extensión .azpkg. La cuenta de almacenamiento es solo para uso temporal, para publicar en Marketplace.

   Después de completar el paquete de galería y de cargarlo con **Add-AzsGalleryItem**, la máquina virtual personalizada aparecerá en Marketplace y en la vista **Crear un recurso**. Tenga en cuenta que el paquete de galería personalizado no es visible en la **administración de Marketplace**.

   [![Elemento de Marketplace personalizado cargado](media/azure-stack-create-and-publish-marketplace-item/pkg6sm.png "Elemento de Marketplace personalizado cargado")](media/azure-stack-create-and-publish-marketplace-item/pkg6.png#lightbox)

6. Una vez que el elemento se ha publicado correctamente en Marketplace, puede eliminar el contenido de la cuenta de almacenamiento.

   Ahora se puede acceder a todos los artefactos de la galería predeterminados y personalizados sin autenticación mediante las direcciones URL siguientes:

   - `https://galleryartifacts.adminhosting.[Region].[externalFQDN]/artifact/20161101/[TemplateName]/DeploymentTemplates/Template.json`
   - `https://galleryartifacts.hosting.[Region].[externalFQDN]/artifact/20161101/[TemplateName]/DeploymentTemplates/Template.json`

6. Puede quitar un elemento de Marketplace mediante el cmdlet **Remove-AzGalleryItem**. Por ejemplo:

   ```powershell
   Remove-AzsGalleryItem -Name <Gallery package name> -Verbose
   ```

> [!Note]  
> La interfaz de usuario de Marketplace puede mostrar un error después de quitar un elemento. Para solucionarlo, haga clic en **Configuración** en el portal. A continuación, seleccione **Descartar modificaciones** en **Personalización del portal**.

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm)

1. Use PowerShell o el Explorador de Azure Storage para cargar el elemento de Marketplace (.azpkg) a Azure Blob Storage. Puede realizar la carga en el almacenamiento de Azure Stack Hub local o en Azure Storage, una ubicación temporal del paquete. Asegúrese de que el blob es accesible públicamente.

2. Para importar el paquete de galería en Azure Stack Hub, el primer paso es conectarse de forma remota (RDP) a la máquina virtual cliente para copiar el archivo que acaba de crear en Azure Stack Hub.

3. Agregue un contexto:

    ```powershell
    $ArmEndpoint = "https://adminmanagement.local.azurestack.external"
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint $ArmEndpoint
    Add-AzureRMAccount -EnvironmentName "AzureStackAdmin"
    ```

4. Ejecute el siguiente script para importar el recurso en la galería:

    ```powershell
    Add-AzsGalleryItem -GalleryItemUri `
    https://sample.blob.core.windows.net/<temporary blob name>/<offerName.publisherName.version>.azpkg -Verbose
    ```

5. Compruebe que tiene una cuenta de almacenamiento válida que está disponible para almacenar el elemento. Puede obtener el valor de `GalleryItemURI` del portal del administrador de Azure Stack Hub. Seleccione **Storage account -> Blob Properties -> URL** (Cuenta de almacenamiento -> Propiedades de blob -> URL), con la extensión .azpkg. La cuenta de almacenamiento es solo para uso temporal, para publicar en Marketplace.

   Después de completar el paquete de galería y de cargarlo con **Add-AzsGalleryItem**, la máquina virtual personalizada aparecerá en Marketplace y en la vista **Crear un recurso**. Tenga en cuenta que el paquete de galería personalizado no es visible en la **administración de Marketplace**.

   [![Elemento de Marketplace personalizado cargado](media/azure-stack-create-and-publish-marketplace-item/pkg6sm.png "Elemento de Marketplace personalizado cargado")](media/azure-stack-create-and-publish-marketplace-item/pkg6.png#lightbox)

6. Una vez que el elemento se ha publicado correctamente en Marketplace, puede eliminar el contenido de la cuenta de almacenamiento.

   Ahora se puede acceder a todos los artefactos de la galería predeterminados y personalizados sin autenticación mediante las direcciones URL siguientes:

   - `https://galleryartifacts.adminhosting.[Region].[externalFQDN]/artifact/20161101/[TemplateName]/DeploymentTemplates/Template.json`
   - `https://galleryartifacts.hosting.[Region].[externalFQDN]/artifact/20161101/[TemplateName]/DeploymentTemplates/Template.json`

6. Puede quitar un elemento de Marketplace mediante el cmdlet **Remove-AzGalleryItem**. Por ejemplo:

   ```powershell
   Remove-AzsGalleryItem -Name <Gallery package name> -Verbose
   ```

> [!Note]  
> La interfaz de usuario de Marketplace puede mostrar un error después de quitar un elemento. Para solucionarlo, haga clic en **Configuración** en el portal. A continuación, seleccione **Descartar modificaciones** en **Personalización del portal**.

---

## <a name="reference-marketplace-item-manifestjson"></a>Referencia: manifest.json del elemento de Marketplace

### <a name="identity-information"></a>Información de identidad

| Nombre | Obligatorio | Tipo | Restricciones | Descripción |
| --- | --- | --- | --- | --- |
| Nombre |X |String |[A-Za-z0-9]+ | |
| Publicador |X |String |[A-Za-z0-9]+ | |
| Versión |X |String |[SemVer v2](https://semver.org/) | |

### <a name="metadata"></a>Metadatos

| Nombre | Obligatorio | Tipo | Restricciones | Descripción |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |Se recomiendan 80 caracteres |Es posible que el portal no muestre el nombre del elemento correctamente si tiene más de 80 caracteres. |
| PublisherDisplayName |X |String |Se recomiendan 30 caracteres |Es posible que el portal no muestre el nombre del editor correctamente si tiene más de 30 caracteres. |
| PublisherLegalName |X |String |256 caracteres como máximo | |
| Resumen |X |String |entre 60 y 100 caracteres | |
| LongSummary |X |String |entre 140 y 256 caracteres |Aún no se aplica a Azure Stack Hub. |
| Descripción |X |[HTML](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization) |Entre 500 y 5000 caracteres | |

### <a name="images"></a>Imágenes

Marketplace usa los siguientes iconos:

| Nombre | Ancho | Alto | Notas |
| --- | --- | --- | --- |
| Ancho |255 px |115 px |Siempre se requiere |
| grande |115 px |115 px |Siempre se requiere |
| Media |90 px |90 px |Siempre se requiere |
| Pequeña |40 px |40 px |Siempre se requiere |
| Instantánea |533 px |324 px |Opcional |

### <a name="categories"></a>Categorías

Todos los elementos de Marketplace deben etiquetarse con una categoría que identifique el lugar donde el elemento aparece en la interfaz de usuario del portal. Puede elegir una de las categorías existentes de Azure Stack Hub (**Compute**, **Data + Storage**, etc.) o elegir una nueva.

### <a name="links"></a>Vínculos

Cada elemento de Marketplace puede incluir varios vínculos a contenido adicional. Los vínculos se especifican en forma de lista de nombres e identificadores URI:

| Nombre | Obligatorio | Tipo | Restricciones | Descripción |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |64 caracteres como máximo. | |
| Identificador URI |X |URI | | |

### <a name="additional-properties"></a>Propiedades adicionales

Además de los metadatos anteriores, los creadores de Marketplace pueden proporcionar datos con el par clave-valor personalizado de la forma siguiente:

| Nombre | Obligatorio | Tipo | Restricciones | Descripción |
| --- | --- | --- | --- | --- |
| DisplayName |X |String |25 caracteres como máximo. | |
| Value |X |String |30 caracteres como máximo. | |

### <a name="html-sanitization"></a>Comprobación del estado de HTML

Todos los campos que permitan HTML [pueden tener los siguientes elementos y atributos](https://github.com/Azure/portaldocs/blob/master/gallery-sdk/generated/index-gallery.md#gallery-item-metadata-html-sanitization):

`h1, h2, h3, h4, h5, p, ol, ul, li, a[target|href], br, strong, em, b, i`

## <a name="reference-marketplace-item-ui"></a>Referencia: IU de elemento de Marketplace

Los iconos y el texto de los elementos de Marketplace, tal como se muestran en el portal de Azure Stack Hub, son los siguientes.

### <a name="create-blade"></a>Hoja Creación

![Hoja Creación: elementos de Marketplace de Azure Stack Hub](media/azure-stack-create-and-publish-marketplace-item/image1.png)

### <a name="marketplace-item-details-blade"></a>Hoja de detalles de elemento de Marketplace

![Hoja de detalles de elemento de Marketplace de Azure Stack Hub](media/azure-stack-create-and-publish-marketplace-item/image3.png)

## <a name="next-steps"></a>Pasos siguientes

- [Introducción al Marketplace de Azure Stack Hub](azure-stack-marketplace.md)
- [Descarga de elementos de Marketplace](azure-stack-download-azure-marketplace-item.md)
- [Formato y estructura de las plantillas de Azure Resource Manager](/azure/azure-resource-manager/resource-group-authoring-templates)
