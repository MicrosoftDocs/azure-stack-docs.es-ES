---
title: Reemplazo de un DIMM
description: Aprenda a reemplazar un DIMM.
author: PatAltimore
ms.topic: how-to
ms.date: 02/05/2021
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: f20cdf77cb5ed3b63bcbd9b57770039efb6d29e5
ms.sourcegitcommit: 5ea0e915f24c8bcddbcaf8268e3c963aa8877c9d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/14/2021
ms.locfileid: "100487942"
---
# <a name="replacing-a-dimm"></a>Reemplazo de un DIMM

Use el procedimiento siguiente para reemplazar un DIMM.

## <a name="prerequisites"></a>Prerrequisitos

1.  Revise la sección *Notas, precauciones y advertencias* de la guía correspondiente.

2.  Revise Precauciones en la manipulación.

3.  Revisar

    -   Conocimientos necesarios para trabajar con nodos de unidad de escalado en un dispositivo en la nube resistente, si está trabajando con un nodo de unidad de escalado

    -   Conocimientos necesarios para trabajar con el host de ciclo de vida de hardware, si está trabajando con el host de ciclo de vida de hardware

4.  Completo

    -   Comprobación del acceso y estado del nodo de la unidad de escalado, si está trabajando con un nodo de unidad de escalado

    -   Comprobación del acceso y estado del host de ciclo de vida de hardware, si está trabajando con el host de ciclo de vida de hardware

5.  Completo

    -   Apagado de los nodos de unidad de escalado, si está trabajando con un nodo de unidad de escalado

    -   Apagado del host de ciclo de vida de hardware, si está trabajando con el host de ciclo de vida de hardware

## <a name="steps"></a>Pasos

1.  Localice el nodo físico en el bastidor.

2.  Compruebe que el nodo está apagado. El LED de alimentación debe ser naranja.

    > [!CAUTION]
    > Antes de desconectar los cables del servidor en el que está trabajando, asegúrese de que todos los cables estén etiquetados correctamente. Los cables DEBEN volver a conectarse a los mismos puertos.
    
3.  Reemplace los DIMM necesarios.

    Siga las indicaciones del [proceso de sustitución del módulo de memoria](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) para los nodos de la unidad de escalado o los hosts de ciclo de vida del hardware.
    
4.  Encienda el nodo.

    Después de volver a conectar la alimentación, si el servidor no se reinicia automáticamente, presione el botón de encendido para volver a activar el nodo.
    
## <a name="next-steps"></a>Pasos siguientes

Si está trabajando con un nodo de unidad de escalado:

1.  Complete Encendido de un nodo de unidad de escalado.

2.  Complete Comprobación del estado del nodo de unidad de escalado. Si está trabajando con el host de ciclo de vida de hardware, complete Comprobación del estado del host de ciclo de vida de hardware.
    
