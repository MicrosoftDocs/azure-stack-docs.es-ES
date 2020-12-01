---
title: Traslado de una máquina virtual de Azure a Azure Stack Hub
description: Aprenda a trasladar una máquina virtual de Azure a Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 9/8/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: cefc127efcdac2d1610803ef90b54c50e7280e97
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2020
ms.locfileid: "95516943"
---
# <a name="move-a-vm-from-azure-to-azure-stack-hub"></a>Traslado de una máquina virtual de Azure a Azure Stack Hub

Puede cargar un disco duro virtual desde una máquina virtual creada en Azure a la instancia de Azure Stack Hub.

## <a name="prepare-and-download-your-vhd-from-azure"></a>Preparación y descarga del disco duro virtual desde Azure

Busque la sección que se adapte mejor a sus necesidades al preparar el disco duro virtual.

#### <a name="windows---specialized"></a>[Windows: Especializado](#tab/win-spec)

- Siga los pasos del artículo [Creación de una VM de Windows desde un disco especializado mediante PowerShell](/azure/virtual-machines/windows/create-vm-specialized#prepare-the-vm) para preparar el disco duro virtual.
- Para implementar extensiones de máquinas virtuales, asegúrese de que el agente de máquina virtual .msi esté disponible.  
  Para más información e indicaciones, consulte [Información general sobre el agente de la máquina virtual de Azure](/azure/virtual-machines/extensions/agent-windows). Asegúrese de que la extensión está instalada en la máquina virtual antes de trasladar esta. Si el agente de máquina virtual no está presente en el disco duro virtual, se producirá un error en la implementación de la extensión. No es necesario establecer el perfil del sistema operativo durante el aprovisionamiento ni establecer `$vm.OSProfile.AllowExtensionOperations = $true`.

#### <a name="windows---generalized"></a>[Windows: generalizado](#tab/win-gen)

::: moniker range="<=azs-1910"
- Siga las instrucciones de [Descargar un VHD de Windows desde Azure](/azure/virtual-machines/windows/download-vhd) para generalizar y descargar correctamente el disco duro virtual antes de trasladarlo a Azure Stack Hub.
- Al aprovisionar la máquina virtual en Azure, use PowerShell. Prepárela sin la marca `-ProvisionVMAgent`.
- Elimine todas las extensiones de VM mediante el cmdlet de la máquina virtual antes de generalizarla en Azure. Para saber qué extensiones de máquina virtual se instalan, vaya a `Windows (C:) > WindowsAzure > Logs > Plugins`.

Use el módulo de Az de PowerShell:

```powershell  
Remove-AzVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```

Use el módulo de AzureRM de PowerShell:

```powershell  
Remove-AzureRmVMExtension -ResourceGroupName winvmrg1 -VMName windowsvm -Name "CustomScriptExtension"
```
::: moniker-end
::: moniker range=">=azs-2002"

Siga las instrucciones de [Descargar un VHD de Windows desde Azure](/azure/virtual-machines/windows/download-vhd) para generalizar y descargar correctamente el disco duro virtual antes de trasladarlo a Azure Stack Hub.
::: moniker-end

#### <a name="linux---specialized"></a>[Linux: Especializado](#tab/lin-spec)

- Antes de descargar la máquina virtual Linux, siga las instrucciones de la sección "Preparación de la máquina virtual" del artículo [Creación de una VM Linux desde un disco personalizado con la CLI de Azure](/azure/virtual-machines/linux/upload-vhd#prepare-the-vm)
- Siga los pasos del artículo [Descarga de un VHD de Linux desde Azure](/azure//virtual-machines/windows/download-vhd) para preparar y descargar el disco duro virtual.
- En el caso de un disco duro virtual especializado, asegúrese de usar la semántica de "conexión" mediante `-CreateOption Attach`. Puede encontrar un ejemplo en el artículo [Creación de una máquina virtual con un disco administrado existente del sistema operativo mediante PowerShell (Windows)](/azure/virtual-machines/scripts/virtual-machines-powershell-sample-create-vm-from-managed-os-disks).

#### <a name="linux---generalized"></a>[Linux: Generalizado](#tab/lin-gen)

1. Detenga el servicio **waagent**:

   ```bash
   sudo waagent -force -deprovision
   export HISTSIZE=0
   logout
   ```

   Las versiones del agente Linux de Azure que funcionan con Azure Stack Hub [se documentan aquí](../operator/azure-stack-linux.md#azure-linux-agent). Asegúrese de que la imagen en la que ha ejecutado Sysprep tenga una versión del agente Linux de Azure compatible con Azure Stack Hub.

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

   1. Ahora puede trasladar este disco duro virtual a Azure Stack Hub.

> [!IMPORTANT]  
> Puede encontrar un script en el artículo [Script de ejemplo para cargar un disco duro virtual en Azure y crear una máquina virtual nueva](/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script) para cargar el disco duro virtual en una cuenta de almacenamiento de usuario de Azure Stack Hub y crear una máquina virtual. Asegúrese de proporcionar `$urlOfUploadedImageVhd` como la cuenta de almacenamiento de Azure Stack Hub y la dirección URL del contenedor. En el caso de un disco duro virtual generalizado, asegúrese de usar el valor `FromImage` al establecer `-CreateOption FromImage`.

---

## <a name="verify-your-vhd"></a>Comprobación del disco duro virtual

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]

## <a name="upload-to-a-storage-account"></a>Carga en una cuenta de almacenamiento

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-vm"></a>Creación de la máquina virtual

Las imágenes personalizadas presentan dos formas: **especializada** y **generalizada**.

### <a name="specialized"></a>[Especializada](#tab/create-vm-spec)

[!INCLUDE [Create the disk in Azure Stack Hub](../includes/user-compute-create-disk.md)]

### <a name="generalized"></a>[Generalizada](#tab/create-vm-gen)

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]
---
## <a name="next-steps"></a>Pasos siguientes

[Introducción al traslado de una máquina virtual a Azure Stack Hub](vm-move-overview.md)
