---
title: Notas de la versión de Azure Stack 1907 | Microsoft Docs
description: Obtenga información sobre la actualización 1907 de los sistemas integrados de Azure Stack, como novedades, problemas conocidos y dónde se puede descargar dicha actualización.
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
ms.date: 07/31/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/31/2019
ms.openlocfilehash: de6d45aa18dab4f6a5e4c7c772732242e296aa3a
ms.sourcegitcommit: bf4d265a3522cbfdd9dd295a0f4ad0daf2ed5eca
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/31/2019
ms.locfileid: "68692068"
---
# <a name="azure-stack-1907-update"></a>Actualización de Azure Stack 1907

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el contenido de la actualización 1907. La actualización incluye novedades, mejoras y correcciones para esta versión de Azure Stack.

> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 1907 de Azure Stack es **1.1907.0.20**.

### <a name="update-type"></a>Tipo de actualización

El tipo de compilación de la actualización 1907 de Azure Stack es **Rápida**. Para obtener más información sobre cómo actualizar los tipos de compilación, consulte el artículo [Administración de actualizaciones en Azure Stack](azure-stack-updates.md). Según las pruebas internas, el tiempo esperado que tarda en completarse la actualización 1907 es de unas 13 horas.

- Por lo general, los tiempos de ejecución de actualización exactos dependen de la capacidad que usen en el sistema las cargas de trabajo de inquilino, de la conectividad de red del sistema (si está conectado a Internet) y de las configuración de hardware del sistema.
- Los tiempos de ejecución superiores a lo esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización.
- Esta aproximación del tiempo de ejecución es específica para la actualización 1907 y no se debe comparar con otras actualizaciones de Azure Stack.

## <a name="whats-in-this-update"></a>Novedades de esta actualización

<!-- The current theme (if any) of this release. -->

### <a name="whats-new"></a>Novedades

<!-- What's new, also net new experiences and features. -->

- Versión de disponibilidad general del servicio de recopilación de registros de diagnóstico de Azure Stack para facilitar y mejorar la colección de registros de diagnóstico. El servicio de recopilación de registros de diagnóstico de Azure Stack proporciona una manera simplificada de recopilar y compartir registros de diagnóstico con los servicios de asistencia al cliente (CSS) de Microsoft. Este servicio de recopilación de registros de diagnóstico proporciona una experiencia de usuario nueva en el portal de administración de Azure Stack que permite que los operadores configuren la carga automática de los registros de diagnóstico a un blob de almacenamiento cuando surgen ciertas alertas críticas o que realicen la misma operación a petición. Para más información, consulte el artículo [Recopilación de registros de diagnóstico](azure-stack-diagnostic-log-collection-overview.md).

- Versión de disponibilidad general de la validación de la infraestructura de red de Azure Stack como parte de la herramienta de validación de Azure Stack **Test-AzureStack**. La infraestructura de red de Azure Stack será parte de **Test-AzureStack**, para identificar si se produce un error en la infraestructura de red de Azure Stack. La prueba revisa la conectividad de la infraestructura de red al omitir la red definida por software de Azure Stack. Muestra la conectividad de una VIP pública a los reenviadores DNS configurados, los servidores NTP y los puntos de conexión de identidad. Además, comprueba la conectividad con Azure cuando se usa Azure AD como el proveedor de identidades o el servidor federado cuando se usa ADFS. Para más información, consulte el artículo sobre la [herramienta de validación de Azure Stack](azure-stack-diagnostic-test.md).

- Se agregó un procedimiento de cambio de secretos internos para cambiar certificados TLS de SQL internos según sea necesario durante una actualización del sistema.

### <a name="improvements"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->

- La hoja de actualización de Azure Stack ahora muestra la hora del **último paso completado** para las actualizaciones activas. Para ver esto, vaya a la hoja de actualización y haga clic en una actualización en ejecución. El **último paso completado** está disponible entonces en la sección **Detalles de ejecución de la actualización**.

- Mejoras en las acciones de operador **Start-AzureStack** y **Stop-AzureStack**. El tiempo para iniciar Azure Stack se redujo en un promedio de 50 %. El tiempo para apagar Azure Stack se redujo en un promedio de 30 %. Los tiempos promedio de inicio y apagado siguen iguales, porque el número de nodos aumenta en una unidad de escalado.

- Se mejoró el control de errores de la herramienta de Marketplace desconectada. Si se produce un error en la descarga o se realiza correctamente de manera parcial cuando se usa **Export-AzSOfflineMarketplaceItem**, aparece un mensaje de error con más detalles sobre el error y los pasos de mitigación, si los hay.

- Se mejoró el rendimiento de la creación de los discos administrados a partir de una instantánea o de un blob en páginas de gran tamaño. Anteriormente, desencadenada un tiempo de expiración agotado cuando se creaba un disco de gran tamaño.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Se mejoró la comprobación de estado de un disco virtual antes de apagar un nodo para evitar que el disco virtual se desasocie de manera inesperada.

