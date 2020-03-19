---
title: Instalación de actualizaciones de Azure Stack Hub
description: Aprenda a instalar actualizaciones de Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.lastreviewed: 09/10/2019
ms.reviewer: ppace
ms.openlocfilehash: 461bad387dd5616eba227df4bd9b6e8beee40e43
ms.sourcegitcommit: 20d10ace7844170ccf7570db52e30f0424f20164
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/13/2020
ms.locfileid: "79295104"
---
# <a name="install-azure-stack-hub-updates"></a>Instalación de actualizaciones de Azure Stack Hub

Puede instalar paquetes de actualizaciones con la hoja **Update** (Actualizar) de Azure Stack Hub. Este artículo le guía por los pasos necesarios para actualizar, supervisar y solucionar problemas del proceso de actualización. Use la hoja Update (Actualizar) para ver información sobre la actualización, instalar actualizaciones, supervisar el progreso de las actualizaciones, revisar el historial de actualizaciones y ver la versión actual de Azure Stack Hub y del paquete de OEM.

Puede administrar las actualizaciones desde el portal del administrador y usar la sección **Actualizaciones** del panel para:

- Ver información importante, como la versión actual.
- Instalar actualizaciones y supervisar el progreso.
- Revisar el historial de actualización de las actualizaciones instaladas anteriormente.
- Ver la versión actual del paquete de OEM de la nube.

## <a name="determine-the-current-version"></a>Determinar la versión actual

Puede ver la versión actual de Azure Stack Hub en la hoja **Updates** (Actualizaciones). Para abrirla:

1.  Abra el portal del administrador de Azure Stack Hub.

2.  Seleccione **Panel**. En la hoja **Actualización**, se muestra la versión actual.

    ![Icono de las actualizaciones en el panel predeterminado](./media/azure-stack-update-apply/image1.png)

    Por ejemplo, en esta imagen la versión es 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Instalar actualizaciones y supervisar el progreso

> [!Important]
> Antes de aplicar las actualizaciones de Azure Stack Hub, asegúrese de que ha completado **TODOS** los pasos de la [lista de comprobación previa a la actualización](release-notes-checklist.md) y ha programado un periodo de mantenimiento apropiado para el tipo de actualización que está aplicando.

1. Abra el portal del administrador de Azure Stack Hub.

2. Seleccione **Panel**. Seleccione **Actualizar**.

3. Seleccione la actualización disponible que quiere instalar. Si no tiene ninguna actualización marcada como **Disponible**, tendrá que [preparar la actualización](azure-stack-update-prepare-package.md).

4. Seleccione **Actualizar ahora**.

    ![Detalles de ejecución de la actualización de Azure Stack Hub](./media/azure-stack-update-apply/image2.png)

5. Puede ver el estado general del proceso de actualización a medida que este recorre los diferentes subsistemas de Azure Stack Hub. Los subsistemas de ejemplo incluyen hosts físicos, Service Fabric, máquinas virtuales de infraestructura y servicios que brindan tanto el portal de usuario como el de administrador. Durante el proceso de actualización, el proveedor de recursos de actualización proporciona detalles adicionales acerca de la actualización, como el número de pasos que se han realizado correctamente y los que están en curso.

6. Seleccione **Descargar resumen** en la hoja de detalles de la ejecución de actualización para descargar los registros completos.

    Si surge algún problema durante la supervisión de la actualización, puede usar el [punto de conexión con privilegios](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint) para supervisar el progreso de la ejecución de una actualización de Azure Stack Hub. Si el portal de Azure Stack Hub deja de estar disponible, también puede usar el punto de conexión con privilegios para reanudar la ejecución de una actualización con errores desde el último paso correcto. Para obtener instrucciones, consulte [Supervisar actualizaciones en Azure Stack Hub con PowerShell](azure-stack-update-monitor.md).

    ![Detalles de ejecución de la actualización de Azure Stack Hub](./media/azure-stack-update-apply/image3.png)

7. Una vez completada la actualización, el proveedor de recursos de actualización proporciona una confirmación de operación **correcta** para mostrar que el proceso de actualización ha finalizado e indicar el tiempo que tardó. Desde ahí, con el filtro puede ver información sobre todas las actualizaciones, las actualizaciones disponibles o las actualizaciones instaladas.

    ![azure-stack-update-apply](./media/azure-stack-update-apply/image4.png)

    Si ocurre un error al actualizar, la hoja **Actualización** mostrará el mensaje **Requiere atención**. Use la opción **Download full logs** (Descargar registros completos) para obtener un estado de alto nivel que indique dónde se ha producido el error. La colección de registros de Azure Stack Hub facilita el diagnóstico y la solución de problemas.

## <a name="review-update-history"></a>Revisión del historial de actualizaciones

1. Abra el portal de administración.

2. Seleccione **Panel**. Seleccione **Actualizar**.

3. Seleccione **Historial de actualizaciones**.

    ![Historial de actualizaciones de Azure Stack Hub](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>Pasos siguientes

-   [Introducción a la administración de actualizaciones en Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)  
-   [Directiva de mantenimiento de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy)  
