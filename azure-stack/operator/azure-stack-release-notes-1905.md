---
title: Notas de la versión de Azure Stack 1905 | Microsoft Docs
description: Obtenga información sobre la actualización 1905 de los sistemas integrados de Azure Stack, como novedades, problemas conocidos y dónde se puede descargar dicha actualización.
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
ms.date: 06/10/2019
ms.author: sethm
ms.reviewer: ''
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 018c5e2a6e099fb0a0ed7bc164ef26728a9d8ed3
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828241"
---
# <a name="azure-stack-1905-update"></a>Actualización 1905 de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el contenido del paquete de actualización 1905. La actualización incluye novedades, mejoras y correcciones para esta versión de Azure Stack. Este archivo contiene la siguiente información:

- [Descripción de las novedades, las mejoras, las correcciones y las actualizaciones de seguridad](#whats-in-this-update)
- [Planeación de la actualización](#update-planning)

> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 1905 de Azure Stack es **1.1905.0.40**.

### <a name="update-type"></a>Tipo de actualización

El tipo de compilación de la actualización 1905 de Azure Stack es **Completa**. Para obtener más información sobre cómo actualizar los tipos de compilación, consulte el artículo [Administración de actualizaciones en Azure Stack](azure-stack-updates.md).

## <a name="whats-in-this-update"></a>Novedades de esta actualización

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Con esta actualización, el motor de actualización de Azure Stack puede actualizar el firmware de los nodos de la unidad de escalado. Esto requiere un paquete de actualización compatible de los asociados de hardware. Póngase en contacto con el asociado de hardware para obtener más información sobre la disponibilidad.

- Windows Server 2019 ahora se admite y está disponible para distribuirse a través de Azure Stack Marketplace.
Con esta actualización, Windows Server 2019 puede activarse ahora correctamente en un host de 2016.

### <a name="improvements"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->
- Como parte de la aplicación de TLS 1.2 en Azure Stack, las siguientes extensiones se han actualizado a estas versiones:

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  Descargue estas versiones de las extensiones inmediatamente, para que las nuevas implementaciones de la extensión no produzcan un error cuando se aplique TLS 1.2 en una versión futura. Siempre establezca **autoupdateminorversion=true** para que las actualizaciones de versiones secundarias de las extensiones (por ejemplo, 1.8 a 1.9) se realicen automáticamente.

- Una nueva **información general de la ayuda y soporte técnico** en Azure Stack Portal facilita a los operadores la comprobación de sus opciones de soporte técnico, la obtención de ayuda experta o más información acerca de Azure Stack. En los sistemas integrados, la creación de una solicitud de soporte técnico preseleccionará el servicio Azure Stack. Recomendamos encarecidamente que los clientes utilicen esta experiencia para enviar vales en lugar de utilizar Azure Portal público. Para más información, consulte [Azure Stack Help and Support](azure-stack-help-and-support-overview.md) (Ayuda y soporte técnico de Azure Stack).
- Cuando se incorporan varias instancias de Azure Active Directory (a través de [este proceso](azure-stack-enable-multitenancy.md)), es posible que no se ejecute de nuevo el script cuando se producen ciertas actualizaciones, o cuando los cambios en la autorización de la entidad de servicio de AAD hacen que falten derechos. Esto puede causar varios problemas, desde el acceso bloqueado a determinadas características, pasando por errores más discretos que son difíciles de rastrear, hasta el problema original. Para evitar esto, la versión 1905 introduce una nueva característica que comprueba estos permisos y crea una alerta cuando se encuentran ciertos problemas de configuración. Esta validación se ejecuta cada hora y muestra las acciones de corrección necesarias para solucionar el problema. La alerta se cierra una vez que todos los inquilinos están en un estado correcto.

- Mejora de la confiabilidad de las operaciones de copia de seguridad de la infraestructura durante la conmutación por error del servicio. 

- Está disponible una nueva versión del [complemento de Azure Stack Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios) que utiliza las [bibliotecas de autenticación de Azure Active Directory](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) para la autenticación. El complemento ahora admite también las implementaciones basadas en Azure Active Directory (AAD) y en los Servicios de federación de Active Directory (AD FS) de Azure Stack. Para más información, consulte el sitio [de complementos de Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details).

- Se ha publicado un nuevo perfil híbrido **2019-03-01-Hybrid** que admite todas las características más recientes de Azure Stack. Tanto Azure PowerShell como la CLI de Azure admiten el perfil **2019-03-01-Hybrid**. Los SDK de .NET, Ruby, Node.js, Go y Python han publicado paquetes que admiten el perfil **2019-03-01-Hybrid**. La documentación respectiva y algunos ejemplos se han actualizado para reflejar los cambios.

- El [SDK de Node.js](https://www.npmjs.com/search?q=2019-03-01-hybrid) ahora admite los perfiles de API. Se publican los paquetes que admiten el perfil **2019-03-01-Hybrid**.

- Ahora hay una extensión de Azure Stack que funciona en Visual Studio Code. Con la extensión **Azure Account**, los desarrolladores pueden dirigirse a Azure Stack mediante el inicio de sesión y la visualización de suscripciones, así como una serie de otros servicios. La extensión Azure Account funciona tanto en entornos de AAD como de ADFS y solo requiere un pequeño cambio en la configuración de usuario de Visual Studio Code para introducir los valores de metadatos de Azure Stack. Para más información, [consulte aquí la documentación](../user/azure-stack-dev-start-vscode-azure.md).

### <a name="changes"></a>Cambios

- Para aumentar la confiabilidad y disponibilidad durante escenarios de mantenimiento planeados y no planeados, Azure Stack agrega una instancia de rol de infraestructura adicional para los servicios de dominio.

- Con esta actualización, durante las operaciones de reparación y adición de nodos, el hardware se valida para garantizar la homogeneidad de los nodos de la unidad de escalado dentro de una unidad de escalado.

- Si no se completan las copias de seguridad programadas y se supera el período de retención definido, el controlador de copias de seguridad de la infraestructura se asegurará de que se conserve al menos una copia de seguridad correcta. 

### <a name="fixes"></a>Correcciones

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Se ha corregido un problema en el que aparecía la advertencia **Compute host agent** (Agente de host de proceso) después de reiniciar un nodo en la unidad de escalado.

- Se han corregido problemas en la administración de Marketplace en el portal de administración que mostraban resultados incorrectos cuando se aplicaban filtros y mostraban nombres de editores duplicados en el filtro de editores. También se han realizado mejoras en el rendimiento para mostrar los resultados más rápidamente.

- Se ha solucionado un problema en la hoja de copia de seguridad disponible que enumeraba una nueva copia de seguridad disponible antes de completar la carga en la ubicación del almacenamiento externo. Ahora la copia de seguridad disponible se mostrará en la lista después de haberse cargado correctamente en la ubicación de almacenamiento. 

<!-- ICM: 114819337; Task: 4408136 -->
- Se ha corregido un problema con la obtención de las claves de recuperación durante la operación de copia de seguridad. 

<!-- Bug: 4525587 -->
- Se ha corregido un problema con la actualización de OEM que mostraba la versión como "no definida" en el portal del operador.

### <a name="security-updates"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack, consulte [Actualizaciones de seguridad de Azure Stack](azure-stack-release-notes-security-updates-1905.md).

## <a name="update-planning"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](azure-stack-release-notes-known-issues-1905.md)
- [Actualizaciones de seguridad](azure-stack-release-notes-security-updates-1905.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](azure-stack-release-notes-checklist.md)

## <a name="download-the-update"></a>Descarga de la actualización

Puede descargar el paquete de actualización 1905 de Azure Stack desde [la página de descarga de Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack para la versión 1904 antes de actualizar Azure Stack a la versión 1905.

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

### <a name="before-applying-the-1905-update"></a>Antes de aplicar la actualización 1905

La versión 1905 de Azure Stack debe aplicarse en la versión 1904 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>Después de aplicar correctamente la actualización 1905

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- Ninguna revisión disponible para la versión 1905.

## <a name="automatic-update-notifications"></a>Notificaciones de actualización automáticas

Los clientes con sistemas que pueden acceder a Internet desde la red de infraestructura verán el mensaje **Actualización disponible** en el portal del operador. Los sistemas sin acceso a Internet pueden descargar e importar el archivo ZIP con el archivo .xml correspondiente.

> [!TIP]  
> Suscríbase a las siguientes fuentes *RRS* o *Atom* para mantenerse al día con las revisiones de Azure Stack:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archived-release-notes"></a>Archivado de notas de la versión

Puede ver [las notas de la versión de las versiones anteriores de Azure Stack en la Galería de TechNet](http://aka.ms/azsarchivedrelnotes). Estas notas de la versión archivadas se proporcionan únicamente con fines de referencia y no implican que estas versiones se admitan. Para obtener información sobre la compatibilidad con Azure Stack, consulte [Directiva de mantenimiento de Azure Stack](azure-stack-servicing-policy.md). Para obtener más ayuda, póngase en contacto con los servicios de asistencia al cliente de Microsoft.

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md).  
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](azure-stack-apply-updates.md).
- Para revisar la directiva de servicio de los sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido, consulte [Directiva de servicio de Azure Stack](azure-stack-servicing-policy.md).  
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](azure-stack-monitor-update.md).  

