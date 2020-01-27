---
title: Solución de problemas de actualizaciones en Azure Stack Hub | Microsoft Docs
description: Como operador de Azure Stack Hub, obtenga información sobre cómo resolver problemas con la actualización para que Azure Stack Hub pueda volver a trabajar lo más rápido posible.
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
ms.date: 09/23/2019
ms.author: mabrigg
ms.lastreviewed: 09/23/2019
ms.reviewer: ppacent
ms.openlocfilehash: 94c6b6882c3b2f88c9815ab535fbef8b23811007
ms.sourcegitcommit: 320eddb281a36d066ec80d67b103efad7d4f33c8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2020
ms.locfileid: "76145774"
---
# <a name="best-practices-for-troubleshooting-azure-stack-hub-patch-and-update-issues"></a>Procedimientos recomendados para la solución de problemas de revisión y actualización de Azure Stack Hub

En este artículo se proporciona una introducción sobre los procedimientos recomendados para solucionar problemas relacionados con la revisión y actualización de Azure Stack Hub, así como las soluciones correspondientes.


El proceso de revisión y actualización de Azure Stack Hub está diseñado para permitir a los operadores aplicar paquetes de actualización de una manera coherente y simplificada. Aunque es poco frecuente, pueden producirse problemas durante la revisión y actualización. Se recomiendan los siguientes pasos en caso de que se produzca un problema durante el proceso de revisión y actualización:

0. **Requisitos previos**: Asegúrese de que ha seguido la [lista de comprobación de actividades de actualización](release-notes-checklist.md) y de que ha [configurado la recopilación automática de registros](azure-stack-configure-automatic-diagnostic-log-collection.md).
1. Siga los pasos de corrección de la alerta de error que se crea cuando se produce un error en la actualización.
2. Revise los [problemas habituales de revisión y actualización de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates-troubleshoot#Common-azure-stack-hub-patch-and-update-issues) y realice las acciones recomendadas si aparece su problema.
3. Si no ha podido resolver el problema con los pasos anteriores, cree una [incidencia de soporte técnico de Azure Stack Hub](azure-stack-help-and-support-overview.md). Asegúrese de que ha [recopilado los registros](https://docs.microsoft.com/azure-stack/operator/azure-stack-configure-on-demand-diagnostic-log-collection) del intervalo de tiempo en el que se produjo el problema.

## <a name="common-azure-stack-hub-patch-and-update-issues"></a>Problemas habituales de revisión y actualización de Azure Stack Hub

*Se aplica a: Sistemas integrados de Azure Stack Hub*

### <a name="preparationfailed"></a>PreparationFailed

**Aplicable a**: este problema se aplica a todas las versiones admitidas.

**Causa**: Al intentar instalar la actualización de Azure Stack Hub, es posible que se produzca un error en el estado de esa actualización y dicho estado cambie a `PreparationFailed`. En el caso de los sistemas conectados a Internet, esto suele indicar que el paquete de actualización no se puede descargar correctamente debido a una conexión de Internet débil. 

**Corrección**: Para solucionar este problema, vuelva a hacer clic en **Instalar ahora**. Si el problema persiste, recomendamos cargar manualmente la actualización siguiendo la [sección de instalación de actualizaciones](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Repetición**: Comunes

## <a name="next-steps"></a>Pasos siguientes

- [Actualización de Azure Stack Hub](azure-stack-updates.md)  
- [Ayuda y soporte técnico para Microsoft Azure Stack Hub](azure-stack-help-and-support-overview.md)
