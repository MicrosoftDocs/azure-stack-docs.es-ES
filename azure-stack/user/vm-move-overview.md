---
title: Traslado de una máquina virtual a Azure Stack Hub
description: Conozca más información sobre las distintas formas en que puede trasladar una máquina virtual a Azure Stack Hub.
author: mattbriggs
ms.topic: conceptual
ms.date: 12/16/2020
ms.author: mabrigg
ms.reviewer: kivenkat
ms.lastreviewed: 9/8/2020
ms.openlocfilehash: 2ee1ae6405bd7216c65d328e2edc7d1606b2bddc
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97867152"
---
# <a name="move-a-vm-to-azure-stack-hub-overview"></a>Introducción al traslado de una máquina virtual a Azure Stack Hub

Puede trasladar máquinas virtuales desde su entorno a Azure Stack Hub. Hay algunas limitaciones previsibles a la hora de planear el traslado de las cargas de trabajo. En este artículo se enumeran los requisitos de disco duro virtual (VHD) en Azure Stack Hub. Azure Stack Hub requiere un disco duro virtual de la generación uno (1). La máquina virtual deberá estar generalizada o especializada. Use máquinas virtuales generalizadas como imagen base para las máquinas virtuales creadas en Azure Stack. Una máquina virtual especializada contiene cuentas de usuario. Para migrar, preparar y descargar el disco duro virtual, compruebe que este cumple los requisitos, cargue la imagen en una cuenta de almacenamiento en Azure Stack Hub y, a continuación, cree la máquina virtual en la nube. Si tiene una tarea de migración más compleja, puede encontrar un análisis completo en las notas del producto en *Migración a Azure Stack Hub*.

Las imágenes personalizadas presentan dos formas: **generalizada** y **especializada**.

- **Imagen generalizada**  
  Una imagen de disco generalizada es aquella que se ha preparado con **Sysprep** para quitar cualquier información exclusiva (como cuentas de usuario), lo que permite que se pueda volver a usar para crear varias máquinas virtuales. Los VHD generalizados son idóneos para cuando se crean imágenes que el operador de nube de Azure Stack Hub planea usar como elementos de Marketplace. Las imágenes que se ofrecen a través del portal de administración o de los puntos de conexión de administrador son **imágenes de plataforma**.

- **Imagen especializada**  
  Una imagen de disco especializada es una copia del disco duro virtual de una máquina virtual existente que contiene las cuentas de usuario, aplicaciones y otros datos de estado de la máquina virtual original. Este suele ser el formato en el que las máquinas virtuales se migran a Azure Stack Hub. Los discos duros virtuales especializados son idóneos para cuando es necesario migrar máquinas virtuales de un entorno local a Azure Stack Hub.

Al mover una imagen a Azure Stack Hub, tenga en cuenta cómo le gustaría que se use la imagen.

- **Carga de trabajo personal**  
    Es posible que tenga una máquina en el entorno local o en Azure global que use para desarrollo o para tareas específicas y le gustaría aprovechar el hospedaje en una nube privada con Azure Stack Hub. Le gustaría conservar los datos y las cuentas de usuario en la máquina. En este caso, deseará trasladar esta máquina a Azure Stack Hub como una imagen especializada.

- **Imagen maestra**  
    Puede que desee compartir una configuración de máquina virtual común y un conjunto de aplicaciones dentro de su grupo de trabajo. No tendrá que compartir la imagen con usuarios ajenos a su dominio de Azure Stack Hub (inquilino de directorio). En este caso, es posible que desee generalizar la imagen quitando los datos y las cuentas de usuario. Después, puede compartir esta imagen con otros usuarios de su inquilino.

