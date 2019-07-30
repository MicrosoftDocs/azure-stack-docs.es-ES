---
title: 'Notas de la versión de Azure Stack: Lista de comprobación de actividades para la actualización | Microsoft Docs'
description: Lista de comprobación rápida para preparar el sistema para la actualización más reciente de Azure Stack.
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
ms.date: 06/27/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 49b4276da5c7e42728cf96261104f59c70355c20
ms.sourcegitcommit: 159da88a52701679571bbedde1c36b72bbfe32dd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/22/2019
ms.locfileid: "68380407"
---
# <a name="azure-stack-update-activity-checklist"></a>Lista de comprobación de actividades para la actualización de Azure Stack

Este artículo contiene una lista de comprobación de actividades relacionadas con la actualización para los operadores de Azure Stack. Si está preparándose para aplicar una actualización a Azure Stack, puede consultar esta información.

## <a name="prepare-for-azure-stack-update"></a>Preparación para una actualización de Azure Stack

| Actividad                     | Detalles                                                   |
|------------------------------|-----------------------------------------------------------|
| Revisión de problemas conocidos     | [Lista de los problemas conocidos](azure-stack-release-notes-known-issues-1906.md).                |
| Revisión de las actualizaciones de seguridad | [Lista de actualizaciones de seguridad](azure-stack-release-notes-security-updates-1906.md).      |
| Aplicación del paquete de OEM más reciente | Póngase en contacto con el OEM para asegurarse de que el sistema cumple los requisitos mínimos del paquete de OEM para la versión de Azure Stack a la que se está actualizando el sistema. |
| Ejecución de Test-AzureStack     | Ejecute `Test-AzureStack -Group UpdateReadiness` para identificar los problemas operativos.      |
| Resolución de problemas          | Resuelva los problemas operativos que haya identificado **Test-AzureStack**.                |
| Aplicación de las revisiones más recientes   | Aplique las revisiones más recientes que sean aplicables a la versión instalada actualmente.         |
| Ejecutar la herramienta Capacity Planner   | Asegúrese de que usa la versión más reciente de la herramienta [Azure Stack Capacity Planner](https://aka.ms/azstackcapacityplanner) para realizar el planeamiento y el ajuste de tamaño de la carga de trabajo. La última versión contiene correcciones de errores y proporciona nuevas características que se publican con cada actualización de Azure Stack. |
| Actualización disponible        | Únicamente en los escenarios en los que estén conectadas, las implementaciones de Azure Stack comprueban periódicamente un punto de conexión seguro y le notifican automáticamente si una actualización está disponible para la nube. Los clientes que estén desconectados pueden descargar e importar el nuevo paquete 1906. Para ello, deben seguir el [proceso que se describe aquí](azure-stack-apply-updates.md). |


## <a name="during-azure-stack-update"></a>Durante la actualización de Azure Stack

| Actividad              | Detalles                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Administración de la actualización         | [Administre las actualizaciones de Azure Stack mediante el portal del operador](azure-stack-updates.md). |
| Supervisión de la actualización        | Si el portal del operador no está disponible, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](azure-stack-monitor-update.md). |
| Reanudar las actualizaciones            | Después de corregir una actualización errónea, [reanude las actualizaciones de Azure Stack mediante el uso de un punto de conexión con privilegios](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> No ejecute **Test-AzureStack** durante una actualización, ya que esto hará que la actualización se detenga.

## <a name="after-azure-stack-update"></a>Después de la actualización de Azure Stack

| Actividad              | Detalles                                                                          |
|-----------------------|----------------------------------------------------------------------------------|
| Aplicación de las revisiones más recientes | Instale las revisiones más recientes que sean aplicables a la versión actualizada.                          |
| Recuperación de claves de cifrado | Recupere las claves de cifrado de los datos en reposo y almacénelas de forma segura fuera de la implementación de Azure Stack. Siga las [instrucciones para recuperar las claves](azure-stack-security-bitlocker.md). |

## <a name="next-steps"></a>Pasos siguientes

- [Revisión de la lista de problemas conocidos](azure-stack-release-notes-known-issues-1906.md)
- [Revisión de la lista de actualizaciones de seguridad](azure-stack-release-notes-security-updates-1906.md)
