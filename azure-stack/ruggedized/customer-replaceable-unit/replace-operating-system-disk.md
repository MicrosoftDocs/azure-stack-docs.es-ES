---
title: Reemplazo de un disco del sistema operativo
description: Aprenda a reemplazar un disco del sistema operativo.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 529a81a8c63e2fe72da4618e7f359832b2267ea3
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97392852"
---
# <a name="replacing-an-operating-system-disk"></a>Reemplazo de un disco del sistema operativo

Utilice el siguiente procedimiento para reemplazar un disco del sistema operativo con errores en un nodo de unidad de escalado.

## <a name="prerequisites"></a>Prerrequisitos

1.  Revise la sección *Notas, precauciones y advertencias* de la guía correspondiente.

2.  Revise Precauciones en la manipulación.

3.  Revise Conocimientos necesarios para trabajar con nodos de unidad de escalado en un dispositivo Tactical Cloud, si está trabajando con un nodo de unidad de escalado.

4.  Complete Comprobación del acceso y estado del nodo de unidad de escalado.

5.  Complete Apagado de nodos de unidad de escalado.

    En el caso de los nodos de unidad de escalado de Azure Stack Hub, el sistema operativo se ejecuta desde un par reflejado de módulos SSD M.2 que residen en la tarjeta BOSS (solución de almacenamiento optimizado de arranque) del sistema Dell PowerEdge. El sistema debe estar apagado para reemplazar un disco del sistema operativo.
    
## <a name="steps"></a>Pasos

1.  Localice el nodo físico en el bastidor.

2.  Compruebe que el nodo está apagado. El LED de alimentación debe ser naranja.

    > [!CAUTION]
    > Antes de desconectar los cables del servidor en el que está trabajando, asegúrese de que todos los cables estén etiquetados correctamente. Los cables DEBEN volver a conectarse a los mismos puertos.
    
3.  Reemplace el módulo SSD M.2 con errores.

    Siga el proceso de reemplazo del módulo SSD M.2 del [Manual de instalación y servicio de Dell EMC PowerEdge R640](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) para los nodos de unidad de escalado.
    
4.  Encienda el nodo.

    Después de volver a conectar la alimentación, si el servidor no se reinicia automáticamente, presione el botón de encendido para volver a activar el nodo.
    
## <a name="next-steps"></a>Pasos siguientes

1.  Complete Encendido y reparación de un nodo de unidad de escalado.

2.  Complete Comprobación del estado del nodo de unidad de escalado.

