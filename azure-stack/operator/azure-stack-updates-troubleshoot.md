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
ms.openlocfilehash: 76c6c21c2a728004d2a33c04a02b905ec674b972
ms.sourcegitcommit: d967cf8cae320fa09f1e97eeb888e3db5b6e7972
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/26/2019
ms.locfileid: "71301248"
---
# <a name="troubleshooting-patch-and-update-issues-for-azure-stack"></a>Solución de problemas de revisión y actualización de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

Puede usar las instrucciones de este artículo para resolver los problemas que tenga al actualizar Azure Stack.

## <a name="preparationfailed"></a>PreparationFailed

**Aplicable a**: este problema se aplica a todas las versiones admitidas.

**Causa**: Al intentar instalar la actualización de Azure Stack, es posible que se produzca un error en el estado de esa actualización y dicho estado cambie a `PreparationFailed`. La causa es que el proveedor de recursos de actualización (URP) no se puede transferir correctamente desde el contenedor de almacenamiento a un recurso compartido de infraestructura interno para su procesamiento.

**Corrección**: A partir de la versión 1901 (1.1901.0.95), para solucionar este problema, puede hacer clic en **Actualizar** ahora de nuevo (en lugar de **Reanudar**). A continuación, URP limpia los archivos del intento anterior y vuelve a iniciar la descarga. Si el problema persiste, recomendamos cargar manualmente la actualización siguiendo la [sección de instalación de actualizaciones](azure-stack-apply-updates.md?#install-updates-and-monitor-progress).

**Repetición**: Común

## <a name="next-steps"></a>Pasos siguientes

- [Actualización de Azure Stack](azure-stack-updates.md)  
- [Ayuda y soporte técnico para Microsoft Azure Stack](azure-stack-help-and-support-overview.md)
