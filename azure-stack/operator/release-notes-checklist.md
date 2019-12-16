---
title: Lista de comprobación de la actividad de actualización de Azure Stack | Microsoft Docs
description: Lista de comprobación para preparar el sistema para la actualización más reciente de Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/10/2019
ms.author: sethm
ms.reviewer: ppacent
ms.lastreviewed: 12/10/2019
ms.openlocfilehash: a560b37051cf3b8b54e3cfec69dc6a9b28cc1cfb
ms.sourcegitcommit: d9430072dd96ae305101da6d8a47d6c23a0a64c2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/10/2019
ms.locfileid: "74995576"
---
# <a name="azure-stack-update-activity-checklist"></a>Lista de comprobación de actividades para la actualización de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

Revise esta lista de comprobación para prepararse para obtener una actualización de Azure Stack. Este artículo contiene una lista de comprobación de actividades relacionadas con la actualización para los operadores de Azure Stack.

## <a name="prepare-for-azure-stack-update"></a>Preparación para una actualización de Azure Stack

| Actividad                     | Detalles                                                   |
|------------------------------|-----------------------------------------------------------|
| Revisión de problemas conocidos     | [Lista de los problemas conocidos](known-issues.md).                |
| Revisión de las actualizaciones de seguridad | [Lista de actualizaciones de seguridad](release-notes-security-updates.md).      |
| Aplicación del paquete de OEM más reciente | Póngase en contacto con el OEM para asegurarse de que el sistema cumple los requisitos mínimos del paquete de OEM para la versión de Azure Stack a la que se está actualizando el sistema. Asegúrese de que su paquete de OEM sea compatible con la versión de Azure Stack a la que está actualizando. Si su paquete de OEM no es compatible con la versión de Azure Stack a la que está actualizando, deberá realizar una actualización del paquete de OEM antes de ejecutar una actualización de Azure Stack. Para obtener instrucciones, consulte "Aplicar actualizaciones del fabricante de equipos originales (OEM) de Azure Stack". |
| Opcional: Configuración de la recopilación de registros automática | Se recomienda configurar la recopilación de registros automática en el entorno de Azure Stack Hub para simplificar el proceso de recopilación de registros del sistema en caso de que sea necesario abrir una incidencia de soporte técnico. Para configurar la recopilación de registros automática, consulte las instrucciones de [Configuración de la recopilación de registros de diagnóstico automática de Azure Stack](azure-stack-configure-automatic-diagnostic-log-collection.md). |
| Aplicación de las revisiones más recientes | Aplique las revisiones más recientes que sean aplicables a la versión instalada actualmente. Para obtener una lista de las últimas revisiones, consulte la sección Revisión de las notas de la versión. |
| Ejecutar la herramienta Capacity Planner | Asegúrese de que usa la versión más reciente de la [herramienta Azure Stack Capacity Planner](azure-stack-capacity-planning-overview.md) para realizar el planeamiento y el ajuste de tamaño de la carga de trabajo. La última versión contiene correcciones de errores y proporciona nuevas características que se publican con cada actualización de Azure Stack. |
| Ejecución de Test-AzureStack | Ejecute `Test-AzureStack -Group UpdateReadiness` para identificar los problemas operativos. El cmdlet es accesible a través de la sesión de punto de conexión con privilegios (PEP). Para más información, consulte [Validación del estado del sistema de Azure Stack](azure-stack-diagnostic-test.md). |
| Resolución de problemas | Resuelva cualquier problema operativo identificado por `Test-AzureStack`. |
| Actualización disponible | Únicamente en los escenarios en los que estén conectadas, las implementaciones de Azure Stack comprueban periódicamente un punto de conexión seguro y le notifican automáticamente si una actualización está disponible para la nube. Los clientes que estén desconectados pueden descargar e importar nuevos paquetes. Para ello, deben seguir el [proceso descrito aquí ](azure-stack-apply-updates.md). |
| Notificación a los usuarios | Notifique a los usuarios cualquier operación de mantenimiento, y programe ventanas de mantenimiento normales durante el horario no laborable si es posible. Las operaciones de mantenimiento pueden afectar tanto a las cargas de trabajo de inquilino como a las operaciones del portal. |

## <a name="during-azure-stack-update"></a>Durante la actualización de Azure Stack

| Actividad | Detalles |
|--------------------|------------------------------------------------------------------------------------------------------|
| Administración de la actualización |[Administre las actualizaciones de Azure Stack mediante el portal del operador](azure-stack-updates.md). |
|  |  |
| Supervisión de la actualización | Si el portal del operador no está disponible, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](azure-stack-monitor-update.md). |
|  |  |
| Reanudar las actualizaciones | Después de corregir una actualización errónea, [reanude las actualizaciones de Azure Stack mediante el uso de un punto de conexión con privilegios](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> No ejecute **Test-AzureStack** durante una actualización, ya que esto hará que la actualización se detenga.

## <a name="after-azure-stack-update"></a>Después de la actualización de Azure Stack

| Actividad | Detalles |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aplicación de las revisiones más recientes | Instale las revisiones más recientes que sean aplicables a la versión actualizada. |
| Recuperación de claves de cifrado | Recupere las claves de cifrado de los datos en reposo y almacénelas de forma segura fuera de la implementación de Azure Stack. Siga las [instrucciones para recuperar las claves](azure-stack-security-bitlocker.md). |
|  |  |
| Nueva habilitación de servicios multiinquilino | En el caso de Azure Stack multiinquilino, [asegúrese de configurar todos los inquilinos del directorio de invitados](azure-stack-enable-multitenancy.md#configure-guest-directory) después de una actualización correcta. |

## <a name="next-steps"></a>Pasos siguientes

- [Revisión de la lista de problemas conocidos](known-issues.md)
- [Revisión de la lista de actualizaciones de seguridad](release-notes-security-updates.md)
