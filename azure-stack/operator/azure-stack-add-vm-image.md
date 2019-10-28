---
title: Adición de una imagen de máquina virtual personalizada a Azure Stack | Microsoft Docs
description: Aprenda a agregar o quitar una imagen de máquina virtual personalizada en Azure Stack.
services: azure-stack
documentationcenter: ''
author: Justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: Justinha
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2018
ms.openlocfilehash: 91fdd5c0068638f3e597f72ce5aee50fe04b324c
ms.sourcegitcommit: b5eb024d170f12e51cc852aa2c72eabf26792d8d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2019
ms.locfileid: "72534041"
---
# <a name="add-a-custom-vm-to-azure-stack"></a>Adición de una máquina virtual personalizada a Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En Azure Stack, puede agregar una imagen de máquina virtual personalizada a Marketplace para que esté disponible para los usuarios. Las imágenes se agregan mediante plantillas de Azure Resource Manager para Azure Stack. También puede agregar estas imágenes a la interfaz de usuario de Azure Marketplace como un elemento de Marketplace mediante el portal del administrador o con Windows PowerShell. Use una imagen de Azure Marketplace global o su propia imagen de máquina virtual personalizada.

## <a name="generalize-the-vm-image"></a>Generalizar la imagen de máquina virtual

### <a name="windows"></a>Windows

Cree un disco duro virtual generalizado personalizado. Si el disco duro virtual está fuera de Azure, siga los pasos descritos en [Carga de un VHD generalizado y su uso para crear máquinas virtuales nuevas en Azure ](/azure/virtual-machines/windows/upload-generalized-managed) para utilizar **Sysprep** correctamente en el disco duro virtual en el sistema y convertirlo en generalizado.

