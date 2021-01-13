---
title: Sustitución de un disco de almacenamiento externo con errores
description: Obtenga información sobre cómo sustituir un disco de almacenamiento externo con errores.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: e53d81e7606c6440ce535726199af31a1dbfd950
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910659"
---
# <a name="replacing-a-failed-external-storage-disk"></a>Sustitución de un disco de almacenamiento externo con errores

Use el procedimiento siguiente para reemplazar un disco externo con errores.

## <a name="prerequisites"></a>Prerrequisitos

1.  Revise *Notas, precauciones y advertencias* al comienzo de esta guía.

2.  Revise Precauciones en la manipulación.

3.  Revisar

    -   Conocimientos necesarios para trabajar con nodos de unidad de escalado en un dispositivo en la nube táctico, si está trabajando con un nodo de unidad de escalado

    -   Conocimientos necesarios para trabajar con el host de ciclo de vida de hardware, si está trabajando con el host de ciclo de vida de hardware

4.  Completo

    -   Comprobación del acceso y estado del nodo de unidad de escalado, si está trabajando con un nodo de unidad de escalado

    -   Comprobación del acceso y estado del host de ciclo de vida de hardware, si está trabajando con el host de ciclo de vida de hardware

5.  Completo

    -   Apagado de nodos de unidad de escalado, si está trabajando con un nodo de unidad de escalado

    -   Apagado del host de ciclo de vida de hardware, si está trabajando con el host de ciclo de vida de hardware

## <a name="steps"></a>Pasos

1.  Localice el nodo físico en el bastidor.

2.  Compruebe el modelo de disco.

    Compruebe que el nuevo modelo de disco aparece como compatible en [Matriz de compatibilidad](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs#q%3Dsupport%20matrix%26sort%3Ddate%20descending%26f%3Alang%3D%5Ben%5D)
    [del sistema integrado para Microsoft Azure Stack Hub 14G](https://www.dell.com/support/home/product-support/product/cloud-for-microsoft-azure-stack14g/docs#q%3Dsupport%20matrix%26sort%3Ddate%20descending%26f%3Alang%3D%5Ben%5D)*.
    Si el disco no está en la matriz de compatibilidad, debe solicitar otra sustitución.
    
    > [!CAUTION]
    > Al insertar un disco que no está en la matriz de compatibilidad, el disco nuevo se pone en cuarentena.
        
    Para comprobar el modelo, busque la etiqueta y compruebe que el componente está en la matriz de compatibilidad.
    
3.  Reemplace el disco de almacenamiento externo con errores.

    Siga el proceso de sustitución del portador de unidad de disco que se encuentra en el [Manual de instalación y servicio de Dell EMC PowerEdge R640](https://www.dell.com/support/manuals/us/en/04/poweredge-r640/per640_ism_pub/dell-emc-poweredge-r640-overview?guid=guid-f39be9ba-158c-45e3-b8b1-f07bb750d6d4) para nodos de unidad de escalado o hosts de ciclo de vida de hardware.
    
    También debe consultar las secciones correspondientes sobre cómo retirar una unidad de disco e instalar una unidad de disco en un portador de unidad de disco o en un adaptador de unidad de disco.
    
4.  Después de reemplazar el disco, compruebe que el firmware sea compatible de acuerdo con la matriz de compatibilidad. Para recuperar la versión de firmware del disco, haga lo siguiente:

    1.  Inicie sesión en iDRAC con la dirección IP enrutable o directamente mediante el acceso a la interfaz de iDRAC sobre una conexión USB directa.

    1.  En el menú superior, seleccione **Storage** (Almacenamiento):

        ![Captura de pantalla en la que se muestra la página de iDRAC con la acción de almacenamiento seleccionada.](media/image-30.png)
    
    1.  Busque el disco reemplazado y expanda el elemento. Compare el campo **Revision** (Revisión) del disco con la revisión de la matriz de compatibilidad. Si el disco no está en la matriz de compatibilidad, póngase en contacto con el departamento de soporte técnico para corregir el problema.

        ![Captura de pantalla en la que se muestra la página de propiedades avanzadas de iDRAC con las opciones de fabricante, identificador de producto y revisión resaltadas.](media/image-31.png)
        
## <a name="next-steps"></a>Pasos siguientes

Si está trabajando con un nodo de unidad de escalado:

1.  Complete Comprobación del estado del nodo de unidad de escalado.

2.  Complete Comprobación del estado del disco del nodo de unidad de escalado si trabaja con el host de ciclo de vida de hardware:

    -   Complete Comprobación del estado del host de ciclo de vida de hardware.
    
