---
title: 'Recuperación de datos en Azure Stack con el servicio Copia de seguridad de infraestructura: MDC'
description: Aprenda a realizar una copia de seguridad de la configuración y los datos de servicio, y a restaurarlos, con el servicio Copia de seguridad de infraestructura. Para un Centro de datos modular (MDC).
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
ms.service: azure-stack
ms.workload: tzl
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/26/2020
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 10/26/2020
ms.openlocfilehash: 5a9e8615168714b3346f735d9182c38b2c604cca
ms.sourcegitcommit: d719f148005e904fa426a001a687e80730c91fda
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/06/2021
ms.locfileid: "97910812"
---
# <a name="recover-data-in-azure-stack-with-the-infrastructure-backup-service---modular-data-center-mdc"></a>Recuperación de datos en Azure Stack con el servicio Copia de seguridad de infraestructura: Centro de datos modular (MDC)

*Se aplica a: Centro de datos modular y Azure Stack Hub resistente*

Puede realizar una copia de seguridad de los metadatos del servicio de infraestructura mediante el servicio Copia de seguridad de infraestructura de Azure Stack. Estas copias de seguridad se usan para corregir los servicios de infraestructura degradados. La copia de seguridad solo incluye los datos de los servicios de infraestructura internos del sistema. Los datos de copia de seguridad no incluyen datos de usuarios y aplicaciones. Los datos de aplicaciones y de usuario se deben proteger por separado.

De forma predeterminada, la copia de seguridad de la infraestructura se habilita en el momento de la implementación. Estas copias de seguridad se almacenan internamente en el sistema y solo son accesibles durante los casos de soporte técnico avanzados. Si el sistema tiene acceso a una ubicación de almacenamiento externa, se puede indicar al servicio Copia de seguridad de infraestructura que exporte una copia de seguridad a esa ubicación de almacenamiento como una copia secundaria.

Antes de habilitar el servicio de copia de seguridad, asegúrese de que cumple con los [requisitos](../../operator/azure-stack-backup-reference.md#backup-controller-requirements).

> [!NOTE]
> El servicio Copia de seguridad de infraestructura no incluye aplicaciones ni datos de usuario. Para más información sobre cómo proteger las aplicaciones basadas en máquinas virtuales de IaaS, consulte los siguientes artículos:
>
> - [Protección de máquinas virtuales implementadas en Azure Stack](../../user/azure-stack-manage-vm-protect.md)
> - Protección de datos de series temporales de Event Hubs en Azure Stack
> - Protección de datos de blobs en Azure Stack

## <a name="the-infrastructure-backup-service"></a>Servicio Infrastructure Backup

El servicio contiene las características siguientes:

| Característica                                            | Descripción                                                                                                                                                |
|----------------------------------------------------|------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Servicios de Infrastructure Backup                     | Coordinan la copia de seguridad en un subconjunto de servicios de infraestructura en Azure Stack. |
| Compresión y cifrado de los datos de la copia de seguridad | El sistema comprime y cifra los datos de copia de seguridad antes de que se almacenen internamente o se exporten a la ubicación de almacenamiento externa que proporciona el operador.                |
| Supervisión de trabajo de copia de seguridad                              | El sistema le informa cuando se producen errores en los trabajos de copia de seguridad y cómo solucionar el problema.                                                                                                |

## <a name="verify-requirements-for-the-infrastructure-backup-service"></a>Comprobación de los requisitos para el servicio Infrastructure Backup

- **Ubicación de almacenamiento** Si hay acceso confiable a una ubicación de almacenamiento externo, necesita un recurso compartido de archivos accesible desde la infraestructura de Azure Stack que pueda almacenar varias copias de seguridad. Para más información acerca de cómo seleccionar una ubicación de almacenamiento para el servicio Copia de seguridad de infraestructura de Azure Stack, consulte [Requisitos de Backup Controller](../../operator/azure-stack-backup-reference.md#backup-controller-requirements).

- **Credenciales** Necesita credenciales de cuenta de usuario que puedan acceder a la ubicación de almacenamiento.

## <a name="next-steps"></a>Pasos siguientes

- Consulte [Habilitación de la copia de seguridad de Azure Stack desde el portal del administrador](../../operator/azure-stack-backup-enable-backup-console.md).

- Consulte [Habilitación de la copia de seguridad de Azure Stack con PowerShell](../../operator/azure-stack-backup-enable-backup-powershell.md).
