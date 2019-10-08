---
title: Notas de la versión de Azure Stack | Microsoft Docs
description: Obtenga información sobre las actualizaciones de los sistemas integrados de Azure Stack, como novedades y dónde se puede descargar dicha actualización.
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
ms.date: 09/27/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 08/30/2019
ms.openlocfilehash: 62440c61ee637a0092b1e956b08ba0a9c94d8bab
ms.sourcegitcommit: c2ea4ffb42563c26faaf2993ba7b484bcb6d5cb7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/27/2019
ms.locfileid: "71342924"
---
# <a name="azure-stack-updates-release-notes"></a>Actualizaciones de Azure Stack: notas de la versión

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el contenido de los paquetes de actualización de Azure Stack. La actualización incluye novedades, mejoras y correcciones para esta versión de Azure Stack.

Para tener acceso a las notas de la versión de una versión diferente, use la lista desplegable del selector de versiones encima de la tabla de contenido de la izquierda.

::: moniker range=">=azs-1905"
> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.
::: moniker-end
::: moniker range="<azs-1905"
> [!IMPORTANT]  
> Si a su instancia le faltan más de dos actualizaciones de Azure Stack, se considera que no cumple los requisitos. [Para recibir soporte técnico, deberá actualizarla al menos a la versión mínima admitida.](azure-stack-servicing-policy.md#keep-your-system-under-support) 
::: moniker-end

## <a name="update-planning"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](known-issues.md)
- [Actualizaciones de seguridad](release-notes-security-updates.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](release-notes-checklist.md)

Para obtener ayuda con la solución de problemas de actualizaciones y el proceso de actualización, vea [Solución de problemas de actualizaciones y revisiones para Azure Stack](azure-stack-updates-troubleshoot.md).

<!---------------------------------------------------------->
<!------------------- SUPPORTED VERSIONS ------------------->
<!---------------------------------------------------------->
::: moniker range="azs-1908"
## <a name="1908-build-reference"></a>Referencia de la compilación 1908

El número de compilación de la actualización 1908 de Azure Stack es **1.1908.4.33**.

### <a name="update-type-1908"></a>Tipo de actualización

En la versión 1908, el sistema operativo subyacente en el que se ejecuta Azure Stack se ha actualizado a Windows Server 2019. Esto permite conseguir mejoras fundamentales básicas y disponer de la capacidad de incorporar funciones adicionales a Azure Stack en un futuro próximo.

El tipo de compilación de la actualización 1908 de Azure Stack es **Completa**. Como resultado, la actualización 1908 tiene un tiempo de ejecución más largo que las actualizaciones rápidas, como 1906 y 1907. Los tiempos de ejecución exactos para las actualizaciones completas suelen depender del número de nodos que contiene la instancia de Azure Stack, de la capacidad del sistema que usan las cargas de trabajo de inquilino, de la conectividad de red del sistema (si está conectado a Internet) y de la configuración del hardware del sistema. La actualización 1908 tuvo los siguientes tiempos de ejecución previstos en nuestras pruebas internas: 4 nodos: 42 horas, 8 nodos: 50 horas, 12 nodos: 60 horas, 16 nodos: 70 horas. Los tiempos de ejecución de la actualización superiores a estos valores previstos son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización.

Para obtener más información sobre los tipos de compilación de actualización, consulte [Administración de actualizaciones en Azure Stack](azure-stack-updates.md).

- Por lo general, los tiempos de ejecución de actualización exactos dependen de la capacidad que usen en el sistema las cargas de trabajo de inquilino, de la conectividad de red del sistema (si está conectado a Internet) y de las configuración de hardware del sistema.
- Los tiempos de ejecución superiores a lo esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización.
- Esta aproximación del tiempo de ejecución es específica para la actualización 1908 y no se debe comparar con otras actualizaciones de Azure Stack.

<!-- ## What's in this update -->

<!-- The current theme (if any) of this release. -->

### <a name="whats-new-1908"></a>Novedades

<!-- What's new, also net new experiences and features. -->

- Tenga en cuenta que en la versión 1908 el sistema operativo subyacente en el que se ejecuta Azure Stack se ha actualizado a Windows Server 2019. Esto permite conseguir mejoras fundamentales básicas y disponer de la capacidad de incorporar funciones adicionales a Azure Stack en un futuro próximo.
- Ahora todos los componentes de la infraestructura de Azure Stack funcionan en modo FIPS 140-2.
- Los operadores de Azure Stack pueden quitar ahora los datos de usuario del portal. Para más información, consulte [Eliminación de los datos de usuario del portal de Azure Stack](azure-stack-portal-clear.md).

### <a name="improvements-1908"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->
- Se han producido mejoras en el cifrado de datos en reposo de Azure Stack para conservar los secretos en el Módulo de plataforma segura (TPM) de hardware de los nodos físicos.

### <a name="changes-1908"></a>Cambios

- Los proveedores de hardware lanzarán el paquete de extensión de OEM de la versión 2.1 o posterior al mismo tiempo que la versión 1908 de Azure Stack. Este paquete será un requisito previo para la versión 1908 de Azure Stack. Para obtener más información acerca de cómo descargar el paquete de extensión de OEM de la versión 2.1 o posterior, póngase en contacto con el proveedor de hardware del sistema y consulte el artículo sobre [actualizaciones de OEM](azure-stack-update-oem.md#oem-contact-information).  

### <a name="fixes-1908"></a>Correcciones

- Se ha corregido un problema de compatibilidad con futuras actualizaciones de OEM de Azure Stack y un problema con la implementación de máquinas virtuales mediante imágenes de usuario de cliente. Este problema se encontró en la versión 1907 y se corrigió en la revisión [KB4517473](https://support.microsoft.com/en-us/help/4517473/azure-stack-hotfix-1-1907-12-44).  
- Se ha corregido un problema con la actualización de firmware de OEM y se ha corregido un error de diagnóstico en la prueba de Azure Stack para el estado del anillo de Fabric. Este problema se encontró en la versión 1907 y se corrigió en la revisión [KB4515310](https://support.microsoft.com/en-us/help/4515310/azure-stack-hotfix-1-1907-7-35).
- Se corrigió un problema con el proceso de actualización de firmware de OEM. Este problema se encontró en la versión 1907 y se corrigió en la revisión [KB4515650](https://support.microsoft.com/en-us/help/4515650/azure-stack-hotfix-1-1907-8-37).


<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

## <a name="security-updates-1908"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack, consulte [Actualizaciones de seguridad de Azure Stack](release-notes-security-updates.md).

## <a name="download-the-update-1908"></a>Descarga de la actualización

Puede descargar la actualización de Azure Stack 1908 desde [la página de descarga de Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes-1908"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack para la versión 1907 antes de actualizar Azure Stack a la versión 1908.

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

### <a name="prerequisites-before-applying-the-1908-update"></a>Requisitos previos: Antes de aplicar la actualización 1908

La versión 1908 de Azure Stack debe aplicarse en la versión 1907 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1907.17.54](https://support.microsoft.com/help/4523826)

La actualización Azure Stack 1908 requiere la **versión de OEM 2.1 o posterior para Azure Stack** del proveedor de hardware del sistema. Las actualizaciones de OEM incluyen actualizaciones de controladores y firmware en el hardware del sistema Azure Stack. Para más información sobre la aplicación de actualizaciones de OEM, consulte [Aplicar actualizaciones del fabricante de equipos originales (OEM) de Azure Stack](azure-stack-update-oem.md).

### <a name="after-successfully-applying-the-1908-update"></a>Después de aplicar correctamente la actualización 1908

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- No hay ninguna revisión disponible para la versión 1908.
::: moniker-end

::: moniker range="azs-1907"
## <a name="1907-build-reference"></a>Referencia de la compilación 1907

El número de compilación de la actualización 1907 de Azure Stack es **1.1907.0.20**.

### <a name="update-type-1907"></a>Tipo de actualización

El tipo de compilación de la actualización 1907 de Azure Stack es **Rápida**. Para obtener más información sobre cómo actualizar los tipos de compilación, consulte el artículo [Administración de actualizaciones en Azure Stack](azure-stack-updates.md). Según las pruebas internas, el tiempo esperado que tarda en completarse la actualización 1907 es de unas 13 horas.

- Por lo general, los tiempos de ejecución de actualización exactos dependen de la capacidad que usen en el sistema las cargas de trabajo de inquilino, de la conectividad de red del sistema (si está conectado a Internet) y de las configuración de hardware del sistema.
- Los tiempos de ejecución superiores a lo esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización.
- Esta aproximación del tiempo de ejecución es específica para la actualización 1907 y no se debe comparar con otras actualizaciones de Azure Stack.

## <a name="whats-in-this-update-1907"></a>Novedades de esta actualización

<!-- The current theme (if any) of this release. -->

### <a name="whats-new-1907"></a>Novedades

<!-- What's new, also net new experiences and features. -->

- Versión de disponibilidad general del servicio de recopilación de registros de diagnóstico de Azure Stack para facilitar y mejorar la colección de registros de diagnóstico. El servicio de recopilación de registros de diagnóstico de Azure Stack proporciona una manera simplificada de recopilar y compartir registros de diagnóstico con los servicios de asistencia al cliente (CSS) de Microsoft. Este servicio de recopilación de registros de diagnóstico proporciona una experiencia de usuario nueva en el portal de administración de Azure Stack que permite que los operadores configuren la carga automática de los registros de diagnóstico a un blob de almacenamiento cuando surgen ciertas alertas críticas o que realicen la misma operación a petición. Para más información, consulte el artículo [Recopilación de registros de diagnóstico](azure-stack-diagnostic-log-collection-overview.md).

- Versión de disponibilidad general de la validación de la infraestructura de red de Azure Stack como parte de la herramienta de validación de Azure Stack **Test-AzureStack**. La infraestructura de red de Azure Stack será parte de **Test-AzureStack**, para identificar si se produce un error en la infraestructura de red de Azure Stack. La prueba revisa la conectividad de la infraestructura de red al omitir la red definida por software de Azure Stack. Muestra la conectividad de una VIP pública a los reenviadores DNS configurados, los servidores NTP y los puntos de conexión de identidad. Además, comprueba la conectividad con Azure cuando se usa Azure AD como el proveedor de identidades o el servidor federado cuando se usa ADFS. Para más información, consulte el artículo sobre la [herramienta de validación de Azure Stack](azure-stack-diagnostic-test.md).

- Se agregó un procedimiento de cambio de secretos internos para cambiar certificados TLS de SQL internos según sea necesario durante una actualización del sistema.

### <a name="improvements-1907"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->

- La hoja de actualización de Azure Stack ahora muestra la hora del **último paso completado** para las actualizaciones activas. Para ver esto, vaya a la hoja de actualización y haga clic en una actualización en ejecución. El **último paso completado** está disponible entonces en la sección **Detalles de ejecución de la actualización**.

- Mejoras en las acciones de operador **Start-AzureStack** y **Stop-AzureStack**. El tiempo para iniciar Azure Stack se redujo en un promedio de 50 %. El tiempo para apagar Azure Stack se redujo en un promedio de 30 %. Los tiempos promedio de inicio y apagado siguen iguales, porque el número de nodos aumenta en una unidad de escalado.

- Se mejoró el control de errores de la herramienta de Marketplace desconectada. Si se produce un error en la descarga o se realiza correctamente de manera parcial cuando se usa **Export-AzSOfflineMarketplaceItem**, aparece un mensaje de error con más detalles sobre el error y los pasos de mitigación, si los hay.

- Se mejoró el rendimiento de la creación de los discos administrados a partir de una instantánea o de un blob en páginas de gran tamaño. Anteriormente, desencadenada un tiempo de expiración agotado cuando se creaba un disco de gran tamaño.  

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127669774/home -->
- Se mejoró la comprobación de estado de un disco virtual antes de apagar un nodo para evitar que el disco virtual se desasocie de manera inesperada.

- Se mejoró el almacenamiento de los registros internos de las operaciones de administrador. Esto genera un mejor rendimiento y confiabilidad durante las operaciones de administrador al minimizar el consumo de memoria y almacenamiento de los procesos de registro internos. También puede observar mejores tiempos de carga de página de la hoja de actualización en el portal del administrador. Como parte de esta mejora, los registros de actualizaciones con más de 6 meses ya no estarán disponibles en el sistema. Si necesita registros para estas actualizaciones, asegúrese de [descargar el resumen](azure-stack-apply-updates.md) de todas las ejecuciones de actualizaciones anteriores a 6 meses antes de realizar la actualización 1907.

### <a name="changes-1907"></a>Cambios

- La versión 1907 de Azure Stack contiene una alerta de advertencia que indica a los operadores que se aseguren de actualizar el paquete de OEM de su sistema a la versión 2.1 o posterior antes de actualizar a la versión 1908. Para más información sobre la aplicación de actualizaciones de OEM en Azure Stack, consulte [Aplicar actualizaciones del fabricante de equipos originales (OEM) de Azure Stack](azure-stack-update-oem.md).

- Se agregó una regla de salida (HTTPS) nueva para permitir la comunicación del servicio de recopilación de registros de diagnóstico de Azure Stack. Para más información, consulte [Integración del centro de datos de Azure Stack: publicar puntos de conexión](azure-stack-integrate-endpoints.md#ports-and-urls-outbound).

- El servicio de copia de seguridad de la infraestructura ahora elimina de manera parcial las copias de seguridad cargadas si la ubicación de almacenamiento externo se queda sin capacidad.

- Las copias de seguridad de la infraestructura ya no incluyen una copia de seguridad de los datos de servicios de dominio. Esto solo se aplica a los sistemas que usan Azure Active Directory como el proveedor de identidades.

- Ahora se valida que la imagen que se está ingiriendo a **Proceso -> Imágenes de VM** es del tipo de blob en páginas.

### <a name="fixes-1907"></a>Correcciones

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->
- Se corrigió un problema en el que se consideraba que el publicador, la oferta y la SKU distinguía entre mayúsculas y minúsculas en una plantilla de Resource Manager: la imagen no se capturó para la implementación, a menos que los parámetros de la imagen hubiesen tenido las mismas mayúsculas y minúsculas que el publicador, la oferta y la SKU.

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/129536438/home -->
- Se corrigió un problema con las copias de seguridad que presentan el mensaje de error **PartialSucceeded**, debido a los tiempos de expiración durante la copia de seguridad de los metadatos del servicio de almacenamiento.  

- Se corrigió un problema en el que la eliminación de las suscripciones de usuario generó recursos huérfanos.

- Se corrigió un problema en el que el campo de descripción no se guardó al crear una oferta.

- Se corrigió un problema en el que un usuario con permisos de **solo lectura** podía crear, editar y eliminar recursos. Ahora el usuario solo puede crear recursos cuando se asigna el permiso de **colaborador**. 

<!-- https://icm.ad.msft.net/imp/v3/incidents/details/127772311/home -->
- Se corrigió un problema en el que la actualización presenta errores debido a un archivo DLL bloqueado por el host de proveedor de WMI.

- Se corrigió un problema en el servicio de actualización que impedía que las actualizaciones disponibles se mostrarán en el proveedor de recursos o en el icono de actualización. Este problema se encontró en 1906 y se corrigió en la revisión [KB4511282](https://support.microsoft.com/help/4511282/).

- Se corrigió un problema que podía provocar errores en las actualizaciones debido a que el plano de administración era incorrecto a causa de una configuración errónea. Este problema se encontró en 1906 y se corrigió en la revisión [KB4512794](https://support.microsoft.com/help/4512794/).

- Se corrigió un problema que impedía que los usuarios completaran la implementación de imágenes de terceros desde Marketplace. Este problema se encontró en 1906 y se corrigió en la revisión [KB4511259](https://support.microsoft.com/help/4511259/).

- Se corrigió un problema que podía hacer que la creación de VM desde imágenes administradas presentaran un error debido al bloqueo del servicio del administrador de imágenes del usuario. Este problema se encontró en 1906 y se corrigió en la revisión [KB4512794](https://support.microsoft.com/help/4512794/).

- Se corrigió un problema en el que las operaciones CRUD de VM podían presentar un error debido a que la caché de la puerta de enlace de una aplicación no se actualizada según lo esperado. Este problema se encontró en 1906 y se corrigió en la revisión [KB4513119](https://support.microsoft.com/en-us/help/4513119/).

- Se corrigió un problema en el proveedor de recursos de estado que afectaba la disponibilidad de las hojas de alerta y región en el portal del administrador. Este problema se encontró en 1906 y se corrigió en la revisión [KB4512794](https://support.microsoft.com/help/4512794).

## <a name="security-updates-1907"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack, consulte [Actualizaciones de seguridad de Azure Stack](release-notes-security-updates.md).

## <a name="update-planning-1907"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](known-issues.md)
- [Actualizaciones de seguridad](release-notes-security-updates.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](release-notes-checklist.md)

## <a name="download-the-update-1907"></a>Descarga de la actualización

Puede descargar la actualización de Azure Stack 1907 desde [la página de descarga de Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes-1907"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack para la versión 1906 antes de actualizar Azure Stack a la versión 1907.

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

### <a name="before-applying-the-1907-update"></a>Antes de aplicar la actualización 1907

La versión 1907 de Azure Stack debe aplicarse en la versión 1906 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1906.13.56](https://support.microsoft.com/help/4520375)

### <a name="after-successfully-applying-the-1907-update"></a>Después de aplicar correctamente la actualización 1907

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1907.17.54](https://support.microsoft.com/help/4523826)
::: moniker-end

::: moniker range="azs-1906"
## <a name="1906-build-reference"></a>Referencia de la compilación 1906

El número de compilación de la actualización 1906 de Azure Stack es **1.1906.0.30**.

### <a name="update-type-1906"></a>Tipo de actualización

El tipo de compilación de la actualización 1906 de Azure Stack es **Rápida**. Para obtener más información sobre cómo actualizar los tipos de compilación, consulte el artículo [Administración de actualizaciones en Azure Stack](azure-stack-updates.md). El tiempo esperado que tarda en completarse la actualización 1906 es de aproximadamente 10 horas, independientemente del número de nodos físicos de su entorno de Azure Stack. Por lo general, los tiempos de ejecución de actualización exactos dependerán de la capacidad que usen en el sistema las cargas de trabajo de inquilino, de la conectividad de red del sistema (si está conectado a Internet) y de las especificaciones de hardware del sistema. Los tiempos de ejecución superiores al valor esperado son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización. Esta aproximación del runtime es específica para la actualización 1906 y no se debe comparar con otras actualizaciones de Azure Stack.

## <a name="whats-in-this-update-1906"></a>Novedades de esta actualización

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Se ha agregado un cmdlet **Set-TLSPolicy** en el punto de conexión con privilegios (PEP) para aplicar TLS 1.2 en todos los puntos de conexión. Para más información, consulte el artículo sobre los [controles de seguridad de Azure Stack](azure-stack-security-configuration.md).

- Se ha agregado un cmdlet **Get-TLSPolicy** en el punto de conexión con privilegios (PEP) para recuperar la directiva TLS aplicada. Para más información, consulte el artículo sobre los [controles de seguridad de Azure Stack](azure-stack-security-configuration.md).

- Se ha agregado un procedimiento de cambio de secretos internos para cambiar certificados TLS internos según sea necesario durante una actualización del sistema.

- Se ha agregado una medida de seguridad para evitar la expiración de secretos internos. Para ello, se aplica el cambio de secretos internos en caso de omisión de una alerta crítica sobre secretos a punto de expirar. No se debería confiar en esta medida como un procedimiento de funcionamiento normal. La rotación de secretos debería planearse durante una ventana de mantenimiento. Para más información, consulte [Cambio de secretos en Azure Stack](azure-stack-rotate-secrets.md).

- Visual Studio Code ahora es compatible con la implementación de Azure Stack mediante AD FS.

### <a name="improvements-1906"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->

- El cmdlet **Get-GraphApplication** del punto de conexión con privilegios ahora muestra la huella digital del certificado usado actualmente. Esto mejora la administración de certificados para las entidades de servicio cuando se implementa Azure Stack con AD FS.

- Se han agregado nuevas reglas de seguimiento de estado para validar la disponibilidad de AD Graph y AD FS, incluida la capacidad de generar alertas.

- Mejoras en la confiabilidad del proveedor de recursos de copia de seguridad cuando el servicio de copia de seguridad de infraestructura se mueve a otra instancia.

- Optimización del rendimiento del procedimiento de cambio de secretos externos para proporcionar un tiempo de ejecución uniforme a fin de facilitar la programación de la ventana de mantenimiento.

- El cmdlet **Test-AzureStack** ahora informa sobre los secretos internos que van a expirar (alertas críticas).

- Existe un nuevo parámetro disponible para el cmdlet **Register-CustomAdfs** en el punto de conexión con privilegios, que permite omitir la comprobación de la lista de revocación de certificados al configurar la confianza de federación de AD FS.

- La versión 1906 presenta una mayor visibilidad del progreso de actualización, lo que garantiza que no se produzcan pausas en las actualizaciones. Esto provoca un aumento en el número total de pasos de actualización que se muestran a los operadores en la hoja **Actualización**. Es posible que también observe más pasos de actualización en paralelo que en las actualizaciones anteriores.

#### <a name="networking-updates-1906"></a>Actualizaciones de redes

- Se ha actualizado el tiempo de concesión establecido en el respondedor DHCP para que sea coherente con Azure.

- Se han mejorado las frecuencias de reintento del proveedor de recursos en el caso de error de implementación de los recursos.

- Se ha quitado la opción de SKU **Estándar** del equilibrador de carga y la dirección IP pública, ya que no se admite actualmente.

### <a name="changes-1906"></a>Cambios

- La creación de una experiencia de cuenta de almacenamiento ahora es coherente con Azure.

- Se han cambiado los desencadenadores de alertas para la expiración de secretos internos:
  - Las alertas de advertencia se generan ahora 90 días antes de la expiración de los secretos.
  - Las alertas críticas se generan ahora 30 días antes de la expiración de los secretos.

- Se han actualizado las cadenas del proveedor de recursos de copia de seguridad de infraestructura para que la terminología sea coherente.

### <a name="fixes-1906"></a>Correcciones

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

## <a name="security-updates-1906"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack, consulte [Actualizaciones de seguridad de Azure Stack](release-notes-security-updates.md).

## <a name="update-planning-1906"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](known-issues.md)
- [Actualizaciones de seguridad](release-notes-security-updates.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](release-notes-checklist.md)

## <a name="download-the-update-1906"></a>Descarga de la actualización

Puede descargar la actualización de Azure Stack 1906 desde [la página de descarga de Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes-1906"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack para la versión 1905 antes de actualizar Azure Stack a la versión 1906. Después de la actualización, instale las [revisiones disponibles para 1906](#after-successfully-applying-the-1906-update).

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

### <a name="before-applying-the-1906-update"></a>Antes de aplicar la actualización 1906

La versión 1906 de Azure Stack debe aplicarse en la versión 1905 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1905.3.48](https://support.microsoft.com/help/4510078)

### <a name="after-successfully-applying-the-1906-update"></a>Después de aplicar correctamente la actualización 1906

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1906.13.56](https://support.microsoft.com/help/4520375)
::: moniker-end

::: moniker range="azs-1905"
## <a name="1905-build-reference"></a>Referencia de compilación 1905

El número de compilación de la actualización 1905 de Azure Stack es **1.1905.0.40**.

### <a name="update-type-1905"></a>Tipo de actualización

El tipo de compilación de la actualización 1905 de Azure Stack es **Completa**. Como resultado, la actualización 1905 tiene un tiempo de ejecución más largo que las actualizaciones rápidas, como 1903 y 1904. Los tiempos de ejecución exactos para las actualizaciones completas suelen depender del número de nodos que contiene la instancia de Azure Stack, de la capacidad del sistema que usan las cargas de trabajo de inquilino, de la conectividad de red del sistema (si está conectado a Internet) y de la configuración del hardware del sistema. La actualización 1905 tenía los siguientes tiempos de ejecución previstos en nuestras pruebas internas: 4 nodos: 35 horas, 8 nodos: 45 horas, 12 nodos: 55 horas, 16 nodos: 70 horas. Los tiempos de ejecución de la actualización 1905 superiores a estos valores previstos son habituales y no requieren ninguna acción por parte de los operadores de Azure Stack, a menos que se produzca un error de actualización. Para obtener más información sobre los tipos de compilación de actualización, consulte [Administración de actualizaciones en Azure Stack](azure-stack-updates.md).

## <a name="whats-in-this-update-1905"></a>Novedades de esta actualización

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

- Con esta actualización, el motor de actualización de Azure Stack puede actualizar el firmware de los nodos de la unidad de escalado. Esto requiere un paquete de actualización compatible de los asociados de hardware. Póngase en contacto con el asociado de hardware para obtener más información sobre la disponibilidad.

- Windows Server 2019 ahora se admite y está disponible para distribuirse a través de Azure Stack Marketplace.
Con esta actualización, Windows Server 2019 puede activarse ahora correctamente en un host de 2016.

- Una nueva [extensión de Visual Studio Code, Cuenta de Azure](../user/azure-stack-dev-start-vscode-azure.md), permite a los desarrolladores dirigirse a Azure Stack mediante el inicio de sesión y la visualización de suscripciones, así como una serie de otros servicios. La extensión Cuenta de Azure funciona en entornos de Azure Active Directory (Azure AD) y AD FS, y solo requiere un pequeño cambio en la configuración del usuario de Visual Studio Code. Visual Studio Code requiere que se proporcione permiso a una entidad de servicio para que se ejecute en este entorno. Para ello, importe el script de identidad y ejecute los cmdlets especificados en [Servicio multiinquilino en Azure Stack](../operator/azure-stack-enable-multitenancy.md). Esto requiere una actualización en el directorio principal y el registro del directorio del inquilino invitado para cada directorio. Después de actualizar a la versión 1905 o una versión posterior, se muestra una alerta para actualizar el inquilino del directorio principal para el que está incluida la entidad de servicio de Visual Studio Code. 

### <a name="improvements-1905"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->
- Como parte de la aplicación de TLS 1.2 en Azure Stack, las siguientes extensiones se han actualizado a estas versiones:

  - microsoft.customscriptextension-arm-1.9.3
  - microsoft.iaasdiagnostics-1.12.2.2
  - microsoft.antimalware-windows-arm-1.5.5.9
  - microsoft.dsc-arm-2.77.0.0
  - microsoft.vmaccessforlinux-1.5.2

  Descargue estas versiones de las extensiones inmediatamente, para que las nuevas implementaciones de la extensión no produzcan un error cuando se aplique TLS 1.2 en una versión futura. Siempre establezca **autoUpgradeMinorVersion=true** para que las actualizaciones de versiones secundarias de las extensiones (por ejemplo, 1.8 a 1.9) se realicen automáticamente.

- Una nueva **información general de la ayuda y soporte técnico** en Azure Stack Portal facilita a los operadores la comprobación de sus opciones de soporte técnico, la obtención de ayuda experta o más información acerca de Azure Stack. En los sistemas integrados, la creación de una solicitud de soporte técnico preseleccionará el servicio Azure Stack. Recomendamos encarecidamente que los clientes utilicen esta experiencia para enviar vales en lugar de utilizar Azure Portal global. Para más información, consulte [Azure Stack Help and Support](azure-stack-help-and-support-overview.md) (Ayuda y soporte técnico de Azure Stack).

- Cuando se incorporan varias instancias de Azure Active Directory (a través de [este proceso](azure-stack-enable-multitenancy.md)), es posible que no se ejecute de nuevo el script cuando se producen ciertas actualizaciones, o cuando los cambios en la autorización de la entidad de servicio de Azure AD hacen que falten derechos. Esto puede causar varios problemas, desde el acceso bloqueado a determinadas características, pasando por errores más discretos que son difíciles de rastrear, hasta el problema original. Para evitar esto, la versión 1905 introduce una nueva característica que comprueba estos permisos y crea una alerta cuando se encuentran ciertos problemas de configuración. Esta validación se ejecuta cada hora y muestra las acciones de corrección necesarias para solucionar el problema. La alerta se cierra una vez que todos los inquilinos están en un estado correcto.

- Mejora de la confiabilidad de las operaciones de copia de seguridad de la infraestructura durante la conmutación por error del servicio.

- Está disponible una nueva versión del [complemento de Azure Stack Nagios](azure-stack-integrate-monitor.md#integrate-with-nagios) que utiliza las [bibliotecas de autenticación de Azure Active Directory](/azure/active-directory/develop/active-directory-authentication-libraries) (ADAL) para la autenticación. El complemento ahora admite también las implementaciones basadas en Azure AD y en los Servicios de federación de Active Directory (AD FS) de Azure Stack. Para más información, consulte el sitio [de complementos de Nagios](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details).

- Se ha publicado un nuevo perfil híbrido **2019-03-01-Hybrid** que admite todas las características más recientes de Azure Stack. Tanto Azure PowerShell como la CLI de Azure admiten el perfil **2019-03-01-Hybrid**. Los SDK de .NET, Ruby, Node.js, Go y Python han publicado paquetes que admiten el perfil **2019-03-01-Hybrid**. La documentación respectiva y algunos ejemplos se han actualizado para reflejar los cambios.

- El [SDK de Node.js](https://www.npmjs.com/search?q=2019-03-01-hybrid) ahora admite los perfiles de API. Se publican los paquetes que admiten el perfil **2019-03-01-Hybrid**.

- La actualización de Azure Stack 1905 agrega dos nuevos roles de infraestructura para mejorar la compatibilidad y confiabilidad de la plataforma:

  - **Anillo de infraestructura**: en el futuro, el anillo de infraestructura hospedará versiones en contenedor de roles de infraestructura existentes (por ejemplo, xrp), que actualmente requieren sus propias VM de infraestructura designadas. Esto mejorará la confiabilidad de la plataforma y reducirá el número de VM de infraestructura que requiere Azure Stack. Por consiguiente, esto reducirá el consumo general de recursos de los roles de infraestructura de Azure Stack en el futuro.
  - **Anillo de soporte técnico**: en el futuro, se utilizará el anillo de soporte técnico para controlar escenarios de compatibilidad mejorada para los clientes.  

  Además, hemos agregado una instancia adicional de VM de controlador de dominio para mejorar la disponibilidad de este rol.

  Estos cambios aumentarán el consumo de recursos de la infraestructura de Azure Stack de las maneras siguientes:
  
    | Azure Stack SKU | Aumento del consumo de procesos | Aumento del consumo de memoria |
    | -- | -- | -- |
    |4 nodos|22 vCPU|28 GB|
    |8 nodos|38 vCPU|44 GB|
    |12 nodos|54 vCPU|60 GB|
    |16 nodos|70 vCPU|76 GB|
  
### <a name="changes-1905"></a>Cambios

- Para aumentar la confiabilidad y disponibilidad durante escenarios de mantenimiento planeados y no planeados, Azure Stack agrega una instancia de rol de infraestructura adicional para los servicios de dominio.

- Con esta actualización, durante las operaciones de reparación y adición de nodos, el hardware se valida para garantizar la homogeneidad de los nodos de la unidad de escalado dentro de una unidad de escalado.

- Si no se completan las copias de seguridad programadas y se supera el período de retención definido, el controlador de copias de seguridad de la infraestructura se asegurará de que se conserve al menos una copia de seguridad correcta. 

### <a name="fixes-1905"></a>Correcciones

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Se ha corregido un problema en el que aparecía la advertencia **Compute host agent** (Agente de host de proceso) después de reiniciar un nodo en la unidad de escalado.

- Se han corregido problemas en la administración de Marketplace en el portal de administración que mostraban resultados incorrectos cuando se aplicaban filtros y mostraban nombres de editores duplicados en el filtro de editores. También se han realizado mejoras en el rendimiento para mostrar los resultados más rápidamente.

- Se ha solucionado un problema en la hoja de copia de seguridad disponible que enumeraba una nueva copia de seguridad disponible antes de completar la carga en la ubicación del almacenamiento externo. Ahora la copia de seguridad disponible se mostrará en la lista después de haberse cargado correctamente en la ubicación de almacenamiento. 

<!-- ICM: 114819337; Task: 4408136 -->
- Se ha corregido un problema con la obtención de las claves de recuperación durante la operación de copia de seguridad. 

<!-- Bug: 4525587 -->
- Se ha corregido un problema con la actualización de OEM que mostraba la versión como "no definida" en el portal del operador.

### <a name="security-updates-1905"></a>Actualizaciones de seguridad

Para obtener información sobre las actualizaciones de seguridad de esta actualización de Azure Stack, consulte [Actualizaciones de seguridad de Azure Stack](release-notes-security-updates.md).

## <a name="update-planning-1905"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](known-issues.md)
- [Actualizaciones de seguridad](release-notes-security-updates.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](release-notes-checklist.md)

## <a name="download-the-update-1905"></a>Descarga de la actualización

Puede descargar el paquete de actualización 1905 de Azure Stack desde [la página de descarga de Azure Stack](https://aka.ms/azurestackupdatedownload). Al usar la herramienta descargador, asegúrese de usar la versión más reciente y no una copia en caché del directorio de descargas.

## <a name="hotfixes-1905"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack para la versión 1904 antes de actualizar Azure Stack a la versión 1905.

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

### <a name="before-applying-the-1905-update"></a>Antes de aplicar la actualización 1905

La versión 1905 de Azure Stack debe aplicarse en la versión 1904 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1904.4.45](https://support.microsoft.com/help/4505688)

### <a name="after-successfully-applying-the-1905-update"></a>Después de aplicar correctamente la actualización 1905

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1905.3.48](https://support.microsoft.com/help/4510078)
::: moniker-end

::: moniker range=">=azs-1905"
## <a name="automatic-update-notifications"></a>Notificaciones de actualización automáticas

Los sistemas que pueden acceder a Internet desde la red de infraestructura verán el mensaje **Actualización disponible** en el portal del operador. Los sistemas sin acceso a Internet pueden descargar e importar el archivo ZIP con el archivo .xml correspondiente.

> [!TIP]  
> Suscríbase a las siguientes fuentes *RRS* o *Atom* para mantenerse al día con las revisiones de Azure Stack:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="archive"></a>Archivar

Para acceder a las notas de la versión archivadas de una versión anterior, use la lista desplegable del selector de versiones encima de la tabla de contenido de la izquierda y seleccione la versión que quiere ver.

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md).  
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](azure-stack-apply-updates.md).
- Para revisar la directiva de servicio de los sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido, consulte [Directiva de servicio de Azure Stack](azure-stack-servicing-policy.md).  
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](azure-stack-monitor-update.md).
::: moniker-end

<!------------------------------------------------------------>
<!------------------- UNSUPPORTED VERSIONS ------------------->
<!------------------------------------------------------------>
::: moniker range="azs-1904"
## <a name="1904-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1904
::: moniker-end
::: moniker range="azs-1903"
## <a name="1903-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1903
::: moniker-end
::: moniker range="azs-1902"
## <a name="1902-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1902
::: moniker-end
::: moniker range="azs-1901"
## <a name="1901-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1901
::: moniker-end
::: moniker range="azs-1811"
## <a name="1811-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1811
::: moniker-end
::: moniker range="azs-1809"
## <a name="1809-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1809
::: moniker-end
::: moniker range="azs-1808"
## <a name="1808-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1808
::: moniker-end
::: moniker range="azs-1807"
## <a name="1807-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1807
::: moniker-end
::: moniker range="azs-1805"
## <a name="1805-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1805
::: moniker-end
::: moniker range="azs-1804"
## <a name="1804-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1804
::: moniker-end
::: moniker range="azs-1803"
## <a name="1803-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1803
::: moniker-end
::: moniker range="azs-1802"
## <a name="1802-archived-release-notes"></a>Notas de la versión archivadas de la compilación 1802
::: moniker-end

::: moniker range="<azs-1905"
Puede consultar las [notas de las versiones anteriores de Azure Stack en la Galería de TechNet](https://aka.ms/azsarchivedrelnotes). Estos documentos archivados se proporcionan únicamente con fines de referencia y no implican que estas versiones sean compatibles. Para obtener información sobre la compatibilidad con Azure Stack, consulte [Directiva de mantenimiento de Azure Stack](azure-stack-servicing-policy.md). Para obtener más ayuda, póngase en contacto con los servicios de asistencia al cliente de Microsoft.
::: moniker-end


