---
title: Envío inmediato de registros de diagnóstico de Azure Stack Hub
description: Aprenda a recopilar los registros de diagnóstico a petición en Azure Stack Hub mediante el portal del administrador o un script de PowerShell.
author: justinha
ms.topic: article
ms.date: 08/24/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 08/24/2020
ms.openlocfilehash: abcf70b6d714f9a759aeaa9a7f591a60e195594f
ms.sourcegitcommit: 4922a14fdbc8a3b67df065336e8a21a42f224867
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/24/2020
ms.locfileid: "88764654"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>Envío inmediato de registros de diagnóstico de Azure Stack Hub

::: moniker range=">= azs-2002"

Antes de solicitar soporte técnico, los operadores de Azure Stack pueden enviar registros de diagnóstico a petición al Soporte técnico de Microsoft mediante el portal de administración o PowerShell. Si Azure Stack Hub está conectado a Azure, se recomienda utilizar la opción **Send logs now** (Enviar registros ahora) del portal del administrador porque es la forma más sencilla de enviar los registros directamente a Microsoft. Si el portal no está disponible, en su lugar, los operadores deben [enviar registros ahora con Send-AzureStackDiagnosticLog](./azure-stack-configure-on-demand-diagnostic-log-collection-powershell.md?view=azs-2002). 

Si está desconectado de Internet o solo desea guardar los registros de forma local, use el método [Get-AzureStackLog](azure-stack-get-azurestacklog.md) para enviar los registros. En el diagrama de flujo siguiente se muestra qué opción utilizar para enviar los registros de diagnóstico en cada caso. 

![Diagrama de flujo que muestra el envío inmediato de registros a Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>Como alternativa a la recopilación de registros a petición, puede simplificar el proceso de solución de problemas mediante la [recopilación proactiva de registros de diagnóstico](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002). Si es necesario investigar el estado de mantenimiento del sistema, los registros se cargan automáticamente para su análisis antes de abrir una incidencia para el Soporte técnico de Microsoft. Si está habilitada la recopilación proactiva de registros, **Ayuda y soporte técnico** muestra cuándo está en curso la recopilación de registros. Si hace clic en **Send logs now** (Enviar registros ahora) para recopilar los registros de una hora específica mientras la recopilación proactiva de registros está en curso, la recopilación a petición se inicia una vez completada la recopilación proactiva de registros.

Especifique la hora de inicio y la hora de finalización de la recopilación de registros y haga clic en **Collect and Upload** (Recopilar y cargar). 

![Captura de pantalla de la opción Send logs now (Enviar registros ahora)](media/azure-stack-help-and-support/send-logs-now.png)


::: moniker-end
::: moniker range=">= azs-2005"
## <a name="save-logs-locally"></a>Almacenamiento local de los registros

Se pueden guardar los registros en un recurso compartido de SMB local cuando Azure Stack Hub está desconectado de Azure. En la hoja **Settings** (Configuración), escriba la ruta de acceso y un nombre de usuario y una contraseña con permiso para escribir en el recurso compartido. Durante una incidencia de soporte técnico, Soporte técnico de Microsoft proporcionará pasos detallados sobre cómo se transfieren estos registros locales. Si el portal de administración no está disponible, puede usar [Get-AzureStackLog](azure-stack-get-azurestacklog.md) para guardar los registros localmente.

![Captura de pantalla de opciones de recopilación de registros de diagnóstico](media/azure-stack-help-and-support/save-logs-locally.png)

::: moniker-end
::: moniker range="<= azs-1910"
## <a name="use-help-and-support-to-collect-diagnostic-logs-on-demand"></a>Uso de la ayuda y soporte técnico para recopilar registros de diagnóstico a petición

Para solucionar un problema, Soporte técnico de Microsoft podría solicitar que un operador de Azure Stack Hub recopilara los registros de diagnóstico a petición de una ventana de tiempo específica de la semana anterior. En ese caso, Soporte técnico de Microsoft proporcionará al operador una dirección URL de SAS para cargar la colección. 
Siga los pasos que se indican a continuación para configurar la recopilación de registros a petición mediante la dirección URL de SAS de Soporte técnico de Microsoft:

1. Abra **Help and Support Overview** (Información general de ayuda y soporte técnico) y haga clic en **Collect logs now** (Recopilar registros ahora). 
1. Puede elegir una ventana deslizante de entre 1 y 4 horas de los últimos siete días. 
1. Elija la zona horaria local.
1. Escriba la dirección URL de SAS que le proporcionó Soporte técnico de Microsoft.

   ![Captura de pantalla de la recopilación de registros a petición](media/azure-stack-automatic-log-collection/collect-logs-now.png)

>[!NOTE]
>Si está habilitada la recopilación de registros de diagnóstico automática, **Ayuda y soporte técnico** muestra cuando la recopilación de registros está en curso. Si hace clic en **Collect logs now** (Recopilar registros ahora) para recopilar registros de una hora específica mientras la recopilación automática de registros está en curso, la recopilación a petición se inicia una vez completada la recopilación de registros automática. 


::: moniker-end


## <a name="next-steps"></a>Pasos siguientes

[Uso del punto de conexión con privilegios para enviar los registros de diagnóstico de Azure Stack Hub](./azure-stack-configure-on-demand-diagnostic-log-collection-powershell.md?view=azs-2002)
