---
title: Adición de una imagen de máquina virtual personalizada a Azure Stack Hub
description: Aprenda a agregar o quitar una imagen de máquina virtual personalizada en Azure Stack Hub.
author: sethmanheim
ms.topic: conceptual
ms.date: 07/14/2020
ms.author: sethm
ms.reviewer: kivenkat
ms.lastreviewed: 07/10/2020
ms.openlocfilehash: 54732e12b330eeda8ba65e9b4add0b78547ac891
ms.sourcegitcommit: 635c8c69d90b7e941659d54141d9f1c58f51cf45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2020
ms.locfileid: "87477631"
---
# <a name="add-a-custom-vm-image-to-azure-stack-hub"></a>Adición de una imagen de máquina virtual personalizada a Azure Stack Hub

En Azure Stack Hub, puede agregar como operador una imagen de máquina virtual personalizada a Marketplace para que esté disponible para los usuarios. Puede agregar estas imágenes al Marketplace de Azure Stack Hub mediante el portal del administrador o Windows PowerShell. Use una imagen de Azure Marketplace global como base para la imagen personalizada o cree la suya propia con Hyper-V.

Un usuario del portal del inquilino de Azure Stack Hub también puede agregar una imagen de máquina virtual personalizada mediante las instrucciones del paso 1. Un usuario puede crear su imagen personalizada como disco duro virtual (VHD) y cargar la imagen en una cuenta de almacenamiento en Azure Stack Hub. Después, se puede crear una máquina virtual desde el disco duro virtual.

Las imágenes personalizadas presentan dos formas: **generalizada** y **especializada**.

- **Imagen generalizada**

  Una imagen de disco generalizada es aquella que se ha preparado con **Sysprep** para quitar cualquier información exclusiva (como cuentas de usuario), lo que permite que se pueda volver a usar para crear varias máquinas virtuales. Esta es una buena opción para los elementos de Marketplace.

- **Imagen especializada**

  Una imagen de disco especializada es una copia del disco duro virtual de una máquina virtual existente que contiene las cuentas de usuario, aplicaciones y otros datos de estado de la máquina virtual original. Este suele ser el formato en el que las máquinas virtuales se migran a Azure Stack Hub.

## <a name="step-1-create-the-custom-vm-image"></a>Paso 1: Creación de una imagen de máquina virtual personalizada

### <a name="windows---create-a-custom-generalized-vhd"></a>Windows: Creación de un disco duro virtual generalizado personalizado

#### <a name="vhd-is-from-outside-azure"></a>El disco duro virtual está fuera de Azure

Siga los pasos descritos en [Carga de un VHD generalizado y su uso para crear máquinas virtuales nuevas en Azure](/azure/virtual-machines/windows/upload-generalized-managed) para utilizar **Sysprep** correctamente en el disco duro virtual y convertirlo en generalizado.

#### <a name="vhd-is-from-azure"></a>El disco duro virtual es de Azure

Antes de generalizar la máquina virtual, asegúrese de lo siguiente:

Antes de la versión Azure Stack 1910:

- Al aprovisionar la máquina virtual en Azure, use PowerShell y aprovisiónela sin la marca `-ProvisionVMAgent`.
- Elimine todas las extensiones de la máquina virtual mediante el cmdlet **Remove-AzureRmVMExtension** de la máquina virtual antes de generalizarla en Azure. Para saber qué extensiones de máquina virtual se instalan, vaya a `Windows (C:) > WindowsAzure > Logs > Plugins`.

```powershell
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```

Antes de la versión Azure Stack 1910:

- Los pasos anteriores no se aplican a los discos duros virtuales que se han trasladado de Azure a una instancia de Azure Stack Hub que esté en la versión 1910 u otra posterior.

Siga las instrucciones de [este documento](/azure/virtual-machines/windows/download-vhd) para generalizar y descargar correctamente el disco duro virtual antes de migrarlo a Azure Stack Hub.

### <a name="windows---specialized"></a>Windows: Especializado

