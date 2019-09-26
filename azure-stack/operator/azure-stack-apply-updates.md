---
title: Instalación de actualizaciones de Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo instalar actualizaciones de Azure Stack.
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
ms.date: 09/10/2019
ms.author: mabriggs
ms.lastreviewed: 09/10/2019
ms.reviewer: ppacent
ms.openlocfilehash: 3d760e981f3dd298284b10ed56c4c6ace683f49b
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974790"
---
# <a name="install-azure-stack-updates"></a>Instalación de actualizaciones de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

Puede instalar las actualizaciones con la hoja **Update** (Actualizar) en Azure Stack. Este artículo le guía por los pasos necesarios para actualizar, supervisar y solucionar problemas del proceso de actualización. Use la hoja Update (Actualizar) para ver la información de actualización, instalar actualizaciones, supervisar el progreso de las actualizaciones, revisar el historial de actualizaciones y ver la versión actual de Azure Stack y del paquete de OEM.

Puede administrar las actualizaciones desde el portal del administrador y usar la sección **Actualizaciones** del panel para:

- Ver información importante, como la versión actual.
- Instalar actualizaciones y supervisar el progreso.
- Revisar el historial de actualización de las actualizaciones instaladas anteriormente.
- Ver la versión actual del paquete de OEM de la nube.

## <a name="determine-the-current-version"></a>Determinar la versión actual

Puede ver la versión actual de Azure Stack en la hoja de **Actualización**. Para abrirla:

1.  Abra el portal de administración de Azure Stack.

2.  Seleccione **Panel**. En la hoja **Actualización**, se muestra la versión actual.

    ![Icono de las actualizaciones en el panel predeterminado](./media/azure-stack-update-apply/image1.png)

    Por ejemplo, en esta imagen la versión es 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Instalar actualizaciones y supervisar el progreso

1. Abra el portal de administración de Azure Stack.

2. Seleccione **Panel**. Seleccione **Actualizar**.

3. Seleccione la actualización disponible que quiere instalar. Si no tiene ninguna actualización marcada como **Disponible**, tendrá que [preparar la actualización](azure-stack-update-prepare-package.md).

4. Seleccione **Actualizar ahora**.

    ![Detalles de ejecución de la actualización de Azure Stack](./media/azure-stack-update-apply/image2.png)

5. Puede ver el estado de alto nivel a medida que el proceso de actualización itera a través de diversos subsistemas en Azure Stack. Los subsistemas de ejemplo incluyen hosts físicos, Service Fabric, máquinas virtuales de infraestructura y servicios que brindan tanto el portal de usuario como el de administrador. Durante el proceso de actualización, el proveedor de recursos de actualización proporciona detalles adicionales acerca de la actualización, como el número de pasos que se han realizado correctamente y los que están en curso.

6. Seleccione **Descargar resumen** en la hoja de detalles de la ejecución de actualización para descargar los registros completos.

    Si surge un problema durante la supervisión de la actualización, puede usar el [punto de conexión con privilegios](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint) para supervisar el progreso de la ejecución de una actualización de Azure Stack. Si el portal de Azure Stack deja de estar disponible, también puede usar el punto de conexión con privilegios para reanudar la ejecución de una actualización con errores desde el último paso correcto. Para obtener instrucciones, consulte [Supervisar actualizaciones en Azure Stack con PowerShell](azure-stack-update-monitor.md).

    ![Detalles de ejecución de la actualización de Azure Stack](./media/azure-stack-update-apply/image3.png)

7. Una vez completada la actualización, el proveedor de recursos de actualización proporciona una confirmación de operación **correcta** para mostrar que el proceso de actualización ha finalizado e indicar el tiempo que tardó. Desde ahí, con el filtro puede ver información sobre todas las actualizaciones, las actualizaciones disponibles o las actualizaciones instaladas.

    ![azure-stack-update-apply](./media/azure-stack-update-apply/image4.png)

    Si ocurre un error al actualizar, la hoja **Actualización** mostrará el mensaje **Requiere atención**. Use la opción **Download full logs** (Descargar registros completos) para obtener un estado de alto nivel que indique dónde se ha producido el error. La colección de registros de Azure Stack ayuda a facilitar el diagnóstico y la solución de problemas.

## <a name="review-update-history"></a>Revisión del historial de actualizaciones

1. Abra el portal de administración.

2. Seleccione **Panel**. Seleccione **Actualizar**.

3. Seleccione **Historial de actualizaciones**.

    ![Historial de actualizaciones de Azure Stack](./media/azure-stack-update-apply/image7.png)

## <a name="next-steps"></a>Pasos siguientes

-   [Introducción a la administración de actualizaciones en Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)  
-   [Directiva de servicio de Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy)  
