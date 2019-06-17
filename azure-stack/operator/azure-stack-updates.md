---
title: Introducción a la administración de actualizaciones en Azure Stack | Microsoft Docs
description: Conozca sobre la administración de actualizaciones en sistemas integrados de Azure Stack.
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
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: 5fa84271c02ebc749871116badb3c767812a48ec
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691556"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Introducción a la administración de actualizaciones en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

Los paquetes de actualización de Microsoft para los sistemas integrados de Azure Stack normalmente se publican todos los meses. Pregunte a su fabricante de equipos originales (OEM) cuál es su procedimiento de notificación específico para asegurarse de su organización reciba las notificaciones de actualización. También puede consultar esta biblioteca de documentación en **Información general** > **Notas de la versión** para información sobre las versiones que se encuentran en soporte activo.

Cada lanzamiento de actualizaciones de software de Microsoft software viene como una única actualización. Como operador de Azure Stack, puede importar, instalar y supervisar el progreso de la instalación de las actualizaciones desde el portal de administración de Azure Stack.

Su distribuidor de OEM también liberará actualizaciones, tanto de controladores como de firmware. Si bien el proveedor entrega estas actualizaciones como paquetes independientes, algunas se importan, instalan y administran de la misma manera que las actualizaciones de Microsoft.

Para que el sistema esté cubierto por el soporte técnico, Azure Stack debe mantenerse actualizado hasta un nivel de versión específico. Asegúrese de revisar la [directiva de servicio de Azure Stack](azure-stack-servicing-policy.md).

> [!NOTE]
> No puede aplicar actualizaciones de Azure Stack al Kit de desarrollo de Azure Stack (ASDK). Las actualizaciones están diseñadas para sistemas integrados. Para obtener información, consulte [Volver a implementar el ASDK](../asdk/asdk-redeploy.md).

## <a name="the-update-resource-provider"></a>El proveedor de recursos de actualización

Azure Stack incluye un proveedor de recursos de actualización que controla la aplicación de las actualizaciones de software de Microsoft. Este proveedor comprueba que las actualizaciones se apliquen en todos los hosts físicos, entornos en tiempo de ejecución y aplicaciones de Service Fabric, y en todas las máquinas virtuales de la infraestructura y sus servicios asociados.

Cuando se instalan las actualizaciones, puede ver el estado general a medida que el proceso de actualización alcanza los diversos subsistemas de Azure Stack (por ejemplo, hosts físicos y máquinas virtuales de la infraestructura).

## <a name="plan-for-updates"></a>Planeamiento de las actualizaciones

Se recomienda firmemente notificar a los usuarios cualquier operación de mantenimiento, así como programar ventanas de mantenimiento normal durante el horario no laborable si es posible. Las operaciones de mantenimiento pueden afectar tanto a las cargas de trabajo de inquilino como a las operaciones del portal.

Al planear su ventana de mantenimiento, es importante revisar el tipo específico de actualización que ha publicado Microsoft como se indica en la nota de la versión correspondiente. Aparte de la revisión ocasional, cada actualización tendrá un tipo correspondiente, **Completo** o **Rápido**. Las actualizaciones completas actualizan los sistemas operativos del host físico de la unidad de escalado y requieren una ventana de mantenimiento mayor. Las actualizaciones rápidas están limitadas y no actualizan los sistemas operativos subyacentes del host físico.

Antes de iniciar la instalación de esta actualización, ejecute [Test-AzureStack](azure-stack-diagnostic-test.md) con los parámetros siguientes para validar el estado de Azure Stack y resolver todos los problemas operativos detectados, incluidas todas las advertencias y errores. Repase también las alertas activas y resuelva las que requieran alguna acción.  

```powershell
Test-AzureStack -Group UpdateReadiness
```

## <a name="using-the-update-tile-to-manage-updates"></a>Uso del icono Actualización para administrar las actualizaciones

Puede administrar las actualizaciones desde el portal de administrador. Como operador de Azure Stack puede usar el icono de **actualización** del panel para:

- Ver información importante, como la versión actual.
- Instalar actualizaciones y supervisar el progreso.
- Revisar el historial de actualización de las actualizaciones instaladas anteriormente.
- Ver la versión actual del paquete de OEM de la nube.

## <a name="determine-the-current-version"></a>Determinar la versión actual

Puede ver la versión actual de Azure Stack en el icono de **actualización**. Para abrir el icono:

1. Abra el portal de administración de Azure Stack.
2. Seleccione **Panel**. En el icono **Actualización** aparece la versión actual.

    ![Icono de las actualizaciones en el panel predeterminado](./media/azure-stack-updates/image1.png)

    Por ejemplo, en esta imagen la versión es 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Instalar actualizaciones y supervisar el progreso

1. Abra el portal de administración de Azure Stack.
2. Seleccione **Panel**. Seleccione el icono **Actualizar**.
3. Seleccione **Actualizar ahora**.

    ![Detalles de ejecución de la actualización de Azure Stack](media/azure-stack-updates/azure-stack-update-button.png)

4. Puede ver el estado de alto nivel a medida que el proceso de actualización itera a través de diversos subsistemas en Azure Stack. Los subsistemas de ejemplo incluyen hosts físicos, Service Fabric, máquinas virtuales de infraestructura y servicios que brindar tanto el portal de usuario como el de administrador. Durante el proceso de actualización, el proveedor de recursos de actualización proporciona detalles adicionales acerca de la actualización, como el número de pasos que se han realizado correctamente y los que están en curso.

5. Seleccione **Download full logs** (Descargar registros completos) en la hoja de detalles de la ejecución de actualización para descargar los registros completos.

    ![Detalles de ejecución de la actualización de Azure Stack](media/azure-stack-updates/update-run-details.png)

6. Una vez que se completa, el proveedor de recursos de actualización confirman que la **operación se ha realizado correctamente** no solo para indicarle que el proceso de actualización se ha completado, sino también cuánto ha tardado en finalizar. Desde ahí, con el filtro puede ver información sobre todas las actualizaciones, las actualizaciones disponibles o las actualizaciones instaladas.

    ![Detalles de ejecución de actualización correcta de Azure Stack](media/azure-stack-updates/update-success.png)

   Si ocurre un error al actualizar, el icono de **actualización** mostrará el mensaje **Needs attention** (Requiere atención). Use la opción **Download full logs** (Descargar registros completos) para obtener un estado de alto nivel que indique dónde se ha producido el error. La colección de registros de Azure Stack ayuda a facilitar el diagnóstico y la solución de problemas.

## <a name="next-steps"></a>Pasos siguientes

- [Directiva de servicio de Azure Stack](azure-stack-servicing-policy.md) 
- [Administración de regiones en Azure Stack](azure-stack-region-management.md)
