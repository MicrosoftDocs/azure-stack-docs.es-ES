---
title: Actualización 1902 de Azure Stack | Microsoft Docs
description: Obtenga información sobre la actualización 1902 de los sistemas integrados de Azure Stack, como novedades, problemas conocidos y dónde se puede descargar dicha actualización.
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
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 6bf973d7e232ad30738abf80396da75e62277529
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248819"
---
# <a name="azure-stack-1902-update"></a>Actualización 1902 de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe el contenido del paquete de actualización 1902. La actualización incluye mejoras, correcciones y nuevas características de esta versión de Azure Stack. En este artículo también se describen los problemas conocidos de esta versión y se incluye un vínculo para que pueda descargar la actualización. Los problemas conocidos se dividen en aquellos que están directamente relacionados con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]  
> Este paquete de actualización es únicamente para los sistemas integrados de Azure Stack. No la aplique al Kit de desarrollo de Azure Stack.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 1902 de Azure Stack es **1.1902.0.69**.

### <a name="update-type"></a>Tipo de actualización

El tipo de compilación de la actualización 1902 de Azure Stack es **Completa**. Para obtener más información sobre cómo actualizar los tipos de compilación, consulte el artículo [Administración de actualizaciones en Azure Stack](../azure-stack-updates.md).

## <a name="hotfixes"></a>Revisiones