- **Oferta de Marketplace de Azure Stack Hub**  
    El operador en la nube puede usar una imagen generalizada como base de una oferta de Marketplace. Una vez que haya preparado la imagen, el operador de la nube puede usar la imagen personalizada para crear una oferta de Marketplace para la instancia de Azure Stack Hub. Los usuarios pueden crear su propia máquina virtual a partir de la imagen tal y como lo harían con cualquier otra oferta en Marketplace. Tendrá que trabajar con su operador en la nube para crear esta oferta.

## <a name="verify-vhd-requirements"></a>Comprobación de los requisitos del disco duro virtual

> [!IMPORTANT]  
> Al preparar el disco duro virtual, debe cumplir los siguientes requisitos o no podrá usar el disco duro virtual en Azure Stack Hub.
> Antes de cargar la imagen, tenga en cuenta lo siguiente:
> - Azure Stack Hub solo admite imágenes de máquinas virtuales de la generación uno (1).
> - El disco duro virtual es de tipo fijo. Azure Stack Hub no admite discos duros virtuales dinámicos.
> - El disco duro virtual tiene un tamaño mínimo de 20 MB.
> - El disco duro virtual está alineado, es decir, el tamaño virtual debe ser un múltiplo de 1 MB.
> - La longitud del blob del disco duro virtual = tamaño virtual + longitud de pie de página del disco duro virtual (512). Un breve pie de página al final del blob describe las propiedades del disco duro virtual. 

Puede encontrar los pasos necesarios para reparar el disco duro virtual en [Comprobación del disco duro virtual](vm-move-from-azure.md#verify-your-vhd)

## <a name="methods-of-moving-a-vm"></a>Métodos para trasladar una máquina virtual

Puede trasladar manualmente la máquina virtual a Azure Stack Hub con los siguientes escenarios:

| Escenario | Instructions |
| --- | --- |
| De Azure global a Azure Stack Hub | Prepare el disco duro virtual en Azure global y luego cárguelo en Azure Stack Hub. Para más información, consulte [Traslado de una máquina virtual desde Azure a Azure Stack Hub](vm-move-from-azure.md). |
| Local generalizado a Azure Stack Hub | Prepare el disco duro virtual y generalícelo de forma local en Hyper-V y, a continuación, cárguelo en Azure Stack Hub. Para más información, consulte [Traslado de una máquina virtual generalizada desde un entorno local a Azure Stack Hub](vm-move-generalized.md). |
| Local especializada a Azure Stack Hub | Prepare el disco duro virtual especializado de forma local en Hyper-V y, a continuación, cárguelo en Azure Stack Hub. Para más información, consulte [Traslado de una máquina virtual especializada desde un entorno local a Azure Stack Hub](vm-move-specialized.md). |

## <a name="migrate-to-azure-stack-hub"></a>Migración a Azure Stack Hub

Puede encontrar detalles, listas de comprobación y procedimientos recomendados para planear y migrar las cargas de trabajo de forma masiva a Azure Stack Hub en una guía escrita por los expertos de AzureCAT en Azure global. Esta guía se centra en la migración de las aplicaciones existentes que se ejecutan en servidores físicos o en las plataformas de virtualización existentes. Al mover estas cargas de trabajo al entorno de IaaS de Azure Stack Hub, los equipos se pueden beneficiar de operaciones más fluidas, autoservicio de implementaciones, configuraciones de hardware estandarizadas y coherencia de Azure.

[Obtenga las notas del producto.](https://azure.microsoft.com/resources/migrate-to-azure-stack-hub-patterns-and-practices-checklists/)

También puede encontrar instrucciones sobre la migración en Cloud Adoption Framework. Para más información, consulte [Planeamiento de la migración a Azure Stack Hub](/azure/cloud-adoption-framework/scenarios/azure-stack/plan). 

## <a name="next-steps"></a>Pasos siguientes

[Introducción a las máquinas virtuales de Azure Stack Hub](azure-stack-compute-overview.md)

[Adición de una imagen de máquina virtual personalizada a Azure Stack Hub](../operator/azure-stack-add-vm-image.md)