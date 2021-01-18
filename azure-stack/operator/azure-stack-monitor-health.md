---
title: Supervisión de estado y alertas en Azure Stack Hub
description: Aprenda cómo supervisar el estado y las alertas en Azure Stack Hub.
author: PatAltimore
ms.topic: how-to
ms.date: 03/04/2020
ms.author: patricka
ms.lastreviewed: 01/18/2019
ms.openlocfilehash: 00357a7b16ee1ec6b158ea881d9f4a2c77d69e72
ms.sourcegitcommit: a90b146769279ffbdb09c68ca0506875a867e177
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/12/2021
ms.locfileid: "98123752"
---
# <a name="monitor-health-and-alerts-in-azure-stack-hub"></a>Supervisión de estado y alertas en Azure Stack Hub

Azure Stack Hub incluye funciones de supervisión de infraestructura que le ayudan a ver el estado y las alertas de una región de Azure Stack Hub. En el icono **Administración de regiones** se muestran todas las regiones implementadas de Azure Stack Hub. Está anclado de forma predeterminada en el portal de administración para la suscripción de proveedor predeterminada. El icono muestra el número de alertas activas críticas y de advertencia para cada región. El icono es el punto de entrada a la funcionalidad de estado y alerta de Azure Stack Hub.

![Icono de administración de regiones en el portal del administrador de Azure Stack Hub](media/azure-stack-monitor-health/image1.png)

## <a name="understand-health-in-azure-stack-hub"></a>Comprender el estado en Azure Stack Hub

El proveedor de recursos de estado administra el estado y las alertas. Los componentes de infraestructura de Azure Stack Hub se registran en el proveedor de recursos de estado durante la implementación y configuración de Azure Stack Hub. Este registro permite mostrar el estado y las alertas para cada componente. El estado en Azure Stack Hub es un concepto simple. Si existen alertas para una instancia registrada de un componente, el estado de mantenimiento de dicho componente refleja la gravedad de la peor alerta activa: advertencia o crítica.

## <a name="alert-severity-definition"></a>Definición de la gravedad de las alertas

En Azure Stack Hub se generan alertas con solo dos grados de gravedad: **advertencia** y **crítica**.

- **Warning (ADVERTENCIA)**  
  Un operador puede abordar la alerta de advertencia de manera programada. Esta alerta generalmente no afecta a las cargas de trabajo del usuario.

- **Critical)** (Crítico)  
  Un operador debe abordar la alerta crítica con urgencia. Estas alertas indican problemas que actualmente afectan o que pronto afectarán a los usuarios de Azure Stack Hub.


## <a name="view-and-manage-component-health-state"></a>Ver y administrar el estado de mantenimiento de un componente

Puede ver el estado de mantenimiento de los componentes en el portal de administración y con la API REST y PowerShell.

Para ver el estado de mantenimiento en el portal, haga clic en la región que quiera ver en la ventana **Region management** (Administración de regiones). Puede ver el estado de mantenimiento de los roles de infraestructura y de los proveedores de recursos.

![Lista de roles de infraestructura](media/azure-stack-monitor-health/image2.png)

Puede hacer clic en un proveedor de recursos o rol de infraestructura para ver información más detallada.

