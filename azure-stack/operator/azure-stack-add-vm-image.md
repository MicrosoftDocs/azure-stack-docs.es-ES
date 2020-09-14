---
title: Adición de una imagen de máquina virtual personalizada a Azure Stack Hub
description: Aprenda a agregar o quitar una imagen de máquina virtual personalizada en Azure Stack Hub.
author: sethmanheim
ms.topic: how-to
ms.date: 9/8/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 26e93e019c0be7b5ef9d5f29b509407011083acd
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89598502"
---
# <a name="add-and-remove-a-custom-vm-image-to-azure-stack-hub"></a>Adición y eliminación de una imagen de máquina virtual personalizada a Azure Stack Hub

En Azure Stack Hub, puede agregar como operador una imagen de máquina virtual personalizada a Marketplace para que esté disponible para los usuarios. Puede agregar estas imágenes al Marketplace de Azure Stack Hub mediante el portal del administrador o Windows PowerShell. Use una imagen de Microsoft Azure Marketplace global como base para la imagen personalizada o cree la suya propia con Hyper-V.

## <a name="add-an-image"></a>Añadir una imagen

Puede encontrar instrucciones para agregar imágenes generalizadas y especializadas en la sección **Proceso** de la guía del usuario. Querrá crear una imagen generalizada antes de ofrecer la imagen a los usuarios. Para obtener instrucciones, consulte [Introducción al traslado de una máquina virtual a Azure Stack Hub](/azure-stack/user/vm-move-overview). Al crear imágenes disponibles para los inquilinos, use el portal de administración de Azure Stack Hub o los puntos de conexión de administrador en lugar del portal de usuarios o los puntos de conexión del directorio de inquilinos.

Tiene dos opciones para hacer que una imagen esté disponible para los usuarios:

- **Ofrecer una imagen solo accesible a través de Azure Resource Manager**  
  Si agrega la imagen a través del portal de administración de Azure Stack Hub en **Proceso** > **Imágenes**, todos los inquilinos pueden acceder a la imagen. Sin embargo, los usuarios deberán usar una plantilla de Azure Resource Manager para acceder a ella. No será visible en el Marketplace de Azure Stack Hub.

- **Ofrecer una imagen a través del Marketplace de Azure Stack Hub**  
    Una vez que haya agregado la imagen a través del portal de administración de Azure Stack Hub, puede crear una oferta de Marketplace. Para obtener más instrucciones, consulte [Creación y publicación de un elemento personalizado de Marketplace de Azure Stack Hub](azure-stack-create-and-publish-marketplace-item.md).


## <a name="add-a-platform-image"></a>Adición de una imagen de plataforma

Para agregar una imagen de plataforma a Azure Stack Hub, use el portal de administración de Azure Stack Hub o el punto de conexión con PowerShell. Debe haber creado un disco duro virtual generalizado. Puede encontrar instrucciones en [Introducción al traslado de una máquina virtual a Azure Stack Hub](/azure-stack/user/vm-move-overview).

### <a name="portal"></a>[Portal](#tab/image-add-portal)

Agregue la imagen de máquina virtual como un operador de Azure Stack Hub mediante el portal.

1. Inicie sesión como operador en Azure Stack Hub. En el menú, seleccione **Todos los servicios** > **Proceso** > **Imágenes** > **Agregar**.

   ![Incorporación de una imagen de máquina virtual](./media/azure-stack-add-vm-image/tca4.png)

2. En **Crear imagen**, escriba el **publicador**, la **oferta**, la **SKU**, la **versión** y el identificador URI del blob de disco del sistema operativo. A continuación, seleccione **Crear** para empezar a crear la imagen de máquina virtual.

   ![Interfaz de usuario de la instalación de prueba de la imagen personalizada](./media/azure-stack-add-vm-image/tca5.png)

   Cuando la imagen se crea correctamente, el estado de la imagen de máquina virtual cambia a **Correcto**.

3. Cuando agregue una imagen, solo estará disponible para las plantillas basadas en Azure Resource Manager y las implementaciones de PowerShell. Para poner una imagen a disposición de los usuarios como elemento de Marketplace, publique el elemento de Marketplace siguiendo los pasos del artículo [Creación y publicación de un elemento de Marketplace](azure-stack-create-and-publish-marketplace-item.md). Asegúrese de que toma nota de los valores del **publicador**, **oferta**, **SKU** y **versión**. Los necesitará cuando modifique la plantilla de Resource Manager y de manifest.json en el archivo .azpkg personalizado.

### <a name="powershell"></a>[PowerShell](#tab/image-add-ps)

 Agregue una imagen de máquina virtual como un operador de Azure Stack Hub mediante PowerShell.

1. [Instale PowerShell para Azure Stack Hub](azure-stack-powershell-install.md).  

2. Inicie sesión como operador en Azure Stack Hub. Para obtener instrucciones, consulte [Iniciar sesión como operador en Azure Stack Hub](azure-stack-powershell-configure-admin.md).