Azure Stack publica revisiones de forma periódica. Asegúrese de instalar la [revisión más reciente de Azure Stack](#azure-stack-hotfixes) para 1901 antes de actualizar Azure Stack a 1902.

Las revisiones de Azure Stack solo son aplicables a los sistemas integrados de Azure Stack; no intente instalar revisiones en el ASDK.

> [!TIP]  
> Suscríbase a las siguientes fuentes *RRS* o *Atom* para mantenerse al día con las revisiones de Azure Stack:
> - [RSS](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/rss)
> - [Atom](https://support.microsoft.com/app/content/api/content/feeds/sap/en-us/32d322a8-acae-202d-e9a9-7371dccf381b/atom)

### <a name="azure-stack-hotfixes"></a>Revisiones de Azure Stack

- **1901**: [KB 4500636: revisión de Azure Stack 1.1901.5.109](https://support.microsoft.com/help/4500636)
- **1902**: [KB 4500637: revisión de Azure Stack 1.1902.3.75](https://support.microsoft.com/help/4500637)

## <a name="prerequisites"></a>Requisitos previos

> [!IMPORTANT]
> 1902 se puede instalar directamente desde las versiones **1.1901.0.95** o **1.1901.0.99** sin necesidad de instalar ninguna revisión de 1901. Sin embargo, si ha instalado la revisión anterior **1901.2.103**, debe instalar la nueva [revisión 1901.3.105](https://support.microsoft.com/help/4495662) antes de continuar con la 1902.

- Antes de iniciar la instalación de esta actualización, ejecute [Test-AzureStack](../azure-stack-diagnostic-test.md) con los parámetros siguientes para validar el estado de Azure Stack y resolver todos los problemas operativos detectados, incluidas todas las advertencias y errores. Repase también las alertas activas y resuelva las que requieran alguna acción:

    ```powershell
    Test-AzureStack -Include AzsDefenderSummary, AzsHostingInfraSummary, AzsHostingInfraUtilization, AzsInfraCapacity, AzsInfraRoleSummary, AzsPortalAPISummary, AzsSFRoleSummary, AzsStampBMCSummary, AzsHostingServiceCertificates
    ```

  Si el parámetro `AzsControlPlane` se incluye cuando se ejecuta **Test-AzureStack**, verá el error siguiente en la salida de **Test-AzureStack**: **FAIL Azure Stack Control Plane Websites Summary** (ERROR Resumen de sitios web de plano de control de Azure Stack). Puede omitir sin problemas este error específico.

- Cuando System Center Operations Manager (SCOM) administre Azure Stack, asegúrese de actualizar el [módulo de administración para Microsoft Azure Stack](https://www.microsoft.com/download/details.aspx?id=55184) a la versión 1.0.3.11 antes de aplicar la revisión 1902.

- El formato del paquete para la actualización de Azure Stack ha cambiado de **.bin/.exe/.xml** a **.zip/.xml** a partir de la versión 1902. Los clientes con unidades de escalado de Azure Stack conectadas verán el mensaje **Actualización disponible** en el portal. Asimismo, los clientes que no están conectados ahora pueden, simplemente, descargar e importar el archivo ZIP con la extensión .xml correspondiente.

<!-- ## New features -->

<!-- ## Fixed issues -->

## <a name="improvements"></a>Mejoras

- La compilación 1902 presenta una nueva interfaz de usuario en el portal de administrador de Azure Stack para crear planes, ofertas, cuotas y planes complementarios. Para obtener más información, incluidas las capturas de pantalla, consulte la documentación sobre [Crear planes, ofertas y cuotas](../azure-stack-create-plan.md).

<!-- 1460884    Hotfix: Adding StorageController service permission to talk to ClusterOrchestrator  Add node -->
- Mejoras en la confiabilidad de la expansión de capacidad durante una operación de incorporación de nodo al cambiar el estado de la unidad de escalado de "Expanding storage" (Ampliando el almacenamiento) a "Running" (En ejecución).

<!--
1426197 3852583: Increase Global VM script mutex wait time to accommodate enclosed operation timeout    PNU
1399240 3322580: [PNU] Optimize the DSC resource execution on the Host  PNU
1398846 Bug 3751038: ECEClient.psm1 should provide cmdlet to resume action plan instance    PNU
1398818 3685138, 3734779: ECE exception logging, VirtualMachine ConfigurePending should take node name from execution context   PNU
1381018 [1902] 3610787 - Infra VM creation should fail if the ClusterGroup already exists   PNU
-->
- Para mejorar la seguridad y la integridad del paquete, así como para simplificar la administración de la ingesta sin conexión, Microsoft ha cambiado el formato del paquete de actualización de los archivos .exe y .bin a un archivo ZIP. El nuevo formato agrega confiabilidad adicional al proceso de desempaquetado que, a veces, puede hacer que la preparación de la actualización se detenga. También se aplica el mismo formato de paquete para actualizar los paquetes de su OEM.
- Para mejorar la experiencia del operador de Azure Stack al ejecutar Test-AzureStack, los operadores ahora pueden usar simplemente "Test-AzureStack -Group UpdateReadiness" en lugar de pasar diez parámetros adicionales después de una instrucción Include.

  ```powershell
    Test-AzureStack -Group UpdateReadiness  
  ```  
  
- Para mejorar la confiabilidad y disponibilidad generales de los servicios de infraestructura básicos durante el proceso de actualización, el proveedor de recursos de actualización nativo detectará e invocará, como parte del plan de acción de actualización, correcciones globales automáticas según sea necesario. Entre los flujos de trabajo de reparación global se incluyen los siguientes:

  - Comprobar si hay máquinas virtuales de infraestructura en un estado que no es óptimo e intentar repararlas según sea necesario.
  - Comprobar si hay problemas con el servicio SQL como parte del plan de control e intentar repararlos según sea necesario.
  - Comprobar el estado del servicio Equilibrador de carga de software (SLB) como parte de la Controladora de red (NC) e intentar repararlo según sea necesario.
  - Comprobar el estado del servicio de la Controladora de red (NC) e intentar repararlo según sea necesario
  - Comprobar el estado de los nodos de Service Fabric del Servicio de consola de recuperación de emergencia (ERCS) y repararlos según sea necesario.
  - Comprobar el estado del rol de la infraestructura y repararlo según sea necesario.
  - Comprobar el estado de los nodos de Service Fabric de Almacenamiento coherente de Azure (ACS) y repararlos según sea necesario.

<!-- 
1426690 [SOLNET] 3895478-Get-AzureStackLog_Output got terminated in the middle of network log   Diagnostics
1396607 3796092: Move Blob services log from Storage role to ACSBlob role to reduce the log size of Storage Diagnostics
1404529 3835749: Enable Group Policy Diagnostic Logs    Diagnostics
1436561 Bug 3949187: [Bug Fix] Remove AzsStorageSvcsSummary test from SecretRotationReadiness Test-AzureStack flag  Diagnostics
1404512 3849946: Get-AzureStackLog should collect all child folders from c:\Windows\Debug   Diagnostics 
-->
- Mejoras en las herramientas de diagnóstico de Azure Stack para perfeccionar la confiabilidad y el rendimiento de la colección de registros. Registro adicional para los servicios de red y de identidad. 

<!-- 1384958    Adding a Test-AzureStack group for Secret Rotation  Diagnostics -->
- Mejoras en la confiabilidad de Test-AzureStack para la prueba de preparación de cambio de secretos.

<!-- 1404751    3617292: Graph: Remove dependency on ADWS.  Identity -->
- Mejoras para aumentar la confiabilidad de AD Graph al establecer comunicación con el entorno de Active Directory del cliente

<!-- 1391444    [ISE] Telemetry for Hardware Inventory - Fill gap for hardware inventory info   System info -->
- Mejoras en la colección de inventario de hardware en Get-AzureStackStampInformation.

- Para mejorar la confiabilidad de las operaciones que se ejecutan en la infraestructura de ERCS, la memoria de cada instancia de ERCS aumenta de 8 GB a 12 GB. En una instalación de sistemas integrados de Azure Stack, esto da como resultado un aumento de 12 GB en general.

<!-- 110303935 IcM Reported by HKEX -->
- La actualización 1902 corrige un problema en el servicio de controladores de red de VSwitch, en el que todas las VM en un nodo específico perdieron la conexión.  El problema provocaba que el usuario se atascase en un estado de pérdida principal, que le impedía establecer contacto con la instancia principal, aunque el rol se había conmutado por error en otra instancia correcta, lo que podía resolverse a través de los servicios de soporte técnico de Microsoft.

> [!IMPORTANT]
> Para asegurarse de que la revisión y el proceso de actualización generan el menor tiempo posible de inactividad en el inquilino, compruebe que su sello de Azure Stack tenga más de 12 GB de espacio disponible en la hoja **Capacidad**. Puede ver este aumento de memoria reflejado en la hoja **Capacidad** después de instalar correctamente la actualización.

## <a name="common-vulnerabilities-and-exposures"></a>Puntos vulnerables y exposiciones comunes

Esta actualización instala las actualizaciones de seguridad siguientes:  
- [ADV190005](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190006)
- [CVE-2019-0595](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0595)
- [CVE-2019-0596](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0596)
- [CVE-2019-0597](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0597)
- [CVE-2019-0598](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0598)
- [CVE-2019-0599](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0599)
- [CVE-2019-0600](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0600)
- [CVE-2019-0601](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0601)
- [CVE-2019-0602](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0602)
- [CVE-2019-0615](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0615)
- [CVE-2019-0616](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0616)
- [CVE-2019-0618](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0618)
- [CVE-2019-0619](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0619)
- [CVE-2019-0621](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0621)
- [CVE-2019-0623](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0623)
- [CVE-2019-0625](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0625)
- [CVE-2019-0626](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0626)
- [CVE-2019-0627](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0627)
- [CVE-2019-0628](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0628)
- [CVE-2019-0630](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0630)
- [CVE-2019-0631](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0631)
- [CVE-2019-0632](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0632)
- [CVE-2019-0633](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0633)
- [CVE-2019-0635](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0635)
- [CVE-2019-0636](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0636)
- [CVE-2019-0656](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0656)
- [CVE-2019-0659](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0659)
- [CVE-2019-0660](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0660)
- [CVE-2019-0662](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0662)
- [CVE-2019-0663](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/CVE-2019-0663)

Para obtener más información sobre estas vulnerabilidades, haga clic en los vínculos anteriores o consulte los artículos de Microsoft Knowledge Base [4487006](https://support.microsoft.com/en-us/help/4487006).

## <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización

- Al intentar instalar una actualización de Azure Stack, es posible que se produzca un error en el estado de esa actualización y dicho estado se cambie a **PreparationFailed**. La causa es que el proveedor de recursos de actualización (URP) no se puede transferir correctamente desde el contenedor de almacenamiento a un recurso compartido de infraestructura interno para su procesamiento. A partir de la versión 1901 (1.1901.0.95), para solucionar este problema, puede hacer clic en **Actualizar ahora** de nuevo (en lugar de en **Reanudar**). A continuación, URP limpia los archivos del intento anterior y vuelve a iniciar la descarga.

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

<!-- Daniel 3/28 -->
- En el portal de usuarios, cuando se desplaza a un blob en una cuenta de almacenamiento e intenta abrir la **directiva de acceso** desde el árbol de navegación, la ventana posterior no se puede cargar. Para solucionar este problema, los cmdlets de PowerShell siguientes permiten crear, recuperar, establecer y eliminar directivas de acceso, respectivamente:

  - [New-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/new-azurestoragecontainerstoredaccesspolicy)
  - [Get-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/get-azurestoragecontainerstoredaccesspolicy)
  - [Set-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/set-azurestoragecontainerstoredaccesspolicy)
  - [Remove-AzureStorageContainerStoredAccessPolicy](/powershell/module/azure.storage/remove-azurestoragecontainerstoredaccesspolicy)

<!-- ### Health and monitoring -->

### <a name="compute"></a>Proceso

- Al crear una nueva máquina virtual (VM) Windows, puede aparecer el siguiente error:

   `'Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'`

   El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque. Para solucionar este problema, vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.

<!-- 2967447 - IS, ASDK, to be fixed in 1902 -->
- La experiencia de creación de conjuntos de escalado de máquinas virtuales proporciona la versión 7.2 basada en CentOS como una opción para la implementación. Como esa imagen no está disponible en Azure Stack, seleccione otro sistema operativo para la implementación o use una plantilla de Azure Resource Manager en la que se especifique otra imagen de CentOS que el operador haya descargado de Marketplace antes de la implementación.  

<!-- TBD - IS ASDK --> 
- Después de aplicar la actualización 1902, se pueden producir los siguientes problemas al implementar VM con Managed Disks:

   - Si la suscripción se creó antes de la actualización 1808, se puede producir un error en la implementación de máquinas virtuales con Managed Disks con un mensaje de error interno. Para resolver el error, siga estos pasos en cada suscripción:
      1. En el portal del inquilino, vaya a **Suscripciones** y busque la suscripción. Haga clic en **Proveedores de recursos**, después en **Microsoft.Compute** y luego en **Volver a registrar**.
      2. En la misma suscripción, vaya a **Control de acceso (IAM)** y verifique que **Azure Stack – Managed Disk** (Azure Stack - Disco administrado) aparece en la lista.
   - Si ha configurado un entorno de varios inquilinos, se puede producir un error con un mensaje de error interno en la implementación de máquinas virtuales en una suscripción asociada con un directorio de invitados. Para solucionar el error, siga los pasos de [este artículo](../azure-stack-enable-multitenancy.md#register-azure-stack-hub-with-the-guest-directory) para volver a configurar cada uno de los directorios de invitado.

- Una máquina virtual de Ubuntu 18.04 creada con la autorización de SSH habilitada no le permitirá usar las claves SSH para iniciar sesión. Como alternativa, utilice el acceso a la VM para la extensión de Linux a fin de implementar las claves SSH después del aprovisionamiento o utilice la autenticación basada en contraseña.

- No se puede quitar un conjunto de escalado desde la hoja **Virtual Machine Scale Sets**. Como alternativa, seleccione el conjunto de escalado que quiere quitar y, a continuación, haga clic en el botón **Eliminar** del panel **Información general**.

- La creación de VM en un conjunto de disponibilidad de 3 dominios de error y la creación de una instancia de conjunto de escalado de máquinas virtuales genera un error **FabricVmPlacementErrorUnsupportedFaultDomainSize** durante el proceso de actualización en un entorno de Azure Stack de 4 nodos. Se pueden crear VM únicas en un conjunto de disponibilidad con 2 dominios de error correctamente. Sin embargo, la creación de instancias del conjunto de escalado todavía no está disponible durante el proceso de actualización en una instancia de Azure Stack de 4 nodos.

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
- Actualmente, Azure Stack no permite conectar más de 4 interfaces de red (NIC) a una instancia de VM, independientemente del tamaño de la instancia.

- En el portal de usuarios, si intenta agregar un **grupo de back-end** a una **instancia de Load Balancer**, se produce un error en la operación con el mensaje **Failed to update Load Balancer...** (No se pudo actualizar Load Balancer...).  Para solucionar este problema, use PowerShell, la CLI o una plantilla de Azure Resource Manager para asociar el grupo de back-end a un recurso del equilibrador de carga.

- En el portal de usuarios, si intenta crear una **regla NAT de entrada** para una instancia de **Load Balancer**, se produce un error en la operación con el mensaje **Failed to update Load Balancer...** (No se pudo actualizar Load Balancer...).  Para solucionar este problema, use PowerShell, la CLI o una plantilla de Azure Resource Manager para asociar el grupo de back-end a un recurso del equilibrador de carga.

- En el portal de usuarios, la ventana **Crear equilibrador de carga** muestra una opción para crear una SKU **estándar** del equilibrador de carga. En Azure Stack no se admite esta opción.

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

Puede descargar el paquete de actualización 1902 de Azure Stack [aquí](https://aka.ms/azurestackupdatedownload). 

Únicamente en los escenarios en los que estén conectadas, las implementaciones de Azure Stack comprueban periódicamente un punto de conexión seguro y le notifican automáticamente si una actualización está disponible para la nube. Para obtener más información, vea la [administración de actualizaciones de Azure Stack](../azure-stack-updates.md#how-to-know-an-update-is-available).

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre la administración de actualizaciones en Azure Stack, consulte [Introducción a la administración de actualizaciones en Azure Stack](../azure-stack-updates.md).  
- Para más información sobre cómo aplicar actualizaciones con Azure Stack, consulte [Aplicación de actualizaciones en Azure Stack](../azure-stack-apply-updates.md).
- Para revisar la directiva de servicio de los sistemas integrados de Azure Stack y lo que debe hacer para mantener el sistema en un estado admitido, consulte [Directiva de servicio de Azure Stack](../azure-stack-servicing-policy.md).  
- Para usar el punto de conexión con privilegios (PEP) para supervisar y reanudar las actualizaciones, consulte [Supervisión de las actualizaciones en Azure Stack mediante el uso del punto de conexión con privilegios](../azure-stack-monitor-update.md).  
