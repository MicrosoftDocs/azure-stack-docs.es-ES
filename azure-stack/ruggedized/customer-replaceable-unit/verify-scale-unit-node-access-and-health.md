---
title: Comprobación del acceso y estado de los nodo de la unidad de escalado
description: Obtenga información sobre cómo comprobar el acceso y el estado del nodo de la unidad de escalado.
author: PatAltimore
ms.topic: how-to
ms.date: 11/13/2020
ms.author: patricka
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: 74addf295c35099e90e3a7fe4fd95aad34e47361
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910642"
---
# <a name="verifying-scale-unit-node-access-and-health"></a>Comprobación del acceso y el estado del nodo de la unidad de escalado



Inicie sesión en la estación de trabajo de acceso con privilegios, inicie el portal de administración, compruebe el estado del sistema, obtenga las direcciones IP del punto de conexión con privilegios e identifique los nodos que se deben purgar o reanudar.

1.  Mediante Escritorio remoto, conéctese a la estación de trabajo de acceso con privilegios.

2.  Acceda al portal del administrador de Azure Stack Hub.

    Inicie sesión en el portal del administrador de Azure Stack Hub con las credenciales obtenidas del cliente.
        
3.  Obtenga las direcciones IP del punto de conexión con privilegios.


    Seleccione el icono **Region Management** (Administración de regiones) y seleccione **Properties** (Propiedades). Desplácese hasta la parte inferior del panel y busque las direcciones IP en el campo **Privileged endpoint IP addresses** (Direcciones IP del punto de conexión con privilegios). Tome nota de ellas, ya que puede que se necesiten más adelante en este procedimiento o que el departamento de soporte técnico las necesite en caso de que surja algún problema.

    [![Captura de pantalla en la que se muestra la página de administración con la sección de direcciones IP del punto de conexión con privilegios resaltada.](media/image-18-inline.png)](media/image-18-expanded.png)
    
4.  Revise las alertas actuales.

    En **Region Management** (Administración de regiones), seleccione **Alerts** (Alertas) y revise las alertas actuales. Si hay alguna alerta inesperada, compruebe con el departamento de soporte técnico de Dell Technologies que se puedan borrar u omitir de forma segura.
    
    [ ![Captura de pantalla en la que se muestra la página de propiedades con la sección de nombre resaltada.](media/image-19-inline.png)](media/image-19-expanded.png)]
    
5.  Identifique los nodos de unidad de escalado.

    Si solo se le ha proporcionado la etiqueta de servicio y no puede identificar qué nodo tiene el problema desde el portal del administrador de Azure Stack Hub (es decir, el estado de energía del nodo ya es Detenido), siga estos pasos para poner en correlación el nodo de la unidad de escalado y la etiqueta de servicio:
    
    1.  En **Region Management** (Administración de regiones), seleccione **Scale Units** (Unidades de escalado) y, a continuación, seleccione el clúster **s-cluster**. Seleccione **Nodos**.
    
    1.  Para obtener la etiqueta de servicio del nodo, seleccione el vínculo de dirección IP **BMC**, que abre la interfaz web de iDRAC del servidor en una nueva pestaña o ventana.

        [ ![Captura de pantalla en la que se muestra la página de nodos con la columna BMC resaltada.](media/image-20-inline.png)](media/image-20-expanded.png) 
    
    1.  Inicie sesión en la interfaz de iDRAC y compruebe la etiqueta de servicio del nodo en el panel **System Information** (Información del sistema).
    
    1.  Repita este procedimiento para cada nodo y correlacione estas etiquetas de servicio con la sustitución de hardware programada para determinar qué nodo o nodos se deben atender.

        [![Captura de pantalla en la que se muestra el panel con la etiqueta de servicio resaltada.](media/image-21-inline.png)](media/image-21-expanded.png)
    
