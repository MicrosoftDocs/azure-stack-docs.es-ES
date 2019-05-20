---
title: Actualización 1903 de Azure Stack | Microsoft Docs
description: Obtenga información sobre la actualización 1903 de los sistemas integrados de Azure Stack, como novedades, problemas conocidos y dónde se puede descargar dicha actualización.
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
ms.date: 05/07/2019
ms.author: sethm
ms.reviewer: adepue
ms.lastreviewed: 04/20/2019
ms.openlocfilehash: 04cec74873869f19c7bd762753f7fe89f51c184d
ms.sourcegitcommit: 39ba6d18781aed98b29ac5e08aac2d75c37bf18c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2019
ms.locfileid: "65386743"
---
# <a name="azure-stack-1903-update"></a>Actualización de Azure Stack 1903

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el contenido del paquete de actualización 1903. La actualización incluye mejoras, correcciones y nuevas características de esta versión de Azure Stack. En este artículo también se describen los problemas conocidos de esta versión y se incluye un vínculo para que pueda descargar la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 1903 de Azure Stack es **1.1903.0.35**.

> [!IMPORTANT]
> La carga de 1903 no incluye una versión de ASDK.

## <a name="hotfixes"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la [revisión más reciente de Azure Stack](#azure-stack-hotfixes) para 1902 antes de actualizar Azure Stack a 1903.

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

> [!TIP]
> Suscríbase a las siguientes fuentes *RRS* o *Atom* para mantenerse al día con las revisiones de Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Revisiones de Azure Stack

