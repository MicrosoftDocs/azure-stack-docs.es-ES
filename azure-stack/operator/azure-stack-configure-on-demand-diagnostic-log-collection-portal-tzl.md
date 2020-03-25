---
title: Envío inmediato de registros de diagnóstico de Azure Stack Hub
description: Aprenda a recopilar los registros de diagnóstico a petición en Azure Stack Hub mediante el portal del administrador o un script de PowerShell.
author: justinha
ms.topic: article
ms.date: 02/26/2020
ms.author: justinha
ms.reviewer: shisab
ms.lastreviewed: 02/26/2020
ms.openlocfilehash: fb79a6378d2dec69804019b3ab0648ce874bf99d
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2020
ms.locfileid: "79520570"
---
# <a name="send-azure-stack-hub-diagnostic-logs-now"></a>Envío inmediato de registros de diagnóstico de Azure Stack Hub

Antes de solicitar soporte técnico, los operadores de Azure Stack pueden enviar registros de diagnóstico a petición a los servicios de soporte técnico al cliente de Microsoft mediante el portal del administrador o PowerShell. Si Azure Stack Hub está conectado a Azure, se recomienda utilizar la opción **Send logs now** (Enviar registros ahora) del portal del administrador porque es la forma más sencilla de enviar los registros directamente a Microsoft. Si el portal no está disponible, en su lugar, los operadores deben [usar PowerShell para enviar los registros ahora](azure-stack-configure-on-demand-diagnostic-log-collection-powershell-tzl.md). 

Si está desconectado de Internet o solo desea guardar los registros de forma local, use el método [Get-AzureStackLog](azure-stack-get-azurestacklog.md) para enviar los registros. En el diagrama de flujo siguiente se muestra qué opción utilizar para enviar los registros de diagnóstico en cada caso. 

![Diagrama de flujo que muestra el envío inmediato de registros a Microsoft](media/azure-stack-help-and-support/send-logs-now-flowchart.png)

>[!NOTE]
>Como alternativa a la recopilación de registros a petición, puede simplificar el proceso de solución de problemas mediante la [recopilación proactiva de registros de diagnóstico](azure-stack-configure-automatic-diagnostic-log-collection-tzl.md). Si es necesario investigar el estado de mantenimiento del sistema, los registros se cargan automáticamente para su análisis antes de abrir una incidencia con los servicios de soporte técnico al cliente. Si está habilitada la recopilación proactiva de registros, **Ayuda y soporte técnico** muestra cuándo está en curso la recopilación de registros. Si hace clic en **Send logs now** (Enviar registros ahora) para recopilar los registros de una hora específica mientras la recopilación proactiva de registros está en curso, la recopilación a petición se inicia una vez completada la recopilación proactiva de registros.

Especifique la hora de inicio y la hora de finalización de la recopilación de registros y haga clic en **Collect and Upload** (Recopilar y cargar). 

![Captura de pantalla de la opción Send logs now (Enviar registros ahora)](media/azure-stack-help-and-support/send-logs-now.png)

## <a name="next-steps"></a>Pasos siguientes

[Uso del punto de conexión con privilegios para enviar los registros de diagnóstico de Azure Stack Hub](azure-stack-get-azurestacklog.md)