Siga los pasos que se indican [aquí](/azure/virtual-machines/windows/create-vm-specialized#prepare-the-vm) para preparar el disco duro virtual correctamente.
Para implementar las extensiones de máquina virtual, asegúrese de que el archivo .msi del agente de máquina virtual disponible [en este artículo](/azure/virtual-machines/extensions/agent-windows#manual-installation) esté instalado en la máquina virtual antes de la implementación de esta. Si el agente de máquina virtual no está presente en el disco duro virtual, se producirá un error en la implementación de la extensión. No es necesario establecer el perfil del sistema operativo durante el aprovisionamiento ni establecer `$vm.OSProfile.AllowExtensionOperations = $true`.

### <a name="linux---generalized"></a>Linux: Generalizado

#### <a name="vhd-from-outside-azure"></a>El disco duro virtual está fuera de Azure

Si el disco duro virtual está fuera de Azure, siga las instrucciones adecuadas para generalizar el disco duro virtual:

- [Distribuciones basadas en CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
- [SLES u openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

#### <a name="vhd-from-azure"></a>El disco duro virtual es de Azure

Si el disco duro virtual es de Azure, siga estas instrucciones para generalizar y descargar el disco duro virtual:

1. Detenga el servicio **waagent**:

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   Las versiones del agente Linux de Azure que funcionan con Azure Stack Hub [se documentan aquí](azure-stack-linux.md#azure-linux-agent). Asegúrese de que la imagen preparada con Sysprep tenga una versión del agente Linux de Azure compatible con Azure Stack Hub.

2. Detenga o desasigne la máquina virtual.

3. Descargue el disco duro virtual.

   1. Para descargar el archivo del disco duro virtual, genere una dirección URL de firma de acceso compartido (SAS). Una vez generada la dirección URL, se asigna un tiempo de expiración a la dirección URL.

   1. En el menú de la hoja de la máquina virtual, seleccione **Discos**.

   1. Seleccione el disco de sistema operativo de la máquina virtual y luego seleccione **Exportación del disco**.

   1. Establezca el tiempo de expiración de la dirección URL en 36 000.

   1. Seleccione **Generar dirección URL**.

   1. Genere la dirección URL.

   1. En la dirección URL generada, seleccione **Descargar el archivo VHD**.

   1. Es posible que tenga que seleccionar **Guardar** en el explorador para iniciar la descarga. El nombre predeterminado del archivo de VHD es **abcd**.

   1. Ahora puede portar este disco duro virtual a Azure Stack Hub.

> [!IMPORTANT]  
> Puede encontrar un script en el artículo [Script de ejemplo para cargar un disco duro virtual en Azure y crear una máquina virtual nueva](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script) para cargar el disco duro virtual en una cuenta de almacenamiento de usuario de Azure Stack Hub y crear una máquina virtual. Asegúrese de proporcionar `$urlOfUploadedImageVhd` como la cuenta de almacenamiento de Azure Stack Hub y la dirección URL del contenedor. En el caso de un disco duro virtual generalizado, asegúrese de usar el valor `FromImage` al establecer `-CreateOption FromImage`.

### <a name="linux---specialized"></a>Linux: Especializado

Los discos duros virtuales especializados no se deben usar como disco duro virtual base de un elemento de Marketplace. Use los discos duros virtuales generalizados para esto. Sin embargo, los discos duros virtuales especializados son idóneos para cuando es necesario migrar máquinas virtuales de un entorno local a una instancia de Azure Stack Hub

#### <a name="vhd-from-outside-azure"></a>El disco duro virtual está fuera de Azure

Paso 1: siga las instrucciones apropiadas para hacer que el disco duro virtual sea adecuado para Azure. Use este artículo hasta el paso de instalación del agente de Linux y luego continúe en el paso 2 antes de instalar el agente:

- [Distribuciones basadas en CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
- [SLES u openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

> [!IMPORTANT]
> No ejecute el último paso (`sudo waagent -force -deprovision`) ya que, de lo contrario, se generalizará el disco duro virtual.

Paso 2: si un disco duro virtual especializado de Linux se traslada desde fuera de Azure a Azure Stack Hub, para ejecutar extensiones de máquina virtual y deshabilitar el aprovisionamiento, haga lo siguiente:

Para identificar qué versión del agente de Linux está instalada en la imagen de máquina virtual de origen, ejecute los siguientes comandos. El número de versión que describe el código de aprovisionamiento es `WALinuxAgent-`, no `Goal state agent`:

```bash
waagent -version
```

Por ejemplo:

```bash
waagent -version
WALinuxAgent-2.2.45 running on centos 7.7.1908
Python: 2.7.5
Goal state agent: 2.2.46
```

Para deshabilitar el aprovisionamiento del agente de Linux con una versión del mismo inferior a la 2.2.4, establezca los parámetros siguientes en **/etc/waagent.conf**: `Provisioning.Enabled=n, and Provisioning.UseCloudInit=n`.

En los escenarios en los que desea ejecutar extensiones:

1. Establezca el siguiente parámetro en **/etc/waagent.conf**:

   - `Provisioning.Enabled=n`
   - `Provisioning.UseCloudInit=n`

2. Para asegurarse de que el aprovisionamiento de walinuxagent está deshabilitado, ejecute: `mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned`

3. Si tiene cloud-init en la imagen, deshabilítelo:

   ```bash
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. Ejecute un cierre de sesión.

Para deshabilitar el aprovisionamiento con el agente de Linux 2.2.45 y versiones posteriores, realice los cambios siguientes en la opción de configuración:

- `Provisioning.Enabled` y `Provisioning.UseCloudInit` se omiten ahora.

En esta versión, actualmente no hay ninguna opción `Provisioning.Agent` para deshabilitar el aprovisionamiento por completo; sin embargo, puede agregar el archivo del marcador de aprovisionamiento y, con la configuración siguiente, se omitirá el aprovisionamiento:

1. En **/etc/waagent.conf** agregue esta opción de configuración: `Provisioning.Agent=Auto`.
2. Para asegurarse de que el aprovisionamiento de walinuxagent está deshabilitado, ejecute: `mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned`.
3. Deshabilite la instalación de cloud-init ejecutando lo siguiente:

   ```bash
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. Cierre sesión.

#### <a name="vhd-from-azure"></a>El disco duro virtual es de Azure

Puede usar [esta guía](/azure/virtual-machines/linux/upload-vhd#requirements) para preparar el disco duro virtual.

> [!IMPORTANT]  
> Puede usar el siguiente ejemplo de PowerShell para cargar el disco duro virtual en una cuenta de almacenamiento de usuario de Azure Stack Hub:

```powershell  
# Provide values for the variables
$resourceGroup = 'myResourceGroup'
$location = 'Orlando'
$storageaccount = 'mystorageaccount'
$storageType = 'Standard_LRS'
$storageaccounturl = 'https://resourcegrpabc.blob.orlando.azurestack.corp.microsoft.com/container'
$localPath = 'C:\Users\Public\Documents\Hyper-V\VHDs\generalized.vhd'
$vhdName = 'myUploadedVhd.vhd'

New-AzResourceGroup -Name $resourceGroup -Location $location
New-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageAccount -Location $location `
  -SkuName $storageType -Kind "Storage"
$urlOfUploadedImageVhd = ($storageaccounturl + '/' + $vhdName)
Add-AzVhd -ResourceGroupName $resourceGroup -Destination $urlOfUploadedImageVhd `
    -LocalFilePath $localPath
```

En el caso de un disco duro virtual especializado, asegúrese de usar la semántica de "conexión" mediante `-CreateOption Attach`, [como en este ejemplo](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-from-managed-os-disks) para crear una máquina virtual a partir de este disco duro virtual.

### <a name="considerations"></a>Consideraciones

Antes de cargar la imagen, es importante tener en cuenta lo siguiente:

- Azure Stack Hub solo admite máquinas virtuales de primera generación en el formato de disco duro virtual de disco fijo. El formato fijo estructura el disco lógico linealmente dentro del archivo, de manera que el desplazamiento del disco **X** se almacena en el desplazamiento del blob **X**. Un breve pie de página al final del blob describe las propiedades del disco duro virtual. Para confirmar que el disco está fijado, use el cmdlet **Get-VHD** de PowerShell.

- Azure Stack Hub no admite discos duros virtuales dinámicos.

## <a name="step-2-upload-a-storage-account"></a>Paso 2: Carga de una cuenta de almacenamiento

Cargue la imagen de máquina virtual en una cuenta de almacenamiento como operador de Azure Stack Hub.

1. [Instale PowerShell para Azure Stack Hub](azure-stack-powershell-install.md).  

2. Inicie sesión como operador en Azure Stack Hub. Para obtener instrucciones, consulte [Iniciar sesión como operador en Azure Stack Hub](azure-stack-powershell-configure-admin.md).

3. Se debe hacer referencia a las imágenes mediante un identificador URI de Blob Storage. Prepare una imagen de sistema operativo Windows o Linux en formato VHD (no VHDX) y, a continuación, cargue la imagen en una cuenta de almacenamiento en Azure Stack Hub.

   - Si el disco duro virtual está en Azure, puede usar una herramienta como [Azcopy](/azure/storage/common/storage-use-azcopy) para transferir directamente el disco duro virtual entre Azure y su cuenta de almacenamiento de Azure Stack Hub si lo está ejecutando en una instancia de Azure Stack Hub conectada.

   - En una instancia de Azure Stack Hub desconectada, si el disco duro virtual está en Azure, debe descargar el disco duro virtual en una máquina que tenga conectividad con Azure y Azure Stack Hub. Después, copie el disco duro virtual en esta máquina desde Azure antes de transferir el disco duro virtual a Azure Stack Hub con cualquiera de las [herramientas de transferencia de datos de almacenamiento](../user/azure-stack-storage-transfer.md) comunes que se pueden usar en Azure y Azure Stack Hub.

     Una de estas herramientas que se usa en este ejemplo es el comando **Add-AzureRmVHD** para cargar un disco duro virtual en una cuenta de almacenamiento en el portal de administración de Azure Stack Hub:

     ```powershell
     Add-AzureRmVhd -Destination "https://bash.blob.redmond.azurestack.com/sample/vhdtestingmgd.vhd" -LocalFilePath "C:\vhd\vhdtestingmgd.vhd"
     ```

4. Anote el identificador URI donde cargará la imagen de Blob Storage. El identificador URI de Blob Storage tiene el siguiente formato: **&lt;storageAccount&gt;/&lt;blobContainer&gt;/&lt;targetVHDName&gt;*.vhd**.

5. Para que sea posible acceder al blob de manera anónima, vaya al contenedor de blobs de la cuenta de almacenamiento donde se cargó el disco duro virtual de la imagen de máquina virtual. Seleccione **Blob** y, a continuación, seleccione **Directiva de acceso**. Opcionalmente, puede generar una firma de acceso compartido para el contenedor e incluirla como parte del identificador URI del blob. Este paso garantiza que el blob está disponible para su uso. Si no se puede acceder al blob de forma anónima, la imagen de la máquina virtual se creará con estado de error.

   ![Vaya a los blobs de la cuenta de almacenamiento.](./media/azure-stack-add-vm-image/tca1.png)

   ![Establecimiento del acceso del blob en público](./media/azure-stack-add-vm-image/tca2.png)

   ![Establecimiento del acceso del blob en público](./media/azure-stack-add-vm-image/tca3.png)

6. También puede usar los pasos anteriores para cargar la imagen en una cuenta de almacenamiento en el portal de usuarios (iniciando sesión como usuario) y crear una máquina virtual directamente a partir de ella. En este caso, será un disco duro virtual personalizado que no está disponible a través de Marketplace. Tampoco tendrá que seguir el paso 3.

## <a name="step-3-option-1-add-using-the-portal"></a>Paso 3, opción 1: Incorporación mediante el portal

Agregue la imagen de máquina virtual como un operador de Azure Stack Hub mediante el portal.

1. Inicie sesión como operador en Azure Stack Hub. En el menú, seleccione **Todos los servicios** > **Proceso** en **Imágenes de VM** > **Agregar**.

   ![Interfaz de usuario de la instalación de prueba de la imagen personalizada](./media/azure-stack-add-vm-image/tca4.png)

2. En **Crear imagen**, escriba el **publicador**, la **oferta**, la **SKU**, la **versión** y el identificador URI del blob de disco del sistema operativo. A continuación, seleccione **Crear** para empezar a crear la imagen de máquina virtual.

   ![Interfaz de usuario de la instalación de prueba de la imagen personalizada](./media/azure-stack-add-vm-image/tca5.png)

   Cuando la imagen se crea correctamente, el estado de la imagen de máquina virtual cambia a **Correcto**.

3. Cuando agregue una imagen, solo estará disponible para las plantillas basadas en Azure Resource Manager y las implementaciones de PowerShell. Para poner una imagen a disposición de los usuarios como elemento de Marketplace, publique el elemento de Marketplace siguiendo los pasos del artículo [Creación y publicación de un elemento de Marketplace](azure-stack-create-and-publish-marketplace-item.md). Asegúrese de que toma nota de los valores del **publicador**, **oferta**, **SKU** y **versión**. Los necesitará cuando modifique la plantilla de Resource Manager y de manifest.json en el archivo .azpkg personalizado.

## <a name="step-3-option-2-add-using-powershell"></a>Paso 3, opción 2: Incorporación mediante PowerShell

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

## <a name="remove-using-the-portal"></a>Eliminación mediante el portal

Para eliminar la imagen de máquina virtual como un operador de Azure Stack Hub mediante el portal de Azure Stack Hub, siga estos pasos:

1. Abra el [portal del administrador](https://portal.azure.com/signin/index) de Azure Stack Hub.

2. Si la imagen de máquina virtual tiene un elemento de Marketplace asociado, seleccione **Marketplace Management** y, después, seleccione el elemento de Marketplace de la máquina virtual que desea eliminar.

3. Si la imagen de máquina virtual no tiene un elemento de Marketplace asociado, vaya a **Todos los servicios > Proceso > Imágenes de máquina virtual** y, a continuación, seleccione el botón de puntos suspensivos ( **...** ) junto a la imagen de máquina virtual.

4. Seleccione **Eliminar**.

## <a name="remove-using-powershell"></a>Eliminación con PowerShell

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

## <a name="next-steps"></a>Pasos siguientes

- [Creación y publicación de un elemento personalizado de Marketplace de Azure Stack Hub](azure-stack-create-and-publish-marketplace-item.md)
- [Aprovisionamiento de una máquina virtual](../user/azure-stack-create-vm-template.md)
