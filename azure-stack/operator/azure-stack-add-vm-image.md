---
title: Adición de una imagen de máquina virtual personalizada a Azure Stack Hub
description: Aprenda a agregar o quitar una imagen de máquina virtual personalizada en Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 02/07/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 06/08/2019
ms.openlocfilehash: 4d2f76e1af47800331aac44715b5b8630baceae1
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "77701435"
---
# <a name="add-a-custom-vm-image-to-azure-stack-hub"></a>Adición de una imagen de máquina virtual personalizada a Azure Stack Hub

En Azure Stack Hub, puede agregar una imagen de máquina virtual personalizada a Marketplace para que esté disponible para los usuarios. Puede agregar estas imágenes al Marketplace de Azure Stack Hub mediante el portal del administrador o Windows PowerShell. Use una imagen de Azure Marketplace global como base para la imagen personalizada o cree la suya propia con Hyper-V.

## <a name="step-1-create-the-custom-vm-image"></a>Paso 1: Creación de una imagen de máquina virtual personalizada

### <a name="windows"></a>Windows

Cree un disco duro virtual generalizado personalizado.

**Si el disco duro virtual está fuera de Azure**, siga los pasos descritos en [Carga de un VHD generalizado y su uso para crear máquinas virtuales nuevas en Azure](/azure/virtual-machines/windows/upload-generalized-managed) para utilizar **Sysprep** correctamente en el disco duro virtual en el sistema y convertirlo en generalizado.

**Si el disco duro virtual es de Azure**, antes de generalizar la máquina virtual, asegúrese de lo siguiente:

