---
title: Actualización 1901 de Azure Stack | Microsoft Docs
description: Aprenda sobre la actualización 1901 de los sistemas integrados de Azure Stack, como novedades, problemas conocidos y dónde se puede descargar la actualización.
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
ms.reviewer: adepue
ms.lastreviewed: 03/27/2019
ms.openlocfilehash: d3225284c8352771042742f8c8d94e4ae01abe2c
ms.sourcegitcommit: f9be5640dd445b3d926c9ce3e2165e96c72ece89
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2021
ms.locfileid: "100009254"
---
# <a name="azure-stack-1901-update"></a>Actualización 1901 de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el contenido del paquete de actualización 1901. La actualización incluye mejoras, correcciones y nuevas características de esta versión de Azure Stack. En este artículo también se describen los problemas conocidos de esta versión y se incluye un vínculo para que pueda descargar la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización de Azure Stack 1901 es **1.1901.0.95** o **1.1901.0.99** a partir del 26 de febrero de 2019. Vea la nota siguiente:

> [!IMPORTANT]  
> Microsoft ha detectado un problema que puede afectar a los clientes que actualicen de 1811 (1.1811.0.101) a 1901 y ha lanzado el paquete actualizado 1901 a fin de solucionar el problema: compilación 1.1901.0.99, actualizada desde 1.1901.0.95. Los clientes que ya hayan realizado la actualización a 1.1901.0.95 no deberán tomar ninguna medida adicional.
>
> Los clientes que estén conectados y usen la versión 1811 verán automáticamente el nuevo paquete 1901 (1.1901.0.99) disponible en el portal de administrador, y deberían instalarlo cuando esté listo. Los clientes que estén desconectados pueden descargar e importar el nuevo paquete 1901 según el proceso [que se describe aquí](../azure-stack-apply-updates.md).
>
> Los clientes que usen cualquiera de las versiones de 1901 no se verán afectados al instalar el siguiente paquete completo o revisión.

## <a name="hotfixes"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la [revisión más reciente de Azure Stack](#azure-stack-hotfixes) para 1811 antes de actualizar Azure Stack a 1901.

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

> [!TIP]  
> Suscríbase a las siguientes fuentes *RRS* o *Atom* para mantenerse al día con las revisiones de Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Revisiones de Azure Stack

Si ya tiene la 1901 y aún no ha instalado ninguna revisión, puede instalar la 1902 directamente, sin instalar primero la revisión 1901.

