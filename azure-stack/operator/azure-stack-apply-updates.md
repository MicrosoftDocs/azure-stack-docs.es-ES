---
title: Aplicación de actualizaciones en Azure Stack | Microsoft Docs
description: Aprenda a importar e instalar paquetes de actualización de Microsoft para un sistema integrado de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: justini
ms.lastreviewed: 02/11/2019
ms.openlocfilehash: 147e6b7555d67b562b44102bfd022e954a87e0ae
ms.sourcegitcommit: 2b6a0b3b4dc63c26df3d0535d630d640ff232fb0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/10/2019
ms.locfileid: "65521222"
---
# <a name="apply-updates-in-azure-stack"></a>Aplicación de actualizaciones en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

Puede utilizar el icono **Actualizar** del portal de administración para aplicar paquetes de actualización de OEM o Microsoft a Azure Stack.

Si usa la versión 1807, o una versión anterior, de los sistemas integrados, debe descargar el paquete de actualización, importar los archivos del paquete en Azure Stack e instalar el paquete de actualización. Para obtener instrucciones, consulte [Actualización de Azure Stack mediante la descarga del paquete](#update-azure-stack-by-downloading-the-package).

Estas instrucciones de actualización funcionan con sistemas integrados de Azure Stack. Si usa el sistema de desarrollo de Azure Stack, debe descargar el paquete de instalación para la versión actual. Para obtener instrucciones, consulte [Instalación del Kit de desarrollo de Azure Stack](../asdk/asdk-install.md).

## <a name="update-azure-stack"></a>Actualización de Azure Stack

### <a name="select-and-apply-an-update-package"></a>Selección y aplicación de un paquete de actualizaciones

1. Abra el portal de administración.

2. Seleccione **Panel**. Seleccione el icono **Actualizar**.

    ![Actualización disponible de Azure Stack](media/azure-stack-apply-updates/azure-stack-updates-1901-dashboard.png)

3. Tome nota de la versión actual de Azure Stack. Puede actualizar a la siguiente versión completa. Por ejemplo, si ejecuta Azure Stack 1811, la próxima versión es 1901.

    ![Aplicación de la actualización de Azure Stack](media/azure-stack-apply-updates/azure-stack-updates-1901-updateavailable.png)

4. Seleccione la siguiente versión disponible en la lista de actualizaciones. Puede seleccionar **Vista** en la columna de notas de la versión para abrir el tema de notas de la versión si quiere revisar los cambios.

5. Seleccione Actualizar ahora. Se iniciará la actualización.

### <a name="review-update-history"></a>Revisión del historial de actualizaciones

1. Abra el portal de administración.

2. Seleccione **Panel**. Seleccione el icono **Actualizar**.

3. Seleccione **Historial de actualizaciones**.

![Historial de actualizaciones de Azure Stack](media/azure-stack-apply-updates/azure-stack-update-history.PNG)

## <a name="update-azure-stack-by-downloading-the-package"></a>Actualización de Azure Stack mediante la descarga del paquete

Si usa la versión 1807, o una versión anterior, de los sistemas integrados, debe descargar el paquete de actualización, importar los archivos del paquete en Azure Stack e instalar el paquete de actualización.

## <a name="download-the-update-package"></a>Descarga del paquete de actualización

Cuando haya disponible un paquete de actualización de OEM o Microsoft para Azure Stack, descárguelo a una ubicación a la que se pueda acceder desde Azure Stack y revise el contenido del paquete. Normalmente, un paquete de actualización consta de los siguientes archivos:

- Un archivo `<PackageName>.zip` autoextraíble. Este archivo contiene la carga útil de la actualización, por ejemplo la actualización acumulativa más reciente de Windows Server.

- Los archivos `<PackageName>.bin` que correspondan. Estos archivos proporcionan compresión para la carga útil que está asociada al archivo *PackageName*.zip.

- Un archivo `Metadata.xml`. Este archivo contiene información esencial acerca de la actualización, como el editor, nombre, requisito previo, tamaño y dirección URL de ruta de acceso de soporte.

> [!IMPORTANT]  
> Después de aplicar el paquete de actualización de Azure Stack 1901, el formato de empaquetado de los paquetes de actualización de Azure Stack cambiará de .zip, .bin(s) y .xml a .zip(s) y .xml. Los operadores de Azure Stack que tengan marcas conectadas no se verán afectados. Los operadores de Azure Stack que están desconectados no tienen más que importar los archivos .xml y .zip utilizando el mismo proceso que se describe a continuación.

## <a name="import-and-install-updates"></a>Importación e instalación de actualizaciones

El siguiente procedimiento muestra cómo importar e instalar actualizaciones en el portal de administración.

> [!IMPORTANT]  
> Se recomienda firmemente notificar a los usuarios cualquier operación de mantenimiento, así como programar ventanas de mantenimiento normal durante el horario no laborable tanto como sea posible. Las operaciones de mantenimiento pueden afectar tanto a las cargas de trabajo del usuario como a las operaciones del portal.

1. En el portal de administración, haga clic en **Todos los servicios**. Luego, en la categoría **DATOS Y ALMACENAMIENTO**, haga clic en **Cuentas de almacenamiento**. (o bien, en el cuadro de filtro, empiece a escribir **storage accounts** y selecciónelo).

    ![Muestra dónde se encuentran las cuentas de almacenamiento en el portal](media/azure-stack-apply-updates/ApplyUpdates1.png)

2. En el cuadro de filtro, escriba **update**y seleccione la cuenta de almacenamiento **updateadminaccount**.

3. En los detalles de la cuenta de almacenamiento, en **Servicios**, seleccione **Blobs**.
 
    ![Muestra como acceder a Blobs para la cuenta de almacenamiento](media/azure-stack-apply-updates/ApplyUpdates3.png) 

4. En **Blob service**, seleccione **+ Contenedor** para crear un contenedor. Escriba un nombre (por ejemplo *update-1811*) y, luego, seleccione **Aceptar**.
 
     ![Muestra cómo agregar un contenedor en la cuenta de almacenamiento](media/azure-stack-apply-updates/ApplyUpdates4.png)

5. Tras crear el contenedor, haga clic en su nombre y, después, haga clic en **Cargar** para cargar los archivos de paquete en el contenedor.
 
    ![Muestra cómo cargar los archivos de paquete](media/azure-stack-apply-updates/ApplyUpdates5.png)

6. En **Cargar blob**, haga clic en el icono de la carpeta, vaya al archivo .zip del paquete de actualización y haga clic en **Abrir** en la ventana del explorador de archivos.
  
7. En **Cargar blob**, haga clic en **Cargar**.
  
    ![Muestra dónde se carga cada archivo de paquete](media/azure-stack-apply-updates/ApplyUpdates6.png)

8. Repita los pasos 6 y 7 para los archivos *PackageName*.bin y Metadata.xml. No importe el archivo Supplemental Notice.txt si está incluido. Tenga en cuenta que los archivos tendrán el formato .zip a partir de la versión 1901 en contraposición a .bin y .zip. Siga importando los archivos .xml como de costumbre.

9. Cuando haya finalizado, puede revisar las notificaciones (el icono de campana de la esquina superior derecha del portal). Las notificaciones deben indicar que la carga se ha completado.
10. Vuelva al icono de actualización del panel. El icono debe indicar que hay una actualización disponible. Haga clic en el icono para revisar el paquete de actualización recién agregado.
11. Para instalar la actualización, seleccione el paquete que está marcado como **Listo** y haga clic con el botón derecho en él y seleccione **Actualizar ahora**, o bien haga clic en la acción **Actualizar ahora** cerca de la parte superior.
12. Al hacer clic en el paquete de actualización de instalación, puede ver el estado en el área **Update run details** (Detalles de ejecución de actualización). Aquí también puede hacer clic en **Download full logs** (Descargar registros completos) para descargar los archivos de registro.
13. Cuando la actualización finaliza, el icono de actualización muestra la versión actualizada de Azure Stack.

Puede eliminar manualmente las actualizaciones de la cuenta de almacenamiento después de haberlas instalado en Azure Stack. Azure Stack busca periódicamente los paquetes de actualización más antiguos y los elimina del almacenamiento. Azure Stack puede tardar dos semanas en quitar los paquetes antiguos.

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md)
- [Directiva de servicio de Azure Stack](azure-stack-servicing-policy.md)
