---
title: Encendido y reparación de un nodo de unidad de escalado
description: Aprenda a encender y reparar un nodo de unidad de escalado.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: d05c696f62aad4d9305137edab777d69b6442814
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97909929"
---
# <a name="powering-on-and-repairing-a-scale-unit-node"></a>Encendido y reparación de un nodo de unidad de escalado

**Pasos**

Para reparar y volver a poner en producción un nodo de unidad de escalado, debe ejecutar el procedimiento de reparación de Azure Stack Hub.

> [!NOTE]
> El procedimiento de reparación tarda aproximadamente tres horas en completarse.

1.  En el **portal de administración**, seleccione el nodo y seleccione **Repair** (Reparar).

    ![Captura de pantalla en la que se muestra la página de administración de nodos con un nodo y la acción de reparar seleccionada.](media/image-52.png)

1.  Indique el valor de **BMC IP Address** (Dirección IP de BMC) que corresponde al nodo que está reparando y seleccione **Repair** (Reparar).

    ![Captura de pantalla en la que se muestra la página de administración de nodos con un nodo seleccionado, la dirección IP resaltada y el cuadro de diálogo para reparar el nodo.](media/image-53.png)

1.  Puede ver el progreso en el panel de notificaciones:

    ![Captura de pantalla en la que se muestra el panel de notificaciones y la indicación de que se está ejecutando el proceso de reparación del nodo.](media/image-54.png)
    
    
    > [!NOTE]
    > Si el procedimiento de reparación no se completa en tres horas o se produce un problema, abra un caso con el soporte técnico de Dell Technologies, que puede interactuar con el servicio de soporte técnico de Microsoft para solucionar el problema.
    
    Una vez completado el proceso de reparación, el nodo vuelve a mostrar **Running** (En ejecución) en Operational State (Estado operativo).
    