3. Abra PowerShell con un símbolo del sistema con privilegios elevados y ejecute:

   ```powershell
    Add-AzsPlatformimage -publisher "<publisher>" `
      -offer "<Offer>" `
      -sku "<SKU>" `
      -version "<#.#.#>" `
      -OSType "<OS type>" `
      -OSUri "<OS URI>"
   ```

   El cmdlet **Add-AzsPlatformimage** especifica valores usados por las plantillas de Azure Resource Manager para hacer referencia a la imagen de máquina virtual. Los valores incluyen:
   - **publisher**  
     Por ejemplo: `Canonical`  
     El segmento del nombre del **publicador** de la imagen de máquina virtual que usan los usuarios al implementar la imagen. No incluya un espacio ni otros caracteres especiales en este campo.  
   - **offer**  
     Por ejemplo: `UbuntuServer`  
     El segmento del nombre de la **oferta** de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. No incluya un espacio ni otros caracteres especiales en este campo.  
   - **sku**  
     Por ejemplo: `14.04.3-LTS`  
     El segmento del nombre de la **SKU** de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. No incluya un espacio ni otros caracteres especiales en este campo.  
   - **version**  
     Por ejemplo: `1.0.0`  
     La versión de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Esta versión está en el formato **\#.\#.\#** . No incluya un espacio ni otros caracteres especiales en este campo.  
   - **osType**  
     Por ejemplo: `Linux`  
     El valor de **osType** de la imagen debe ser **Windows** o **Linux**.  
   - **OSUri**  
     Por ejemplo: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Puede especificar un URI de Blob Storage para `osDisk`.  

     Para más información, consulte la referencia de PowerShell para el cmdlet [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage).

4. Cuando agregue una imagen, solo estará disponible para las plantillas basadas en Azure Resource Manager y las implementaciones de PowerShell. Para poner una imagen a disposición de los usuarios como elemento de Marketplace, publique el elemento de Marketplace siguiendo los pasos del artículo [Creación y publicación de un elemento de Marketplace](azure-stack-create-and-publish-marketplace-item.md). Asegúrese de que toma nota de los valores del **publicador**, **oferta**, **SKU** y **versión**. Los necesitará cuando modifique la plantilla de Resource Manager y de manifest.json en el archivo .azpkg personalizado.

---

## <a name="remove-a-platform-image"></a>Eliminación de una imagen de plataforma

Puede quitar una imagen de plataforma mediante el portal o con PowerShell.

### <a name="portal"></a>[Portal](#tab/image-rem-portal)

Para eliminar la imagen de máquina virtual como un operador de Azure Stack Hub mediante el portal de Azure Stack Hub, siga estos pasos:

1. Abra el [portal del administrador](https://portal.azure.com/signin/index) de Azure Stack Hub.

2. Si la imagen de máquina virtual tiene un elemento de Marketplace asociado, seleccione **Marketplace Management** y, después, seleccione el elemento de Marketplace de la máquina virtual que desea eliminar.

3. Si la imagen de máquina virtual no tiene un elemento de Marketplace asociado, vaya a **Todos los servicios > Proceso > Imágenes de máquina virtual** y, a continuación, seleccione el botón de puntos suspensivos ( **...** ) junto a la imagen de máquina virtual.

4. Seleccione **Eliminar**.

### <a name="powershell"></a>[PowerShell](#tab/image-rem-ps)

Para eliminar la imagen de máquina virtual como un operador de Azure Stack Hub mediante PowerShell, siga estos pasos:

1. [Instale PowerShell para Azure Stack Hub](azure-stack-powershell-install.md).

2. Inicie sesión como operador en Azure Stack Hub.

3. Abra PowerShell con un símbolo del sistema con privilegios elevados y ejecute:

   ```powershell  
   Remove-AzsPlatformImage `
    -publisher "<Publisher>" `
    -offer "<Offer>" `
    -sku "<SKU>" `
    -version "<Version>" `
   ```

   El cmdlet **Remove-AzsPlatformImage** especifica valores usados por las plantillas de Azure Resource Manager para hacer referencia a la imagen de máquina virtual. Los valores incluyen:
   - **publisher**  
     Por ejemplo: `Canonical`  
     El segmento del nombre del **publicador** de la imagen de máquina virtual que usan los usuarios al implementar la imagen. No incluya un espacio ni otros caracteres especiales en este campo.  
   - **offer**  
     Por ejemplo: `UbuntuServer`  
     El segmento del nombre de la **oferta** de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. No incluya un espacio ni otros caracteres especiales en este campo.  
   - **sku**  
     Por ejemplo: `14.04.3-LTS`  
     El segmento del nombre de la **SKU** de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. No incluya un espacio ni otros caracteres especiales en este campo.  
   - **version**  
     Por ejemplo: `1.0.0`  
     La versión de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Esta versión está en el formato **\#.\#.\#** . No incluya un espacio ni otros caracteres especiales en este campo.  

     Para más información sobre el cmdlet **Remove-AzsPlatformImage**, consulte la [documentación del módulo de operador de Azure Stack Hub](/powershell/azure/azure-stack/overview) para Microsoft PowerShell.
---
## <a name="next-steps"></a>Pasos siguientes

- [Creación y publicación de un elemento personalizado de Marketplace de Azure Stack Hub](azure-stack-create-and-publish-marketplace-item.md)
- [Aprovisionamiento de una máquina virtual](../user/azure-stack-create-vm-template.md)
