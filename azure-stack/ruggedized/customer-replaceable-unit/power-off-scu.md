---
title: Apagado de los nodos de la unidad de escalado
description: Aprenda a apagar nodos de unidad de escalado.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 2eb2c85bcdb350173923b3d8b5a3b463ce6a16ff
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910727"
---
# <a name="powering-off-scale-unit-nodes"></a>Apagado de nodos de unidad de escalado

Antes de que se pueda apagar un nodo de unidad de escalado, debe comprobar el estado de la unidad e identificar el nodo que necesita reparación.

Si el valor de **Power Status** (Estado de energía) del nodo no es **Stopped** (Detenido), use el siguiente procedimiento para apagar el nodo de forma segura.

**Pasos**

1.  Purgue el nodo de unidad de escalado.

    1.  En el portal de administración, seleccione el nodo que necesita reparación y, a continuación, seleccione **Drain** (Purgar).

        ![Captura en la que se muestra la página de administración con la acción de purgar seleccionada y un nodo resaltado.](media/image-23.png)
        
    1.  Cuando se le solicite, escriba el nombre del nodo que desea purgar y seleccione **Yes** (Sí).

        ![Captura de pantalla en la que se muestra la página de administración de nodos.](media/image-24.png)
    
    1.  Verá una notificación que indica que la purga está en curso.
    
        ![Captura de pantalla en la que se muestra la página de administración de nodos con la notificación de purga.](media/image-25.png)
        
    1.  Inicie sesión en la interfaz de iDRAC y compruebe la etiqueta de servicio del nodo en **System Information** (Información del sistema).
    

2.  Apague el nodo de unidad de escalado.

    1.  Una vez completada la purga, seleccione el nodo de nuevo, compruebe que el valor de **Operational State** (Estado operativo) es **Maintenance** (Mantenimiento) y seleccione **Shutdown** (Apagar).

        ![Captura de pantalla en la que se muestra la página de administración de nodos con un nodo y la acción de apagar seleccionada.](media/image-26.png)
        
    1.  Cuando se le solicite, seleccione **Yes** (Sí) para confirmar el apagado.
    
        ![Captura de pantalla en la que se muestra la página de administración de nodos con el cuadro de diálogo para apagar un nodo.](media/image-27.png)
        
    1.  Verá una notificación que indica que el cierre está en curso.

        ![Captura de pantalla en la que se muestra la página de administración de nodos con el cuadro de diálogo del proceso de apagado en curso.](media/image-28.png)
    
    1.  Una vez finalizado el cierre, en **Power Status** (Estado de energía) se muestra **Stopped** (Detenido).
    
        ![Captura de pantalla en la que se muestra la página de administración de nodos con un nodo seleccionado y el estado de energía detenido resaltado.](media/image-29.png)
        