- Se mejoró el almacenamiento de los registros internos de las operaciones de administrador. Esto genera un mejor rendimiento y confiabilidad durante las operaciones de administrador al minimizar el consumo de memoria y almacenamiento de los procesos de registro internos. También puede observar mejores tiempos de carga de página de la hoja de actualización en el portal del administrador. Como parte de esta mejora, los registros de actualizaciones con más de 6 meses ya no estarán disponibles en el sistema. Si necesita registros para estas actualizaciones, asegúrese de [descargar el resumen](azure-stack-apply-updates.md) de todas las ejecuciones de actualizaciones anteriores a 6 meses antes de realizar la actualización 1907.

### <a name="changes"></a>Cambios

- Se agregó una regla de salida (HTTPS) nueva para permitir la comunicación del servicio de recopilación de registros de diagnóstico de Azure Stack. Para más información, consulte [Integración del centro de datos de Azure Stack: publicar puntos de conexión](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- El servicio de copia de seguridad de la infraestructura ahora elimina de manera parcial las copias de seguridad cargadas si la ubicación de almacenamiento externo se queda sin capacidad.

- Las copias de seguridad de la infraestructura ya no incluyen una copia de seguridad de los datos de servicios de dominio. Esto solo se aplica a los sistemas que usan Azure Active Directory como el proveedor de identidades.

- Ahora se valida que la imagen que se está ingiriendo a **Proceso -> Imágenes de VM** es del tipo de blob en páginas.

### <a name="fixes"></a>Correcciones

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Se corrigió un problema en el que se consideraba que el publicador, la oferta y la SKU distinguía entre mayúsculas y minúsculas en una plantilla de Resource Manager: la imagen no se capturó para la implementación, a menos que los parámetros de la imagen hubiesen tenido las mismas mayúsculas y minúsculas que el publicador, la oferta y la SKU.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- Se corrigió un problema con las copias de seguridad que presentan el mensaje de error **PartialSucceeded**, debido a los tiempos de expiración durante la copia de seguridad de los metadatos del servicio de almacenamiento.  

- Se corrigió un problema en el que la eliminación de las suscripciones de usuario generó recursos huérfanos.

- Se corrigió un problema en el que el campo de descripción no se guardó al crear una oferta.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- Se corrigió un problema en el que la actualización presenta errores debido a un archivo DLL bloqueado por el host de proveedor de WMI.

- Se corrigió un problema en el servicio de actualización que impedía que las actualizaciones disponibles se mostrarán en el proveedor de recursos o en el icono de actualización. Este problema se encontró en 1906 y se corrigió en la revisión [KB4511282](https://support.microsoft.com/help/4511282/).

- Se corrigió un problema que podía provocar errores en las actualizaciones debido a que el plano de administración era incorrecto a causa de una configuración errónea. Este problema se encontró en 1906 y se corrigió en la revisión [KB4512794](https://support.microsoft.com/help/4512794/).

- Se corrigió un problema que impedía que los usuarios completaran la implementación de imágenes de terceros desde Marketplace. Este problema se encontró en 1906 y se corrigió en la revisión [KB4511259](https://support.microsoft.com/help/4511259/).

- Se corrigió un problema que podía hacer que la creación de VM desde imágenes administradas presentaran un error debido al bloqueo del servicio del administrador de imágenes del usuario. Este problema se encontró en 1906 y se corrigió en la revisión [KB4512794](https://support.microsoft.com/help/4512794/).

- Se corrigió un problema en el que las operaciones CRUD de VM podían presentar un error debido a que la caché de la puerta de enlace de una aplicación no se actualizada según lo esperado. Este problema se encontró en 1906 y se corrigió en la revisión [KB4513119](https://support.microsoft.com/en-us/help/4513119/).

- Se corrigió un problema en el proveedor de recursos de estado que afectaba la disponibilidad de las hojas de alerta y región en el portal del administrador. Este problema se encontró en 1906 y se corrigió en la revisión [KB4512794](https://support.microsoft.com/help/4512794).

## <a name="security-updates"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack, consulte [Actualizaciones de seguridad de Azure Stack](azure-stack-release-notes-security-updates-1907.md).

## <a name="update-planning"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](azure-stack-release-notes-known-issues-1907.md)
- [Actualizaciones de seguridad](azure-stack-release-notes-security-updates-1907.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Descarga de la actualización

Puede descargar la actualización de Azure Stack 1907 desde [la página de descarga de Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack para la versión 1906 antes de actualizar Azure Stack a la versión 1907.

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

### <a name="before-applying-the-1907-update"></a>Antes de aplicar la actualización 1907

La versión 1907 de Azure Stack debe aplicarse en la versión 1906 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1906.11.52](https://support.microsoft.com/help/4513119)

### <a name="after-successfully-applying-the-1907-update"></a>Después de aplicar correctamente la actualización 1907

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- No hay ninguna revisión disponible para la versión 1907.

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
