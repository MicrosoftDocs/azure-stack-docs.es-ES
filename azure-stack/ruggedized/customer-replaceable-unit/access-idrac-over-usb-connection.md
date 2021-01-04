---
title: Acceso al controlador iDRAC a través de una conexión USB
description: Aprenda a acceder al controlador iDRAC a través de una conexión USB.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 7381aae6b7b08de01e27f895d79519a024a1955a
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97393223"
---
# <a name="accessing-the-idrac-interface-over-a-direct-usb-connection"></a>Acceso a la interfaz del controlador iDRAC a través de una conexión USB directa

La característica iDRAC Direct le permite conectar directamente el portátil al puerto USB del controlador iDRAC. Esta característica permite interactuar directamente con las interfaces del controlador iDRAC, como la interfaz web, RACADM y WSMan para la administración y el mantenimiento avanzados de servidores.



Para acceder a la interfaz de iDRAC a través del puerto USB, haga lo siguiente desde el portátil.

**Pasos**

1.  En el portátil, desactive las redes inalámbricas y desconéctese de otras redes por cable.

2.  Conecte un cable micro USB del portátil al puerto iDRAC Direct, situado en la parte delantera del servidor.
    Consulte el elemento 4 del diagrama.

    ![](media/image-67.png)

3.  Espere a que el portátil adquiera la dirección IP 169.254.0.4.

    Este paso puede tardar varios segundos. El controlador iDRAC adquiere la dirección IP 169.254.0.3.

4.  Conéctese a la interfaz web de iDRAC.

    Para acceder a la interfaz web de iDRAC, abra un explorador y vaya a 169.254.0.3.

5.  Complete las actividades necesarias.

    

    > [!NOTE]
    > Cuando el controlador iDRAC usa el puerto USB (el elemento 3 en el diagrama anterior), el LED parpadea indicando que hay actividad. El LED parpadea cuatro veces por segundo.
    
6.  Desconecte el cable micro USB.

    Después de completar las acciones deseadas, desconecte el cable USB del sistema. El LED se apaga.
    
