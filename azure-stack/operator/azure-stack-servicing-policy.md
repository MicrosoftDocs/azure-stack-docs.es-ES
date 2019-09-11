---
title: Directiva de mantenimiento de Azure Stack | Microsoft Docs
description: Aprenda sobre la directiva de servicio de Azure Stack y cómo mantener un sistema integrado en un estado admitido.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: caac3d2f-11cc-4ff2-82d6-52b58fee4c39
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/01/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 1022ab056157ea1a9bc925d3992a99bd0b395a35
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188081"
---
- **Actualizaciones suministradas por el proveedor de hardware OEM**. Los asociados de hardware de Azure Stack son responsables del ciclo de vida de servicio de un extremo a otro (lo que incluye las instrucciones) de las actualizaciones de controladores y firmware relacionados con el hardware. Asimismo, poseen y mantienen las instrucciones de todo el software y el hardware del host de ciclo de vida del hardware. El proveedor de hardware OEM hospeda estas actualizaciones en su propio sitio de descarga.

## <a name="update-package-release-cadence"></a>Ritmo de lanzamientos de las actualizaciones

Microsoft espera lanzar actualizaciones de software a un ritmo mensual. Sin embargo, puede haber varios lanzamientos de actualizaciones en un mes, o ninguno. Los proveedores de hardware de OEM lanzan sus actualizaciones en función de la necesidad.

Busque documentación sobre cómo planear y administrar actualizaciones y cómo determinar la versión actual en [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md).

Para obtener información sobre una actualización concreta, incluido cómo descargarla, vea las notas de versión de la actualización:

- [Actualización 1908 de Azure Stack](azure-stack-release-notes-1908.md)
- [Actualización de Azure Stack 1907](azure-stack-release-notes-1907.md)
- [Actualización de Azure Stack 1906](azure-stack-release-notes-1906.md)
- [Actualización 1905 de Azure Stack](azure-stack-release-notes-1905.md)

## <a name="hotfixes"></a>Revisiones

En ocasiones, Microsoft ofrece revisiones para Azure Stack que solucionan un determinado problema que suele ser de naturaleza preventiva o urgente.  Todas las revisiones se publican con su correspondiente artículo de Microsoft Knowledge Base que detalla el problema, la causa y la resolución.

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe la directiva de mantenimiento para sistemas integrados de Azure Stack, lo que debe hacer para mantener el sistema en un estado admitido y cómo obtener soporte técnico.

## <a name="keep-your-system-under-support"></a>Mantenimiento del sistema dentro del soporte técnico

Para que su instancia de Azure Stack permanezca en un estado admitido, la instancia debe ejecutar la versión de actualización más reciente o cualquiera de las dos versiones de actualización anteriores.

Las revisiones no se consideran versiones de actualización importantes. Si a su instancia le faltan *más de dos actualizaciones* de Azure Stack, se considera que no cumple los requisitos. Para recibir soporte técnico, deberá actualizarla al menos a la versión mínima admitida.

Por ejemplo, si la versión de actualización disponible más reciente es la 1904 y las dos actualizaciones anteriores eran las versiones 1903 y 1902, estas dos últimas versiones siguen optando a recibir soporte técnico. Sin embargo, la versión 1901 queda fuera del soporte técnico. La directiva es válida cuando no hay ninguna versión durante uno o dos meses. Por ejemplo, si la versión actual es la 1807 y no hubo ninguna versión 1806, las dos actualizaciones anteriores, 1805 y 1804, permanecen dentro del soporte técnico.

Las actualizaciones de software de Microsoft no son acumulativas y necesitan la revisión o actualización anteriores como requisito previo. Si decide aplazar una o varias actualizaciones, tenga en cuenta el tiempo de ejecución total si quiere llegar hasta la versión más reciente.

## <a name="get-support"></a>Obtención de soporte técnico

Azure Stack sigue el mismo proceso de soporte técnico que Azure. Los clientes de empresa pueden seguir el proceso descrito en [Creación de una solicitud de soporte técnico de Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Si es cliente de un proveedor de servicios en la nube (CSP), póngase en contacto con el CSP para recibir soporte técnico. Para más información, consulte [Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Pasos siguientes

- [Administrar las actualizaciones en Azure Stack](https://docs.microsoft.com/azure-stack/operator/azure-stack-updates)
