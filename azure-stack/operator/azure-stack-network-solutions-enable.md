---
title: Oferta de una solución de red en Azure Stack con FortiGate de Fortinet | Microsoft Docs
description: Más información sobre cómo habilitar una solución de red en Azure Stack con FortiGate de Fortinet
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: how-to
ms.date: 09/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 27012c491054043f45004d76787538091864577c
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2019
ms.locfileid: "71962715"
---
# <a name="offer-a-network-solution-in-azure-stack-with-fortinet-fortigate"></a>Oferta de una solución de red en Azure Stack con FortiGate de Fortinet

Puede agregar el Firewall de próxima generación de FortiGate al Marketplace de Azure Stack. FortiGate permite a los usuarios crear soluciones de red, como una red privada virtual (VPN), para el emparejamiento de red virtual y Azure Stack. Una aplicación virtual de red (NVA) controla el flujo del tráfico de red desde una red perimetral a otras redes o subredes. 

Para más información sobre FortiGate en Azure Marketplace, consulte el artículo sobre la [solución una sola máquina virtual del firewall de próxima generación de FortiGate de Fortinet](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-marketplace-items"></a>Descarga de los elementos requeridos del Marketplace de Azure Stack

1.  Abra el portal de administración de Azure Stack.

2.  Seleccione **Administración de Marketplace** y seleccione **Agregar desde Azure**.

3. Escriba `Forti` en el cuadro de búsqueda y haga doble clic en > Seleccionar **Descargar** para obtener las últimas versiones disponibles de los siguientes elementos: 
    - FortiGate-VM de Fortinet para BYOL de Azure
    - FortiGate NGFW: implementación de una sola máquina virtual (BYOL)

    ![Azure Stack FortiGate Fortinet](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  Espere hasta que los elementos del Marketplace tengan el estado **Descargado**. Estos elementos puede tardar varios minutos en descargarse.

    ![Azure Stack FortiGate Fortinet](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Pasos siguientes

[Configuración de una VPN para Azure Stack con la NVA FortiGate](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[Conexión de dos redes virtuales mediante el emparejamiento](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[Establecimiento de una conexión de red virtual a red virtual con la NVA FortiGate de Fortinet](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
