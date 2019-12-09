---
title: Directiva de mantenimiento de Azure Stack
titleSuffix: Azure Stack
description: Conozca la directiva de mantenimiento de Azure Stack y cómo mantener un sistema integrado.
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
ms.date: 10/30/2019
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: b874e81afc37684e15dd9cfe79adbf123f126f89
ms.sourcegitcommit: 62283e9826ea78b218f5d2c6c555cc44196b085d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/03/2019
ms.locfileid: "74780667"
---
# <a name="azure-stack-servicing-policy"></a>Directiva de mantenimiento de Azure Stack

En este artículo se describe la directiva de mantenimiento para sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema.

## <a name="download-update-packages-for-integrated-systems"></a>Descarga de actualizaciones para sistemas integrados

Microsoft publica actualizaciones mensuales completas, así como revisiones, para tratar problemas específicos.

Las actualizaciones mensuales se hospedan en un punto de conexión seguro de Azure. Puede descargarlas manualmente mediante la [herramienta de descarga de actualizaciones de Azure Stack](https://aka.ms/azurestackupdatedownload). Si la unidad de escalado está conectada, la actualización aparece automáticamente en el portal del administrador como **Actualización disponible**. Las actualizaciones mensuales completas están bien documentadas en cada versión. Para más información acerca de cada versión, puede hacer clic en cualquier versión de la sección [Ritmo de lanzamiento de las actualizaciones](#update-package-release-cadence) de este artículo.

Las actualizaciones de revisiones se hospedan en el mismo punto de conexión seguro de Azure. Puede descargarlas mediante los vínculos insertados en cada uno de los artículos correspondientes de Knowledge Base de revisiones; por ejemplo, [Revisión de Azure Stack 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). Al igual que con las actualizaciones mensuales completas, los operadores de Azure Stack pueden descargar archivos .xml, .bin y .exe e importarlos mediante el procedimiento descrito en [Aplicación de actualizaciones en Azure Stack](azure-stack-apply-updates.md). Los operadores de Azure Stack con unidades de escalado conectadas verán que las revisiones aparecen automáticamente en el portal del administrador con el mensaje **Actualización disponible**.

Si la unidad de escalado no está conectada y quiere recibir notificaciones acerca de la versión de cada revisión, suscríbase a las fuentes [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) o [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) indicadas en cada versión.

## <a name="update-package-types"></a>Tipos de actualizaciones

Hay dos tipos de actualizaciones para los sistemas integrados:

- **Actualizaciones de software de Microsoft**. Microsoft es responsable del ciclo de vida de servicio de un extremo a otro de las actualizaciones de software de Microsoft. Estas actualizaciones pueden incluir las últimas actualizaciones de seguridad de Windows Server, las actualizaciones que no son de seguridad y las actualizaciones de características de Azure Stack. Puede descargar estas actualizaciones directamente de Microsoft.

- **Actualizaciones suministradas por el proveedor de hardware OEM**. Los asociados de hardware de Azure Stack son responsables del ciclo de vida de servicio de un extremo a otro (lo que incluye las instrucciones) de las actualizaciones de controladores y firmware relacionados con el hardware. Asimismo, poseen y mantienen las instrucciones de todo el software y el hardware del host de ciclo de vida del hardware. El proveedor de hardware OEM hospeda estas actualizaciones en su propio sitio de descarga.

## <a name="update-package-release-cadence"></a>Ritmo de lanzamientos de las actualizaciones

Microsoft espera lanzar actualizaciones de software a un ritmo mensual. Sin embargo, puede haber varios lanzamientos de actualizaciones en un mes, o ninguno. Los proveedores de hardware de OEM lanzan sus actualizaciones en función de la necesidad.

Busque documentación sobre cómo planear y administrar actualizaciones y cómo determinar la versión actual en [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md).

Para obtener información sobre una actualización concreta, incluido cómo descargarla, vea las notas de versión de la actualización:

- [Actualización 1910 de Azure Stack](/azure-stack/operator/release-notes?view=azs-1910)
- [Actualización 1908 de Azure Stack](/azure-stack/operator/release-notes?view=azs-1908)
- [Actualización de Azure Stack 1907](/azure-stack/operator/release-notes?view=azs-1907)
- [Actualización de Azure Stack 1906](/azure-stack/operator/release-notes?view=azs-1906)

## <a name="hotfixes"></a>Revisiones

*Se aplica a: Sistemas integrados de Azure Stack*

En ocasiones, Microsoft ofrece revisiones para Azure Stack que solucionan un determinado problema que suele ser de naturaleza preventiva o urgente. Todas las revisiones se publican con su correspondiente artículo de Microsoft Knowledge Base que detalla el problema, la causa y la resolución.

Las revisiones se descargan e instalan del mismo modo que las actualizaciones completas normales de Azure Stack. Pero, a diferencia de una actualización completa, las revisiones pueden instalarse en minutos. Se recomienda que los operadores de Azure Stack establezcan ventanas de mantenimiento al instalar las revisiones. Las revisiones actualizan la versión de la nube de Azure Stack para se que pueda determinar fácilmente si la revisión se ha aplicado. Se ofrece una revisión independiente para cada versión de Azure Stack siga dentro del soporte técnico. Cada revisión para una iteración concreta es acumulativa e incluye las actualizaciones anteriores de esa misma versión. Puede obtener más información sobre la aplicabilidad de una revisión determinada en el correspondiente artículo de Knowledge Base. Consulte los vínculos de notas de la versión en la sección anterior.

Para más información acerca de las revisiones disponibles actualmente, consulte las notas de la versión de la actualización:

- [Revisión 1910 de Azure Stack](/azure-stack/operator/release-notes?view=azs-1910#hotfixes)
- [Revisión 1908 de Azure Stack](/azure-stack/operator/release-notes?view=azs-1908#hotfixes)
- [Revisión 1907 de Azure Stack](/azure-stack/operator/release-notes?view=azs-1907#hotfixes)
- [Revisión 1906 de Azure Stack](/azure-stack/operator/release-notes?view=azs-1906#hotfixes)

## <a name="keep-your-system-under-support"></a>Mantenimiento del sistema dentro del soporte técnico

Para que su instancia de Azure Stack permanezca en un estado admitido, la instancia debe ejecutar la versión de actualización más reciente o cualquiera de las dos versiones de actualización anteriores.

Las revisiones no se consideran versiones de actualización importantes. Si a su instancia le faltan *más de dos actualizaciones* de Azure Stack, se considera que no cumple los requisitos. Para recibir soporte técnico, deberá actualizarla al menos a la versión mínima admitida.

Por ejemplo, si la versión de actualización disponible más reciente es la 1904 y las dos actualizaciones anteriores eran las versiones 1903 y 1902, estas dos últimas versiones siguen optando a recibir soporte técnico. Sin embargo, la versión 1901 queda fuera del soporte técnico. La directiva es válida cuando no hay ninguna versión durante uno o dos meses. Por ejemplo, si la versión actual es la 1807 y no hubo ninguna versión 1806, las dos actualizaciones anteriores, 1805 y 1804, permanecen dentro del soporte técnico.

Las actualizaciones de software de Microsoft no son acumulativas y necesitan la revisión o actualización anteriores como requisito previo. Si decide aplazar una o varias actualizaciones, tenga en cuenta el tiempo de ejecución total si quiere llegar hasta la versión más reciente.

## <a name="get-support"></a>Obtención de soporte técnico

Azure Stack sigue el mismo proceso de soporte técnico que Azure. Los clientes de empresa pueden seguir el proceso descrito en [Creación de una solicitud de soporte técnico de Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Si es cliente de un proveedor de soluciones en la nube (CSP), póngase en contacto con el CSP para recibir soporte técnico. Para más información, consulte [Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq/).

## <a name="next-steps"></a>Pasos siguientes

- [Administrar las actualizaciones en Azure Stack](azure-stack-updates.md)
