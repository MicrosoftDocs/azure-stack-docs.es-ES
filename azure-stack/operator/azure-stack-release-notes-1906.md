---
title: Notas de la versión de Azure Stack 1906 | Microsoft Docs
description: Obtenga información sobre la actualización 1906 de los sistemas integrados de Azure Stack, como novedades, problemas conocidos y dónde se puede descargar dicha actualización.
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
ms.date: 07/15/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 07/15/2019
monikerRange: azs-1906
ms.openlocfilehash: bf8e32d6395b3ddfc54fe586568b51b76b71dc63
ms.sourcegitcommit: 71d7990a2b21576c44bb2aea13ae2026e9510c55
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/30/2019
ms.locfileid: "70188310"
---
# <a name="azure-stack-1906-update"></a>Actualización de Azure Stack 1906

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el contenido del paquete de actualización 1906. La actualización incluye novedades, mejoras y correcciones para esta versión de Azure Stack.

> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 1906 de Azure Stack es **1.1906.0.30**.

### <a name="update-type"></a>Tipo de actualización

El tipo de compilación de la actualización 1906 de Azure Stack es **Rápida**. Para obtener más información sobre cómo actualizar los tipos de compilación, consulte el artículo [Administración de actualizaciones en Azure Stack](azure-stack-updates.md). El tiempo esperado que tarda en completarse la actualización 1906 es de aproximadamente 10 horas, independientemente del número de nodos físicos de su entorno de Azure Stack. Por lo general, los tiempos de ejecución de actualización exactos dependerán de la capacidad que usen en el sistema las cargas de trabajo de inquilino, de la conectividad de red del sistema (si está conectado a Internet) y de las especificaciones de hardware del sistema. Los tiempos de ejecución superiores al valor esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización. Esta aproximación del runtime es específica para la actualización 1906 y no se debe comparar con otras actualizaciones de Azure Stack.

## <a name="whats-in-this-update"></a>Novedades de esta actualización

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Se ha agregado un cmdlet **Set-TLSPolicy** en el punto de conexión con privilegios (PEP) para aplicar TLS 1.2 en todos los puntos de conexión. Para más información, consulte el artículo sobre los [controles de seguridad de Azure Stack](azure-stack-security-configuration.md).

- Se ha agregado un cmdlet **Get-TLSPolicy** en el punto de conexión con privilegios (PEP) para recuperar la directiva TLS aplicada. Para más información, consulte el artículo sobre los [controles de seguridad de Azure Stack](azure-stack-security-configuration.md).

- Se ha agregado un procedimiento de cambio de secretos internos para cambiar certificados TLS internos según sea necesario durante una actualización del sistema.

- Se ha agregado una medida de seguridad para evitar la expiración de secretos internos. Para ello, se aplica el cambio de secretos internos en caso de omisión de una alerta crítica sobre secretos a punto de expirar. No se debería confiar en esta medida como un procedimiento de funcionamiento normal. La rotación de secretos debería planearse durante una ventana de mantenimiento. Para más información, consulte [Cambio de secretos en Azure Stack](azure-stack-rotate-secrets.md).

- Visual Studio Code ahora es compatible con la implementación de Azure Stack mediante AD FS.

### <a name="improvements"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->

- El cmdlet **Get-GraphApplication** del punto de conexión con privilegios ahora muestra la huella digital del certificado usado actualmente. Esto mejora la administración de certificados para las entidades de servicio cuando se implementa Azure Stack con AD FS.

- Se han agregado nuevas reglas de seguimiento de estado para validar la disponibilidad de AD Graph y AD FS, incluida la capacidad de generar alertas.

- Mejoras en la confiabilidad del proveedor de recursos de copia de seguridad cuando el servicio de copia de seguridad de infraestructura se mueve a otra instancia.

- Optimización del rendimiento del procedimiento de cambio de secretos externos para proporcionar un tiempo de ejecución uniforme a fin de facilitar la programación de la ventana de mantenimiento.

- El cmdlet **Test-AzureStack** ahora informa sobre los secretos internos que van a expirar (alertas críticas).

- Existe un nuevo parámetro disponible para el cmdlet **Register-CustomAdfs** en el punto de conexión con privilegios, que permite omitir la comprobación de la lista de revocación de certificados al configurar la confianza de federación de AD FS.

