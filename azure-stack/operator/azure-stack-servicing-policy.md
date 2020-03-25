---
title: Directiva de servicio de Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Conozca la directiva de servicio de Azure Stack Hub y cómo mantener un sistema integrado en un estado admitido.
author: sethmanheim
ms.topic: article
ms.date: 02/07/2020
ms.author: sethm
ms.reviewer: harik
ms.lastreviewed: 03/18/2020
ms.openlocfilehash: 9b1459fbb5e484e3fc0588e8c9717f8e09b6e309
ms.sourcegitcommit: 53efd12bf453378b6a4224949b60d6e90003063b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/18/2020
ms.locfileid: "79512408"
---
# <a name="azure-stack-hub-servicing-policy"></a>Directiva de servicio de Azure Stack Hub

En este artículo se describe la directiva de servicio para sistemas integrados de Azure Stack Hub y lo que debe hacer para mantener el sistema en un estado admitido.

## <a name="download-update-packages-for-integrated-systems"></a>Descarga de actualizaciones para sistemas integrados

Microsoft publica actualizaciones mensuales completas, así como revisiones, para tratar problemas específicos.

Las actualizaciones mensuales se hospedan en un punto de conexión seguro de Azure. Puede descargarlas manualmente mediante la [herramienta de descarga de actualizaciones de Azure Stack Hub](https://aka.ms/azurestackupdatedownload). Si la unidad de escalado está conectada, la actualización aparece automáticamente en el portal del administrador como **Actualización disponible**. Las actualizaciones mensuales completas están bien documentadas en cada versión. Para más información acerca de cada versión, puede hacer clic en cualquier versión de la sección [Ritmo de lanzamiento de las actualizaciones](#update-package-release-cadence) de este artículo.

Las actualizaciones de revisiones se hospedan en el mismo punto de conexión seguro de Azure. Puede descargarlas mediante los vínculos insertados en cada uno de los artículos de KB de revisión respectivos; por ejemplo, [Revisión de Azure Stack Hub 1.1809.12.114](https://support.microsoft.com/help/4481548/azure-stack-hotfix-1-1809-12-114). Al igual que con los paquetes de actualización mensuales completos, los operadores de Azure Stack Hub pueden descargar los archivos .xml, .bin y .exe e importarlos mediante el procedimiento descrito en [Aplicación de actualizaciones en Azure Stack Hub](azure-stack-apply-updates.md). Los operadores de Azure Stack Hub con unidades de escalado conectadas verán que las revisiones aparecen automáticamente en el portal del administrador con el mensaje **Actualización disponible**.

Si la unidad de escalado no está conectada y quiere recibir notificaciones acerca de la versión de cada revisión, suscríbase a las fuentes [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss) o [ATOM](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom) indicadas en cada versión.

## <a name="update-package-types"></a>Tipos de actualizaciones

Hay dos tipos de actualizaciones para los sistemas integrados:

- **Actualizaciones de software de Microsoft**. Microsoft es responsable del ciclo de vida de servicio de un extremo a otro de las actualizaciones de software de Microsoft. Estos paquetes pueden incluir las últimas actualizaciones de seguridad de Windows Server, actualizaciones que no son de seguridad y actualizaciones de características de Azure Stack Hub. Puede descargar estas actualizaciones directamente de Microsoft.

- **Actualizaciones suministradas por el proveedor de hardware OEM**. Los asociados de hardware de Azure Stack Hub son responsables del ciclo de vida de servicio de un extremo a otro (lo que incluye las instrucciones) de las actualizaciones de controladores y firmware relacionados con el hardware. Asimismo, poseen y mantienen las instrucciones de todo el software y el hardware del host de ciclo de vida del hardware. El proveedor de hardware OEM hospeda estas actualizaciones en su propio sitio de descarga.

## <a name="update-package-release-cadence"></a>Ritmo de lanzamientos de las actualizaciones

Microsoft espera lanzar actualizaciones de software a un ritmo mensual. Sin embargo, puede haber varios lanzamientos de actualizaciones en un mes, o ninguno. Los proveedores de hardware de OEM lanzan sus actualizaciones en función de la necesidad.

Busque documentación sobre cómo planear y administrar actualizaciones y cómo determinar la versión actual en [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md).

Para obtener información sobre una actualización concreta, incluido cómo descargarla, vea las notas de versión de la actualización:

- [Actualización de Azure Stack Hub 2002](/azure-stack/operator/release-notes?view=azs-2002)
- [Actualización de Azure Stack Hub 1910](/azure-stack/operator/release-notes?view=azs-1910)
- [Actualización de Azure Stack Hub 1908](/azure-stack/operator/release-notes?view=azs-1908)
- [Actualización de Azure Stack Hub 1907](/azure-stack/operator/release-notes?view=azs-1907)

## <a name="hotfixes"></a>Revisiones

En ocasiones, Microsoft ofrece revisiones para Azure Stack Hub que solucionan un determinado problema que suele ser de naturaleza preventiva o urgente. Todas las revisiones se publican con su correspondiente artículo de Microsoft Knowledge Base que detalla el problema, la causa y la resolución.

Las revisiones se descargan e instalan del mismo modo que los paquetes de actualización completos normales de Azure Stack Hub. Pero, a diferencia de una actualización completa, las revisiones pueden instalarse en minutos. Se recomienda que los operadores de Azure Stack Hub establezcan ventanas de mantenimiento al instalar revisiones. Las revisiones actualizan la versión de la nube de Azure Stack Hub para que se pueda determinar fácilmente si se han aplicado. Se ofrece una revisión distinta para cada versión de Azure Stack Hub que aún reciba soporte técnico. Cada revisión para una iteración concreta es acumulativa e incluye las actualizaciones anteriores de esa misma versión. Puede obtener más información sobre la aplicabilidad de una revisión determinada en el correspondiente artículo de Knowledge Base. Consulte los vínculos de notas de la versión en la sección anterior.

Para más información acerca de las revisiones disponibles actualmente, consulte las notas de la versión de la actualización:

- [Revisión de Azure Stack Hub 2002](/azure-stack/operator/release-notes?view=azs-2002#hotfixes)
- [Revisión de Azure Stack Hub 1910](/azure-stack/operator/release-notes?view=azs-1910#hotfixes-1)
- [Revisión de Azure Stack Hub 1908](/azure-stack/operator/release-notes?view=azs-1908#hotfixes-2)
- [Revisión de Azure Stack Hub 1907](/azure-stack/operator/release-notes?view=azs-1907#hotfixes-3)

## <a name="keep-your-system-under-support"></a>Mantenimiento del sistema dentro del soporte técnico

::: moniker range="azs-2002"

> [!IMPORTANT]  
> Con la actualización de Azure Stack Hub 2002, Microsoft amplía temporalmente las instrucciones de directiva de soporte técnico de Azure Stack Hub. Estamos trabajando con clientes de todo el mundo que tienen que hacer frente a la COVID-19 y que deben tomar decisiones importantes sobre sus sistemas de Azure Stack Hub, cómo se actualizan y administran, para garantizar que las operaciones empresariales del centro de datos continúan funcionando con normalidad. Para dar soporte técnico a nuestros clientes, Microsoft ofrece una extensión de cambio de directiva de soporte técnico temporal que incluye tres versiones de actualización anteriores. Como resultado, se admitirán la actualización 2002 recién publicada y cualquiera de las tres versiones de actualización anteriores (por ejemplo, 1910, 1908 y 1907).

::: moniker-end

Para que su instancia de Azure Stack Hub permanezca en un estado admitido, la instancia debe ejecutar la versión de actualización más reciente o cualquiera de las dos versiones de actualización anteriores.

Las revisiones no se consideran versiones de actualización importantes. Si a su instancia le faltan *más de dos actualizaciones* de Azure Stack Hub, se considera que no cumple los requisitos. Para recibir soporte técnico, deberá actualizarla al menos a la versión mínima admitida.

Por ejemplo, si la versión de actualización disponible más reciente es la 1904 y las dos actualizaciones anteriores eran las versiones 1903 y 1902, estas dos últimas versiones siguen optando a recibir soporte técnico. Sin embargo, la versión 1901 queda fuera del soporte técnico. La directiva es válida cuando no hay ninguna versión durante uno o dos meses. Por ejemplo, si la versión actual es la 1807 y no hubo ninguna versión 1806, las dos actualizaciones anteriores, 1805 y 1804, permanecen dentro del soporte técnico.

Las actualizaciones de software de Microsoft no son acumulativas y necesitan la revisión o actualización anteriores como requisito previo. Si decide aplazar una o varias actualizaciones, tenga en cuenta el tiempo de ejecución total si quiere llegar hasta la versión más reciente.

## <a name="get-support"></a>Obtención de soporte técnico

Azure Stack Hub sigue el mismo proceso de soporte técnico que Azure. Los clientes de empresa pueden seguir el proceso descrito en [Creación de una solicitud de soporte técnico de Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Si es cliente de un proveedor de soluciones en la nube (CSP), póngase en contacto con el CSP para recibir soporte técnico. Para más información, consulte [Preguntas más frecuentes de soporte técnico de Azure](https://azure.microsoft.com/support/faq/).

Para más información sobre la solución de problemas de actualización, consulte [Procedimientos recomendados para la solución de problemas de revisión y actualización de Azure Stack Hub](azure-stack-updates-troubleshoot.md).

## <a name="next-steps"></a>Pasos siguientes

- [Administrar las actualizaciones en Azure Stack Hub](azure-stack-updates.md)
- [Procedimientos recomendados para la solución de problemas de revisión y actualización de Azure Stack Hub](azure-stack-updates-troubleshoot.md)