- **1902**: [KB 4500637: revisión de Azure Stack 1.1902.3.75](https://support.microsoft.com/help/4500637)
- **1903**: [KB 4500638: revisión de Azure Stack 1.1903.2.39](https://support.microsoft.com/help/4500638)

## <a name="improvements"></a>Mejoras

- La carga de la actualización 1903 contiene una actualización para los componentes de Azure Stack que no incluyen el sistema operativo subyacente que hospeda Azure Stack. Esto permite limitar determinadas actualizaciones a un ámbito específico. Como resultado, el tiempo esperado para completar la actualización 1903 es menor (aproximadamente 16 horas, pero el tiempo exacto puede variar). Esta disminución en el tiempo de ejecución es específica de la actualización 1903 y las actualizaciones posteriores pueden contener actualizaciones del sistema operativo, lo que implica diferentes tiempos de ejecución. Las actualizaciones futuras proporcionarán una orientación similar sobre el tiempo esperado que la actualización tardará en completarse, dependiendo de la carga incluida.

- Se ha corregido un error relativo a las redes que impedía que los cambios realizados en el valor de **tiempo de espera de inactividad (minutos)** de una **dirección IP pública** surtieran efecto. Anteriormente, los cambios en este valor se ignoraban, por lo que, independientemente de los cambios realizados, el valor se establecía de forma predeterminada en 4 minutos. Este parámetro controla la cantidad de minutos para mantener una conexión TCP abierta sin que dependa del envío de mensajes de mantenimiento de los clientes. Tenga en cuenta que este error solo afectaba a las direcciones IP públicas de nivel de instancia, no a las direcciones IP públicas asignadas a un equilibrador de carga.

- Mejoras en la confiabilidad del motor de actualización, incluida la corrección automática de problemas comunes para que las actualizaciones se apliquen sin interrupción.

- Mejoras en la detección y corrección de las condiciones de espacio en disco insuficiente.

- Azure Stack ahora admite agentes de Linux para Microsoft Azure posteriores a la versión 2.2.35. Esta compatibilidad permite que los clientes conserven imágenes coherentes de Linux entre Azure y Azure Stack. Se agregó como parte de las revisiones de 1901 y 1902.

### <a name="secret-management"></a>Administración de secretos

- Azure Stack ahora admite la rotación del certificado raíz que usan los certificados para la rotación de secretos externos. Para más información, [consulte este artículo](azure-stack-rotate-secrets.md).

- 1903 contiene mejoras de rendimiento para la rotación de secretos que disminuyen el tiempo que tarda la ejecución de la rotación de secretos internos.

## <a name="prerequisites"></a>Requisitos previos

> [!IMPORTANT]
> Instale la [revisión más reciente de Azure Stack](#azure-stack-hotfixes) para 1902 (si la hay) antes de actualizar a 1903.

- Asegúrese de usar la versión más reciente de la [herramienta de planeamiento de capacidad de Azure Stack](https://aka.ms/azstackcapacityplanner) para planificar la carga de trabajo y ajustar su tamaño. La última versión contiene correcciones de errores y proporciona nuevas características que se publican con cada actualización de Azure Stack.

- Antes de iniciar la instalación de esta actualización, ejecute [Test-AzureStack](azure-stack-diagnostic-test.md) con el parámetro siguiente para validar el estado de Azure Stack y resolver todos los problemas operativos detectados, incluidas todas las advertencias y errores. Repase también las alertas activas y resuelva las que requieran alguna acción:

    ```powershell
   Test-AzureStack -Group UpdateReadiness
    ```

- Cuando System Center Operations Manager administre Azure Stack, asegúrese de actualizar el [módulo de administración para Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) a la versión 1.0.3.11 antes de aplicar la revisión 1903.

- El formato del paquete para la actualización de Azure Stack ha cambiado de **.bin/.exe/.xml** a **.zip/.xml** a partir de la versión 1902. Los clientes con unidades de escalado de Azure Stack conectadas verán el mensaje **Actualización disponible** en el portal. Asimismo, los clientes que no están conectados ahora pueden, simplemente, descargar e importar el archivo ZIP con la extensión .xml correspondiente.

<!-- ## New features -->

<!-- ## Fixed issues -->

<!-- ## Common vulnerabilities and exposures -->

## <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización

- Al ejecutar [Test-AzureStack](azure-stack-diagnostic-test.md), se muestra un mensaje de advertencia desde el Controlador de administración de placa base (BMC). Puede omitir esta advertencia sin problemas.

<!-- 2468613 - IS -->
- Durante la instalación de esta actualización, puede que vea alertas con el título **Error – Template for FaultType UserAccounts. New is missing.** (Error: Falta la plantilla para FaultType UserAccounts.New). Puede omitir estas alertas con seguridad. Estas alertas se cierran automáticamente cuando se complete la instalación de esta actualización.

## <a name="post-update-steps"></a>Pasos posteriores a la actualización

- Después de instalar esta actualización, instale todas las revisiones aplicables. Para obtener más información, vea [Hotfixes](#hotfixes) (Revisiones), así como nuestra [directiva de mantenimiento](azure-stack-servicing-policy.md).

- Recupere las claves de cifrado de los datos en reposo y almacénelas de forma segura fuera de la implementación de Azure Stack. Siga las [instrucciones para recuperar las claves](azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

Los siguientes son problemas conocidos posteriores a la instalación de esta compilación.

### <a name="portal"></a>Portal

- En el panel de portal de usuarios, al intentar hacer clic en el icono **Comentarios**, se abre una pestaña de explorador vacía. Como alternativa, puede usar [UserVoice de Azure Stack](https://aka.ms/azurestackuservoice) para presentar una solicitud a UserVoice.

<!-- 2930820 - IS ASDK -->
- En los portales de administrador y de usuario, si se busca "Docker", el elemento se devuelve de forma incorrecta. No está disponible en Azure Stack. Si se intenta crearlo, se muestra una hoja con una indicación de error.

<!-- 2931230 - IS  ASDK -->
- No se pueden eliminar los planes que se agregan a una suscripción de usuario como planes complementarios, aunque se quite el plan de la suscripción de usuario. El plan permanecerá hasta que también se eliminen las suscripciones a las que haga referencia el plan complementario.

<!-- TBD - IS ASDK -->
- Los dos tipos de suscripción administrativa que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**. Estos tipos de suscripción están visibles en los nuevos entornos de Azure Stack a partir de la versión 1804 pero aún no están listos para su uso. Tendrá que seguir usando el tipo de suscripción del **proveedor predeterminado**.

<!-- 3557860 - IS ASDK -->
- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y después elimine las suscripciones del usuario.

<!-- 1663805 - IS ASDK --> 
- No puede ver los permisos de la suscripción mediante los portales de Azure Stack. Como alternativa, use [PowerShell para comprobar los permisos](/powershell/module/azurerm.resources/get-azurermroleassignment).

<!-- Daniel 3/28 -->
- En el portal de usuarios, cuando se desplaza a un blob en una cuenta de almacenamiento e intenta abrir la **directiva de acceso** desde el árbol de navegación, la ventana posterior no se puede cargar. Para solucionar este problema, los cmdlets de PowerShell siguientes permiten crear, recuperar, establecer y eliminar directivas de acceso, respectivamente:

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

  
<!-- Daniel 3/28 -->
- En el portal de usuarios, al intentar cargar un blob mediante la opción **OAuth(preview)**, la tarea genera un mensaje de error. Para solucionar este problema, cargue el blob mediante la opción **SAS**.

- Cuando inicie sesión en los portales de Azure Stack, podría ver notificaciones sobre la instancia pública de Azure Portal. Puede ignorar estas notificaciones de forma segura, ya que actualmente no se aplican a Azure Stack (por ejemplo, "1 nueva actualización: las siguientes actualizaciones ya están disponibles: actualización de abril de 2019 de Azure Portal").

- En el panel de portal de usuarios, al seleccionar el icono **Comentarios**, se abre una pestaña de explorador vacía. Como alternativa, puede usar [UserVoice de Azure Stack](https://aka.ms/azurestackuservoice) para presentar una solicitud a UserVoice.

<!-- ### Health and monitoring -->

### <a name="compute"></a>Proceso

- Al crear una nueva máquina virtual (VM) Windows, puede aparecer el siguiente error:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque. Para solucionar este problema, vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- La experiencia de creación de conjuntos de escalado de máquinas virtuales proporciona la versión 7.2 basada en CentOS como una opción para la implementación. Como esa imagen no está disponible en Azure Stack, seleccione otro sistema operativo para la implementación o use una plantilla de Azure Resource Manager en la que se especifique otra imagen de CentOS que el operador haya descargado de Marketplace antes de la implementación.

<!-- TBD - IS ASDK -->
- Después de aplicar la actualización 1903, se pueden producir los siguientes problemas al implementar VM con Managed Disks:

   - Si la suscripción se creó antes de la actualización 1808, se puede producir un error en la implementación de máquinas virtuales con Managed Disks con un mensaje de error interno. Para resolver el error, siga estos pasos en cada suscripción:
      1. En el portal del inquilino, vaya a **Suscripciones** y busque la suscripción. Haga clic en **Proveedores de recursos**, después en **Microsoft.Compute** y luego en **Volver a registrar**.
      2. En la misma suscripción, vaya a **Control de acceso (IAM)** y verifique que **Azure Stack – Managed Disk** (Azure Stack - Disco administrado) aparece en la lista.
   - Si ha configurado un entorno de varios inquilinos, se puede producir un error con un mensaje de error interno en la implementación de máquinas virtuales en una suscripción asociada con un directorio de invitados. Para solucionar el error, siga los pasos de [este artículo](azure-stack-enable-multitenancy.md#registering-azure-stack-with-the-guest-directory) para volver a configurar cada uno de los directorios de invitado.

- Una máquina virtual de Ubuntu 18.04 creada con la autorización de SSH habilitada no le permitirá usar las claves SSH para iniciar sesión. Como alternativa, utilice el acceso a la VM para la extensión de Linux a fin de implementar las claves SSH después del aprovisionamiento o utilice la autenticación basada en contraseña.

- Si no tiene un host de ciclo de vida de hardware (HLH): antes de la compilación 1902, tenía que establecer la directiva de grupo **Configuración del equipo\Configuración de Windows\Configuración de seguridad\Directivas locales\Opciones de seguridad** en **Send LM & NTLM - use NTLMv2 session security if negotiated** (Enviar LM y NTLM: usar la seguridad de sesión NTLMv2 si se negocia). Desde las compilación 1902, debe dejarla como **Sin definir** o establecerla en **Enviar solo respuesta NTLMv2** (que es el valor predeterminado). De lo contrario, no podrá establecer ninguna sesión remota de PowerShell y verá el error **Acceso denegado**:

   ```powershell
   $Session = New-PSSession -ComputerName x.x.x.x -ConfigurationName PrivilegedEndpoint -Credential $Cred
   New-PSSession : [x.x.x.x] Connecting to remote server x.x.x.x failed with the following error message : Access is denied. For more information, see the
   about_Remote_Troubleshooting Help topic.
   At line:1 char:12
   + $Session = New-PSSession -ComputerName x.x.x.x -ConfigurationNa ...
   +            ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
      + CategoryInfo          : OpenError: (System.Manageme....RemoteRunspace:RemoteRunspace) [New-PSSession], PSRemotingTransportException
      + FullyQualifiedErrorId : AccessDenied,PSSessionOpenFailed
   ```

- No se puede quitar un conjunto de escalado desde la hoja **Virtual Machine Scale Sets**. Como alternativa, seleccione el conjunto de escalado que quiere quitar y, a continuación, haga clic en el botón **Eliminar** del panel **Introducción**.

### <a name="networking"></a>Redes

<!-- 3239127 - IS, ASDK -->
- En el portal de Azure Stack, al cambiar una dirección IP estática por una configuración de IP que está enlazada a un adaptador de red conectado a una instancia de máquina virtual, verá un mensaje de advertencia que indica que

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`

    Puede ignorar este mensaje; la dirección IP se cambiará incluso si no se reinicia la instancia de máquina virtual.

<!-- 3632798 - IS, ASDK -->
- En el portal, si agrega una regla de seguridad de entrada y selecciona **Etiqueta de servicio** como origen, se muestran varias opciones en la lista **Etiqueta de origen** que no están disponibles para Azure Stack. Las únicas opciones que son válidas en Azure Stack son las siguientes:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**

  No se admiten las demás opciones como etiquetas de origen en Azure Stack. De forma similar, si agrega una regla de seguridad de salida y selecciona **Etiqueta de servicio** como destino, se muestra la misma lista de opciones para **Etiqueta de origen**. Las únicas opciones válidas son los mismas que para **Etiqueta de origen**, tal y como se describe en la lista anterior.

- Los grupos de seguridad de red (NSG) no funcionan en Azure Stack de la misma manera que en Azure global. En Azure, se pueden establecer varios puertos en una regla de NSG (mediante el portal, PowerShell y plantillas de Resource Manager). En Azure Stack, sin embargo, no se pueden establecer varios puertos en una regla de NSG mediante el portal. Para solucionar este problema, use una plantilla de Resource Manager o PowerShell para establecer estas reglas adicionales.

<!-- 3203799 - IS, ASDK -->
- Actualmente, Azure Stack no permite conectar más de 4 interfaces de red (NIC) a una instancia de VM, independientemente del tamaño de la instancia.

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

Puede descargar el paquete de actualización 1903 de Azure Stack [aquí](https://aka.ms/azurestackupdatedownload).

Únicamente en los escenarios en los que estén conectadas, las implementaciones de Azure Stack comprueban periódicamente un punto de conexión seguro y le notifican automáticamente si una actualización está disponible para la nube. Para obtener más información, vea la [administración de actualizaciones de Azure Stack](azure-stack-updates.md#using-the-update-tile-to-manage-updates).

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](azure-stack-updates.md).
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](azure-stack-apply-updates.md).
- Para revisar la directiva de servicio de los sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido, consulte [Directiva de servicio de Azure Stack](azure-stack-servicing-policy.md).
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](azure-stack-monitor-update.md).