- La versión 1906 presenta una mayor visibilidad del progreso de actualización, lo que garantiza que no se produzcan pausas en las actualizaciones. Esto provoca un aumento en el número total de pasos de actualización que se muestran a los operadores en la hoja **Actualización**. Es posible que también observe más pasos de actualización en paralelo que en las actualizaciones anteriores.

#### <a name="networking-updates"></a>Actualizaciones de redes

- Se ha actualizado el tiempo de concesión establecido en el respondedor DHCP para que sea coherente con Azure.

- Se han mejorado las frecuencias de reintento del proveedor de recursos en el caso de error de implementación de los recursos.

- Se ha quitado la opción de SKU **Estándar** del equilibrador de carga y la dirección IP pública, ya que no se admite actualmente.

### <a name="changes"></a>Cambios

- La creación de una experiencia de cuenta de almacenamiento ahora es coherente con Azure.

- Se han cambiado los desencadenadores de alertas para la expiración de secretos internos:
  - Las alertas de advertencia se generan ahora 90 días antes de la expiración de los secretos.
  - Las alertas críticas se generan ahora 30 días antes de la expiración de los secretos.

- Se han actualizado las cadenas del proveedor de recursos de copia de seguridad de infraestructura para que la terminología sea coherente.

### <a name="fixes"></a>Correcciones

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Se ha corregido un problema que provocaba un **error de funcionamiento interno** al cambiar el tamaño de una VM de disco administrado.

- Se ha corregido un problema en que un error de creación de usuario daba lugar a un estado incorrecto del servicio de administración de imágenes, que bloqueaba la eliminación de la imagen errónea y la creación de nuevas imágenes. Esto también se ha corregido en la revisión 1905.

- Las alertas activas sobre los secretos internos a punto de expirar ahora se cierran automáticamente después de la ejecución correcta del cambio de secretos internos.

- Se ha corregido un problema en que la duración de la actualización en la pestaña del historial de actualizaciones recortaba el primer dígito si la actualización se ejecutaba durante más de 99 horas.

- La hoja **Actualización** ahora incluye una opción **Reanudar** para las actualizaciones que no se realizan correctamente.

- En los portales de administradores y usuarios, se ha corregido un problema del Marketplace en que la búsqueda devolvía por error la extensión de Docker, pero no se podía realizar ninguna otra acción, porque no está disponible en Azure Stack.

- Se ha corregido un problema de la interfaz de usuario de implementación de plantillas en que los parámetros no se rellenaban si el nombre de la plantilla empezada con el carácter de subrayado “_”.

- Se corrigió un problema donde la experiencia de creación de conjuntos de escalado de máquinas virtuales proporciona la versión 7.2 basada en CentOS como una opción para la implementación. CentOS 7.2 no está disponible en Azure Stack. Ahora se proporciona Centos 7.5 como la opción de implementación.

- Ahora puede quitar un conjunto de escalado desde la hoja **Conjuntos de escalado de máquinas virtuales**.

## <a name="security-updates"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack, consulte [Actualizaciones de seguridad de Azure Stack](azure-stack-release-notes-security-updates.md).

## <a name="update-planning"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](azure-stack-release-notes-known-issues-1906.md)
- [Actualizaciones de seguridad](azure-stack-release-notes-security-updates.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Descarga de la actualización

Puede descargar la actualización de Azure Stack 1906 desde [la página de descarga de Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack para la versión 1905 antes de actualizar Azure Stack a la versión 1906. Después de la actualización, instale las [revisiones disponibles para 1906](#after-successfully-applying-the-1906-update).

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

### <a name="before-applying-the-1906-update"></a>Antes de aplicar la actualización 1906

La versión 1906 de Azure Stack debe aplicarse en la versión 1905 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Después de aplicar correctamente la actualización 1906

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1906.12.54](https://support.microsoft.com/help/4518365)

## <a name="automatic-update-notifications"></a>Notificaciones de actualización automáticas

Los clientes con sistemas que pueden acceder a Internet desde la red de infraestructura verán el mensaje **Actualización disponible** en el portal del operador. Los sistemas sin acceso a Internet pueden descargar e importar el archivo ZIP con el archivo .xml correspondiente.

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
