---
title: Adición de Commvault a Marketplace de Azure Stack | Microsoft Docs
description: Aprenda a agregar Commvault a Marketplace de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/28/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 787453550e9a8ed69aa9dfda0a03ea5e57c49d7a
ms.sourcegitcommit: ca358ea5c91a0441e1d33f540f6dbb5b4d3c92c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/08/2019
ms.locfileid: "73802325"
---
# <a name="add-commvault-to-the-azure-stack-marketplace"></a>Incorporación de Commvault a Marketplace de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Este artículo le guía por la forma de ofrecer Commvault Live Sync para actualizar una máquina virtual de recuperación situada en una unidad de escalado de Azure Stack independiente. Commvault se puede descargar y ofrecer como una solución de copia de seguridad y replicación para los usuarios. 

## <a name="notes-for-commvault"></a>Notas de Commvault

- El usuario debe instalar el software de copia de seguridad y replicación en una máquina virtual que se encuentre en su suscripción de Azure Stack de origen. Azure Site Recovery y Azure Backup pueden ofrecer una ubicación fuera de Stack para almacenar copias de seguridad e imágenes de recuperación. Ambos requieren la creación de un almacén de Recovery Services en Azure antes de descargar de las siguientes ubicaciones las imágenes de software que se van a instalar en Azure Stack: [Azure Backup Server](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) y [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus).  
    
- Es posible que necesite licencias para el software de terceros (si se elige).
- Puede que los usuarios necesiten ayuda para conectar su origen y destino a través de una puerta de enlace de red virtual (VPN) o una dirección IP pública en el host de copia de seguridad y replicación.
- Suscripción a la nube de Azure de destino o suscripción a Azure Stack en un destino de recuperación.
- Grupo de recursos de destino y cuenta de Blob Storage en Azure Stack en el destino de recuperación.
- Algunas soluciones requieren que se creen en la suscripción de destino máquinas virtuales que necesitan ejecutarse de forma ininterrumpida para recibir los cambios del servidor de origen. En el artículo en el que se explica cómo[realizar una copia de seguridad de una máquina virtual en Azure Stack con Commvault](../user/azure-stack-network-howto-backup-commvault.md), Commvault Live Sync crea las máquinas virtuales de recuperación de destino en la configuración inicial y las mantiene inactivas (no se ejecutan, por lo que no se pueden facturar nada) hasta que deban aplicarse los cambios durante un ciclo de replicación.


## <a name="get-commvault-for-your-marketplace"></a>Obtención de Commvault para Marketplace

1. Abra el portal de administración de Azure Stack.
2. Seleccione **Marketplace management** (Administración de Marketplace) > **Add from Azure** (Agregar desde Azure).

    ![Commvault para Azure Stack](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Escriba `commvault`.
4. Seleccione **Commvault Trail** (Prueba de Commvault). Y, después, seleccione **Download** (Descargar).


## <a name="next-steps"></a>Pasos siguientes

[Copia de seguridad de una máquina virtual en Azure Stack con Commvault](../user/azure-stack-network-howto-backup-commvault.md)

[Introducción a la oferta de servicios en Azure Stack](service-plan-offer-subscription-overview.md)
