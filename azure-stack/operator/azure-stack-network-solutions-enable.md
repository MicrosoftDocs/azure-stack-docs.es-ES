---
title: Oferta de una solución de red en Azure Stack Hub con FortiGate de Fortinet
description: Aprenda a habilitar una solución de red en Azure Stack Hub con FortiGate de Fortinet.
author: mattbriggs
ms.topic: how-to
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 87122d755ef7065747fc2df214ed270f92c4e49e
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881734"
---
# <a name="offer-a-network-solution-in-azure-stack-hub-with-fortinet-fortigate"></a>Oferta de una solución de red en Azure Stack Hub con FortiGate de Fortinet

Puede agregar el Firewall de próxima generación de FortiGate a Marketplace de Azure Stack Hub. FortiGate permite a los usuarios crear soluciones de red, como una red privada virtual (VPN), para el emparejamiento de red virtual y Azure Stack Hub. Una aplicación virtual de red (NVA) controla el flujo del tráfico de red desde una red perimetral a otras redes o subredes. 

Para más información sobre FortiGate en Azure Marketplace, consulte el artículo sobre la [solución una sola máquina virtual del firewall de próxima generación de FortiGate de Fortinet](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-hub-marketplace-items"></a>Descarga de los elementos de Marketplace de Azure Stack Hub necesarios

1.  Abra el portal del administrador de Azure Stack Hub.

2.  Seleccione **Administración de Marketplace** y seleccione **Agregar desde Azure**.

3. Escriba `Forti` en el cuadro de búsqueda y haga doble clic en > Seleccionar **Descargar** para obtener las últimas versiones disponibles de los siguientes elementos: 
    - FortiGate-VM de Fortinet para BYOL de Azure
    - FortiGate NGFW: implementación de una sola máquina virtual (BYOL)

    ![FortiGate de Fortinet para Azure Stack Hub](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

2.  Espere hasta que los elementos del Marketplace tengan el estado **Descargado**. Estos elementos puede tardar varios minutos en descargarse.

    ![FortiGate de Fortinet para Azure Stack Hub](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Pasos siguientes

[Configuración de una VPN para Azure Stack Hub con FortiGate NVA](../user/azure-stack-network-howto-vnet-to-onprem.md)  
[Conexión de dos redes virtuales mediante el emparejamiento](../user/azure-stack-network-howto-vnet-to-vnet.md)  
[Establecimiento de una conexión de red virtual a red virtual con la NVA FortiGate de Fortinet](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
