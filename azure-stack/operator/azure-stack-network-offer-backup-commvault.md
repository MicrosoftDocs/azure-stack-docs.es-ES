---
title: Incorporación de Commvault a Marketplace de Azure Stack Hub
description: Aprenda a agregar Commvault a Marketplace de Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: d79137c5a00b4762cf1f7729000f8912f13ba066
ms.sourcegitcommit: 32834e69ef7a804c873fd1de4377d4fa3cc60fb6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/21/2020
ms.locfileid: "81661369"
---
# <a name="add-commvault-to-the-azure-stack-hub-marketplace"></a>Incorporación de Commvault a Marketplace de Azure Stack Hub

Este artículo le guía por la forma de ofrecer Commvault Live Sync para actualizar una máquina virtual de recuperación situada en una unidad de escalado de Azure Stack Hub independiente. Commvault se puede descargar y ofrecer como una solución de copia de seguridad y replicación para los usuarios. 

## <a name="notes-for-commvault"></a>Notas de Commvault

- El usuario debe instalar el software de copia de seguridad y replicación en una máquina virtual que se encuentre en su suscripción de Azure Stack Hub de origen. Azure Site Recovery y Azure Backup pueden ofrecer una ubicación fuera de Stack para almacenar copias de seguridad e imágenes de recuperación. Ambos requieren la creación de un almacén de Recovery Services en Azure antes de descargar de las siguientes ubicaciones las imágenes de software que se van a instalar en Azure Stack Hub: [Azure Backup Server](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) y [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus).  
    
- Es posible que necesite licencias para el software de terceros (si se elige).
- Puede que los usuarios necesiten ayuda para conectar su origen y destino a través de una puerta de enlace de red virtual (VPN) o una dirección IP pública en el host de copia de seguridad y replicación.
- Suscripción a la nube de Azure de destino o suscripción a Azure Stack Hub en un destino de recuperación.
- Grupo de recursos de destino y cuenta de Blob Storage en Azure Stack Hub en el destino de recuperación.
- Algunas soluciones requieren que se creen en la suscripción de destino máquinas virtuales que necesitan ejecutarse de forma ininterrumpida para recibir los cambios del servidor de origen. En [Copia de seguridad de una máquina virtual en Azure Stack Hub con Commvault](../user/azure-stack-network-howto-backup-commvault.md), Commvault Live Sync crea las máquinas virtuales de recuperación de destino en la configuración inicial y las mantiene inactivas (no se ejecutan, por lo que no se pueden facturar nada) hasta que deban aplicarse los cambios durante un ciclo de replicación.


## <a name="get-commvault-for-your-marketplace"></a>Obtención de Commvault para Marketplace

1. Abra el portal de administración de Azure Stack Hub.
2. Seleccione **Marketplace management** (Administración de Marketplace) > **Add from Azure** (Agregar desde Azure).

    ![Commvault para Azure Stack Hub](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Escriba `commvault`.
4. Seleccione **Commvault Trail** (Prueba de Commvault). Y, después, seleccione **Download** (Descargar).


## <a name="next-steps"></a>Pasos siguientes

[Copia de seguridad de una máquina virtual en Azure Stack Hub con Commvault](../user/azure-stack-network-howto-backup-commvault.md)

[Introducción a la oferta de servicios en Azure Stack Hub](service-plan-offer-subscription-overview.md)