- Al aprovisionar la máquina virtual en Azure, use PowerShell y aprovisiónela sin la marca `-ProvisionVMAgent`.
- Elimine todas las extensiones de la máquina virtual mediante el cmdlet **Remove-AzureRmVMExtension** de la máquina virtual antes de generalizarla en Azure. Para saber qué extensiones de máquina virtual se instalan, vaya a `Windows (C:) > WindowsAzure > Logs > Plugins`.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```

Siga las instrucciones de [este documento](/azure/virtual-machines/windows/download-vhd) para generalizar y descargar correctamente el disco duro virtual antes de migrarlo a Azure Stack Hub.

### <a name="linux"></a>Linux

**Si el disco duro virtual está fuera de Azure**, siga las instrucciones adecuadas para generalizar el disco duro virtual:

- [Distribuciones basadas en CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](/azure/azure-stack/azure-stack-redhat-create-upload-vhd)
- [SLES u openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

**Si el disco duro virtual es de Azure**, siga estas instrucciones para generalizar y descargar el disco duro virtual:

1. Detenga el servicio **waagent**:

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   Tenga en cuenta las versiones del agente Linux de Azure que funcionan con Azure Stack Hub [como se documenta aquí](azure-stack-linux.md#azure-linux-agent). Asegúrese de que la imagen preparada con Sysprep tenga una versión del agente Linux de Azure compatible con Azure Stack Hub.

2. Detenga o desasigne la máquina virtual.

3. Descargue el disco duro virtual.

   1. Para descargar el archivo del disco duro virtual, debe generar una dirección URL de firma de acceso compartido (SAS). Una vez generada la dirección URL, se asigna un tiempo de expiración a la dirección URL.

   1. En el menú de la hoja de la máquina virtual, seleccione **Discos**.

   1. Seleccione el disco de sistema operativo de la máquina virtual y luego seleccione **Exportación del disco**.

   1. Establezca el tiempo de expiración de la dirección URL en 36 000.

   1. Seleccione **Generar dirección URL**.

   1. Genere la dirección URL.

   1. En la dirección URL generada, seleccione **Descargar el archivo VHD**.

   1. Es posible que tenga que seleccionar **Guardar** en el explorador para iniciar la descarga. El nombre predeterminado del archivo de VHD es _abcd_.

### <a name="considerations"></a>Consideraciones

Antes de cargar la imagen, es importante tener en cuenta lo siguiente:

- Azure Stack Hub solo admite máquinas virtuales de primera generación (1) en el formato de disco duro virtual de disco fijo. El formato fijo estructura el disco lógico linealmente dentro del archivo, de manera que el desplazamiento del disco *X* se almacena en el desplazamiento del blob *X*. Un breve pie de página al final del blob describe las propiedades del disco duro virtual. Para confirmar que el disco está fijado, use el cmdlet **Get-VHD** de PowerShell.

- Azure Stack Hub no admite discos duros virtuales dinámicos.

## <a name="step-2-upload-the-vm-image-to-a-storage-account"></a>Paso 2: Carga de la imagen de máquina virtual en una cuenta de almacenamiento

1. [Instale PowerShell para Azure Stack Hub](azure-stack-powershell-install.md).  

2. Inicie sesión como operador en Azure Stack Hub. Para obtener instrucciones, consulte [Iniciar sesión como operador en Azure Stack Hub](azure-stack-powershell-configure-admin.md).

3. Debe poderse hacer referencia a las imágenes mediante un identificador URI de Blob Storage. Prepare una imagen de sistema operativo Windows o Linux en formato VHD (no VHDX) y, a continuación, cargue la imagen en una cuenta de almacenamiento en Azure Stack Hub.

   - Si el disco duro virtual está en Azure, puede usar una herramienta como [Azcopy](/azure/storage/common/storage-use-azcopy) para transferir directamente el disco duro virtual entre Azure y su cuenta de almacenamiento de Azure Stack Hub si lo está ejecutando en una instancia de Azure Stack conectada.

   - En una instancia de Azure Stack Hub desconectada, si el disco duro virtual está en Azure, deberá descargar el disco duro virtual en una máquina que tenga conectividad con Azure y Azure Stack Hub. Después, copie el disco duro virtual en esta máquina desde Azure antes de transferir el disco duro virtual a Azure Stack Hub con cualquiera de las [herramientas de transferencia de datos de almacenamiento](../user/azure-stack-storage-transfer.md) comunes que se pueden usar en Azure y Azure Stack Hub.

     Una de estas herramientas que se usa en este ejemplo es el comando Add-AzureRmVHD para cargar un disco duro virtual en una cuenta de almacenamiento en el portal de administración de Azure Stack Hub.  

     ```powershell
     Add-AzureRmVhd -Destination "https://bash.blob.redmond.azurestack.com/sample/vhdtestingmgd.vhd" -LocalFilePath "C:\vhd\vhdtestingmgd.vhd"
     ```

4. Anote el identificador URI donde cargará la imagen de Blob Storage. El identificador URI de Blob Storage tiene el siguiente formato: *&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;* .vhd.

5. Para que sea posible acceder al blob de manera anónima, vaya al contenedor de blobs de la cuenta de almacenamiento donde se cargó el disco duro virtual de la imagen de máquina virtual. Seleccione **Blob** y, a continuación, seleccione **Directiva de acceso**. Opcionalmente, puede generar una firma de acceso compartido para el contenedor e incluirla como parte del identificador URI del blob. Este paso garantiza que el blob está disponible para su uso. Si no se puede acceder al blob de forma anónima, la imagen de la máquina virtual se creará con estado de error.

   ![Vaya a los blobs de la cuenta de almacenamiento.](./media/azure-stack-add-vm-image/tca1.png)

   ![Establecimiento del acceso del blob en público](./media/azure-stack-add-vm-image/tca2.png)

   ![Establecimiento del acceso del blob en público](./media/azure-stack-add-vm-image/tca3.png)

## <a name="step-3-option-1-add-the-vm-image-as-an-azure-stack-hub-operator-using-the-portal"></a>Paso 3, opción 1: Incorporación de la imagen de máquina virtual como un operador de Azure Stack Hub mediante el portal

1. Inicie sesión como operador en Azure Stack Hub. En el menú, seleccione **Todos los servicios** > **Proceso** en **Imágenes de VM** > **Agregar**.

   ![Interfaz de usuario de la instalación de prueba de la imagen personalizada](./media/azure-stack-add-vm-image/tca4.png)

2. En **Crear imagen**, escriba el publicador, la oferta, la SKU, la versión y el identificador URI del blob de disco del sistema operativo. A continuación, seleccione **Crear** para empezar a crear la imagen de máquina virtual.

   ![Interfaz de usuario de la instalación de prueba de la imagen personalizada](./media/azure-stack-add-vm-image/tca5.png)

   Cuando la imagen se crea correctamente, el estado de la imagen de máquina virtual cambia a **Correcto**.

3. Cuando agregue una imagen, solo estará disponible para las plantillas basadas en Azure Resource Manager y las implementaciones de PowerShell. Para poner una imagen a disposición de los usuarios como elemento de Marketplace, publique el elemento de Marketplace siguiendo los pasos del artículo [Creación y publicación de un elemento de Marketplace](azure-stack-create-and-publish-marketplace-item.md). Asegúrese de que toma nota de los valores del **publicador**, **oferta**, **SKU** y **versión**. Los necesitará cuando modifique la plantilla de Resource Manager y de manifest.json en el archivo .azpkg personalizado.

## <a name="step-3-option-2-add-a-vm-image-as-an-azure-stack-hub-operator-using-powershell"></a>Paso 3, opción 2: Adición de una imagen de máquina virtual como un operador de Azure Stack Hub mediante PowerShell

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
     La versión de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Esta versión está en el formato *\#.\#.\#* . No incluya un espacio ni otros caracteres especiales en este campo.  
   - **osType**  
     Por ejemplo: `Linux`  
     El valor de **osType** de la imagen debe ser **Windows** o **Linux**.  
   - **OSUri**  
     Por ejemplo: `https://storageaccount.blob.core.windows.net/vhds/Ubuntu1404.vhd`  
     Puede especificar un URI de Blob Storage para `osDisk`.  

     Para más información, consulte la referencia de PowerShell para el cmdlet [Add-AzsPlatformimage](/powershell/module/azs.compute.admin/add-azsplatformimage).

