---
title: Traslado de una máquina virtual especializada desde un entorno local a Azure Stack Hub
description: Aprenda a trasladar una máquina virtual especializada desde un entorno local a Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 9/8/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 383e96889f742b05a5f4b25e91bab48e1fd4c075
ms.sourcegitcommit: 9a340b383dcf42c85bc6ec0d01ff3c9ae29dfe4c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2020
ms.locfileid: "89609929"
---
# <a name="move-a-specialized-vm-from-on-premises-to-azure-stack-hub"></a>Traslado de una máquina virtual especializada desde un entorno local a Azure Stack Hub

Puede agregar una imagen de máquina virtual desde el entorno local. Puede crear su imagen personalizada como disco duro virtual y cargar la imagen en una cuenta de almacenamiento en la instancia de Azure Stack Hub. Después, puede crear una máquina virtual desde el disco duro virtual.

Una imagen de disco especializada es una copia del disco duro virtual de una máquina virtual existente que contiene las cuentas de usuario, aplicaciones y otros datos de estado de la máquina virtual original. Este suele ser el formato en el que las máquinas virtuales se migran a Azure Stack Hub. Los discos duros virtuales especializados son idóneos para cuando es necesario migrar máquinas virtuales de un entorno local a Azure Stack Hub.

## <a name="how-to-move-an-image"></a>Traslado de una imagen

Busque la sección que se adapte mejor a sus necesidades al preparar el disco duro virtual.

#### <a name="windows-vm"></a>[Máquina virtual Windows](#tab/port-win)

- Siga los pasos descritos en [Preparación de un VHD o un VHDX de Windows antes de cargarlo en Azure](/azure/virtual-machines/windows/prepare-for-upload-vhd-image) para preparar correctamente el disco duro virtual. Debe usar un disco duro virtual para Azure Stack Hub.
   > [!NOTE]  
   > **No** generalice la VM mediante Sysprep.
- Quite todas las herramientas de virtualización de invitado y los agentes instalados en la VM (como las herramientas de VMware).
- Asegúrese de que la VM se configura para obtener la dirección IP y la configuración de DNS desde DHCP. Esto garantiza que el servidor obtiene una dirección IP dentro de la red virtual cuando se inicia.
- Asegúrese de que RDP/SSH está habilitado y de que el firewall permite la comunicación.
- Para implementar extensiones de máquinas virtuales, asegúrese de que el agente de máquina virtual `.msi` esté disponible. Para obtener instrucciones, consulte [Información general del agente de máquina virtual de Azure](/azure/virtual-machines/extensions/agent-windows). Si el agente de máquina virtual no está presente en el disco duro virtual, se producirá un error en la implementación de la extensión. No es necesario establecer el perfil del sistema operativo durante el aprovisionamiento ni establecer `$vm.OSProfile.AllowExtensionOperations = $true`.

#### <a name="linux-vm"></a>[Máquina virtual Linux](#tab/port-linux)

#### <a name="generalize-the-vhd"></a>Generalización del disco duro virtual

Siga las instrucciones apropiadas para preparar el disco duro virtual para el sistema operativo Linux:

- [Distribuciones basadas en CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](../operator/azure-stack-redhat-create-upload-vhd.md)
- [SLES u openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

> [!IMPORTANT]
> No ejecute el último paso (`sudo waagent -force -deprovision`) ya que, de lo contrario, se generalizará el disco duro virtual.

#### <a name="identify-the-version-of-the-linux-agent"></a>Identificación de la versión del agente de Linux

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

#### <a name="linux-agent-224-and-earlier-disable-the-linux-agent-provisioning"></a>Agente de Linux 2.2.4 y versiones anteriores: deshabilitar el aprovisionamiento del agente de Linux 

Para deshabilitar el aprovisionamiento del agente de Linux con una versión del mismo inferior a la 2.2.4, establezca los parámetros siguientes en **/etc/waagent.conf**: `Provisioning.Enabled=n, and Provisioning.UseCloudInit=n`.

#### <a name="linux-agent-2245-and-later-disable-the-linux-agent-provisioning"></a>Agente de Linux 2.2.45 y versiones posteriores: deshabilitar el aprovisionamiento del agente de Linux

Para deshabilitar el aprovisionamiento con el agente de Linux 2.2.45 y versiones posteriores, realice los cambios siguientes en la opción de configuración:

`Provisioning.Enabled` y `Provisioning.UseCloudInit` se omiten ahora.

En esta versión, actualmente no hay ninguna opción `Provisioning.Agent` para deshabilitar el aprovisionamiento por completo; sin embargo, puede agregar el archivo del marcador de aprovisionamiento y, con la configuración siguiente, se omitirá el aprovisionamiento:

1. En **/etc/waagent.conf** agregue esta opción de configuración: `Provisioning.Agent=Auto`.
2. Para asegurarse de que el aprovisionamiento de walinuxagent está deshabilitado, ejecute: `mkdir -p /var/lib/waagent && touch /var/lib/waagent/provisioned`.
3. Deshabilite la instalación de cloud-init ejecutando lo siguiente:

   ```bash  
   touch /etc/cloud/cloud-init.disabled
   sudo sed -i '/azure_resource/d' /etc/fstab
   ```

4. Cierre sesión.

#### <a name="run-an-extension"></a>Ejecutar una extensión

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

---

## <a name="verify-your-vhd"></a>Comprobación del disco duro virtual

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]

## <a name="upload-to-a-storage-account"></a>Carga en una cuenta de almacenamiento

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-disk-in-azure-stack-hub"></a>Creación del disco en Azure Stack Hub

[!INCLUDE [Create the disk in Azure Stack Hub](../includes/user-compute-create-disk.md)]

## <a name="next-steps"></a>Pasos siguientes

[Introducción al traslado de una máquina virtual a Azure Stack Hub](vm-move-overview.md)
