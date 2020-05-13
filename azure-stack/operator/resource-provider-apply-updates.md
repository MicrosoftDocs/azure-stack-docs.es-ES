---
title: Aplicación de actualizaciones a un proveedor de recursos de Azure Stack Hub
description: Aprenda a aplicar una actualización de servicio a un proveedor de recursos de Azure Stack Hub.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: how-to
ms.date: 11/18/2019
ms.reviewer: jfggdl
ms.lastreviewed: 11/18/2019
ms.openlocfilehash: 5ec6f85fc2f2e526c4b85521337ab65dc2b939cc
ms.sourcegitcommit: 519f4298dc1ed5c33f9c4fef811f61d61731dd84
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/05/2020
ms.locfileid: "82799873"
---
# <a name="how-to-update-an-azure-stack-hub-resource-provider"></a>Actualización de un proveedor de recursos de Azure Stack Hub

> [!IMPORTANT]
> Antes de continuar, revise las notas de la versión más reciente del proveedor de recursos para obtener información sobre las nuevas funciones, correcciones y problemas conocidos que podrían afectar a la implementación. Las notas de la versión también pueden especificar la versión mínima de Azure Stack Hub requerida para el proveedor de recursos.

Los proveedores de recursos instalados desde Marketplace requerirán mantenimiento periódico. El mantenimiento se realiza mediante la aplicación de actualizaciones de servicio, que proporciona Microsoft de forma periódica. Las actualizaciones pueden incluir nuevas características y correcciones.  

## <a name="check-for-updates"></a>Buscar actualizaciones

Los proveedores de recursos se actualizan con la misma característica de actualización que se usa para aplicar las actualizaciones de Azure Stack Hub.

1. Inicie sesión en el Portal de administración de Azure Stack Hub.
2. Seleccione el vínculo **Todos los servicios** de la izquierda y, en la sección, **Administración**, seleccione **Actualizaciones**.
   ![Página Todos los servicios](media/resource-provider-apply-updates/1-all-services.png)

3. En la sección **Proveedor de recursos** de la página **Actualizaciones** encontrará actualizaciones para los proveedores de recursos.

   [![Página Actualización disponible](media/resource-provider-apply-updates/3-update-available.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

## <a name="apply-an-update"></a>Aplicación de una actualización

Si hay una actualización disponible para un proveedor de recursos determinado:

1. Seleccione la fila del proveedor de recursos que quiere actualizar. Observe que el vínculo **Descargar** de la parte superior de la página se habilita.
   [![Página Actualización disponible](media/resource-provider-apply-updates/4-download.png)](media/resource-provider-apply-updates/3-update-available.png#lightbox)

2. Haga clic en el vínculo **Descargar** para comenzar la descarga del paquete de instalación del proveedor de recursos. Observe que la columna **Estado** de la fila del proveedor de recursos cambia de "Disponible" a "Descargando".
3. Cuando el valor de **Estado** cambie a "Listo para instalar", la descarga se ha completado. Observe que el vínculo **Instalar ahora** de la parte superior de la página también se habilita.
4. Seleccione el vínculo **Instalar ahora** y se le dirigirá a la página **Instalar** del proveedor de recursos. 
5. Seleccione el botón **Instalar** para iniciar la instalación.
6. Se mostrará una notificación de "instalación en curso" en la esquina superior derecha y volverá a la página **Actualizaciones**. La columna **Estado** de la fila del proveedor de recursos también cambia a "Instalando".
7. Cuando finalice la instalación, otra notificación indicará si se ha realizado correctamente o no. Una instalación correcta también actualizará el valor de **Versión** de la página **Administración de Marketplace - Proveedores de recursos**.

## <a name="next-steps"></a>Pasos siguientes

Más información sobre la [característica de actualizaciones del panel de administrador](azure-stack-apply-updates.md).
