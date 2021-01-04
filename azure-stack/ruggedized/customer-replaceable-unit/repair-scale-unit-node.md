---
title: Encendido y reparación de un nodo de unidad de escalado
description: Aprenda a encender y reparar un nodo de unidad de escalado.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 255a40bc2439ae1a6995d4ddf89df192d5e57551
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97393192"
---
# <a name="powering-on-and-repairing-a-scale-unit-node"></a>Encendido y reparación de un nodo de unidad de escalado

**Pasos**

Para reparar y volver a poner en producción un nodo de unidad de escalado, debe ejecutar el procedimiento de reparación de Azure Stack Hub.

> [!NOTE]
> El procedimiento de reparación tarda aproximadamente tres horas en completarse.

1.  En el **portal de administración**, seleccione el nodo y seleccione **Repair** (Reparar).

    ![](media/image-52.png)

1.  Indique el valor de **BMC IP Address** (Dirección IP de BMC) que corresponde al nodo que está reparando y seleccione **Repair** (Reparar).

    ![](media/image-53.png)

1.  Puede ver el progreso en el panel de notificaciones:

    ![](media/image-54.png)
    
    
    > [!NOTE]
    > Si el procedimiento de reparación no se completa en tres horas o se produce un problema, abra un caso con el soporte técnico de Dell Technologies, que puede interactuar con el servicio de soporte técnico de Microsoft para solucionar el problema.
    
    Una vez completado el proceso de reparación, el nodo vuelve a mostrar **Running** (En ejecución) en Operational State (Estado operativo).
    
