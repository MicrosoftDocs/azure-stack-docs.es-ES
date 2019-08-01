---
title: Notas de la versión 1904 de Azure Stack | Microsoft Docs
description: Obtenga información sobre la actualización 1904 de los sistemas integrados de Azure Stack, como novedades, problemas conocidos y dónde se puede descargar dicha actualización y las notas de la versión archivadas de Azure Stack.
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
ms.date: 05/30/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/30/2019
ms.openlocfilehash: 71ad5812472534ae4a7e3dc34feabf7a1a02faa0
ms.sourcegitcommit: f6ea6daddb92cbf458f9824cd2f8e7e1bda9688e
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/25/2019
ms.locfileid: "68494048"
---
# <a name="azure-stack-1904-update"></a>Actualización 1904 de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el contenido del paquete de actualización 1904. La actualización incluye novedades, mejoras y correcciones para esta versión de Azure Stack. Este archivo contiene la siguiente información:

- [Descripción de las novedades, las mejoras, las correcciones y las actualizaciones de seguridad](azure-stack-release-notes-1904.md#whats-in-this-update)
- [Planeación de la actualización](azure-stack-release-notes-1904.md#update-planning)

> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="archived-release-notes"></a>Archivado de notas de la versión

Puede ver [las notas de la versión de las versiones anteriores de Azure Stack en la Galería de TechNet](http://aka.ms/azsarchivedrelnotes). Estas notas de la versión archivadas se proporcionan únicamente con fines de referencia y no implican que estas versiones se admitan. Para obtener más ayuda, póngase en contacto con los servicios de asistencia al cliente de Microsoft.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 1904 de Azure Stack es **1.1904.0.36**.

### <a name="update-type"></a>Tipo de actualización

El tipo de compilación de la actualización 1904 de Azure Stack es **Rápida**. Para obtener más información sobre cómo actualizar los tipos de compilación, consulte el artículo [Administración de actualizaciones en Azure Stack](azure-stack-updates.md). Se espera que la actualización 1904 tarde unas 16 horas en completarse, pero el tiempo exacto puede variar. Esta aproximación del runtime es específica para la actualización 1904 y no se debe comparar con otras actualizaciones de Azure Stack.

## <a name="whats-in-this-update"></a>Novedades de esta actualización

<!-- The current theme (if any) of this release. -->

<!-- What's new, also net new experiences and features. -->

### <a name="improvements"></a>Mejoras

<!-- Changes and product improvements with tangible customer-facing value. -->

- La actualización 1904 incluye mejoras significativas para la pila de redes definidas por software (SDN). Estas mejoras mejoran el mantenimiento general y la confiabilidad de la pila de SDN en Azure Stack.

- El portal de administradores muestra una notificación cuando el usuario con una sesión iniciada no tiene los permisos necesarios, lo que permite que el panel se cargue correctamente. También contiene un vínculo a la documentación que explica qué cuentas tienen los permisos adecuados, según el proveedor de identidades usado durante la implementación.

- Se han incorporado mejoras para el tiempo de actividad y la resistencia de las VM. Estas resuelven el escenario en el que todas las VM se desconectan si se desconecta el volumen de almacenamiento que contiene los archivos de configuración de VM.

<!-- 1901,2,3 related hotfix -->
- Se ha agregado optimización para el número de VM evacuadas simultáneamente y aplicado un límite para el ancho de banda consumido, con la finalidad de abordar las caídas de voltaje y las interrupciones de las VM si la red presenta sobrecarga. Este cambio aumenta el tiempo de actividad de las VM cuando se está actualizando un sistema.

<!-- 1901,2,3 related hotfix -->
- Limitación de recursos mejorada cuando se está ejecutando un sistema a escala para protegerlo de procesos internos que agotan los recursos de la plataforma, lo que provoca que las operaciones no se realicen correctamente en el portal.

- Las funcionalidades de filtrado mejoradas permiten a los operadores aplicar varios filtros al mismo tiempo. Solo puede establecer el orden por la columna **Nombre** en la nueva interfaz de usuario.

- Mejoras en el proceso de eliminación de ofertas, planes, cuotas y suscripciones. Ahora puede eliminar correctamente ofertas, cuotas, planes y suscripciones desde el portal de administradores si el objeto que quiere eliminar no tiene ninguna dependencia. Para obtener más información, consulte [este artículo](azure-stack-delete-offer.md).  

<!-- this applies to bug 3725384 and bug #4225643 -->
- Volumen de mensajes de syslog mejorado al filtrar eventos innecesarios y proporcionar un parámetro de configuración para seleccionar el nivel de gravedad deseado para los mensajes reenviados. Para obtener más información sobre cómo configurar el nivel de gravedad, consulte [Azure Stack datacenter integration - syslog forwarding](azure-stack-integrate-security.md) (Integración del centro de datos de Azure Stack: reenvío de syslog).

<!--this applied to Bug 1473487 -->
- Se ha agregado una nueva funcionalidad al cmdlet **Get-AzureStackLog** mediante la incorporación de un parámetro adicional, `-OutputSASUri`. Ahora puede recopilar registros de Azure Stack de su entorno y almacenarlos en el contenedor de blobs de Azure Storage especificado. Para más información, consulte [Diagnósticos de Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#examples).

- Se ha agregado una nueva comprobación de memoria en el grupo **Test-AzureStack** `UpdateReadiness`, que determina si tiene suficiente memoria disponible en la pila para la actualización se realice correctamente.

<!-- Bug/Task 4311058 -->
- Mejoras en **Test-AzureStack** para evaluar el mantenimiento de Service Fabric.

<!-- feature: 2976966 -->
- Mejoras en las actualizaciones de hardware, que reducen el tiempo necesario para completar la actualización del firmware de la unidad de 2 a 4 horas. El motor de actualización determina de forma dinámica qué partes de la actualización deben ejecutarse en función del contenido del paquete.

<!-- Feature 3906611 -->
- Se han agregado sólidas comprobaciones previas de las operaciones para evitar operaciones de instancias de rol de infraestructura perjudiciales que afecten a la disponibilidad.

<!-- Feature 3780326 -->
- Mejoras en la idempotencia del plan de acción de copia de seguridad de la infraestructura.

<!--Bug/Task 3139609 -->
- Mejoras en la recopilación de registros de Azure Stack. Estas mejoras reducen el tiempo necesario para recuperar el conjunto de registros. Además, el cmdlet [Get-AzureStackLog](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system) ya no genera registros predeterminados para el rol de OEM. Debe ejecutar el cmdlet [Invoke-AzureStackOnDemandLog](azure-stack-configure-on-demand-diagnostic-log-collection.md#invoke-azurestackondemandlog) y especificar el rol para recuperar los registros de OEM. Para más información, consulte [Diagnósticos de Azure Stack](azure-stack-configure-on-demand-diagnostic-log-collection.md#run-get-azurestacklog-on-an-azure-stack-development-kit-asdk-system).

- Azure Stack ahora supervisa la dirección URL de datos de federación proporcionada para la integración del centro de datos con ADFS. Esto mejora la confiabilidad durante el cambio de secretos de la granja o la instancia de ADFS del cliente.

### <a name="changes"></a>Cambios

<!-- Feature 3906611 -->
- Se ha quitado la opción para que los operadores de Azure Stack apaguen las instancias de rol de infraestructura en el portal de administradores. La funcionalidad de reinicio garantiza un intento de cierre correcto antes de reiniciar la instancia de rol de infraestructura. Para escenarios avanzados, la funcionalidad de la API y de PowerShell sigue estando disponible.

<!-- Feature ## 4199257 -->
- Se incluye una nueva experiencia de administración de Marketplace, con pantallas independientes para los proveedores de recursos y las imágenes de Marketplace. Por ahora, la ventana **Proveedor de recursos** está vacía, pero en futuras versiones aparecerán nuevas ofertas de servicios PaaS, que se administrarán en esta misma ventana **.**

<!-- Feature ## 4199257 -->
- Cambios en la experiencia de actualización en el portal del operador. Se incluye una nueva cuadrícula para las actualizaciones del proveedor de recursos. La capacidad de actualizar los proveedores de recursos aún no está disponible.

<!-- Task ## 3748423  -->
- Cambios en la experiencia de instalación de actualizaciones en el portal del operador. Para ayudar a los operadores de Azure Stack a responder adecuadamente a un problema de actualización, el portal ahora proporciona recomendaciones más específicas según el mantenimiento de la unidad de escalado, tal y como se deriva automáticamente al ejecutar **Test-AzureStack** y analizar los resultados. En función del resultado, informará al operador para que realice una de las dos acciones siguientes:

  - Se muestra una alerta de advertencia "ligera" en el portal similar a la siguiente: "La actualización más reciente requiere atención. Microsoft recomienda abrir una solicitud de servicio durante el horario comercial. Como parte del proceso de actualización, se ejecuta Test-AzureStack y, en función de la salida, se genera la alerta más adecuada. En este caso, Test-AzureStack se ejecutó correctamente".

  - Se muestra una alerta crítica "severa" en el portal similar a la siguiente: "Se produjo un error en la actualización más reciente. Microsoft recomienda abrir una solicitud de servicio lo antes posible. Como parte del proceso de actualización, se ejecuta Test-AzureStack y, en función de la salida, se genera la alerta más adecuada. En este caso, también se produjeron errores en Test-AzureStack”.

- Versión 2.2.38.0 del agente Linux de Azure actualizado. Esta compatibilidad permite que los clientes conserven imágenes coherentes de Linux entre Azure y Azure Stack.

- Cambios en los registros de actualización en el portal del operador. Ya no están disponibles las solicitudes para recuperar registros de actualizaciones correctas. Al ser accionables en los diagnósticos, los registros de actualizaciones con errores siguen estando disponibles para su descarga.

### <a name="fixes"></a>Correcciones

<!-- Product fixes that came up from customer deployments worth highlighting, especially if there is an SR/ICM associated to it. -->

- Se ha corregido un error en que la configuración de syslog no persistía durante todo un ciclo de actualización, lo que hacía que el cliente de syslog perdiera su configuración y se dejaran de reenviar los mensajes de syslog. La configuración de syslog ahora se conserva.

- Se ha corregido un problema en CRP que bloqueaba la desasignación de VM. Anteriormente, si una VM contenía varios discos administrados de gran tamaño, la desasignación de la VM podía generar errores de tiempo de expiración.

- Se ha corregido un problema con el motor de Windows Defender que afectaba al acceso al almacenamiento de la unidad de escalado.

- Se ha corregido un problema del portal de usuarios que impedía cargar la ventana Directiva de acceso de las cuentas de almacenamiento de blobs.

- Se ha corregido un problema en los portales de administradores y usuarios, en el que se mostraban notificaciones erróneas en Azure Portal global.

- Se ha corregido un problema del portal de usuarios en que al seleccionar el icono **Comentarios** se abría una pestaña vacía del explorador.

- Se ha corregido un problema del portal en que, al cambiar una dirección IP estática por una configuración de IP enlazada a un adaptador de red conectado a una instancia de VM, se mostraba un mensaje de error.

- Se ha corregido un problema del portal de usuarios en que, al intentar **conectar la interfaz de red** a una VM existente a través de la ventana **Redes**, la operación generaba un mensaje de error.

- Se ha corregido un problema en que Azure Stack no permitía conectar más de 4 interfaces de red (NIC) a una instancia de VM.

- Se ha corregido un error del portal en que, al agregar una regla de seguridad de entrada y seleccionar **Etiqueta de servicio** como origen, se mostraban varias opciones que no están disponibles para Azure Stack.

- Se ha corregido un problema en que los grupos de seguridad de red (NSG) no funcionaban en Azure Stack de la misma manera que en Azure global.

- Se ha corregido un problema de la administración de Marketplace que ocultaba todos los productos descargados si expiraba o se quitaba el registro.

- Se ha corregido un problema en que la emisión un comando **Set-AzureRmVirtualNetworkGatewayConnection** en PowerShell para una conexión de puerta de enlace de red virtual existente generaba el mensaje de error **Invalid shared key configured...** (La clave compartida configurada no es válida...).

- Se ha corregido un problema que causaba que el proveedor de recursos de red (NRP) no se sincronizara con el controlador de red, lo que provocaba la solicitud de recursos duplicados. En algunos casos, el recurso principal permanecía con un estado de error.

- Se ha corregido un problema en que, si se asignaba un rol de colaborador a un usuario para una suscripción, pero no se le concedían explícitamente permisos de lectura, se generaba un error similar a: **...El cliente "somelogonaccount@domain.com" con el id. de objeto {GUID} no tiene autorización para realizar la acción...** cuando se intentaba guardar un cambio en un recurso.

- Se ha corregido un problema en que la pantalla de administración de Marketplace estaba vacía si se usaba la herramienta de redifusión sin conexión para cargar imágenes y alguna de ellas no tenía los URI del icono.

- Se ha corregido un problema que impedía que los productos que no se descargaban se eliminaran de Marketplace Management.

### <a name="security-updates"></a>Actualizaciones de seguridad

Esta actualización de Azure Stack no incluye actualizaciones de seguridad para el sistema operativo subyacente que hospeda Azure Stack. Para más información, consulte el tema sobre las [actualizaciones de seguridad de Azure Stack](azure-stack-release-notes-security-updates-1904.md).

## <a name="update-planning"></a>Planeación de la actualización

Antes de aplicar la actualización, asegúrese de revisar la información siguiente:

- [Problemas conocidos](azure-stack-release-notes-known-issues-1904.md)
- [Actualizaciones de seguridad](azure-stack-release-notes-security-updates-1904.md)
- [Lista de comprobación de las actividades antes y después de aplicar la actualización](azure-stack-release-notes-checklist.md)

> [!NOTE]
> Asegúrese de que usa la versión más reciente de la herramienta [Capacity Planner de Azure Stack](https://aka.ms/azstackcapacityplanner) para planificar la carga de trabajo y ajustar su tamaño. La última versión contiene correcciones de errores y proporciona nuevas características que se publican con cada actualización de Azure Stack.

## <a name="download-the-update"></a>Descarga de la actualización

Puede descargar el paquete de actualización 1904 de Azure Stack desde [la página de descarga de Azure Stack](https://aka.ms/azurestackupdatedownload).

## <a name="hotfixes"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la revisión más reciente de Azure Stack para la compilación 1903 antes de actualizar Azure Stack a la versión 1904.

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

### <a name="before-applying-the-1904-update"></a>Antes de aplicar la actualización 1904

La versión 1904 de Azure Stack debe aplicarse en la versión 1903 con las revisiones siguientes:

<!-- One of these. Either no updates at all, nothing is required, or the LATEST hotfix that is required-->
- [Azure Stack, revisión 1.1903.2.39](https://support.microsoft.com/help/4500638)

### <a name="after-successfully-applying-the-1904-update"></a>Después de aplicar correctamente la actualización 1904

Después de instalar esta actualización, instale todas las revisiones aplicables. Para más información, consulte nuestra [directiva de servicio](azure-stack-servicing-policy.md).

- [Azure Stack, revisión 1.1904.4.45](https://support.microsoft.com/help/4505688)

## <a name="automatic-update-notifications"></a>Notificaciones de actualización automáticas

Los clientes con sistemas que pueden acceder a Internet desde la red de infraestructura verán el mensaje **Actualización disponible** en el portal del operador. Los sistemas sin acceso a Internet pueden descargar e importar el archivo ZIP con el archivo .xml correspondiente.

> [!TIP]  
> Suscríbase a las siguientes fuentes *RRS* o *Atom* para mantenerse al día con las revisiones de Azure Stack:
>
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md).  
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](azure-stack-apply-updates.md).
- Para revisar la directiva de servicio de los sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido, consulte [Directiva de servicio de Azure Stack](azure-stack-servicing-policy.md).  
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](azure-stack-monitor-update.md).  

