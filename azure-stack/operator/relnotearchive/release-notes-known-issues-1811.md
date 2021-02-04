---
title: Actualización de Azure Stack 1811 | Microsoft Docs
description: Obtenga información sobre la actualización 1811 de los sistemas integrados de Azure Stack, incluyendo las novedades, los problemas conocidos y dónde se puede descargar la actualización.
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
ms.date: 04/09/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 02/28/2019
ms.openlocfilehash: 76a9909ac35197d8e51115e07d977a0ee39442b5
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248754"
---
# <a name="azure-stack-1811-update"></a>Actualización de Azure Stack 1811

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el contenido del paquete de actualización 1811. El paquete de actualización incluye mejoras, correcciones y nuevas características de esta versión de Azure Stack. En este artículo también se describen problemas conocidos de esta versión e incluye un vínculo para que pueda descargar la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 1811 de Azure Stack es **1.1811.0.101**.

## <a name="hotfixes"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la [revisión más reciente de Azure Stack](#azure-stack-hotfixes) para 1809 antes de actualizar Azure Stack a 1811.

### <a name="azure-stack-hotfixes"></a>Revisiones de Azure Stack

- **1809**: [KB 4481548: revisión de Azure Stack 1.1809.12.114](https://support.microsoft.com/help/4481548/)
- **1811**: Ninguna revisión actual disponible.

## <a name="prerequisites"></a>Requisitos previos

> [!IMPORTANT]
> Durante la instalación de la actualización 1811, debe asegurarse de que están cerradas todas las instancias del portal de administración. El portal de usuarios puede permanecer abierto, pero se debe cerrar el portal de administración.

- Prepare la implementación de Azure Stack para el host de extensiones de Azure Stack. Prepare el sistema mediante las siguientes instrucciones: [Preparación de un host de extensiones de Azure Stack](../azure-stack-extension-host-prepare.md). 
 
- Instale la [revisión más reciente de Azure Stack](#azure-stack-hotfixes) para la actualización 1809 antes de actualizar a 1811.

- Antes de iniciar la instalación de esta actualización, ejecute [Test-AzureStack](../azure-stack-diagnostic-test.md) con los parámetros siguientes para validar el estado de Azure Stack y resolver todos los problemas operativos detectados, incluidas todas las advertencias y errores. Repase también las alertas activas y resuelva las que requieran alguna acción.  

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

    Si no cumple los requisitos de host de extensión, la salida de `Test-AzureStack` muestra el siguiente mensaje: 
  
    `To proceed with installation of the 1811 update, you will need to import 
    the SSL certificates required for Extension Host, which simplifies network 
    integration and increases the security posture of Azure Stack. Refer to this 
    link to prepare for Extension Host:
    https://docs.microsoft.com/azure-stack/operator/azure-stack-extension-host-prepare`

- La actualización de Azure Stack 1811 requiere que importe correctamente los certificados de host de extensión obligatorios en su entorno de Azure Stack. Para continuar con la instalación de la actualización 1811, debe importar los certificados SSL necesarios para el host de la extensión. Para importar los certificados, vea [esta sección](../azure-stack-extension-host-prepare.md#import-extension-host-certificates).

    Si omite cada advertencia y sigue optando por instalar la actualización 1811, se producirá un error en la actualización en aproximadamente una hora con el siguiente mensaje:   
 
    `The required SSL certificates for the Extension Host have not been found.
    The Azure Stack update will halt. Refer to this link to prepare for 
    Extension Host: https://docs.microsoft.com/azure-stack/operator/azure-stack-extension-host-prepare,
    then resume the update.
    Exception: The Certificate path does not exist: [certificate path here]` 
 
    Después de importar correctamente los certificados de host de extensión obligatoria, puede reanudar la actualización 1811 desde el portal de administrador. Aunque Microsoft aconseja a los operadores de Azure Stack programar una ventana de mantenimiento durante el proceso de actualización, un error debido a los certificados de host de extensión que faltan no debería afectar a las cargas de trabajo o los servicios existentes.  

    Durante la instalación de esta actualización, el portal de usuarios de Azure Stack no estará disponible mientras se configura el host de extensiones. La configuración del host de extensiones puede tardar hasta 5 horas. Durante ese tiempo, puede comprobar el estado de una actualización o reanudar la instalación de una actualización con errores con [el punto de conexión con privilegios elevados o PowerShell de administrador de Azure Stack](../azure-stack-monitor-update.md).

- Si System Center Operations Manager (SCOM) administra Azure Stack, asegúrese de actualizar el módulo de administración para Microsoft Azure Stack a la versión 1.0.3.11 antes de aplicar la revisión 1811.

## <a name="new-features"></a>Nuevas características

Esta actualización incluye las siguientes nuevas características y correcciones para Azure Stack:

- Con esta versión se habilita el [host de extensiones](../azure-stack-extension-host-prepare.md). El host de extensión simplifica la integración de red y mejora la posición de seguridad de Azure Stack.

- Se ha agregado compatibilidad con autenticación de dispositivos con Active Directory Federated Services (AD FS), cuando se usa la CLI de Azure en particular. Para obtener más información, vea [Uso de los perfiles de la versión de la API con la CLI de Azure en Azure Stack](../../user/azure-stack-version-profiles-azurecli2.md)

- Se ha agregado compatibilidad para entidades de servicio con un secreto de cliente de Active Directory Federated Services (AD FS). Para obtener más información, vea [Crear una entidad de servicio para AD FS](../azure-stack-create-service-principals.md#manage-an-azure-ad-app-identity).

- Esta versión agrega compatibilidad con las siguientes versiones de API de servicio de Azure Storage: **2017-07-29** y **2017-11-09**. También se ha agregado compatibilidad con las siguientes versiones de la API de Azure Storage Resource Provider: **2016-05-01**, **2016-12-01**, **2017-06-01** y **2017-10-01**. Para más información, consulte [Azure Stack Storage: diferencias y consideraciones](../../user/azure-stack-acs-differences.md).

- Se han agregado nuevos comandos con privilegios de punto de conexión para actualizar y quitar entidades de servicio para AD FS. Para obtener más información, vea [Crear una entidad de servicio para AD FS](../azure-stack-create-service-principals.md#manage-an-azure-ad-app-identity).

- Se han agregado nuevas operaciones de nodo de la unidad de escalado que permiten a un operador de Azure Stack iniciar, detener y apagar un nodo de unidad de escalado. Para obtener más información, vea [Acciones de los nodos de unidad de escalado en Azure Stack](../azure-stack-node-actions.md).

- Se ha agregado una nueva hoja de propiedades de región en la que se muestran los detalles de registro del entorno. Puede ver esta información si hace clic en el icono **Administración de regiones** en el panel predeterminado en el portal de administrador y después selecciona **Propiedades**.

- Se ha agregado un nuevo comando de punto de conexión con privilegios para actualizar las credenciales BMC con el nombre de usuario y la contraseña, que se usa para comunicarse con las máquinas físicas. Para obtener más información, vea [Actualización de la contraseña del controlador de administración de placa base \(BMC)](../azure-stack-rotate-secrets.md).

- Se ha agregado la capacidad de acceder a la hoja de Azure a través del icono de ayuda y soporte técnico (signo de interrogación) en la esquina superior derecha de los portales de administrador y usuario, similar a la forma en que está disponible en Azure Portal.

- Se ha agregado una experiencia de administración mejorada de Marketplace para usuarios desconectados. Se ha simplificado el proceso de carga para publicar un elemento de Marketplace en un entorno desconectado a un solo paso, en lugar de cargar la imagen y el paquete de Marketplace por separado. El producto cargado también estará visible en la hoja de administración de Marketplace.

- Esta versión reduce la ventana de mantenimiento necesaria para la rotación secreta agregando la capacidad para girar solo certificados externos durante la [rotación secreta de Azure Stack](../azure-stack-rotate-secrets.md).

- [PowerShell de Azure Stack](../azure-stack-powershell-install.md) se ha actualizado a la versión 1.6.0. La actualización incluye compatibilidad con las nuevas características relacionadas con el almacenamiento en Azure Stack. Para obtener más información, vea las notas de la versión del [módulo de administración de Azure Stack 1.6.0 en la Galería de PowerShell](https://www.powershellgallery.com/packages/AzureStack/1.6.0). Para obtener información sobre cómo actualizar o instalar PowerShell de Azure Stack, vea [Instalación de PowerShell para Azure Stack](../azure-stack-powershell-install.md).

- Managed Disks ahora está habilitado de forma predeterminada al crear máquinas virtuales mediante el portal de Azure Stack. Vea la sección [problemas conocidos](#known-issues-post-installation) para conocer los pasos adicionales necesarios para que Managed Disks evite errores de creación de máquina virtual.

- Esta versión presenta acciones de **Reparación** de alerta para el operador de Azure Stack. Algunas alertas en 1811 proporcionan un botón **Reparación** en la alerta en el que se puede hacer clic para resolver el problema. Para obtener más información, vea [Supervisar el estado y las alertas en Azure Stack](../azure-stack-monitor-health.md).

- Actualizaciones en la experiencia de actualización de Azure Stack. Las mejoras de esta actualización incluyen: 
  - Pestañas que dividen las actualizaciones del historial de actualizaciones para realizar un mejor seguimiento de las actualizaciones en curso y las completadas.
  - Visualizaciones de estado mejoradas en la sección Essentials con nuevos iconos y diseño para las versiones actuales y de OEM, así como la última fecha de actualización.
  - El vínculo **Ver** de la columna de notas de la versión lleva al usuario directamente a la documentación específica para esa actualización en lugar de a la página de actualizaciones genérica.
  - La pestaña **Historial de actualizaciones** que se usa para determinar los tiempos de ejecución para cada una de las actualizaciones, así como funcionalidades de filtrado mejoradas.  
  - Las unidades de escalado de Azure Stack que están conectadas seguirán recibiendo automáticamente las **actualizaciones disponibles** conforme estén disponibles.
  - Las unidades de escalado de Azure Stack que no están conectadas pueden importar las actualizaciones al igual que antes. 
  - No hay ningún cambio en el proceso para descargar los registros JSON desde el portal. Los operadores de Azure Stack verán los pasos de expansión que expresan el progreso.

    Para más información, consulte [Aplicación de actualizaciones en Azure Stack](../azure-stack-apply-updates.md).


## <a name="fixed-issues"></a>Problemas corregidos

<!-- TBD - IS ASDK --> 
- Se ha corregido un problema en el que la dirección IP pública mostraba el mismo valor **EventDateTime** para cada registro en lugar de la marca **TimeDate** que muestra cuándo se creó el registro. Ya puede usar estos datos para realizar un recuento adecuado del uso de la dirección IP pública.

<!-- 3099544 – IS, ASDK --> 
- Se ha corregido un problema que se producía al crear una máquina virtual (VM) mediante el portal de Azure Stack. Seleccionar el tamaño de la máquina virtual hacía que la columna **USD/mes** mostrará el mensaje **No disponible**. Esta columna ya no aparece, ya que en Azure Stack no se permite mostrar la columna de precios de la máquina virtual.

<!-- 2930718 - IS ASDK --> 
- Se ha corregido un problema en el que el portal de administrador, al acceder a los detalles de cualquier suscripción de usuario después de cerrar la hoja y hacer clic en **Recientes**, no mostraba el nombre de la suscripción de usuario. Ahora aparece el nombre de la suscripción de usuario.

<!-- 3060156 - IS ASDK --> 
- Se ha corregido un problema en los portales de administrador y usuario: hacer clic en la configuración del portal y seleccionar **Eliminar todas las opciones de configuración y los paneles privados** no funcionaba según lo previsto y se mostraba una notificación de error. Esta opción ahora funciona correctamente.

<!-- 2930799 - IS ASDK --> 
- Se ha solucionado un problema en los portales de administrador y de usuario, en **Todos los servicios**, el recurso **Plan de DDoS Protection** se mostraba de forma incorrecta. No está disponible en Azure Stack. Se ha quitado la descripción.
 
<!--2760466 – IS  ASDK --> 
- Se ha corregido un problema en el que, cuando se instala un nuevo entorno de Azure Stack, no se mostraba la alerta de **Activación necesaria**. Ahora se muestra correctamente.

<!--1236441 – IS  ASDK --> 
- Se ha corregido un problema que impedía aplicar las directivas RBAC a un grupo de usuarios cuando se usa ADFS.

<!--3463840 - IS, ASDK --> 
- Se ha corregido un problema en el que las copias de seguridad de infraestructura contenían errores debido a que no se podía acceder al servidor de archivos desde la red VIP pública. Esta corrección traslada el servicio de copia de seguridad de infraestructura de nuevo a la red de infraestructura pública. Si ha aplicado la [revisión más reciente de Azure Stack para 1809](#azure-stack-hotfixes) que soluciona este problema, la actualización 1811 no realizará ninguna modificación adicional. 

<!-- 2967387 – IS, ASDK --> 
- Se ha corregido un problema en el que la cuenta que se usa para iniciar sesión en el portal de administrador o usuario de Azure Stack se mostraba como **Usuario no identificado**. Este mensaje se mostraba cuando en la cuenta no se había especificado un **nombre** o un **apellido**.   

<!--  2873083 - IS ASDK --> 
- Se ha corregido un problema en el que, al usar el portal para crear un conjunto de escalado de máquinas virtuales (VMSS), causaba que la lista desplegable **Tamaño de instancia** no se cargase correctamente con Internet Explorer. Este explorador ahora funciona correctamente.  

<!-- 3190553 - IS ASDK -->
- Se ha corregido un problema que generaba alertas falsas que indicaban que una instancia de rol de infraestructura no estaba disponible o que el nodo de la unidad de escala estaba sin conexión.

<!-- 2724961 - IS ASDK -->
- Se ha corregido un problema por el cual la página de información general de una máquina virtual no mostraba correctamente el gráfico de métricas de la máquina virtual. 

## <a name="changes"></a>Cambios

- Se ha introducido una nueva forma de ver y editar las cuotas de un plan en 1811. Para obtener más información, vea [Visualización de una cuota existente](../azure-stack-quota-types.md#view-an-existing-quota).

<!-- 3083238 IS -->
- Las mejoras de seguridad en esta actualización dan como resultado un aumento en el tamaño de copia de seguridad del rol de servicio de directorio. Para obtener instrucciones de ajuste de tamaño actualizadas para la ubicación de almacenamiento, vea la [documentación de copia de seguridad de infraestructura](../azure-stack-backup-reference.md#storage-location-sizing). Este cambio tiene como resultado un aumento del tiempo necesario para completar la copia de seguridad debido a la transferencia de datos de mayor tamaño. Este cambio afecta a los sistemas integrados. 

- Se cambia el nombre del cmdlet PEP existente para recuperar las claves de recuperación de BitLocker en 1811, de Get-AzsCsvsRecoveryKeys a Get-AzsRecoveryKeys. Para obtener más información sobre cómo recuperar las claves de recuperación de BitLocker, vea las [instrucciones para recuperar las claves](../azure-stack-security-bitlocker.md).

## <a name="common-vulnerabilities-and-exposures"></a>Puntos vulnerables y exposiciones comunes

Esta actualización instala las actualizaciones de seguridad siguientes:  

- [CVE-2018-8256](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8256)
- [CVE-2018-8407](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8407)
- [CVE-2018-8408](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8408)
- [CVE-2018-8415](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8415)
- [CVE-2018-8417](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8417)
- [CVE-2018-8450](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8450)
- [CVE-2018-8471](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8471)
- [CVE-2018-8476](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8476)
- [CVE-2018-8485](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8485)
- [CVE-2018-8544](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8544)
- [CVE-2018-8547](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8547)
- [CVE-2018-8549](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8549)
- [CVE-2018-8550](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8550)
- [CVE-2018-8553](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8553)
- [CVE-2018-8561](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8561)
- [CVE-2018-8562](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8562)
- [CVE-2018-8565](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8565)
- [CVE-2018-8566](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8566)
- [CVE-2018-8584](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2018-8584)

Para más información sobre estas vulnerabilidades, haga clic en los vínculos anteriores o consulte los artículos de Microsoft Knowledge Base [4478877](https://support.microsoft.com/help/4478877).

## <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización

- Si ejecuta el cmdlet de PowerShell **Get-AzureStackLog** después de ejecutar **Test-AzureStack** en la misma sesión del punto de conexión con privilegios (PEP), **Get-AzureStackLog** devuelve un error. Para solucionar este problema, cierre la sesión PEP en la que ejecutó **Test-AzureStack** y, a continuación, abra una sesión nueva para ejecutar **Get-AzureStackLog**.

- Durante la instalación de la actualización 1811, asegúrese de que estén cerradas todas las instancias del portal de administración. El portal de usuarios puede permanecer abierto, pero se debe cerrar el portal de administración.

- Cuando se ejecuta [Test-AzureStack](../azure-stack-diagnostic-test.md), si se produce un error en la prueba de **AzsInfraRoleSummary** o **AzsPortalApiSummary**, se le pedirá que ejecute **Test-AzureStack** con la marca `-Repair`.  Si ejecuta este comando, se produce un error con el siguiente mensaje de error:  `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`  El problema se solucionará en una futura versión.

- Durante la instalación de la actualización 1811, el portal de usuarios de Azure Stack no estará disponible mientras se configura el host de extensiones. La configuración del host de extensiones puede tardar hasta 5 horas. Durante ese tiempo, puede comprobar el estado de una actualización o reanudar la instalación de una actualización con errores con [el punto de conexión con privilegios elevados o PowerShell de administrador de Azure Stack](../azure-stack-monitor-update.md). 

- Durante la instalación de la actualización 1811, el panel del portal de usuario podría no estar disponible y se pueden perder las personalizaciones. Puede restaurar el panel a la configuración predeterminada una vez finalizada la actualización si abre la configuración del portal y hace clic en **Restaurar la configuración predeterminada**.

- Al ejecutar [Test-AzureStack](../azure-stack-diagnostic-test.md), se muestra un mensaje de advertencia desde el Controlador de administración de placa base (BMC). Puede omitir esta advertencia sin problemas.

- <!-- 2468613 - IS --> Durante la instalación de esta actualización, es posible que vea alertas con el título `Error – Template for FaultType UserAccounts.New is missing.` Puede ignorarlas. Estas alertas se cierran automáticamente cuando se complete la instalación de esta actualización.

- <!-- 3139614 | IS --> Si aplicó una actualización a Azure Stack de su OEM, es posible que no se muestre la notificación **Actualización disponible** en el portal de administrador de Azure Stack. Para instalar la actualización de Microsoft, descárguela e impórtela manualmente siguiendo las instrucciones que encontrará aquí: [Aplicación de actualizaciones en Azure Stack](../azure-stack-apply-updates.md).

## <a name="post-update-steps"></a>Pasos posteriores a la actualización

- Después de instalar esta actualización, instale todas las revisiones aplicables. Para obtener más información, vea [Hotfixes](#hotfixes) (Revisiones), así como nuestra [directiva de mantenimiento](../azure-stack-servicing-policy.md).  

- Recupere las claves de cifrado de los datos en reposo y almacénelas de forma segura fuera de la implementación de Azure Stack. Siga las [instrucciones para recuperar las claves](../azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

Los siguientes son problemas conocidos posteriores a la instalación de esta compilación.

### <a name="portal"></a>Portal

<!-- 2930820 - IS ASDK --> 
- En los portales de administrador y de usuario, si se busca "Docker", el elemento se devuelve de forma incorrecta. No está disponible en Azure Stack. Si se intenta crearlo, se muestra una hoja con una indicación de error. 

<!-- 2931230 – IS  ASDK --> 
- No se pueden eliminar los planes que se agregan a una suscripción de usuario como planes complementarios, aunque se quite el plan de la suscripción de usuario. El plan permanecerá hasta que también se eliminen las suscripciones a las que haga referencia el plan complementario. 

<!-- TBD - IS ASDK --> 
- Los dos tipos de suscripción administrativa que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**. Estos tipos de suscripción están visibles en los nuevos entornos de Azure Stack a partir de la versión 1804 pero aún no están listos para su uso. Tendrá que seguir usando el tipo de suscripción del **proveedor predeterminado**.

<!-- TBD - IS ASDK --> 
- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y después elimine las suscripciones del usuario.

<!-- TBD - IS ASDK --> 
- No puede ver los permisos de la suscripción mediante los portales de Azure Stack. Como alternativa, use [PowerShell para comprobar los permisos](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

### <a name="health-and-monitoring"></a>Estado y supervisión

<!-- 1264761 - IS ASDK -->  
-  Es posible que vea alertas del componente **Controlador de mantenimiento** con los siguientes detalles:  

  - Alerta 1:
     - NOMBRE: rol de infraestructura incorrecto
     - GRAVEDAD: advertencia
     - COMPONENTE: controlador de mantenimiento
     - DESCRIPCIÓN: el controlador de mantenimiento Heartbeat Scanner no está disponible. Esto puede afectar a los informes y a las métricas de mantenimiento.  

  - Alerta 2:
     - NOMBRE: rol de infraestructura incorrecto
     - GRAVEDAD: advertencia
     - COMPONENTE: controlador de mantenimiento
     - DESCRIPCIÓN: el controlador de mantenimiento Fault Scanner no está disponible. Esto puede afectar a los informes y a las métricas de mantenimiento.

    Ambas alertas pueden omitirse sin problemas. Se cerrarán automáticamente pasado un tiempo.  

### <a name="compute"></a>Proceso

- Al crear una máquina virtual (VM) Windows, la hoja **Configuración** requiere que seleccione un puerto de entrada público para poder continuar. En 1811, esta configuración es necesaria, pero no tiene ningún efecto. Esto se debe a que la característica depende de Azure Firewall, que no está implementado en Azure Stack. Puede seleccionar **No hay puertos de entrada públicos** o cualquiera de las otras opciones para continuar con la creación de máquinas virtuales. La configuración no tendrá ningún efecto.

- Al crear una nueva máquina virtual (VM) Windows, puede aparecer el siguiente error:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque. Para solucionar este problema, vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.

- Al crear una [VM de la serie Dv2](../../user/azure-stack-vm-considerations.md#vm-sizes), las VM D11-14v2 le permitirán crear 4, 8, 16 y 32 discos de datos, respectivamente. Sin embargo, el panel de creación de la VM muestra 8, 16, 32 y 64 discos de datos.

- Los registros de uso en Azure Stack pueden contener mayúsculas inesperadas; por ejemplo:

   `{"Microsoft.Resources":{"resourceUri":"/subscriptions/<subid>/resourceGroups/ANDREWRG/providers/Microsoft.Compute/
   virtualMachines/andrewVM0002","location":"twm","tags":"null","additionalInfo":
   "{\"ServiceType\":\"Standard_DS3_v2\",\"ImageType\":\"Windows_Server\"}"}}`

   En este ejemplo, el nombre del grupo de recursos debería ser **AndrewRG**. Puede omitir esta incoherencia sin problemas.

<!-- 3235634 – IS, ASDK -->
- Para implementar máquinas virtuales con tamaños que contienen un sufijo **v2**; por ejemplo, **Standard_A2_v2**, especifique el sufijo como **Standard_A2_v2** (v minúscula). No use **Standard_A2_V2** (v mayúscula). Esto funciona en Azure global y es una incoherencia en Azure Stack.

<!-- 2869209 – IS, ASDK --> 
- Cuando se usa el [cmdlet **Add-AzsPlatformImage**](/powershell/module/azs.compute.admin/add-azsplatformimage), se debe usar el parámetro **-OsUri** como el URI de la cuenta de almacenamiento donde se ha cargado el disco. Si usa la ruta de acceso local del disco, se produce el siguiente error en el cmdlet: 

    `Long running operation failed with status 'Failed'`

<!--  2795678 – IS, ASDK --> 
- Cuando usa el portal para crear máquinas virtuales (VM) en un tamaño de máquina virtual premium (DS, Ds_v2, FS, FSv2), la VM se crea en una cuenta de almacenamiento estándar. La creación en una cuenta de almacenamiento estándar no afecta al funcionamiento, E/S por segundo ni la facturación. Puede omitir la siguiente advertencia sin problemas: 

    `You've chosen to use a standard disk on a size that supports premium disks. This could impact operating system performance and is not recommended. Consider using premium storage (SSD) instead.`

<!-- 2967447 - IS, ASDK --> 
- La experiencia de creación de conjuntos de escalado de máquinas virtuales (VMSS) proporciona la versión 7.2 basada en CentOS como una opción para la implementación. Como esa imagen no está disponible en Azure Stack, seleccione otro sistema operativo para la implementación o use una plantilla de Azure Resource Manager en la que se especifique otra imagen de CentOS que el operador haya descargado de Marketplace antes de la implementación.  

<!-- 2724873 - IS --> 
-  Al usar los cmdlets de PowerShell **Start-AzsScaleUnitNode** o **Stop-AzsScaleunitNode** para administrar las unidades de escalado, el primer intento para iniciar o detener la unidad de escalado podría producir un error. Si se produce un error en el cmdlet en la primera ejecución, ejecute el cmdlet una segunda vez. La segunda ejecución debe completar correctamente la operación. 

<!-- TBD - IS ASDK --> 
- Si aprovisionar una extensión en una implementación de máquina virtual tarda demasiado tiempo, deje que se agote el tiempo de espera de aprovisionamiento en lugar de intentar detener el proceso para desasignar o eliminar la máquina virtual.  

<!-- 1662991 IS ASDK --> 
- No se admite el diagnóstico de máquinas virtuales Linux en Azure Stack. Si implementa una máquina virtual Linux con diagnósticos de máquina virtual habilitado, se producirá un error en la implementación. Tampoco se podrá realizar la implementación si habilita las métricas básicas de máquina virtual Linux a través de la configuración de diagnóstico.  

<!-- 3507629 - IS, ASDK --> 
- Managed Disks crea dos nuevos [tipos de cuota de proceso](../azure-stack-quota-types.md#compute-quota-types) para limitar la capacidad máxima de los discos administrados que se pueden aprovisionar. De forma predeterminada, se asignan 2 048 GiB para cada tipo de cuota de discos administrados. Sin embargo, es posible que encuentre los siguientes problemas:

   - Para las cuotas creadas antes de la actualización 1808, la cuota de Managed Disks mostrará valores 0 en el portal de administrador, aunque se han asignado 2 048 GiB. Puede aumentar o disminuir el valor según sus necesidades reales y el valor de la cuota recién establecido invalida el predeterminado de 2 048 GiB.
   - Si actualiza el valor de la cuota a 0, equivale al valor predeterminado de 2 048 GiB. Como alternativa, establezca el valor de la cuota en 1.

<!-- TBD - IS ASDK --> 
- Después de aplicar la actualización 1811, se pueden producir los siguientes problemas al implementar máquinas virtuales con Managed Disks:

   - Si la suscripción se creó antes de la actualización 1808, se puede producir un error en la implementación de máquinas virtuales con Managed Disks con un mensaje de error interno. Para resolver el error, siga estos pasos en cada suscripción:
      1. En el portal del inquilino, vaya a **Suscripciones** y busque la suscripción. Haga clic en **Proveedores de recursos**, después en **Microsoft.Compute** y luego en **Volver a registrar**.
      2. En la misma suscripción, vaya a **Control de acceso (IAM)** y compruebe que el rol **AzureStack-DiskRP-Client** aparece en la lista.
   - Si ha configurado un entorno de varios inquilinos, se puede producir un error con un mensaje de error interno en la implementación de máquinas virtuales en una suscripción asociada con un directorio de invitados. Para solucionar el error, siga los pasos de [este artículo](../azure-stack-enable-multitenancy.md#register-azure-stack-hub-with-the-guest-directory) para volver a configurar cada uno de los directorios de invitado.

- Una máquina virtual de Ubuntu 18.04 creada con la autorización de SSH habilitada no le permitirá usar las claves SSH para iniciar sesión. Como alternativa, utilice el acceso a la VM para la extensión de Linux a fin de implementar las claves SSH después del aprovisionamiento o utilice la autenticación basada en contraseña.

### <a name="networking"></a>Redes  

<!-- 1766332 - IS ASDK --> 
-  En **Redes**, si hace clic en **Create VPN Gateway** (Crear instancia de VPN Gateway) para configurar una conexión VPN, aparece **Policy Based** (Basada en directivas) como un tipo de VPN. No seleccione esta opción. En Azure Stack solo se admite la opción **Route Based** (Basada en rutas).

<!-- 1902460 - IS ASDK --> 
- Azure Stack admite una única *puerta de enlace de red local* por dirección IP. Y esto se aplica a las suscripciones de todos los inquilinos. Tras la creación de la primera conexión a la puerta de enlace de red local, los sucesivos intentos para crear un recurso de puerta de enlace de red local con la misma dirección IP se rechazan.

<!-- 16309153 - IS ASDK --> 
- En una red virtual que se ha creado con una configuración de servidor DNS de **Automática**, se produce un error al cambiar a un servidor DNS personalizado. La configuración actualizada no se inserta en las máquinas virtuales de esa red virtual.

<!-- 2529607 - IS ASDK --> 
- Durante la *rotación secreta* de Azure Stack, hay un período en el que las direcciones IP públicas no son accesibles entre dos y cinco minutos.

<!-- 2664148 - IS ASDK --> 
- En los escenarios en los que el inquilino acceda a sus máquinas virtuales mediante un túnel VPN S2S, podría encontrarse con que los intentos de conexión producen un error si la subred local se ha agregado a la puerta de enlace de red local una vez que la puerta de enlace ya está creada. 

- En el portal de Azure Stack, al cambiar una dirección IP estática por una configuración de IP que está enlazada a un adaptador de red conectado a una instancia de máquina virtual, verá un mensaje de advertencia que indica que 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`. 

    Puede ignorar este mensaje; la dirección IP se cambiará incluso si no se reinicia la instancia de máquina virtual.

- En el portal, en la hoja **Propiedades de redes** hay un vínculo para **Reglas de seguridad efectivas** para cada adaptador de red. Si hace clic en este vínculo, se abrirá una nueva hoja que muestra el mensaje de error `Not Found.`. Este error se produce porque Azure Stack no admite aún las **reglas de seguridad efectivas**.

- En el portal, si agrega una regla de seguridad de entrada y selecciona **Etiqueta de servicio** como origen, se muestran varias opciones en la lista **Etiqueta de origen** que no están disponibles para Azure Stack. Las únicas opciones que son válidas en Azure Stack son las siguientes:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    No se admiten las demás opciones como etiquetas de origen en Azure Stack. De forma similar, si agrega una regla de seguridad de salida y selecciona **Etiqueta de servicio** como destino, se muestra la misma lista de opciones para **Etiqueta de origen**. Las únicas opciones válidas son los mismas que para **Etiqueta de origen**, tal y como se describe en la lista anterior.

- El cmdlet **New-AzureRmIpSecPolicy** de PowerShell no admite la configuración **DHGroup24** para el parámetro `DHGroup`.

- Los grupos de seguridad de red (NSG) no funcionan en Azure Stack de la misma manera que en Azure global. En Azure, se pueden establecer varios puertos en una regla de NSG (mediante el portal, PowerShell y plantillas de Resource Manager). En Azure Stack, no se pueden establecer varios puertos en una regla de NSG a través del portal. Para solucionar este problema, use una plantilla de Resource Manager para establecer estas reglas adicionales.

### <a name="infrastructure-backup"></a>Copia de seguridad de infraestructura

<!--scheduler config lost, bug 3615401, new issue in 1811,  hectorl-->
<!-- TSG: https://www.csssupportwiki.com/index.php/Azure_Stack/KI/Backup_scheduler_configuration_lost --> 
- Después de habilitar las copias de seguridad automáticas, el servicio de programador pasa al estado deshabilitado de forma inesperada. El servicio del controlador de copia de seguridad detectará que las copias de seguridad automáticas están desactivadas y generará una advertencia en el portal de administrador. Esta advertencia se espera cuando las copias de seguridad automáticas están deshabilitadas. 
    - Causa: Este problema se debe a un error en el servicio que da lugar a la pérdida de la configuración del programador. Este error no cambia la ubicación de almacenamiento, el nombre de usuario, la contraseña o la clave de cifrado.   
    - Corrección: Para mitigar este problema, abra la hoja de configuración del controlador de copia de seguridad en el proveedor de recursos de copia de seguridad de infraestructura y seleccione **Habilitar copias de seguridad automáticas**. Asegúrese de establecer el período de retención y la frecuencia deseada.
    - Repetición: Bajo 

<!-- ### SQL and MySQL-->

### <a name="app-service"></a>App Service

<!-- 2352906 - IS ASDK --> 
- Debe registrar el proveedor de recursos de almacenamiento antes de crear su primera función de Azure en la suscripción.


<!-- ### Usage -->

 
<!-- #### Identity -->
<!-- #### Marketplace -->

### <a name="syslog"></a>syslog

- La configuración de syslog no persiste durante todo un ciclo de actualización, lo que hace que el cliente de syslog pierda su configuración y se dejen de reenviar los mensajes de syslog. Este problema se aplica a todas las versiones de Azure Stack desde la disponibilidad general del cliente de syslog (1809). Para solucionar este programa, vuelva a configurar el cliente de syslog después de aplicar una actualización de Azure Stack.

## <a name="download-the-update"></a>Descarga de la actualización

Puede descargar el paquete de actualización de Azure Stack 1811 desde [aquí](https://aka.ms/azurestackupdatedownload). 

Únicamente en los escenarios en los que estén conectadas, las implementaciones de Azure Stack comprueban periódicamente un punto de conexión seguro y le notifican automáticamente si una actualización está disponible para la nube. Para obtener más información, vea la [administración de actualizaciones de Azure Stack](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Pasos siguientes

- Para revisar la directiva de servicio de los sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido, consulte [Directiva de servicio de Azure Stack](../azure-stack-servicing-policy.md).  
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](../azure-stack-monitor-update.md).  
- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).  
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](../azure-stack-apply-updates.md).  