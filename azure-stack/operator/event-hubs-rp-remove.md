---
title: Cómo quitar Event Hubs en Azure Stack Hub
description: Aprenda a quitar el proveedor de recursos de Event Hubs en Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 12/09/2019
ms.reviewer: jfggdl
ms.lastreviewed: 12/09/2019
ms.openlocfilehash: 2a1525f4b91998479d368714aa3a88df6ecfcef9
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "80424599"
---
# <a name="how-to-remove-event-hubs-on-azure-stack-hub"></a>Cómo quitar Event Hubs en Azure Stack Hub

[!INCLUDE [preview-banner](../includes/event-hubs-preview.md)]

> [!WARNING]
> Al desinstalar Event Hubs, se quita (borra) el proveedor de recursos y todos los clústeres de Event Hubs, espacios de nombres y recursos de Event Hubs creados por el usuario. También se quitarán los datos de eventos asociados.  
> Tenga mucho cuidado antes de decidir desinstalar Event Hubs. La desinstalación de Event Hubs **no** elimina los paquetes usados para instalarlo. Si desea eliminarlos, consulte [Eliminación de paquetes de Event Hubs](#delete-event-hubs-packages).

## <a name="uninstall-event-hubs"></a>Desinstalación de Event Hubs

Esta secuencia de pasos quitará todos los recursos de Event Hubs, incluidos los clústeres, los espacios de nombres, los centros de eventos y el proveedor de recursos.

Para quitar Event Hubs y todos los recursos relacionados creados por los usuarios, complete los pasos siguientes:

1. Inicie sesión en el Portal de administración de Azure Stack Hub.
2. Seleccione **Marketplace Management** (Administración de Marketplace) a la izquierda.
3. Seleccione **Proveedores de recursos**.
4. Seleccione **Event Hubs** en la lista de proveedores de recursos. Es posible que desee filtrar la lista escribiendo "Event Hubs" en el cuadro de texto de búsqueda proporcionado.

   [![Eliminación de Event Hubs 1](media/event-hubs-rp-remove/1-uninstall.png)](media/event-hubs-rp-remove/1-uninstall.png#lightbox)

5. Seleccione **Uninstall** (Desinstalar) en las opciones que aparecen en la parte superior de la página.

   [![Eliminación de Event Hubs 2](media/event-hubs-rp-remove/2-uninstall.png)](media/event-hubs-rp-remove/2-uninstall.png#lightbox)

6. Escriba el nombre del proveedor de recursos y, a continuación, seleccione **Uninstall** (Desinstalar). Esta acción confirma que desea desinstalar:
   - El proveedor de recursos de Event Hubs
   - Todos los clústeres, espacios de nombres, centros de eventos y datos de eventos creados por el usuario.

   [![Eliminación de Event Hubs 3](media/event-hubs-rp-remove/3-uninstall.png)](media/event-hubs-rp-remove/3-uninstall.png#lightbox)

   [![Eliminación de Event Hubs 4](media/event-hubs-rp-remove/4-uninstall.png)](media/event-hubs-rp-remove/4-uninstall.png#lightbox)

   > [!IMPORTANT]
   > Debe esperar al menos 10 minutos después de que se haya quitado correctamente Event Hubs antes de instalarlo de nuevo. Esto se debe a que las actividades de limpieza todavía pueden estar en ejecución, lo que puede entrar en conflicto con cualquier instalación nueva.

## <a name="delete-event-hubs-packages"></a>Eliminación de paquetes de Event Hubs

Utilice esta opción si, después de desinstalar Event Hubs, también desea quitar todos los paquetes usados para instalarlo. 

## <a name="next-steps"></a>Pasos siguientes

Para volver a instalar, vuelva al artículo [Instalación del proveedor de recursos de Event Hubs](event-hubs-rp-install.md).