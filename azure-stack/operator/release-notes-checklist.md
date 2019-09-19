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
ms.date: 08/30/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 63b695b58a4c55f71714a5faa76d62e53f4d691e
ms.sourcegitcommit: c46d913ebfa4cb6c775c5117ac5c9e87d032a271
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/18/2019
ms.locfileid: "71106524"
---
# <a name="azure-stack-update-activity-checklist"></a>Lista de comprobación de actividades para la actualización de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

Revise esta lista de comprobación para prepararse para obtener una actualización de Azure Stack. Este artículo contiene una lista de comprobación de actividades relacionadas con la actualización para los operadores de Azure Stack.

## <a name="prepare-for-azure-stack-update"></a>Preparación para una actualización de Azure Stack

| Actividad                     | Detalles                                                   |
|------------------------------|-----------------------------------------------------------|
| Revisión de problemas conocidos     | [Lista de los problemas conocidos](known-issues.md).                |
| Revisión de las actualizaciones de seguridad | [Lista de actualizaciones de seguridad](release-notes-security-updates.md).      |
| Aplicación del paquete de OEM más reciente | Póngase en contacto con el OEM para asegurarse de que el sistema cumple los requisitos mínimos del paquete de OEM para la versión de Azure Stack a la que se está actualizando el sistema. |
| Ejecución de Test-AzureStack | Ejecute `Test-AzureStack -Group UpdateReadiness` para identificar los problemas operativos. |
| Resolución de problemas | Resuelva cualquier problema operativo identificado por `Test-AzureStack`. |
| Aplicación de las revisiones más recientes | Aplique las revisiones más recientes que sean aplicables a la versión instalada actualmente. |
| Ejecutar la herramienta Capacity Planner | Asegúrese de que usa la versión más reciente de la [herramienta Azure Stack Capacity Planner](azure-stack-capacity-planning-overview.md) para realizar el planeamiento y el ajuste de tamaño de la carga de trabajo. La última versión contiene correcciones de errores y proporciona nuevas características que se publican con cada actualización de Azure Stack. |
| Actualización disponible | Únicamente en los escenarios en los que estén conectadas, las implementaciones de Azure Stack comprueban periódicamente un punto de conexión seguro y le notifican automáticamente si una actualización está disponible para la nube. Los clientes que estén desconectados pueden descargar e importar nuevos paquetes. Para ello, deben seguir el [proceso descrito aquí ](azure-stack-apply-updates.md). |

## <a name="during-azure-stack-update"></a>Durante la actualización de Azure Stack

| Actividad | Detalles |
|--------------------|------------------------------------------------------------------------------------------------------|
| Administración de la actualización |[Administre las actualizaciones de Azure Stack mediante el portal del operador](azure-stack-updates.md). |
|  |  |
| Supervisión de la actualización | Si el portal del operador no está disponible, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](azure-stack-monitor-update.md). |
|  |  |
| Reanudar las actualizaciones | Después de corregir una actualización errónea, [reanude las actualizaciones de Azure Stack mediante el uso de un punto de conexión con privilegios](azure-stack-monitor-update.md). |

> [!Important]  
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
