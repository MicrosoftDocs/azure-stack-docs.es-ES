---
title: Planificar una actualización de Azure Stack | Microsoft Docs
description: Aprenda a planificar una actualización de Azure Stack.
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
ms.date: 10/17/2019
ms.author: mabrigg
ms.lastreviewed: 08/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 44eca76e3b9e969681f4540093088785ad06f97c
ms.sourcegitcommit: b5eb024d170f12e51cc852aa2c72eabf26792d8d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/17/2019
ms.locfileid: "72534008"
---
# <a name="plan-for-an-azure-stack-update"></a>Planificar una actualización de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

Puede preparar su instancia de Azure Stack para que el proceso de actualización se realice de la forma más fluida posible a fin de que tenga un impacto mínimo para los usuarios. En este artículo, revisará los pasos para notificar a los usuarios de posibles interrupciones de servicio y los pasos a seguir para preparar una actualización.

## <a name="notify-your-users-of-maintenance-operations"></a>Notificar a los usuarios cualquier operación de mantenimiento

Notifique a los usuarios cualquier operación de mantenimiento, y programe ventanas de mantenimiento normales durante el horario no laborable si es posible. Las operaciones de mantenimiento pueden afectar tanto a las cargas de trabajo de inquilino como a las operaciones del portal.

## <a name="prepare-for-an-azure-stack-update"></a>Prepararse para una actualización de Azure Stack

Para prepararse para la actualización, asegúrese de que ha aplicado todas las revisiones, revisiones de seguridad y actualizaciones de OEM, ha validado el estado de la instancia de Azure Stack, ha comprobado la capacidad disponible y ha revisado la actualización.

1. Revise los problemas conocidos. Para obtener instrucciones, consulte [Problemas conocidos de Azure Stack](https://docs.microsoft.com/azure-stack/operator/release-notes).

2. Revise las actualizaciones de seguridad. Para obtener una lista de actualizaciones, consulte [Actualizaciones de seguridad de Azure Stack](https://docs.microsoft.com/azure-stack/operator/release-notes-security-updates).

3. Antes de iniciar la instalación de esta actualización, ejecute [Test-AzureStack](https://docs.microsoft.com/azure-stack/operator/azure-stack-diagnostic-test) para validar el estado de Azure Stack y resolver todos los problemas operativos detectados, incluidas todas las advertencias y errores. Repase también las alertas activas y resuelva las que requieran alguna acción.

    1. Abra una sesión de punto de conexión con privilegios desde un equipo con acceso de red a las máquinas virtuales de Azure Stack ERCS. Para obtener instrucciones del uso de PEP, consulte [Uso del punto de conexión con privilegios en Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-privileged-endpoint).

    2. Test-AzureStack -Group UpdateReadiness

    3. Revise el resultado y resuelva los errores de estado. Para obtener más información, consulte [Run a validation test of Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-diagnostic-test) (Ejecutar una prueba de validación de Azure Stack).

4. Resuelva los problemas. Puede consultar el tema Supervisar el estado y las alertas para obtener una guía sobre cómo obtener más información sobre el problema marcado. Para obtener instrucciones, consulte [Supervisar el estado y las alertas en Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-monitor-health).

5. Aplique las revisiones más recientes. Para obtener una lista de las últimas revisiones, consulte la sección Revisión de las notas de la versión. Repita los pasos 3 y 4 después de aplicar la última revisión.

6. Asegúrese de que su paquete de OEM sea compatible con la versión de Azure Stack a la que está actualizando. Si su paquete de OEM no es compatible con la versión de Azure Stack a la que está actualizando, deberá realizar una actualización del paquete de OEM antes de ejecutar una actualización de Azure Stack. Para obtener instrucciones, consulte "Aplicar actualizaciones del fabricante de equipos originales (OEM) de Azure Stack". Repita los pasos 3 y 4 después de aplicar la actualización del paquete de OEM.

7. Ejecute la herramienta de planeamiento de capacidad. Para obtener una introducción e instrucciones sobre el uso de la herramienta, consulte [Introducción al planeamiento de capacidad de Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-capacity-planning-overview).

8. De manera opcional, puede [habilitar la recopilación de registros de diagnóstico automática](azure-stack-configure-automatic-diagnostic-log-collection.md) para recopilar de forma proactiva registros del análisis de atención al cliente si ve una alerta como **Error de actualización**. 

8. Revise el paquete de actualización. Al planear su ventana de mantenimiento, es importante revisar el tipo específico de actualización que ha publicado Microsoft como se indica en las notas de la versión.

## <a name="next-steps"></a>Pasos siguientes

[Aplique la actualización de Azure Stack](azure-stack-apply-updates.md).
