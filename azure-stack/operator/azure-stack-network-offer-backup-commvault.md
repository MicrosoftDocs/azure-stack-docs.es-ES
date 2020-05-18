---
title: Incorporación de Commvault a Marketplace de Azure Stack Hub
description: Aprenda a agregar Commvault a Marketplace de Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 04/20/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 10/28/2019
ms.openlocfilehash: 6f61e1d34cd8c8fcd22d4ebb87ece01376dbb5dd
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/13/2020
ms.locfileid: "83375132"
---
# <a name="add-commvault-to-azure-stack-hub-marketplace"></a>Incorporación de Commvault a Marketplace de Azure Stack Hub

Este artículo le guía por oferta de Commvault Live Sync para actualizar una máquina virtual de recuperación situada en una unidad de escalado de Azure Stack Hub independiente. Commvault se puede descargar y ofrecer como una solución de copia de seguridad y replicación para los usuarios.

## <a name="notes-for-commvault"></a>Notas de Commvault

- El usuario debe instalar el software de copia de seguridad y replicación en una máquina virtual que se encuentre en su suscripción de Azure Stack Hub de origen. Azure Site Recovery y Azure Backup pueden ofrecer una ubicación fuera de Stack para almacenar copias de seguridad e imágenes de recuperación. Ambos requieren la creación de un almacén de Recovery Services en Azure antes de descargar las imágenes de software que se van a instalar en Azure Stack Hub. Las imágenes de software se pueden descargar desde: [Azure Backup Server](https://go.microsoft.com/fwLink/?LinkId=626082&clcid=0x0409) y [Azure Site Recovery](https://aka.ms/unifiedinstaller_eus).  

- Es posible que necesite licencias para el software de terceros (si lo elige).
- También es posible que los usuarios necesiten ayuda para conectar su origen y destino a través de una puerta de enlace de red virtual o una IP pública en el host de copia de seguridad y replicación.
- Una suscripción a la nube de Azure de destino o una suscripción a Azure Stack Hub en un destino de recuperación.
- Un grupo de recursos de destino y una cuenta de Blob Storage en Azure Stack Hub en un destino de recuperación.
- Algunas soluciones requieren que se creen máquinas virtuales en la suscripción de destino que necesitan ejecutarse de forma ininterrumpida para recibir los cambios del servidor de origen. En [Copia de seguridad de una máquina virtual en Azure Stack Hub con Commvault](../user/azure-stack-network-howto-backup-commvault.md), Commvault Live Sync crea las máquinas virtuales de recuperación de destino en la configuración inicial y las mantiene inactivas (al no ejecutarse, no se factura nada) hasta que deban aplicarse los cambios durante un ciclo de replicación.

## <a name="get-commvault-for-your-marketplace"></a>Obtención de Commvault para Marketplace

1. Abra el portal del administrador de Azure Stack Hub.
2. Seleccione **Marketplace management** (Administración de Marketplace) > **Add from Azure** (Agregar desde Azure).

    ![Commvault para Azure Stack Hub](./media/azure-stack-network-offer-backup-commvault/get-commvault-for-marketplace.png)

3. Escriba `commvault`.
4. Seleccione **Commvault Trail** (Prueba de Commvault). Y, después, seleccione **Download** (Descargar).

## <a name="next-steps"></a>Pasos siguientes

- [Copia de seguridad de una máquina virtual en Azure Stack Hub con Commvault](../user/azure-stack-network-howto-backup-commvault.md)
- [Introducción a la oferta de servicios en Azure Stack Hub](service-plan-offer-subscription-overview.md)
