---
title: Incorporación de Fortinet FortiGate a Marketplace de Azure Stack Hub
description: Aprenda a agregar Fortinet FortiGate a Marketplace de Azure Stack Hub, lo que permite a los usuarios crear soluciones de red.
author: mattbriggs
ms.topic: how-to
ms.date: 12/2/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 12/2/2020
ms.openlocfilehash: fec899c2040b71ad10be95d18dfc56a17d1fe617
ms.sourcegitcommit: 9ef2cdc748cf00cd3c8de90705ea0542e29ada97
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/02/2020
ms.locfileid: "96525530"
---
# <a name="offer-a-network-solution-in-azure-stack-hub-with-fortinet-fortigate"></a>Oferta de una solución de red en Azure Stack Hub con FortiGate de Fortinet

FortiGate Next-Generation Firewall (NGFW) to se puede agregar a Marketplace de Azure Stack Hub. FortiGate permite a los usuarios crear soluciones de red, como una red privada virtual (VPN), para el emparejamiento de red virtual y Azure Stack Hub. Una aplicación virtual de red (NVA) controla el flujo del tráfico de red desde una red perimetral a otras redes o subredes.

Para más información sobre FortiGate en Azure Marketplace, consulte el artículo sobre la [solución Fortinet FortiGate Next-Generation Firewall para máquinas virtuales individuales](https://azuremarketplace.microsoft.com/marketplace/apps/fortinet.fortinet-FortiGate-singlevm).

## <a name="download-the-required-azure-stack-hub-marketplace-items"></a>Descarga de los elementos de Marketplace de Azure Stack Hub necesarios

1. Abra el portal del administrador de Azure Stack Hub.

2. Seleccione **Administración de Marketplace** y seleccione **Agregar desde Azure**.

3. Escriba `Forti` en el cuadro de búsqueda y haga doble clic en > Seleccionar **Descargar** para obtener las últimas versiones disponibles de los siguientes elementos:
    - FortiGate-VM de Fortinet para BYOL de Azure
    - FortiGate NGFW: implementación de una sola máquina virtual (BYOL)

    ![Captura de pantalla que muestra los elementos descargados disponibles.](./media/azure-stack-network-solutions-enable/azure-stack-marketplace-FortiGate-fortinet.png)

4. Espere hasta que los elementos del Marketplace tengan el estado **Descargado**. Estos elementos puede tardar varios minutos en descargarse.

    ![FortiGate de Fortinet para Azure Stack Hub](./media/azure-stack-network-solutions-enable/image4.png)

## <a name="next-steps"></a>Pasos siguientes

- [Configuración de una VPN para Azure Stack Hub con FortiGate NVA](../user/azure-stack-network-howto-vnet-to-onprem.md)  
- [Conexión de dos redes virtuales mediante el emparejamiento](../user/azure-stack-network-howto-vnet-to-vnet.md)  
- [Establecimiento de una conexión de red virtual a red virtual con la NVA FortiGate de Fortinet](../user/azure-stack-network-howto-vnet-to-vnet-stacks.md)  
