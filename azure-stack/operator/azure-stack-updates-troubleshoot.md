---
title: Solución de problemas de actualizaciones en Azure Stack | Microsoft Docs
description: Como operador de Azure Stack, obtenga información sobre cómo resolver problemas con la actualización para que Azure Stack pueda volver a trabajar lo más rápido posible.
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
ms.openlocfilehash: d5606e7904fe311a54d792a18e5d4029c709b33c
ms.sourcegitcommit: 0866555e0ed240a65595052899ef1b836dd07fbc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/10/2019
ms.locfileid: "72257747"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>Solución de problemas de revisión y actualización de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

Puede usar las instrucciones de este artículo para resolver los problemas que tenga al actualizar Azure Stack.

## <a name="preparationfailed"></a>PreparationFailed

**Aplicable a**: este problema se aplica a todas las versiones admitidas.

**Causa**: Al intentar instalar la actualización de Azure Stack, es posible que se produzca un error en el estado de esa actualización y dicho estado cambie a `PreparationFailed`. En el caso de los sistemas conectados a Internet, esto suele indicar que el paquete de actualización no se puede descargar correctamente debido a una conexión de Internet débil. 

**Corrección**: Para solucionar este problema, vuelva a hacer clic en **Instalar ahora**. Si el problema persiste, recomendamos cargar manualmente la actualización siguiendo la [sección de instalación de actualizaciones](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Repetición**: Común

## <a name="next-steps"></a>Pasos siguientes

- [Actualización de Azure Stack](azure-stack-updates.md)  
- [Ayuda y soporte técnico para Microsoft Azure Stack](azure-stack-help-and-support-overview.md)
