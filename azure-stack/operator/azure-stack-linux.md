---
title: Agregar imágenes de Linux a Azure Stack
description: Aprenda a agregar imágenes de Linux a Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/15/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 11/16/2018
ms.openlocfilehash: 2f7f0c55f02fd99a419619d878be8300d7326303
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64309651"
---
# <a name="add-linux-images-to-azure-stack"></a>Agregar imágenes de Linux a Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede implementar máquinas virtuales Linux en Azure Stack agregando una imagen basada en Linux a Marketplace de Azure Stack. La manera más fácil de agregar una imagen de Linux a Azure Stack es con la administración de Marketplace. Estas imágenes se prepararon y probaron para que sean compatibles con Azure Stack.

## <a name="marketplace-management"></a>Administración de Marketplace

Para descargar las imágenes de Linux desde Azure Marketplace, use los procedimientos descritos en el artículo [Descarga de elementos de Marketplace desde Azure a Azure Stack](azure-stack-download-azure-marketplace-item.md). Seleccione las imágenes de Linux que quiera ofrecer a los usuarios en Azure Stack. 

Tenga en cuenta que hay actualizaciones frecuentes de estas imágenes, así que compruebe la administración de Marketplace a menudo para estar al día.

## <a name="prepare-your-own-image"></a>Preparación de su propia imagen

Siempre que sea posible, descargue las imágenes disponibles en Marketplace Management, que se han preparado y probado para Azure Stack.

### <a name="azure-linux-agent"></a>Agente Linux de Azure
Es necesario el agente Linux de Azure (suele denominarse `WALinuxAgent` o `walinuxagent`), y no todas las versiones del agente funcionarán en Azure Stack. No se admiten las versiones entre 2.2.20 y 2.2.35 en Azure Stack. Para usar las versiones de agente más recientes superiores a 2.2.35, aplique la revisión 1901/1902 o actualice su instancia de Azure Stack a la versión 1903 (o superior). Tenga en cuenta que no se admite [cloud-init](https://cloud-init.io/) en Azure Stack en este momento.

| Compilación de Azure Stack | Compilación del agente Linux de Azure |
| ------------- | ------------- |
| 1.1901.0.99 o versiones anteriores | 2.2.20 |
| 1.1902.0.69  | 2.2.20  |
|  1.1901.3.105   | 2.2.35 o más reciente |
| 1.1902.2.73  | 2.2.35 o más reciente |
| 1.1903.0.35  | 2.2.35 o más reciente |
| No compatible | 2.2.21-2.2.34 |

Puede preparar su propia imagen de Linux mediante las siguientes instrucciones:

* [Distribuciones basadas en CentOS](/azure/virtual-machines/linux/create-upload-centos?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Debian Linux](/azure/virtual-machines/linux/debian-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Red Hat Enterprise Linux](azure-stack-redhat-create-upload-vhd.md)
* [SLES y openSUSE](/azure/virtual-machines/linux/suse-create-upload-vhd?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu Server](/azure/virtual-machines/linux/create-upload-ubuntu?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

## <a name="add-your-image-to-the-marketplace"></a>Incorporación de la imagen a Marketplace

Consulte [Incorporación de la imagen a Marketplace](azure-stack-add-vm-image.md). Asegúrese de que el `OSType` parámetro está establecido en `Linux`.

Después de agregar la imagen a Marketplace, se crea un elemento de Marketplace y los usuarios podrán implementar una máquina virtual Linux.

## <a name="next-steps"></a>Pasos siguientes

Para obtener más información, consulte los siguientes artículos:

- [Descarga de elementos de Marketplace desde Azure a Azure Stack](azure-stack-download-azure-marketplace-item.md)
- [Información general de Azure Stack Marketplace](azure-stack-marketplace.md)
