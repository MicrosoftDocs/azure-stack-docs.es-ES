---
title: 'Notas de la versión de Azure Stack: problemas conocidos de la compilación 1904 | Microsoft Docs'
description: Obtenga información sobre los problemas conocidos de Azure Stack 1904.
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
ms.reviewer: hectorl
ms.lastreviewed: 05/07/2019
ms.openlocfilehash: 782e0aaea0eadddae24795616aaba6053b728134
ms.sourcegitcommit: 39ba6d18781aed98b29ac5e08aac2d75c37bf18c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/07/2019
ms.locfileid: "65387171"
---
# <a name="azure-stack-1904-known-issues"></a>Problemas conocidos de Azure Stack 1904

En este artículo se enumeran los problemas conocidos de la versión de 1904 de Azure Stack. La lista se actualiza conforme se identifican nuevos problemas.

> [!IMPORTANT]  
> Revise esta sección antes de aplicar la actualización.

## <a name="portal"></a>Portal

### <a name="add-on-plans"></a>Planes complementarios

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se pueden eliminar los planes que se agregan a una suscripción de usuario como planes complementarios, aunque se quite el plan de la suscripción de usuario. El plan permanece hasta que también se eliminan las suscripciones que hacen referencia al plan complementario.
- Corrección: Sin mitigación.
- Repetición: Común

### <a name="administrative-subscriptions"></a>Suscripciones administrativas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Las dos suscripciones administrativas que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**.
- Corrección: Estas suscripciones se suspenderán a partir de la versión 1905 y, posteriormente, se eliminarán. Si tiene recursos que se ejecutan en estas dos suscripciones, vuelva a crearlos en las suscripciones de usuario anteriores a la versión 1905.
- Repetición: Común

### <a name="subscription-resources"></a>Recursos de suscripción

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La eliminación de las suscripciones del usuario da como resultado recursos huérfanos.
- Corrección: Elimine primero los recursos del usuario o todo el grupo de recursos y, después, elimine las suscripciones del usuario.
- Repetición: Común

### <a name="subscription-permissions"></a>Permisos de suscripción

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No puede ver los permisos de la suscripción mediante los portales de Azure Stack.
- Corrección: Use [PowerShell para verificar los permisos](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Repetición: Común

### <a name="marketplace-management"></a>Administración de Marketplace

- Aplicable a: este problema se aplica a la versión 1904.
- Causa: La pantalla de administración de Marketplace no está visible al iniciar sesión en el portal de administradores.
- Corrección: Actualice el explorador.
- Repetición: Intermitente

### <a name="upload-blob"></a>Carga de blob

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, al intentar cargar un blob mediante la opción OAuth(preview), la tarea genera un mensaje de error.
- Corrección: Cargue el blob mediante la opción SAS.
- Repetición: Común

## <a name="networking"></a>Redes

### <a name="load-balancer"></a>Load Balancer

#### <a name="add-backend-pool"></a>Agregar un grupo de back-end

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, si intenta agregar un **grupo de back-end** a una **instancia de Load Balancer**, se produce un error en la operación con el mensaje **Failed to update Load Balancer...** (No se pudo actualizar Load Balancer...).
- Corrección: Use PowerShell, la CLI o una plantilla de Resource Manager para asociar el grupo de back-end a un recurso de equilibrador de carga.
- Repetición: Común

#### <a name="create-inbound-nat"></a>Crear una regla NAT de entrada

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, si intenta crear una **regla NAT de entrada** para una instancia de **Load Balancer**, se produce un error en la operación con el mensaje **Failed to update Load Balancer...** (No se pudo actualizar Load Balancer...).
- Corrección: Use PowerShell, la CLI o una plantilla de Resource Manager para asociar el grupo de back-end a un recurso de equilibrador de carga.
- Repetición: Común

#### <a name="create-load-balancer"></a>Cree un equilibrador de carga

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la ventana **Crear equilibrador de carga** muestra una opción para crear una SKU **estándar** de Load Balancer. En Azure Stack no se admite esta opción.
- Corrección: Use las opciones del equilibrador de carga básico en su lugar.
- Repetición: Común

#### <a name="public-ip-address"></a>Dirección IP pública

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la ventana **Crear dirección IP pública** muestra una opción para crear una SKU **estándar**. En Azure Stack no se admite la SKU **estándar**.
- Corrección: Utilice en su lugar el SKU básico para la dirección IP pública.
- Repetición: Común

## <a name="compute"></a>Proceso

### <a name="vm-boot-diagnostics"></a>Diagnósticos de arranque de VM

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al crear una nueva máquina virtual (VM) Windows, puede aparecer el siguiente error: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** (No se pudo iniciar la máquina virtual "vm-name". No se pudo actualizar la configuración de salida de serie de la VM 'vm-name').
El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque.
- Corrección: Vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.
- Repetición: Común

### <a name="virtual-machine-scale-set"></a>Conjunto de escalado de máquinas virtuales

#### <a name="centos"></a>CentOS

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La experiencia de creación de conjuntos de escalado de máquinas virtuales (VMSS) proporciona la versión 7.2 basada en CentOS como una opción para la implementación. CentOS 7.2 no está disponible en Azure Stack.
- Corrección: Seleccione otro sistema operativo para la implementación o use una plantilla de Azure Resource Manager en la que se especifique otra imagen de CentOS que el operador haya descargado de Marketplace antes de la implementación.
- Repetición: Común

#### <a name="remove-scale-set"></a>Quitar el conjunto de escalado

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede quitar un conjunto de escalado desde la hoja **Virtual Machine Scale Sets**.
- Corrección: Seleccione el conjunto de escalado que quiere quitar y, a continuación, haga clic en el botón **Eliminar** del panel **Información general**.
- Repetición: Común

### <a name="ubuntu-ssh-access"></a>Acceso a SSH en Ubuntu

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Una VM de Ubuntu 18.04 creada con la autorización de SSH habilitada no le permite usar las claves SSH para iniciar sesión.
- Corrección: Utilice el acceso a la VM para la extensión de Linux a fin de implementar las claves SSH después del aprovisionamiento o utilice la autenticación basada en contraseña.
- Repetición: Común

## <a name="infrastructure-backup"></a>Copia de seguridad de infraestructura

<!--Bug 3615401 - scheduler config lost; new issue in YYMM;  hectorl-->
Después de habilitar las copias de seguridad automáticas, el servicio de programador pasa al estado deshabilitado de forma inesperada. El servicio del controlador de copia de seguridad detectará que las copias de seguridad automáticas están desactivadas y generará una advertencia en el portal de administrador. Esta advertencia se espera cuando las copias de seguridad automáticas están deshabilitadas.

- Aplicable a: se trata de un nuevo problema con la versión 1904.
- Causa: Este problema se debe a un error en el servicio que da lugar a la pérdida de la configuración del programador. Este error no cambia la ubicación de almacenamiento, el nombre de usuario, la contraseña o la clave de cifrado.
- Corrección: Para mitigar este problema, abra la hoja de configuración del controlador de copia de seguridad en el proveedor de recursos de copia de seguridad de infraestructura y seleccione **Habilitar copias de seguridad automáticas**. Asegúrese de establecer el período de retención y la frecuencia deseada.
- Repetición: Bajo

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Pasos siguientes

- [Revisión de la lista de comprobación de actividades de actualización](azure-stack-release-notes-checklist.md)
- [Revisión de la lista de actualizaciones de seguridad](azure-stack-release-notes-security-updates-1904.md)
