---
title: Supervisión y administración de pruebas en el portal de VaaS de Azure Stack
description: Supervisión y administración de pruebas en el portal de VaaS de Azure Stack.
author: mattbriggs
ms.topic: tutorial
ms.date: 11/11/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/11/2019
ROBOTS: NOINDEX
ms.openlocfilehash: b2a6f2b1e40ddfad9edbf64c92f29e93786fc6f9
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76885084"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>Supervisión y administración de pruebas en el portal de VaaS

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Después de programar las pruebas en la solución Azure Stack, la validación como servicio (VaaS) comenzará a informar del estado de ejecución de estas. Esta información está disponible en el portal de VaaS junto con otras acciones como reprogramar y cancelar pruebas.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>Ir a la página de resumen de las pruebas de flujo de trabajo

1. En el panel de soluciones, seleccione una solución existente que tenga al menos un flujo de trabajo.

    ![Iconos de flujo de trabajo](media/tile_all-workflows.png)

1. Seleccione **Administrar** en el icono de flujo de trabajo. La página siguiente muestra los flujos de trabajo creados para la solución seleccionada.

1. Seleccione el nombre del flujo de trabajo para abrir el resumen de sus pruebas.

## <a name="change-workflow-parameters"></a>Cambio de los parámetros de flujo de trabajo

Puede editar los [parámetros de prueba](azure-stack-vaas-parameters.md#test-parameters) especificados durante la creación del flujo de trabajo para cualquier tipo de flujo de trabajo.

1. En la página de resumen de las pruebas, seleccione el botón **Editar**.

1. Proporcione nuevos valores según se indica en [Parámetros comunes del flujo de trabajo en la validación como servicio de Azure Stack](azure-stack-vaas-parameters.md).

1. Seleccione **Enviar** para guardar los valores.

> [!NOTE]
> En el flujo de trabajo **Prueba superada**, deberá completar la selección de la prueba e ir a la página de revisión para poder guardar los nuevos valores de los parámetros.

### <a name="add-tests-test-pass-only"></a>Agregar pruebas (solo Pruebas superadas)

En los flujos de trabajo de **Pruebas superadas**, los botones **Agregar pruebas** y **Editar** le permiten programar nuevas pruebas en el flujo de trabajo.

> [!TIP]
> Seleccione **Agregar pruebas** si solo desea programar nuevas pruebas y no es necesario editar los parámetros de un flujo de trabajo de **Pruebas superadas**.

## <a name="managing-test-instances"></a>Administración de instancias de prueba

Para las ejecuciones no oficiales (es decir, las del flujo de trabajo de **Pruebas superadas**), la página de resumen de las pruebas muestra las pruebas programadas en la solución Azure Stack.

Para las ejecuciones oficiales (es decir, las de los flujos de trabajo de **validación**), la página de resumen de las pruebas muestra las pruebas necesarias para completar la validación en la solución Azure Stack. Las pruebas de validación se programan desde esta página.

Cada instancia de prueba programada muestra la siguiente información:

| Columna | Descripción |
| --- | --- |
| Nombre de la prueba | El nombre y versión de la prueba. |
| Category | Propósito de la prueba. |
| Creado | La hora en la que estaba programada la prueba. |
| Iniciado | La hora a la que empezó a ejecutarse la prueba. |
| Duration | La duración de la ejecución de la prueba. |
| Status | El estado o resultado de la prueba. Los estados previos a la ejecución o los de prueba en curso son: `Pending`, `Running`. Los estados de finalización son: `Cancelled`, `Failed`, `Aborted`, `Succeeded`. |
| Nombre del agente | El nombre del agente que ejecutó la prueba. |
| Total de operaciones | El número total de operaciones que se han intentado durante la prueba. |
| Operaciones correctas | El número de operaciones que se realizaron correctamente durante la prueba. |
|  Operaciones erróneas | El número de operaciones que no se realizaron correctamente durante la prueba. |

### <a name="actions"></a>Acciones

Cada instancia de prueba muestra las acciones disponibles que puede realizar al seleccionar el menú contextual **[...]** en la tabla de instancias de prueba.

#### <a name="view-information-about-the-test-definition"></a>Ver información sobre la definición de la prueba

Seleccione **Ver información** en el menú contextual para ver información general acerca de la definición de la prueba. Esta es una información que comparten cada instancia de la prueba con el mismo nombre y versión.

| Propiedad de la prueba | Descripción |
| -- | -- |
| Nombre de la prueba | Nombre de la prueba. |
| Versión de la prueba | La versión de la prueba. |
| Publicador | El publicador de la prueba. |
| Category |  Propósito de la prueba. |
| Servicios de destino | Los servicios de Azure Stack que se están probando. |
| Descripción | Descripción de la prueba. |
| Duración estimada (minutos) | El tiempo de ejecución esperado de la prueba. |
| Vínculos | Cualquier información pertinente acerca de la prueba o los puntos de contacto. |

#### <a name="view-test-instance-parameters"></a>Ver parámetros de la instancia de prueba

Seleccione **Ver parámetros** en el menú contextual para ver los parámetros proporcionados para la instancia de la prueba durante la programación. No se muestran las cadenas confidenciales como las contraseñas. Esta acción solo está disponible para las pruebas que se han programado.

Esta ventana incluye los siguientes metadatos para todas las instancias de prueba:

| Propiedad de la instancia de prueba | Descripción |
| -- | -- |
| Nombre de la prueba | Nombre de la prueba. |
| Versión de la prueba | La versión de la prueba. |
| Identificador de instancia de la prueba | Un GUID que identifica la instancia específica de la prueba. |

#### <a name="view-test-instance-operations"></a>Ver operaciones de la instancia de prueba

Seleccione **Ver operaciones** en el menú contextual para ver un estado detallado de las operaciones realizadas durante la prueba. Esta acción solo está disponible para las pruebas que se han programado.

![Ver operaciones](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>Descarga de registros de una instancia de prueba completada

Seleccione **Descargar registros** en el menú contextual para descargar un archivo `.zip` de los registros generados durante la ejecución de la prueba. Esta acción solo está disponible para pruebas que se han completado, es decir, una prueba con un estado `Cancelled`, `Failed`, `Aborted` o `Succeeded`.

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>Reprogramación de una instancia de prueba o programación de una prueba

La programación de pruebas desde la página de administración depende del tipo de flujo de trabajo en el que se ejecuta la prueba.

##### <a name="test-pass-workflow"></a>Flujo de trabajo de Prueba superada

En el flujo de trabajo de Prueba superada, si **reprograma** una instancia de prueba se reutilizará el mismo conjunto de parámetros que la instancia de prueba original y se *reemplazará* el resultado original, incluidos sus registros. Tendrá que volver a especificar cadenas confidenciales como contraseñas cuando reprograme.

1. Seleccione **Reprogramar** en el menú contextual para abrir un símbolo del sistema y reprogramar la instancia de prueba.

1. Escriba los parámetros que necesite.

1. Seleccione **Enviar** para reprogramar la instancia de prueba y reemplazar la instancia existente.

##### <a name="validation-workflows"></a>Flujos de trabajo de validación

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>Cancelación de una instancia de prueba

Una prueba programada se puede cancelar si su estado es `Pending` o `Running`.  

1. Seleccione **Cancelar** en el menú contextual para abrir un símbolo del sistema y cancelar la instancia de prueba.

1. Seleccione **Enviar** para cancelar la instancia de la prueba.

## <a name="next-steps"></a>Pasos siguientes

- [Solución de problemas de la validación como servicio](azure-stack-vaas-troubleshoot.md)
