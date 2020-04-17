---
title: Recuperación de datos de Azure Stack Hub con el servicio Copia de seguridad de infraestructura
description: Aprenda a realizar una copia de seguridad de la configuración y los datos de servicio de Azure Stack Hub, así como a restaurarlos, con el servicio Copia de seguridad de infraestructura.
author: justinha
ms.topic: article
ms.date: 05/16/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 96d1062b7b6b4c30d1d635965b51de27c0b49269
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "77703288"
---
# <a name="recover-data-in-azure-stack-hub-with-the-infrastructure-backup-service"></a>Recuperación de datos de Azure Stack Hub con el servicio Copia de seguridad de infraestructura

Puede hacer una copia de seguridad de la configuración y los datos de servicio, así como restaurarlos, utilizando el servicio Copia de seguridad de infraestructura de Azure Stack Hub. Cada instalación de Azure Stack Hub contiene una instancia del servicio. Puede usar las copias de seguridad creadas por el servicio para volver a implementar la nube de Azure Stack Hub con el fin de restaurar la identidad, la seguridad y los datos de Azure Resource Manager.

Habilite la copia de seguridad cuando esté listo para poner la nube en producción. No habilite la copia de seguridad si tiene previsto realizar tareas de pruebas y validación durante un largo período de tiempo.

Antes de habilitar el servicio de copia de seguridad, asegúrese de que cumple con los [requisitos](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> El servicio Copia de seguridad de infraestructura no incluye aplicaciones ni datos de usuario. Consulte [Protección de máquinas virtuales implementadas en Azure Stack Hub](../user/azure-stack-manage-vm-protect.md) para más información acerca de cómo proteger aplicaciones basadas en máquinas virtuales IaaS. Para ver una descripción completa acerca de cómo proteger las aplicaciones en Azure Stack Hub, consulte las [notas del producto sobre las consideraciones para la continuidad empresarial y recuperación ante desastres en Azure Stack Hub](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="the-infrastructure-backup-service"></a>Servicio Infrastructure Backup

El servicio contiene las características siguientes:

| Característica                                            | Descripción                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Servicios de Infrastructure Backup                     | Coordine la copia de seguridad en un subconjunto de servicios de infraestructura de Azure Stack Hub. Si se produce un desastre, se pueden restaurar los datos como parte de la nueva implementación. |
| Compresión y cifrado de los datos exportados de copia de seguridad | El sistema comprime y cifra los datos de copia de seguridad antes de que se exporten a la ubicación de almacenamiento externa que proporciona el administrador.                |
| Supervisión de trabajo de copia de seguridad                              | El sistema le informa cuando se producen errores en los trabajos de copia de seguridad y cómo solucionar el problema.                                                                                                |
| Experiencia de administración de copia de seguridad                       | Los proveedores de recursos de copia de seguridad admiten la habilitación de la copia de seguridad.                                                                                                                         |
| Recuperación en la nube                                     | Si se produce una pérdida de datos grave, pueden utilizarse las copias de seguridad para restaurar la información básica de Azure Stack Hub como parte de la implementación.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Comprobación de los requisitos para el servicio Infrastructure Backup

- **Ubicación de almacenamiento**  
  Necesita un recurso compartido de archivos accesible desde Azure Stack Hub que pueda contener siete copias de seguridad. Cada copia de seguridad ocupa aproximadamente 10 GB. El recurso compartido debe ser capaz de almacenar 140 GB de copias de seguridad. Para más información sobre cómo seleccionar una ubicación de almacenamiento para el servicio Copia de seguridad de infraestructura de Azure Stack, consulte [Requisitos de Backup Controller](azure-stack-backup-reference.md#backup-controller-requirements).
- **Credenciales**  
  Necesita una cuenta de usuario de dominio y credenciales. Por ejemplo, puede usar las credenciales de administrador de Azure Stack Hub.
- **Certificado de cifrado**  
  Los archivos de copia de seguridad se cifran mediante la clave pública del certificado. Asegúrese de almacenar este certificado en una ubicación segura. 


## <a name="next-steps"></a>Pasos siguientes

Consulte [Habilitación de la copia de seguridad de Azure Stack Hub desde el portal del administrador](azure-stack-backup-enable-backup-console.md).

Consulte [Habilitar la copia de seguridad de Azure Stack Hub con PowerShell](azure-stack-backup-enable-backup-powershell.md).

Consulte [Copia de seguridad de Azure Stack Hub](azure-stack-backup-back-up-azure-stack.md).

Consulte [Recuperación después de una pérdida de datos grave](azure-stack-backup-recover-data.md).
