---
title: Lista de comprobación de la actividad de actualización de Azure Stack Hub
description: Lista de comprobación para preparar el sistema para la actualización más reciente de Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 08/10/2020
ms.author: sethm
ms.reviewer: TBD
ms.lastreviewed: 01/14/2020
ms.openlocfilehash: 188f403b89e0e77b75f88a5e50440fd72cc61fdb
ms.sourcegitcommit: 52b33ea180c38a5ecce150f5a9ea4a026344cc3d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/11/2020
ms.locfileid: "88074271"
---
# <a name="azure-stack-hub-update-activity-checklist"></a>Lista de comprobación de la actividad de actualización de Azure Stack Hub

Revise esta lista de comprobación para prepararse para obtener una actualización de Azure Stack Hub. Este artículo contiene una lista de comprobación de actividades relacionadas con la actualización para los operadores de Azure Stack Hub.

## <a name="prepare-for-azure-stack-hub-update"></a>Preparación para una actualización de Azure Stack Hub

| &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; Actividad &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp; &nbsp;                   | Detalles                                                   |
|------------------------------|-----------------------------------------------------------|
| Revisión de problemas conocidos     | [Lista de los problemas conocidos](known-issues.md).                |
| Revisión de las actualizaciones de seguridad | [Lista de actualizaciones de seguridad](release-notes-security-updates.md).      |
| Revisión de las actualizaciones del proveedor de recursos del complemento | [App Service](azure-stack-app-service-update.md)<br>[Event Hubs](resource-provider-apply-updates.md)<br> [MySQL](azure-stack-sql-resource-provider-update.md)<br>[SQL](azure-stack-mysql-resource-provider-update.md)<br>  |
| Aplicación del paquete de OEM más reciente | Póngase en contacto con el OEM para asegurarse de que el sistema cumple los requisitos mínimos del paquete de OEM para la versión de Azure Stack Hub a la que se está actualizando el sistema. Asegúrese de que su paquete de OEM sea compatible con la versión de Azure Stack Hub a la que está actualizando. Si su paquete de OEM no es compatible con la versión de Azure Stack Hub a la que está actualizando, deberá realizar una actualización del paquete de OEM antes de ejecutar una actualización de Azure Stack Hub. Para obtener instrucciones, consulte "Aplicar actualizaciones del fabricante de equipos originales (OEM) de Azure Stack Hub". |
| Opcional: Configuración de la recopilación de registros automática | Se recomienda configurar la recopilación de registros automática en el entorno de Azure Stack Hub para simplificar el proceso de recopilación de registros del sistema en caso de que sea necesario abrir una incidencia de soporte técnico. Para configurar la recopilación de registros automática, consulte las instrucciones de [Configuración de la recopilación de registros de diagnóstico automática de Azure Stack Hub](./azure-stack-configure-automatic-diagnostic-log-collection.md?view=azs-2002). |
| Aplicación de las revisiones más recientes | Aplique las revisiones más recientes que sean aplicables a la versión instalada actualmente. Para obtener una lista de las últimas revisiones, consulte la sección [Revisión de las notas de la versión](release-notes.md). |
| Ejecutar la herramienta Capacity Planner | Asegúrese de que usa la versión más reciente de la [herramienta Azure Stack Hub Capacity Planner](azure-stack-capacity-planning-overview.md) para realizar el planeamiento y el ajuste de tamaño de la carga de trabajo. La última versión contiene correcciones de errores y proporciona nuevas características que se publican con cada actualización de Azure Stack Hub. |
| Ejecute **Test-AzureStack**. | Ejecute `Test-AzureStack -Group UpdateReadiness` para identificar los problemas operativos. El cmdlet es accesible a través de la sesión de punto de conexión con privilegios (PEP). Para más información, consulte [Validación del estado del sistema de Azure Stack Hub](azure-stack-diagnostic-test.md). |
| Resolución de problemas | Resuelva cualquier problema operativo identificado por `Test-AzureStack`. |
| Actualización disponible | Únicamente en los escenarios en los que estén conectadas, las implementaciones de Azure Stack Hub comprueban periódicamente un punto de conexión seguro y le notifican automáticamente si una actualización está disponible para la nube. Los clientes que estén desconectados pueden descargar e importar nuevos paquetes. Para ello, deben seguir el [proceso descrito aquí ](azure-stack-apply-updates.md). |
| Programación de un período de inactividad por mantenimiento y notificación a los usuarios | Notifique a los usuarios cualquier operación de mantenimiento, y programe ventanas de mantenimiento normales durante el horario no laborable si es posible. Las operaciones de mantenimiento pueden afectar a las cargas de trabajo de inquilinos existentes y provocar errores en las operaciones de nuevos inquilinos (por ejemplo, crear, reconfigurar o eliminar máquinas virtuales), tanto si la operación se ha iniciado desde el portal o mediante programación desde la API de Azure Resource Manager. Puede que otras operaciones, como la copia de seguridad, no estén disponibles hasta que finalice la actualización. Para las actualizaciones rápidas y completas de Azure Stack Hub, puede consultar las [notas de la versión](release-notes.md) para obtener una previsión de cuánto tiempo se espera que tarde la actualización para la versión que se está aplicando. |

## <a name="during-azure-stack-hub-update"></a>Durante la actualización de Azure Stack Hub

| Actividad | Detalles |
|--------------------|------------------------------------------------------------------------------------------------------|
| Administración de la actualización |[Administre las actualizaciones de Azure Stack Hub mediante el portal del operador](azure-stack-updates.md). |
|  |  |
| Supervisión de la actualización | Si el portal del operador no está disponible, consulte [Supervisión de las actualizaciones en Azure Stack Hub mediante el uso del punto de conexión con privilegios](azure-stack-monitor-update.md). |
|  |  |
| Reanudar las actualizaciones | Después de corregir una actualización errónea, [reanude las actualizaciones de Azure Stack Hub mediante el uso de un punto de conexión con privilegios](azure-stack-monitor-update.md). |

> [!IMPORTANT]  
> No ejecute **Test-AzureStack** durante una actualización, ya que esto hará que la actualización se detenga.

## <a name="after-azure-stack-hub-update"></a>Después del proceso de actualización de Azure Stack Hub

| Actividad | Detalles |
|--------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Aplicación de las revisiones más recientes | Instale las revisiones más recientes que sean aplicables a la versión actualizada. |
| Recuperación de claves de cifrado | Recupere las claves de cifrado de los datos en reposo y almacénelas de forma segura fuera de la implementación de Azure Stack Hub. Siga las [instrucciones para recuperar las claves](azure-stack-security-bitlocker.md). |
|  |  |
| Nueva habilitación de servicios multiinquilino | En el caso de Azure Stack Hub multiinquilino, [asegúrese de configurar todos los inquilinos del directorio de invitados](azure-stack-enable-multitenancy.md#configure-guest-directory) después de una actualización correcta. |

## <a name="next-steps"></a>Pasos siguientes

- [Revisión de la lista de problemas conocidos](known-issues.md)
- [Revisión de la lista de actualizaciones de seguridad](release-notes-security-updates.md)