Si el disco duro virtual es de Azure, siga las instrucciones de [Generalización de la máquina virtual de origen mediante Sysprep ](/azure/virtual-machines/windows/upload-generalized-managed#generalize-the-source-vm-by-using-sysprep) antes de migrarlo a Azure Stack.

### <a name="linux"></a>Linux

Si el disco duro virtual está fuera de Azure, siga las instrucciones adecuadas para generalizar el disco duro virtual:

- [Distribuciones basadas en CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](/azure/azure-stack/azure-stack-redhat-create-upload-vhd)
- [SLES u openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Si el disco duro virtual es de Azure, siga estas instrucciones para generalizar el disco duro virtual:

1. Detenga el servicio **waagent**:

   ```bash
   # sudo waagent -force -deprovision
   # export HISTSIZE=0
   # logout
   ```

2. Apague la máquina virtual y descargue el disco duro virtual. Si va a llevar el disco duro virtual de Azure, puede hacerlo mediante la exportación de discos, como se muestra en [Descargar un VHD de Windows desde Azure ](/azure/virtual-machines/windows/download-vhd).

Tenga en cuenta las versiones del agente Linux de Azure que funcionan con Azure Stack [como se documenta aquí](azure-stack-linux.md#azure-linux-agent). Asegúrese de que la imagen preparada con Sysprep tenga una versión del agente Linux de Azure compatible con Azure Stack.

### <a name="common-steps-for-both-windows-and-linux"></a>Pasos comunes para Windows y Linux

Antes de cargar la imagen, es importante tener en cuenta lo siguiente:

- Azure Stack solo admite la generación de una (1) máquina virtual (1) en el formato de disco duro virtual de disco fijo. El formato fijo estructura el disco lógico linealmente dentro del archivo, de manera que el desplazamiento del disco *X* se almacena en el desplazamiento del blob *X*. Un breve pie de página al final del blob describe las propiedades del disco duro virtual. Para confirmar que el disco está fijado, use el cmdlet **Get-VHD** de PowerShell.

- Azure Stack no admite discos duros virtuales dinámicos. El cambio de tamaño de un disco dinámico conectado a una máquina virtual producirá un error de estado en esta. Para mitigar este problema, elimine la máquina virtual sin eliminar el disco de la máquina virtual, un blob de disco duro virtual de una cuenta de almacenamiento. A continuación, convierta el disco duro virtual dinámico a fijo y vuelva a crear la máquina virtual.

## <a name="add-a-vm-image-as-an-azure-stack-operator-using-the-portal"></a>Adición de una imagen de máquina virtual como un operador de Azure Stack mediante el portal

1. Debe poderse hacer referencia a las imágenes mediante un identificador URI de Blob Storage. Prepare una imagen de sistema operativo Windows o Linux en formato VHD (no VHDX) y, a continuación, cargue la imagen en una cuenta de almacenamiento en Azure o Azure Stack.

   - Si el disco duro virtual está en Azure, puede usar una herramienta como [Azcopy](/azure/storage/common/storage-use-azcopy) para transferir directamente el disco duro virtual entre Azure y su cuenta de almacenamiento de Azure Stack si está ejecutando en un Azure Stack conectado.

   - En una instancia de Azure Stack desconectada, si el disco duro virtual está en Azure, deberá descargar el disco duro virtual en un equipo que tenga conectividad con Azure y Azure Stack. Después, copie el disco duro virtual en esta máquina desde Azure antes de transferir el disco duro virtual a Azure Stack con cualquiera de las [herramientas de transferencia de datos de almacenamiento](../user/azure-stack-storage-transfer.md) comunes que se pueden usar en Azure y Azure Stack.

2. Puede seguir [este ejemplo](/powershell/module/azurerm.compute/add-azurermvhd?view=azurermps-6.13.0) para cargar un disco duro virtual en una cuenta de almacenamiento en el portal de administrador de Azure Stack.

   - Es más eficaz cargar una imagen de almacenamiento de blobs de Azure Stack que en un almacenamiento de blobs de Azure porque se tarda menos tiempo en insertar la imagen en el repositorio de imágenes de Azure Stack.

   - Al cargar la [imagen de máquina virtual Windows](https://azure.microsoft.com/documentation/articles/virtual-machines-windows-upload-image/), asegúrese de sustituir el paso **Inicio de sesión en Azure** por el paso [Configuración del entorno de PowerShell del operador de Azure Stack](azure-stack-powershell-configure-admin.md).  

3. Anote el identificador URI donde cargará la imagen de Blob Storage. El identificador URI de Blob Storage tiene el siguiente formato: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* .vhd.

4. Para que sea posible acceder al blob de manera anónima, vaya al contenedor de blobs de la cuenta de almacenamiento donde se cargó el disco duro virtual de la imagen de máquina virtual. Seleccione **Blob** y, a continuación, seleccione **Directiva de acceso**. Opcionalmente, puede generar una firma de acceso compartido para el contenedor e incluirla como parte del identificador URI del blob. Este paso garantiza que el blob está disponible para su uso. Si no se puede acceder al blob de forma anónima, la imagen de la máquina virtual se creará con estado de error.

   ![Vaya a los blobs de la cuenta de almacenamiento.](./media/azure-stack-add-vm-image/image1.png)

   ![Establecimiento del acceso del blob en público](./media/azure-stack-add-vm-image/image2.png)

5. Inicie sesión como operador en Azure Stack. En el menú, seleccione **Todos los servicios** > **Imágenes** en **Proceso** > **Agregar**.

6. En **Crear imagen**, escriba los datos de nombre, suscripción, grupo de recursos, ubicación, disco del sistema operativo, tipo de sistema operativo, URI del blob de almacenamiento, tipo de cuenta y almacenamiento en caché de host. A continuación, seleccione **Crear** para empezar a crear la imagen de máquina virtual.

   ![Inicio de la creación de la imagen](./media/azure-stack-add-vm-image/image4.png)

   Cuando la imagen se crea correctamente, el estado de la imagen de máquina virtual cambia a **Correcto**.

7. Cuando agregue una imagen, solo estará disponible para las plantillas basadas en Azure Resource Manager y las implementaciones de PowerShell. Para poner una imagen a disposición de los usuarios como elemento de Marketplace, publique el elemento de Marketplace siguiendo los pasos del artículo [Creación y publicación de un elemento de Marketplace](azure-stack-create-and-publish-marketplace-item.md). Asegúrese de que toma nota de los valores del **publicador**, **oferta**, **SKU** y **versión**. Los necesitará cuando modifique la plantilla de Resource Manager y de manifest.json en el archivo .azpkg personalizado.

## <a name="remove-the-vm-image-as-an-azure-stack-operator-using-the-portal"></a>Eliminación de la imagen de máquina virtual como un operador de Azure Stack mediante el portal

1. Abra el [portal de administración](https://adminportal.local.azurestack.external) de Azure Stack.

2. Si la imagen de máquina virtual tiene un elemento de Marketplace asociado, seleccione **Marketplace Management** y, después, seleccione el elemento de Marketplace de la máquina virtual que desea eliminar.

3. Si la imagen de máquina virtual no tiene un elemento de Marketplace asociado, vaya a **Todos los servicios > Proceso > Imágenes de máquina virtual** y, a continuación, seleccione el botón de puntos suspensivos ( **...** ) junto a la imagen de máquina virtual.

4. Seleccione **Eliminar**.

## <a name="add-a-vm-image-as-an-azure-stack-operator-using-powershell"></a>Adición de una imagen de máquina virtual como un operador de Azure Stack mediante PowerShell

1. [Instale PowerShell para Azure Stack](azure-stack-powershell-install.md).  

2. Inicie sesión como operador en Azure Stack. Para obtener instrucciones, consulte [Iniciar sesión como operador en Azure Stack](azure-stack-powershell-configure-admin.md).

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
     La versión de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Esta versión está en el formato *\#.\#.\#* . No incluya un espacio ni otros caracteres especiales en este campo.  
   - **osType**  
     Por ejemplo: `Linux`  
     El valor de **osType** de la imagen debe ser **Windows** o **Linux**.  
   - **OSUri**  
     Por ejemplo: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Puede especificar un URI de Blob Storage para `osDisk`.  

     Para más información, consulte la referencia de PowerShell para los cmdlets [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage) y [New-DataDiskObject](/powershell/module/Azs.Compute.Admin/New-DataDiskObject).

## <a name="remove-a-vm-image-as-an-azure-stack-operator-using-powershell"></a>Eliminación de una imagen de máquina virtual como un operador de Azure Stack mediante PowerShell

Cuando ya no necesite la imagen de máquina virtual que ha cargado, puede eliminarla de Marketplace mediante el siguiente cmdlet:

1. [Instale PowerShell para Azure Stack](azure-stack-powershell-install.md).

2. Inicie sesión como operador en Azure Stack.

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
     La versión de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Esta versión está en el formato *\#.\#.\#* . No incluya un espacio ni otros caracteres especiales en este campo.  

     Para más información sobre el cmdlet **Remove-AzsPlatformImage**, consulte la [documentación del módulo de operador de Azure Stack](/powershell/module/) para Microsoft PowerShell.

## <a name="next-steps"></a>Pasos siguientes

- [Creación y publicación de un elemento personalizado de Marketplace de Azure Stack](azure-stack-create-and-publish-marketplace-item.md)
- [Aprovisionamiento de una máquina virtual](../user/azure-stack-create-vm-template.md)