> [!WARNING]  
> Si hace clic en un rol de infraestructura y, luego, hace clic en la instancia de rol, hay opciones para **Iniciar**, **Reiniciar** o **Apagar**. No use estas acciones al aplicar actualizaciones a un sistema integrado. **Tampoco** use estas opciones en un entorno del Kit de desarrollo de Azure Stack (ASDK). Estas opciones están diseñadas únicamente para un entorno de sistemas integrados, donde hay más de una instancia de rol por rol de infraestructura. Reiniciar una instancia de rol (especialmente AzS-Xrp01) en el ASDK provoca inestabilidad del sistema. Para obtener asistencia para solucionar problemas, publique su problema en el [foro de Azure Stack Hub](https://aka.ms/azurestackforum).
>

## <a name="view-alerts"></a>Visualización de alertas

La lista de alertas activas para cada región de Azure Stack Hub está disponible directamente desde la hoja **Administración de regiones**. El primer icono en la configuración predeterminada es **Alertas**, que muestra un resumen de las alertas críticas y de advertencia para la región. Puede anclar la ventana de alertas, al igual que cualquier otra ventana en esta hoja, al panel para un acceso rápido.

![Icono Alertas que muestra una advertencia en el portal del administrador de Azure Stack Hub](media/azure-stack-monitor-health/image3.png)

 Para ver una lista de todas las alertas activas de la región, seleccione la parte superior del icono **Alertas**. Para ver una lista filtrada de alertas (Crítica o Advertencia), seleccione elemento de línea **Crítica** o **Advertencia** en el icono.

La hoja **Alertas** admite la capacidad de filtrar tanto por estado (Activa o Cerrada) como por gravedad (Crítica o Advertencia). La vista predeterminada muestra todas las alertas activas. Todas las alertas cerradas se quitan del sistema después de siete días.

>[!Note]
>Si una alerta permanece activa pero no se ha actualizado en más de un día, puede ejecutar [Test-AzureStack](azure-stack-diagnostic-test.md) y cerrar la alerta si no se informa de ningún problema.

![Panel de filtros para filtrar por el estado Crítica o Advertencia en el portal del administrador de Azure Stack Hub](media/azure-stack-monitor-health/alert-view.png)

En la acción **View API** (Ver API) se muestra la API de REST que se usó para generar la vista de lista. Esta acción proporciona una forma rápida de familiarizarse con la sintaxis de la API de REST que puede usar para consultar las alertas. Puede utilizar esta API para automatización o para integración con las soluciones existentes de supervisión, informe y vales del centro de datos.

Puede hacer clic en una alerta específica para ver los detalles de la alerta. En los detalles de alerta se muestran todos los campos que están asociados a la alerta y se permite la navegación rápida al componente afectado y al origen de la alerta. Por ejemplo, la alerta siguiente se produce si una de las instancias de rol de infraestructura se queda sin conexión o no es accesible.  

![Hoja de detalles de alerta en el portal del administrador de Azure Stack Hub](media/azure-stack-monitor-health/alert-detail.png)

## <a name="alert-remediation"></a>Corrección de alertas

### <a name="automated-remediation"></a>Corrección automatizada

Algunas alertas admiten una opción de **reparación**, tal como se muestra en la imagen anterior. Cuando se selecciona, la acción **Reparar** realiza pasos específicos para la alerta a fin de intentar resolver el problema. Una vez seleccionada, el estado de la acción **Reparar** está disponible como una notificación del portal.

![Acción de reparación de alerta en curso](media/azure-stack-monitor-health/repair-in-progress.png)

La acción **Reparar** notificará la finalización correcta o el error al completar la acción en la misma hoja de notificación del portal.  Si se produce un error en una acción de reparación de una alerta, puede volver a ejecutar la acción **Reparar** a partir de los detalles de alerta. Si la acción de **reparación** se completa correctamente, **no** vuelva a ejecutarla. Después de que la instancia de rol de la infraestructura vuelve a estar en línea, esta alerta se cierra automáticamente.

![La acción Reparar se completa correctamente](media/azure-stack-monitor-health/repair-completed.png)

### <a name="manual-remediation"></a>Corrección manual

Si la opción **Reparar** no se admite, asegúrese de seguir el conjunto completo de instrucciones de corrección proporcionadas en la alerta. Por ejemplo, los pasos de corrección de la expiración del certificado interno le guiarán a lo largo del proceso de rotación de secretos:

![Corrección de expiración del certificado](media/azure-stack-monitor-health/certificate-expiration.png)

### <a name="alert-closure"></a>Cierre de la alerta

Muchas alertas, aunque no todas, se cierran automáticamente cuando se resuelve el problema subyacente. Las alertas que proporcionan un botón de acción Reparar se cerrarán de forma automática si Azure Stack Hub resuelve el problema. Para todas las demás alertas, seleccione **Cerrar alerta** cuando haya seguido los pasos de corrección. Si el problema persiste, Azure Stack Hub genera una alerta nueva. Si se resuelve, la alerta permanecerá cerrada y no se tendrán que seguir más pasos.

## <a name="next-steps"></a>Pasos siguientes

[Administrar las actualizaciones en Azure Stack Hub](azure-stack-updates.md)

[Administración de regiones en Azure Stack Hub](azure-stack-region-management.md)
