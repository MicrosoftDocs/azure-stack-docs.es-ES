---
title: Reemplazo de un disco del sistema operativo
description: Aprenda a reemplazar un disco del sistema operativo.
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 24447611a4b3c775b92f4d163bb250060e29417e
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487840"
---
# <a name="replacing-an-operating-system-disk"></a>Reemplazo de un disco del sistema operativo

Utilice el siguiente procedimiento para reemplazar un disco del sistema operativo con errores en un nodo de unidad de escalado.

## <a name="prerequisites"></a>Prerrequisitos

1.  Revise la sección *Notas, precauciones y advertencias* de la guía correspondiente.

2.  Revise Precauciones en la manipulación.

3.  Conocimientos necesarios para trabajar con nodos de unidad de escalado en Azure Stack Hub Ruggedized, si está trabajando con un nodo de unidad de escalado.

4.  Complete Comprobación del acceso y estado del nodo de unidad de escalado.

5.  Complete Apagado de nodos de unidad de escalado.

    En el caso de los nodos de unidad de escalado de Azure Stack Hub, el sistema operativo se ejecuta desde un par reflejado de módulos SSD M.2 que residen en la tarjeta de solución de almacenamiento optimizado de arranque (BOSS). El sistema debe estar apagado para reemplazar un disco del sistema operativo.
    
## <a name="steps"></a>Pasos

1.  Localice el nodo físico en el bastidor.

2.  Compruebe que el nodo está apagado. El LED de alimentación debe ser naranja.

    > [!CAUTION]
    > Antes de desconectar los cables del servidor en el que está trabajando, asegúrese de que todos los cables estén etiquetados correctamente. Los cables DEBEN volver a conectarse a los mismos puertos.
    
3.  Reemplace el módulo SSD M.2 con errores.

    Siga el [proceso de reemplazo del módulo SSD M.2](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) para los nodos de unidad de escalado.
    
4.  Encienda el nodo.

    Después de volver a conectar la alimentación, si el servidor no se reinicia automáticamente, presione el botón de encendido para volver a activar el nodo.
    
## <a name="next-steps"></a>Pasos siguientes

1.  Complete Encendido y reparación de un nodo de unidad de escalado.

2.  Complete Comprobación del estado del nodo de unidad de escalado.