4. Cuando agregue una imagen, solo estará disponible para las plantillas basadas en Azure Resource Manager y las implementaciones de PowerShell. Para poner una imagen a disposición de los usuarios como elemento de Marketplace, publique el elemento de Marketplace siguiendo los pasos del artículo [Creación y publicación de un elemento de Marketplace](azure-stack-create-and-publish-marketplace-item.md). Asegúrese de que toma nota de los valores del **publicador**, **oferta**, **SKU** y **versión**. Los necesitará cuando modifique la plantilla de Resource Manager y de manifest.json en el archivo .azpkg personalizado.

## <a name="remove-the-vm-image-as-an-azure-stack-hub-operator-using-the-portal"></a>Eliminación de la imagen de máquina virtual como un operador de Azure Stack Hub mediante el portal

1. Abra el [portal del administrador](https://adminportal.local.azurestack.external) de Azure Stack Hub.

2. Si la imagen de máquina virtual tiene un elemento de Marketplace asociado, seleccione **Marketplace Management** y, después, seleccione el elemento de Marketplace de la máquina virtual que desea eliminar.

3. Si la imagen de máquina virtual no tiene un elemento de Marketplace asociado, vaya a **Todos los servicios > Proceso > Imágenes de máquina virtual** y, a continuación, seleccione el botón de puntos suspensivos ( **...** ) junto a la imagen de máquina virtual.

4. Seleccione **Eliminar**.

## <a name="remove-a-vm-image-as-an-azure-stack-hub-operator-using-powershell"></a>Eliminación de una imagen de máquina virtual como un operador de Azure Stack Hub mediante PowerShell

Cuando ya no necesite la imagen de máquina virtual que ha cargado, puede eliminarla de Marketplace mediante el siguiente cmdlet:

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
     La versión de la imagen de máquina virtual que usan los usuarios al implementar la imagen de máquina virtual. Esta versión está en el formato *\#.\#.\#* . No incluya un espacio ni otros caracteres especiales en este campo.  

     Para más información sobre el cmdlet **Remove-AzsPlatformImage**, consulte la [documentación del módulo de operador de Azure Stack Hub](/powershell/module/) para Microsoft PowerShell.

## <a name="next-steps"></a>Pasos siguientes

- [Creación y publicación de un elemento personalizado de Marketplace de Azure Stack Hub](azure-stack-create-and-publish-marketplace-item.md)
- [Aprovisionamiento de una máquina virtual](../user/azure-stack-create-vm-template.md)
