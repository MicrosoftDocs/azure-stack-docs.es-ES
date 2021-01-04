---
title: Apagado de los nodos de la unidad de escalado
description: Aprenda a apagar nodos de unidad de escalado.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: fde7496a54f0c2d3b28d98584295a761c683e17c
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392860"
---
# <a name="powering-off-scale-unit-nodes"></a>Apagado de nodos de unidad de escalado

Antes de que se pueda apagar un nodo de unidad de escalado, debe comprobar el estado de la unidad e identificar el nodo que necesita reparación.

Si el valor de **Power Status** (Estado de energía) del nodo no es **Stopped** (Detenido), use el siguiente procedimiento para apagar el nodo de forma segura.

**Pasos**

1.  Purgue el nodo de unidad de escalado.

    1.  En el portal de administración, seleccione el nodo que necesita reparación y, a continuación, seleccione **Drain** (Purgar).

        ![](media/image-23.png)
        
    1.  Cuando se le solicite, escriba el nombre del nodo que desea purgar y seleccione **Yes** (Sí).

        ![](media/image-24.png)
    
    1.  Verá una notificación que indica que la purga está en curso.
    
        ![](media/image-25.png)
        
    1.  Inicie sesión en la interfaz de iDRAC y compruebe la etiqueta de servicio del nodo en **System Information** (Información del sistema).
    

2.  Apague el nodo de unidad de escalado.

    1.  Una vez completada la purga, seleccione el nodo de nuevo, compruebe que el valor de **Operational State** (Estado operativo) es **Maintenance** (Mantenimiento) y seleccione **Shutdown** (Apagar).

        ![](media/image-26.png)
        
    1.  Cuando se le solicite, seleccione **Yes** (Sí) para confirmar el apagado.
    
        ![](media/image-27.png)
        
    1.  Verá una notificación que indica que el cierre está en curso.

        ![](media/image-28.png)
    
    1.  Una vez finalizado el cierre, en **Power Status** (Estado de energía) se muestra **Stopped** (Detenido).
    
        ![](media/image-29.png)
        