---
title: Traslado de una máquina virtual generalizada desde un entorno local a Azure Stack Hub
description: Aprenda a trasladar una máquina virtual generalizada desde un entorno local a Azure Stack Hub.
author: mattbriggs
ms.topic: how-to
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 105beaa0805fe0aea1aacfce8bd22f3bd01714b1
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97872881"
---
# <a name="move-a-generalized-vm-from-on-premises-to-azure-stack-hub"></a>Traslado de una máquina virtual generalizada desde un entorno local a Azure Stack Hub

Puede agregar una imagen de máquina virtual desde el entorno local. Puede crear su imagen personalizada como disco duro virtual y cargar la imagen en una cuenta de almacenamiento en la instancia de Azure Stack Hub. Después, puede crear una máquina virtual desde el disco duro virtual.

Una imagen de disco generalizada es aquella que se ha preparado con **Sysprep** para quitar cualquier información exclusiva (como cuentas de usuario), lo que permite que se pueda volver a usar para crear varias máquinas virtuales. Los VHD generalizados son idóneos para cuando se crean imágenes que el operador de nube de Azure Stack Hub planea usar como elementos de Marketplace.

## <a name="how-to-move-an-image"></a>Traslado de una imagen

Busque la sección que se adapte mejor a sus necesidades al preparar el disco duro virtual.

#### <a name="windows-vm"></a>[Máquina virtual Windows](#tab/port-win)

Siga los pasos descritos en [Preparación de un VHD o un VHDX de Windows antes de cargarlo en Azure](/azure/virtual-machines/windows/prepare-for-upload-vhd-image) para generalizar correctamente el disco duro virtual antes de la carga. Debe usar un disco duro virtual para Azure Stack Hub.

#### <a name="linux-vm"></a>[Máquina virtual Linux](#tab/port-linux)

Siga las instrucciones apropiadas para generalizar el disco duro virtual para el sistema operativo Linux:

- [Distribuciones basadas en CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Red Hat Enterprise Linux](../operator/azure-stack-redhat-create-upload-vhd.md)
- [SLES u openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
- [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

---

## <a name="verify-your-vhd"></a>Comprobación del disco duro virtual

[!INCLUDE [Verify VHD](../includes/user-compute-verify-vhd.md)]
## <a name="upload-to-a-storage-account"></a>Carga en una cuenta de almacenamiento

[!INCLUDE [Upload to a storage account](../includes/user-compute-upload-vhd.md)]

## <a name="create-the-image-in-azure-stack-hub"></a>Creación de la imagen en Azure Stack Hub

[!INCLUDE [Create the image in Azure Stack Hub](../includes/user-compute-create-image.md)]

## <a name="next-steps"></a>Pasos siguientes

[Introducción al traslado de una máquina virtual a Azure Stack Hub](vm-move-overview.md)
