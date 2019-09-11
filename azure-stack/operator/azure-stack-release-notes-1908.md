---
title: Notas de la versión de Azure Stack 1908 | Microsoft Docs
description: Obtenga información sobre la actualización 1908 de los sistemas integrados de Azure Stack, como novedades, problemas conocidos y dónde se puede descargar dicha actualización.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/30/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 08/30/2019
monikerRange: azs-1908
ms.openlocfilehash: fbf4983f6f7d2f88da70552ae182792ee316994a
ms.sourcegitcommit: 314fd74caf356b157583d38d2b8b1dee30408b7d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/03/2019
ms.locfileid: "70234957"
---
# <a name="azure-stack-1908-update"></a>Actualización de Azure Stack 1908

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el contenido de la actualización 1908. La actualización incluye novedades, mejoras y correcciones para esta versión de Azure Stack.

> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="previous-release-notes"></a>Notas de la versión anteriores

A partir de la versión 1908, las versiones anteriores de las notas de la versión ya no están visibles en la tabla de contenido de la izquierda. Para acceder a ellas, seleccione otro artículo (por ejemplo, [Introducción a Azure Stack](azure-stack-overview.md)) y, a continuación, seleccione 1905, 1906, 1907 o 1908 en el selector de versiones de la parte superior de dicha tabla. Para las versiones anteriores de las notas de la versión, consulte la sección [Archivado de notas de la versión](#archived-release-notes).

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 1908 de Azure Stack es **1.1908.0.20**.

### <a name="update-type"></a>Tipo de actualización

En la versión 1908, el sistema operativo subyacente en el que se ejecuta Azure Stack se ha actualizado a Windows Server 2019. Esto permite conseguir mejoras fundamentales básicas y disponer de la capacidad de incorporar funciones adicionales a Azure Stack en un futuro próximo.

El tipo de compilación de la actualización 1908 de Azure Stack es **Completa**. Como resultado, la actualización 1908 tiene un tiempo de ejecución más largo que las actualizaciones rápidas, como 1906 y 1907. Los tiempos de ejecución exactos para las actualizaciones completas suelen depender del número de nodos que contiene la instancia de Azure Stack, de la capacidad del sistema que usan las cargas de trabajo de inquilino, de la conectividad de red del sistema (si está conectado a Internet) y de la configuración del hardware del sistema. La actualización 1908 tuvo los siguientes tiempos de ejecución previstos en nuestras pruebas internas: 4 nodos: 42 horas, 8 nodos: 50 horas, 12 nodos: 60 horas, 16 nodos: 70 horas. Los tiempos de ejecución de la actualización superiores a estos valores previstos son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización.

Para obtener más información sobre los tipos de compilación de actualización, consulte [Administración de actualizaciones en Azure Stack](azure-stack-updates.md).

- Por lo general, los tiempos de ejecución de actualización exactos dependen de la capacidad que usen en el sistema las cargas de trabajo de inquilino, de la conectividad de red del sistema (si está conectado a Internet) y de las configuración de hardware del sistema.
- Los tiempos de ejecución superiores a lo esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización.
- Esta aproximación del tiempo de ejecución es específica para la actualización 1908 y no se debe comparar con otras actualizaciones de Azure Stack.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novedades

<!-- What's new, also net new experiences and features. -->

- Tenga en cuenta que en la versión 1908 el sistema operativo subyacente en el que se ejecuta Azure Stack se ha actualizado a Windows Server 2019. Esto permite conseguir mejoras fundamentales básicas y disponer de la capacidad de incorporar funciones adicionales a Azure Stack en un futuro próximo.
- Ahora todos los componentes de la infraestructura de Azure Stack funcionan en modo FIPS 140-2.


### <a name="improvements"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->
- Se han producido mejoras en el cifrado de datos en reposo de Azure Stack para conservar los secretos en el Módulo de plataforma segura (TPM) de hardware de los nodos físicos.

### <a name="changes"></a>Cambios

- Los proveedores de hardware lanzarán el paquete de extensión de OEM de la versión 2.1 o posterior al mismo tiempo que la versión 1908 de Azure Stack. Este paquete será un requisito previo para la versión 1908 de Azure Stack. Para obtener más información acerca de cómo descargar el paquete de extensión de OEM de la versión 2.1 o posterior, póngase en contacto con el proveedor de hardware del sistema y consulte el artículo sobre [actualizaciones de OEM](azure-stack-update-oem.md#oem-contact-information).  

### <a name="fixes"></a>Correcciones

- Se ha corregido un problema de compatibilidad con futuras actualizaciones de OEM de Azure Stack y un problema con la implementación de máquinas virtuales mediante imágenes de usuario de cliente. Este problema se encontró en la versión 1907 y se corrigió en la revisión [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44).  
- Se ha corregido un problema con la actualización de firmware de OEM y se ha corregido un error de diagnóstico en la prueba de Azure Stack para el estado del anillo de Fabric. Este problema se encontró en la versión 1907 y se corrigió en la revisión [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35).
- Se corrigió un problema con el proceso de actualización de firmware de OEM. Este problema se encontró en la versión 1907 y se corrigió en la revisión [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37).


<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack, consulte [Actualizaciones de seguridad de Azure Stack](azure-stack-release-notes-security-updates.md).

## <a name="update-planning"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](azure-stack-release-notes-known-issues-1908.md)
- [Actualizaciones de seguridad](azure-stack-release-notes-security-updates.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Descarga de la actualización

Puede descargar la actualización de Azure Stack 1908 desde [la página de descarga de Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack para la versión 1907 antes de actualizar Azure Stack a la versión 1908.

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Requisitos previos: Antes de aplicar la actualización 1908

La versión 1908 de Azure Stack debe aplicarse en la versión 1907 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1907.12.44](https://support.microsoft.com/help/4517473)

La actualización Azure Stack 1908 requiere la **versión de OEM 2.1 o posterior para Azure Stack** del proveedor de hardware del sistema. Las actualizaciones de OEM incluyen actualizaciones de controladores y firmware en el hardware del sistema Azure Stack. Para más información sobre la aplicación de actualizaciones de OEM, consulte [Aplicar actualizaciones del fabricante de equipos originales (OEM) de Azure Stack](azure-stack-update-oem.md).

### <a name="after-successfully-applying-the-1908-update"></a>Después de aplicar correctamente la actualización 1908

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- No hay ninguna revisión disponible para la versión 1908.

## <a name="automatic-update-notifications"></a>Notificaciones de actualización automáticas

Los sistemas que pueden acceder a Internet desde la red de infraestructura verán el mensaje **Actualización disponible** en el portal del operador. Los sistemas sin acceso a Internet pueden descargar e importar el archivo ZIP con el archivo .xml correspondiente.

> [!TIP]  
> Suscríbase a las siguientes fuentes *RRS* o *Atom* para mantenerse al día con las revisiones de Azure Stack:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Archivado de notas de la versión

Puede ver [las notas de la versión de las versiones anteriores de Azure Stack en la Galería de TechNet](https://aka.ms/azsarchivedrelnotes). Estas notas de la versión archivadas se proporcionan únicamente con fines de referencia y no implican que estas versiones se admitan. Para obtener información sobre la compatibilidad con Azure Stack, consulte [Directiva de mantenimiento de Azure Stack](azure-stack-servicing-policy.md). Para obtener más ayuda, póngase en contacto con los servicios de asistencia al cliente de Microsoft.

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md).  
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](azure-stack-apply-updates.md).
- Para revisar la directiva de servicio de los sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido, consulte [Directiva de servicio de Azure Stack](azure-stack-servicing-policy.md).  
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](azure-stack-monitor-update.md).
