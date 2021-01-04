---
title: Comprobación del acceso y estado del host de ciclo de vida de hardware
description: Obtenga información sobre cómo comprobar el acceso y el estado del host de ciclo de vida de hardware.
author: myoungerman
ms.topic: how-to
ms.date: 11/13/2020
ms.author: v-myoung
ms.reviewer: ''
ms.lastreviewed: ''
ms.openlocfilehash: b14bff53b8c8d5545a00424a543656a190c06bf3
ms.sourcegitcommit: 3bd42be22e626564b62e560dc037aed4d462011f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/14/2020
ms.locfileid: "97393163"
---
# <a name="verifying-hardware-lifecycle-host-access-and-health"></a>Comprobación del acceso y el estado del host de ciclo de vida de hardware

Inicie sesión en iDRAC y en el sistema operativo del host de ciclo de vida de hardware (HLH) y compruebe el estado del sistema.

1.  Conéctese a iDRAC.

    1.  Con un explorador web, vaya a la interfaz web de iDRAC e inicie sesión con las credenciales proporcionadas por el cliente.

        ![](media/image-3.png) 
    
    1.  En el menú de navegación superior, seleccione **System** (Sistema).

        ![](media/image-4.png)
        
    1.  En la pestaña **Overview** (Información general), compruebe si el sistema está completamente en estado correcto o si muestra el problema esperado que debe corregirse durante esta sustitución de hardware.
    
        ![](media/image-5.png)
    
2.  Conéctese al sistema operativo del HLH mediante la consola virtual de iDRAC.

    > [!NOTE]
    > Puede omitir este paso si inicia sesión con un carro de bloqueos con conexiones VGA y USB.
    
    1.  En la interfaz web de iDRAC, seleccione **Dashboard** (Panel).

        ![](media/image-6.png)
    
    1.  En el panel **Virtual Console** (Consola virtual), seleccione **Settings** (Configuración).
    
        ![](media/image-7.png)
        
    1.  Compruebe que **Plug-in Type** (Tipo de complemento) está establecido en **HTML 5**. Si no es así, cambie esto, seleccione **Apply** (Aplicar) y, a continuación, seleccione **OK** (Aceptar) cuando se le solicite.
    
        ![](media/image-8.png)
        
    1.  Seleccione **Launch Virtual Console** (Iniciar consola virtual).

        ![](media/image-9.png)
    
    1.  Si se muestra una advertencia emergente, cambie la configuración del explorador a Permitir siempre. Por ejemplo, en Internet Explorer, seleccione **Opciones para este sitio** y seleccione **Permitir siempre**. Si es necesario, después de cambiar la configuración del explorador, repita el paso anterior para iniciar la consola virtual.
    
        ![](media/image-10.png)
        
    1.  La consola virtual debe estar presente ahora. Para iniciar sesión en el sistema operativo, seleccione **Console Controls** (Controles de consola) en el menú de la parte superior.
    
        ![](media/image-11.png)
        
    1.  En **Keyboard Macros** (Macros de teclado), seleccione **Ctrl+Alt-Del** (Ctrl+Alt-Supr) y seleccione **Apply** (Aplicar) y **Close** (Cerrar).
    
        ![](media/image-12.png)
        
    1.  Seleccione el **usuario** según las credenciales proporcionadas por el cliente, escriba la contraseña y seleccione la **flecha** para iniciar sesión.
    
        ![](media/image-13.png)
        
        Ahora ha iniciado sesión en el HLH.
        
3.  Compruebe el estado.

    1.  Inicie **Administrador del servidor**.

        ![](media/image-14.png)
        
    1.  Seleccione **Sí** en el mensaje de **Control de cuentas de usuario**.
    
        ![](media/image-15.png)
        
    1.  En el menú **Herramientas**, seleccione **Hyper-V Manager**.
    
        ![](media/image-16.png)
        
    1.  En **Hyper-V Manager**, seleccione el nodo superior en el menú de la izquierda y, a continuación, compruebe que las máquinas virtuales, como la **estación de trabajo de acceso con privilegios**, si procede, se encuentran en estado **Running** (En ejecución).