- **1811**: Ninguna revisión actual disponible.
- **1901**: [KB 4500636: revisión de Azure Stack 1.1901.5.109](https://support.microsoft.com/help/4500636)

## <a name="prerequisites"></a>Requisitos previos

> [!IMPORTANT]
> Instale la [revisión más reciente de Azure Stack](#azure-stack-hotfixes) para 1811 (si la hay) antes de actualizar a 1901. Si ya tiene la 1901 y aún no ha instalado ninguna revisión, puede instalar la 1902 directamente, sin instalar primero la revisión 1901.

- Antes de iniciar la instalación de esta actualización, ejecute [Test-AzureStack](../azure-stack-diagnostic-test.md) con los parámetros siguientes para validar el estado de Azure Stack y resolver todos los problemas operativos detectados, incluidas todas las advertencias y errores. Repase también las alertas activas y resuelva las que requieran alguna acción:

    ```powershell
    Test-AzureStack -Include AzsControlPlane, AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

- Cuando System Center Operations Manager (SCOM) administre Azure Stack, asegúrese de actualizar el módulo de administración para Microsoft Azure Stack a la versión 1.0.3.11 antes de aplicar la revisión 1901.

## <a name="new-features"></a>Nuevas características

Esta actualización incluye las siguientes nuevas características y correcciones para Azure Stack:

- Las imágenes administradas en Azure Stack le permiten crear un objeto de imagen administrada en una máquina virtual generalizada (tanto administrada como no administrada) que, de aquí en adelante, solo puede crear máquinas virtuales de discos administrados. Para más información, consulte [Managed Disks de Azure Stack](../../user/azure-stack-managed-disk-considerations.md#managed-images).

- **AzureRm 2.4.0**
   * **AzureRm.Profile**  
         Corrección del error: `Import-AzureRmContext` para deserializar el token guardado correctamente.  
   * **AzureRm.Resources**  
         Corrección del error: `Get-AzureRmResource` para la realización de consultas sin distinción entre mayúsculas y minúsculas por tipo de recurso.  
   * **Azure.Storage**  
         El módulo de acumulación de AzureRm incluye ahora la versión 4.5.0 ya publicada que admite **api-version 2017-07-29**.  
   * **AzureRm.Storage**  
         El módulo de acumulación de AzureRm incluye ahora la versión 5.0.4 ya publicada que admite **api-version 2017-10-01**.  
   * **AzureRm.Compute**  
         Se han agregado conjuntos de parámetros en `New-AzureRmVM` y `New-AzureRmVmss`, y el parámetro `-Image` admite la especificación de imágenes de usuario.  
   * **AzureRm.Insights**  
         El módulo de acumulación AzureRm incluye ahora la versión 5.1.5 ya publicada que admite **api-version 2018-01-01** para métricas, definiciones de métricas y tipos de recursos.

- **AzureStack 1.7.1**: esta es una versión de cambios importantes. Para más información sobre los cambios importantes, consulte https://aka.ms/azspshmigration171.
   * **Módulo Azs.Backup.Admin**  
         Cambio importante: La copia de seguridad cambia al modo de cifrado basado en certificados. La compatibilidad con claves simétricas está en desuso.  
   * **Módulo Azs.Fabric.Admin**  
         `Get-AzsInfrastructureVolume` está desusado. Use el nuevo cmdlet `Get-AzsVolume`.  
         `Get-AzsStorageSystem` está desusado.  Use el nuevo cmdlet `Get-AzsStorageSubSystem`.  
         `Get-AzsStoragePool` está desusado. El objeto `StorageSubSystem` contiene la propiedad de capacidad.  
   * **Módulo Azs.Compute.Admin**  
         Corrección de los errores: `Add-AzsPlatformImage`, `Get-AzsPlatformImage`: llamada solo a `ConvertTo-PlatformImageObject` en la ruta de acceso correcta.  
         Corrección de los errores: `Add-AzsVmExtension`, `Get-AzsVmExtension`: llamada a ConvertTo-VmExtensionObject solo en la ruta de acceso correcta.  
   * **Módulo Azs.Storage.Admin**  
         Corrección del error: la nueva cuota de almacenamiento usa valores predeterminados si no se proporciona ninguno.

Para revisar la referencia de los módulos actualizados, consulte [Referencia de los módulos de Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0&viewFallbackFrom=azurestackps-1.7.0&preserve-view=true).

## <a name="fixed-issues"></a>Problemas corregidos

- Se ha corregido un problema en el que el portal mostraba una opción para crear puertas de enlace de VPN basadas en directivas, que no se admiten en Azure Stack. Esta opción se ha eliminado del portal.

<!-- 16523695 - IS, ASDK -->
- Se ha corregido un problema en el que después de actualizar la configuración de DNS para la red virtual de **Use Azure Stack DNS** (Usar DNS de Azure Stack) a **Custom DNS** (DNS personalizado), las instancias no se actualizaban con el nuevo valor.

- <!-- 3235634 - IS, ASDK -->
  Se ha corregido un problema por el que, al implementar VM con tamaños que contienen un sufijo **v2** (por ejemplo, **Standard_A2_v2**), era necesario especificar el sufijo como **Standard_A2_v2** (v minúscula). Al igual que con Azure global, puede usar ahora **Standard_A2_V2** (V en mayúscula).

<!--  2795678 - IS, ASDK --> 
- Se ha corregido un problema en el que se generaba una advertencia al usar el portal para crear máquinas virtuales (VM) con un tamaño de máquina virtual premium (DS,Ds_v2,FS,FSv2). La máquina virtual se creaba en una cuenta de almacenamiento estándar. Aunque este error no afectaba al funcionamiento, la IOPS o la facturación, se ha corregido la advertencia.

<!-- 1264761 - IS ASDK -->  
- Se ha corregido un problema con el componente **Controlador mantenimiento** que generaba las siguientes alertas. Las alertas podían omitirse con seguridad:

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


<!-- 3507629 - IS, ASDK --> 
- Se ha corregido un problema en el que el establecimiento del valor de las cuotas de Managed Disks en 0 en [tipos de cuota de proceso](../azure-stack-quota-types.md#compute-quota-types), era equivalente al valor predeterminado de 2048 GiB. Ahora se respeta el valor de cuota de cero.

<!-- 2724873 - IS --> 
- Se ha corregido un problema en el que al usar los cmdlets de PowerShell **Start-AzsScaleUnitNode** o **Stop-AzsScaleUnitNode** para administrar las unidades de escalado, el primer intento para iniciar o detener la unidad de escalado podría dar error.

<!-- 2724961- IS ASDK --> 
- Se ha corregido un problema en el que al registrar el proveedor de recursos **Microsoft.Insight** en la configuración de la suscripción y crear una máquina virtual Windows con el diagnóstico del sistema operativo invitado habilitado, el gráfico de porcentaje de CPU de la página de información general de la máquina virtual no mostraba los datos de métricas. Ahora se muestran correctamente.

- Se ha corregido un problema en el que al ejecutar el cmdlet **Get-AzureStackLog** después de ejecutar **Test-AzureStack** en la misma sesión del punto de conexión con privilegios (PEP), se devolvía un error. Ahora puede usar la misma sesión PEP en la que ejecutó **Test-AzureStack**.

<!-- bug 3615401, IS -->
- Se ha corregido un problema con las copias de seguridad automáticas donde el servicio de programador pasa al estado deshabilitado de forma inesperada. 

<!--2850083, IS ASDK -->
- Se ha quitado el botón **Reset Gateway** (Restablecer puerta de enlace) del portal de Azure Stack, que daba un error cuando se hacía clic en él. Este botón no tiene ninguna función en Azure Stack, dado que Azure Stack tiene una puerta de enlace multiinquilino en lugar de instancias de máquina virtual dedicadas para la puerta de enlace de VPN de cada inquilino, por lo que se quitó para evitar confusiones. 

<!-- 3209594, IS ASDK -->
- Se ha quitado el vínculo **Effective Security Rules** (Reglas de seguridad vigentes) de la hoja **Networking Properties** (Propiedades de red) dado que esta característica no se admite en Azure Stack. La existencia del vínculo daba la impresión de que esta característica se admitía pero no funcionaba. Para evitar confusiones, se ha eliminado el vínculo.

<!-- 3139614 | IS -->
- Se ha corregido un problema en el que después de aplicar una actualización a Azure Stack desde un OEM, la notificación **Actualización disponible** no aparecía en el portal del administrador de Azure Stack.

## <a name="changes"></a>Cambios

<!-- 3083238 IS -->
- Las mejoras de seguridad en esta actualización dan como resultado un aumento en el tamaño de copia de seguridad del rol de servicio de directorio. Para obtener instrucciones de ajuste de tamaño actualizadas para la ubicación de almacenamiento externo, consulte la [documentación de copia de seguridad de infraestructura../azure-stack-backup-reference.md#storage-location-sizing). Este cambio tiene como resultado un aumento del tiempo necesario para completar la copia de seguridad debido a la transferencia de datos de mayor tamaño. Este cambio afecta a los sistemas integrados. 

- A partir de enero de 2019, puede implementar clústeres de Kubernetes en Servicios de federación de Active Directory (AD FS) registrados y sellos conectados de Azure Stack (se requiere acceso a Internet). Siga las instrucciones que se indican [aquí](../azure-stack-solution-template-kubernetes-cluster-add.md) para descargar el nuevo elemento Marketplace de Kubernetes. Siga las instrucciones que se indican [aquí](../../user/azure-stack-solution-template-kubernetes-adfs.md) para implementar un clúster de Kubernetes. Observe los nuevos parámetros para indicar si el sistema de destino es ADD o AD FS registrado. Si es AD FS, hay nuevos campos para escribir los parámetros de Key Vault donde se almacena el certificado de implementación.

   Tenga en cuenta que a pesar de la compatibilidad con AD FS, la implementación de clústeres de Kubernetes requiere acceso a Internet.

- Después de instalar actualizaciones o revisiones en Azure Stack, se pueden introducir nuevas características que requieren la concesión de nuevos permisos a una o varias aplicaciones de identidad. La concesión de estos permisos requiere acceso administrativo al directorio particular, así que no se puede realizar automáticamente. Por ejemplo:

   ```powershell
   $adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
   $homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com" # This is the primary tenant Azure Stack is registered to

   Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
     -DirectoryTenantName $homeDirectoryTenantName -Verbose
   ```

- Hay una nueva consideración para planear la capacidad de Azure Stack con precisión. Con la actualización 1901, ahora hay un límite en el número total de máquinas virtuales que se pueden crear.  Este límite está diseñado para ser temporal y evitar la inestabilidad de la solución. El origen del problema de estabilidad en los números mayores de las máquinas virtuales se está abordando, pero aún no se ha determinado un tiempo específico para la corrección. Con la actualización 1901, ahora hay un límite por servidor de 60 máquinas de virtuales con un límite total de en la solución de 700.  Por ejemplo, el límite de una máquina virtual de Azure Stack de 8 servidores sería 480 (8 * 60).  Para una solución de Azure Stack de 12 a 16 servidores, el límite sería 700. Este límite se ha creado teniendo todas las consideraciones de capacidad de proceso en cuenta, como la reserva de resistencia y la relación entre la CPU virtual y la física que le gustaría mantener en el sello al operador. Para más información, consulte la nueva versión del planeador de capacidad.  
En caso de que se alcanzara el límite de escalado de máquinas virtuales, se devolverían como resultado los códigos de error siguientes: VMsPerScaleUnitLimitExceeded, VMsPerScaleUnitNodeLimitExceeded. 
 

- La versión de la API de proceso ha aumentado a 2017-12-01.

- La copia de seguridad de infraestructura ahora requiere un certificado con solo una clave pública (. CER) para el cifrado de datos de copia de seguridad. La compatibilidad con claves de cifrado simétricas está en desuso a partir de la versión 1901. Si se configura la copia de seguridad de infraestructura antes de actualizar a 1901, las claves de cifrado permanecerán en su sitio. Dispondrá de al menos 2 actualizaciones más con compatibilidad con versiones anteriores para actualizar la configuración de copia de seguridad. Para más información, consulte [Procedimientos recomendados de copia de seguridad de infraestructura de Azure Stack](../azure-stack-backup-best-practices.md).

## <a name="common-vulnerabilities-and-exposures"></a>Puntos vulnerables y exposiciones comunes

Esta actualización instala las actualizaciones de seguridad siguientes:  

- [CVE-2018-8477](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8477)
- [CVE-2018-8514](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8514)
- [CVE-2018-8580](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8580)
- [CVE-2018-8595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8595)
- [CVE-2018-8596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8596)
- [CVE-2018-8598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8598)
- [CVE-2018-8621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8621)
- [CVE-2018-8622](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8622)
- [CVE-2018-8627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8627)
- [CVE-2018-8637](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8637)
- [CVE-2018-8638](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2018-8638)
- [ADV190001](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190001)
- [CVE-2019-0536](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0536)
- [CVE-2019-0537](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0537)
- [CVE-2019-0545](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0545)
- [CVE-2019-0549](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0549)
- [CVE-2019-0553](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0553)
- [CVE-2019-0554](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0554)
- [CVE-2019-0559](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0559)
- [CVE-2019-0560](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0560)
- [CVE-2019-0561](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0561)
- [CVE-2019-0569](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0569)
- [CVE-2019-0585](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0585)
- [CVE-2019-0588](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0588)


Para más información sobre estas vulnerabilidades, haga clic en los vínculos anteriores o consulte los artículos de Microsoft Knowledge Base [4480977](https://support.microsoft.com/en-us/help/4480977).

## <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización

- Al intentar instalar una actualización de Azure Stack, es posible que se produzca un error en el estado de esa actualización y dicho estado se cambie a **PreparationFailed**. La causa es que el proveedor de recursos de actualización (URP) no se puede transferir correctamente desde el contenedor de almacenamiento a un recurso compartido de infraestructura interno para su procesamiento. A partir de la versión 1901 (1.1901.0.95), para solucionar este problema, puede hacer clic en **Actualizar ahora** de nuevo (en lugar de en **Reanudar**). A continuación, URP limpia los archivos del intento anterior y vuelve a iniciar la descarga.

- Cuando se ejecuta [Test-AzureStack](../azure-stack-diagnostic-test.md), si se produce un error en la prueba de **AzsInfraRoleSummary** o **AzsPortalApiSummary**, se le pedirá que ejecute **Test-AzureStack** con la marca `-Repair`.  Si ejecuta este comando, se produce un error con el siguiente mensaje: `Unexpected exception getting Azure Stack health status. Cannot bind argument to parameter 'TestResult' because it is null.`

- Al ejecutar [Test-AzureStack](../azure-stack-diagnostic-test.md), se muestra un mensaje de advertencia desde el Controlador de administración de placa base (BMC). Puede omitir esta advertencia sin problemas.

- <!-- 2468613 - IS --> Durante la instalación de esta actualización, es posible que vea alertas con el título `Error - Template for FaultType UserAccounts.New is missing.` Puede ignorarlas. Estas alertas se cierran automáticamente cuando se complete la instalación de esta actualización.

## <a name="post-update-steps"></a>Pasos posteriores a la actualización

- Después de instalar esta actualización, instale todas las revisiones aplicables. Para obtener más información, vea [Hotfixes](#hotfixes) (Revisiones), así como nuestra [directiva de mantenimiento](../azure-stack-servicing-policy.md).  

- Recupere las claves de cifrado de los datos en reposo y almacénelas de forma segura fuera de la implementación de Azure Stack. Siga las [instrucciones para recuperar las claves](../azure-stack-security-bitlocker.md).

## <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

Los siguientes son problemas conocidos posteriores a la instalación de esta compilación.

### <a name="portal"></a>Portal

<!-- 2930820 - IS ASDK --> 
- En los portales de administrador y de usuario, si se busca "Docker", el elemento se devuelve de forma incorrecta. No está disponible en Azure Stack. Si se intenta crearlo, se muestra una hoja con una indicación de error. 

<!-- 2931230 - IS  ASDK --> 
- No se pueden eliminar los planes que se agregan a una suscripción de usuario como planes complementarios, aunque se quite el plan de la suscripción de usuario. El plan permanecerá hasta que también se eliminen las suscripciones a las que haga referencia el plan complementario. 

<!-- TBD - IS ASDK --> 
- Los dos tipos de suscripción administrativa que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**. Estos tipos de suscripción están visibles en los nuevos entornos de Azure Stack a partir de la versión 1804 pero aún no están listos para su uso. Tendrá que seguir usando el tipo de suscripción del **proveedor predeterminado**.

<!-- 3557860 - IS ASDK --> 
- La eliminación de las suscripciones del usuario da como resultado recursos huérfanos. Como alternativa, elimine primero los recursos del usuario o todo el grupo de recursos y después elimine las suscripciones del usuario.

<!-- 1663805 - IS ASDK --> 
- No puede ver los permisos de la suscripción mediante los portales de Azure Stack. Como alternativa, use [PowerShell para comprobar los permisos](/powershell/module/azs.subscriptions.admin/get-azssubscriptionplan).

<!-- ### Health and monitoring -->

### <a name="compute"></a>Proceso

- Al crear una nueva máquina virtual (VM) Windows, puede aparecer el siguiente error:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque. Para solucionar este problema, vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- La experiencia de creación de conjuntos de escalado de máquinas virtuales (VMSS) proporciona la versión 7.2 basada en CentOS como una opción para la implementación. Como esa imagen no está disponible en Azure Stack, seleccione otro sistema operativo para la implementación o use una plantilla de Azure Resource Manager en la que se especifique otra imagen de CentOS que el operador haya descargado de Marketplace antes de la implementación.  

<!-- TBD - IS ASDK --> 
- Después de aplicar la actualización 1901, se pueden producir los siguientes problemas al implementar máquinas virtuales con Managed Disks:

   - Si la suscripción se creó antes de la actualización 1808, se puede producir un error en la implementación de máquinas virtuales con Managed Disks con un mensaje de error interno. Para resolver el error, siga estos pasos en cada suscripción:
      1. En el portal del inquilino, vaya a **Suscripciones** y busque la suscripción. Haga clic en **Proveedores de recursos**, después en **Microsoft.Compute** y luego en **Volver a registrar**.
      2. En la misma suscripción, vaya a **Control de acceso (IAM)** y compruebe que **AzureStack-DiskRP-Client** aparece en la lista.
   - Si ha configurado un entorno de varios inquilinos, se puede producir un error con un mensaje de error interno en la implementación de máquinas virtuales en una suscripción asociada con un directorio de invitados. Para solucionar el error, siga los pasos de [este artículo](../azure-stack-enable-multitenancy.md#register-a-guest-directory) para volver a configurar cada uno de los directorios de invitado.

- Una máquina virtual de Ubuntu 18.04 creada con la autorización de SSH habilitada no le permitirá usar las claves SSH para iniciar sesión. Como alternativa, utilice el acceso a la VM para la extensión de Linux a fin de implementar las claves SSH después del aprovisionamiento o utilice la autenticación basada en contraseña.

- No se puede quitar un conjunto de escalado desde la hoja **Virtual Machine Scale Sets**. Como alternativa, seleccione el conjunto de escalado que quiere quitar y, a continuación, haga clic en el botón **Eliminar** del panel **Información general**.

### <a name="networking"></a>Redes  

<!-- 3239127 - IS, ASDK -->
- En el portal de Azure Stack, al cambiar una dirección IP estática por una configuración de IP que está enlazada a un adaptador de red conectado a una instancia de máquina virtual, verá un mensaje de advertencia que indica que 

    `The virtual machine associated with this network interface will be restarted to utilize the new private IP address...`.

    Puede ignorar este mensaje; la dirección IP se cambiará incluso si no se reinicia la instancia de máquina virtual.

<!-- 3632798 - IS, ASDK -->
- En el portal, si agrega una regla de seguridad de entrada y selecciona **Etiqueta de servicio** como origen, se muestran varias opciones en la lista **Etiqueta de origen** que no están disponibles para Azure Stack. Las únicas opciones que son válidas en Azure Stack son las siguientes:

  - **Internet**
  - **VirtualNetwork**
  - **AzureLoadBalancer**
  
    No se admiten las demás opciones como etiquetas de origen en Azure Stack. De forma similar, si agrega una regla de seguridad de salida y selecciona **Etiqueta de servicio** como destino, se muestra la misma lista de opciones para **Etiqueta de origen**. Las únicas opciones válidas son los mismas que para **Etiqueta de origen**, tal y como se describe en la lista anterior.

- Los grupos de seguridad de red (NSG) no funcionan en Azure Stack de la misma manera que en Azure global. En Azure, se pueden establecer varios puertos en una regla de NSG (mediante el portal, PowerShell y plantillas de Resource Manager). En Azure Stack, sin embargo, no se pueden establecer varios puertos en una regla de NSG mediante el portal. Para solucionar este problema, use una plantilla de Resource Manager o PowerShell para establecer estas reglas adicionales.

<!-- 3203799 - IS, ASDK -->
- Actualmente, Azure Stack no permite asociar más de 4 tarjetas de interfaz de red (NIC) a una instancia de máquina virtual, con independencia del tamaño de instancia.

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

Puede descargar el paquete de actualización 1901 de Azure Stack [aquí](https://aka.ms/azurestackupdatedownload). 

Únicamente en los escenarios en los que estén conectadas, las implementaciones de Azure Stack comprueban periódicamente un punto de conexión seguro y le notifican automáticamente si una actualización está disponible para la nube. Para obtener más información, vea la [administración de actualizaciones de Azure Stack](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).  
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](../azure-stack-apply-updates.md).
- Para revisar la directiva de servicio de los sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido, consulte [Directiva de servicio de Azure Stack](../azure-stack-servicing-policy.md).  
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](../azure-stack-monitor-update.md).  
