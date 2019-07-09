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
ms.date: 06/26/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 8965ad94d1c8576e437e85a9714997f842f7dd50
ms.sourcegitcommit: bcaad8b7db2ea596018d973cb29283d8c6daebfb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/27/2019
ms.locfileid: "67419458"
---
# <a name="azure-stack-servicing-policy"></a>Directiva de mantenimiento de Azure Stack

En este artículo se describe la directiva de mantenimiento para sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido.

## <a name="download-update-packages-for-integrated-systems"></a>Descarga de actualizaciones para sistemas integrados

Microsoft lanzará actualizaciones mensuales completas, así como revisiones, para tratar problemas específicos.

Las actualizaciones mensuales se hospedan en un punto de conexión seguro de Azure. Puede descargarlas manualmente mediante la [herramienta de descarga de actualizaciones de Azure Stack](https://aka.ms/azurestackupdatedownload). Si la unidad de escalado está conectada, la actualización aparece automáticamente en el portal del administrador como **Actualización disponible**. Las actualizaciones mensuales completas están bien documentadas en cada versión. Para más información acerca de cada versión, puede hacer clic en cualquier versión de la sección [Ritmo de lanzamiento de las actualizaciones](#update-package-release-cadence) de este artículo.

Las actualizaciones de revisiones se hospedan en el mismo punto de conexión seguro de Azure. Puede descargarlas mediante los vínculos insertados en cada uno de los artículos correspondientes de Knowledge Base de revisiones; por ejemplo, [Revisión de Azure Stack 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). Al igual que con las actualizaciones mensuales completas, los operadores de Azure Stack pueden descargar archivos .xml, .bin y .exe e importarlos mediante el procedimiento descrito en [Aplicación de actualizaciones en Azure Stack](azure-stack-apply-updates.md). Los operadores de Azure Stack con unidades de escalado conectadas verán que las revisiones aparecen automáticamente en el portal del administrador con el mensaje **Actualización disponible**.

Si la unidad de escalado no está conectada y le gustaría recibir notificaciones acerca de cada versión de revisión, suscríbase a las fuentes [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) o [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) indicadas en cada versión.  

## <a name="update-package-types"></a>Tipos de actualizaciones

Hay dos tipos de actualizaciones para los sistemas integrados:

- **Actualizaciones de software de Microsoft**. Microsoft es responsable del ciclo de vida de servicio de un extremo a otro de las actualizaciones de software de Microsoft. Estas actualizaciones pueden incluir las últimas actualizaciones de seguridad de Windows Server, las actualizaciones que no son de seguridad y las actualizaciones de características de Azure Stack. Puede descargar estas actualizaciones directamente de Microsoft.

- **Actualizaciones suministradas por el proveedor de hardware OEM**. Los asociados de hardware de Azure Stack son responsables del ciclo de vida de servicio de un extremo a otro (lo que incluye las instrucciones) de las actualizaciones de controladores y firmware relacionados con el hardware. Asimismo, poseen y mantienen las instrucciones de todo el software y el hardware del host de ciclo de vida del hardware. El proveedor de hardware OEM hospeda estas actualizaciones en su propio sitio de descarga.

## <a name="update-package-release-cadence"></a>Ritmo de lanzamientos de las actualizaciones

Microsoft espera lanzar actualizaciones de software a un ritmo mensual. Sin embargo, puede haber varios lanzamientos de actualizaciones en un mes, o ninguno. Los proveedores de hardware de OEM lanzan sus actualizaciones en función de la necesidad.

Busque documentación sobre cómo planear y administrar actualizaciones y cómo determinar la versión actual en [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md).

Para obtener información sobre una actualización concreta, incluido cómo descargarla, vea las notas de versión de la actualización:

- [Actualización de Azure Stack 1906](azure-stack-release-notes-1906.md)
- [Actualización 1905 de Azure Stack](azure-stack-release-notes-1905.md)
- [Actualización de Azure Stack 1904](azure-stack-release-notes-1904.md)
- [Actualización de Azure Stack 1903](azure-stack-update-1903.md)

## <a name="hotfixes"></a>Revisiones

En ocasiones, Microsoft ofrece revisiones para Azure Stack que solucionan un determinado problema que suele ser de naturaleza preventiva o urgente.  Todas las revisiones se publican con su correspondiente artículo de Microsoft Knowledge Base que detalla el problema, la causa y la resolución.

Para obtener más información y vínculos a las revisiones específicas, consulte los vínculos de notas de la versión en la sección anterior.

Las revisiones se descargan e instalan del mismo modo que las actualizaciones completas normales de Azure Stack. Pero, a diferencia de una actualización completa, las revisiones pueden instalarse en minutos. Se recomienda que los operadores de Azure Stack establezcan ventanas de mantenimiento al instalar las revisiones. Las revisiones actualizan la versión de la nube de Azure Stack para se que pueda determinar fácilmente si la revisión se ha aplicado. Se ofrece una revisión independiente para cada versión de Azure Stack siga dentro del soporte técnico. Cada revisión para una iteración concreta es acumulativa e incluye las actualizaciones anteriores de esa misma versión. Puede obtener más información sobre la aplicabilidad de una revisión determinada en el correspondiente artículo de Knowledge Base. Consulte los vínculos de notas de la versión en la sección anterior.  

## <a name="keep-your-system-under-support"></a>Mantenimiento del sistema dentro del soporte técnico

Para continuar recibiendo soporte técnico, debe mantener actualizada la implementación de Azure Stack. La directiva de aplazamiento de actualizaciones indica que, para que la implementación de Azure Stack siga optando a recibir soporte técnico, debe ejecutar la versión de actualización más reciente o una de las dos versiones de actualización anteriores. Las revisiones no se consideran versiones de actualización importantes. Si a la nube de Azure Stack se retrasa en *más de dos actualizaciones*, se considera fuera de cumplimiento y debe actualizarse con, al menos, la versión mínima admitida para recibir soporte técnico.

Por ejemplo, si la versión de actualización disponible más reciente es la 1904 y las dos actualizaciones anteriores eran las versiones 1903 y 1902, estas dos últimas versiones siguen optando a recibir soporte técnico. Sin embargo, la versión 1901 queda fuera del soporte técnico. La directiva es válida cuando no hay ninguna versión durante uno o dos meses. Por ejemplo, si la versión actual es la 1807 y no hubo ninguna versión 1806, las dos actualizaciones anteriores, 1805 y 1804, permanecen dentro del soporte técnico.

Las actualizaciones de software de Microsoft no son acumulativas y necesitan la revisión o actualización anteriores como requisito previo. Si decide aplazar una o varias actualizaciones, tenga en cuenta el tiempo de ejecución total si quiere llegar hasta la versión más reciente.

## <a name="get-support"></a>Obtención de soporte técnico

Azure Stack sigue el mismo proceso de soporte técnico que Azure. Los clientes de empresa pueden seguir el proceso descrito en [Creación de una solicitud de soporte técnico de Azure](/azure/azure-supportability/how-to-create-azure-support-request). Si es cliente de un proveedor de servicios en la nube (CSP), póngase en contacto con el CSP para recibir soporte técnico.  Para más información, consulte [Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Pasos siguientes

- [Administrar las actualizaciones en Azure Stack](azure-stack-updates.md)