---
title: Copia de seguridad y recuperación de datos para Azure Stack con el servicio Infrastructure Backup | Microsoft Docs
description: Puede realizar una copia de seguridad de la configuración y los datos de servicio y restaurarlos con el servicio Infrastructure Backup.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2019
ms.author: justinha
ms.reviewer: hectorl
ms.lastreviewed: 05/16/2019
ms.openlocfilehash: 05bdf2fe5930777d44ec42eab8dede97b758cd15
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268850"
---
# <a name="backup-and-data-recovery-for-azure-stack-with-the-infrastructure-backup-service"></a>Copia de seguridad y recuperación de datos para Azure Stack con el servicio Infrastructure Backup

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede realizar una copia de seguridad de la configuración y los datos de servicio y restaurarlos con el servicio Infrastructure Backup. Cada instalación de Azure Stack contiene una instancia del servicio. Puede usar las copias de seguridad que ha creado por el servicio para volver a implementar la nube de Azure Stack, con el fin de restaurar la identidad, la seguridad y los datos de Azure Resource Manager. 

Puede habilitar la copia de seguridad cuando esté listo para poner la nube en producción. No habilite la copia de seguridad si tiene previsto realizar tareas de pruebas y validación durante un largo período de tiempo.

Antes de habilitar el servicio de copia de seguridad, asegúrese de que cumple con los [requisitos](#verify-requirements-for-the-infrastructure-backup-service).

> [!Note]  
> El servicio Infrastructure Backup no incluye aplicaciones ni datos de usuario. Para más información acerca de cómo proteger aplicaciones basadas en máquinas virtuales IaaS, consulte [Protección de máquinas virtuales implementadas en Azure Stack](../user/azure-stack-manage-vm-protect.md). Para obtener una descripción completa de cómo proteger las aplicaciones en Azure Stack, vea las [notas del producto sobre las consideraciones para la continuidad empresarial y recuperación ante desastres en Azure Stack](https://aka.ms/azurestackbcdrconsiderationswp).

## <a name="the-infrastructure-backup-service"></a>Servicio Infrastructure Backup

Los servicios contienen las características siguientes.

| Característica                                            | DESCRIPCIÓN                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Servicios de Infrastructure Backup                     | Coordine la copia de seguridad en un subconjunto de servicios de infraestructura en Azure Stack. Si se produce un desastre, se pueden restaurar los datos como parte de la nueva implementación. |
| Compresión y cifrado de los datos exportados de copia de seguridad | Los datos de copia de seguridad se comprimen y cifran por el sistema antes de que se exporten a la ubicación de almacenamiento externo proporcionada por el administrador.                |
| Supervisión de trabajo de copia de seguridad                              | El sistema notifica cuando se produce un error en los trabajos de copia de seguridad e informa de los pasos de solución.                                                                                                |
| Experiencia de administración de copia de seguridad                       | Los proveedores de recursos de copia de seguridad admiten la habilitación de la copia de seguridad.                                                                                                                         |
| Recuperación en la nube                                     | Si se produce una pérdida de datos catastrófica, pueden utilizarse las copias de seguridad para restaurar la información básica de Azure Stack como parte de la implementación.                                 |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Comprobación de los requisitos para el servicio Infrastructure Backup

- **Ubicación de almacenamiento**  
  Necesita un recurso compartido de archivos accesible desde Azure Stack que pueda contener siete copias de seguridad. Cada copia de seguridad ocupa aproximadamente 10 GB. El recurso compartido debe ser capaz de almacenar 140 GB de copias de seguridad. Para obtener más información acerca de cómo seleccionar una ubicación de almacenamiento para el servicio Infrastructure Backup de Azure Stack, consulte [Backup Controller requirements](azure-stack-backup-reference.md#backup-controller-requirements) (Requisitos de Backup Controller).
- **Credenciales**  
  Se necesita una cuenta de usuario de dominio y credenciales, por ejemplo, puede utilizar las credenciales de administrador de Azure Stack.
- **Certificado de cifrado**  
  Los archivos de copia de seguridad se cifran mediante la clave pública del certificado. Asegúrese de almacenar este certificado en una ubicación segura. 


## <a name="next-steps"></a>Pasos siguientes

Consulte [Habilitación de la copia de seguridad de Azure Stack desde el portal de administración](azure-stack-backup-enable-backup-console.md).

Consulte [Habilitación de la copia de seguridad de Azure Stack con PowerShell](azure-stack-backup-enable-backup-powershell.md).

Consulte [Copia de seguridad de Azure Stack](azure-stack-backup-back-up-azure-stack.md ).

Consulte [Recover from catastrophic data loss](azure-stack-backup-recover-data.md) (Recuperación después de una pérdida de datos grave).
