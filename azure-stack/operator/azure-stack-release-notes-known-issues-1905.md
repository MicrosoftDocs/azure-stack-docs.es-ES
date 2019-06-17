---
title: Problemas conocidos de Azure Stack 1905 | Microsoft Docs
description: Obtenga información sobre los problemas conocidos de Azure Stack 1905.
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
ms.date: 06/05/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/05/2019
ms.openlocfilehash: 38c7ec337ba8cdb73925b1c07f77331c05b25d8a
ms.sourcegitcommit: e51cdc84a09250e8fa701bb2cb09de38d7de2c07
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66836674"
---
# <a name="azure-stack-1905-known-issues"></a>Problemas conocidos de Azure Stack 1905

En este artículo se enumeran los problemas conocidos de la versión 1905 de Azure Stack. La lista se actualiza conforme se identifican nuevos problemas.

> [!IMPORTANT]  
> Revise esta sección antes de aplicar la actualización.

## <a name="update-process"></a>Proceso de actualización

### <a name="host-node-update-prerequisite-failure"></a>Error en los requisitos previos de actualización del nodo de host

- Aplicable a: Este problema se aplica a la actualización de la versión 1905.
- Causa: Al intentar instalar la actualización 1905 de Azure Stack, es posible que se produzca un error debido a un **requisito previo de la actualización del nodo de host**. Esto se debe generalmente a que un nodo de host no tiene suficiente espacio libre en el disco.
- Corrección: Póngase en contacto con el soporte técnico de Azure Stack para recibir asistencia en el borrado de espacio en disco en el nodo de host.
- Repetición: Poco frecuente

### <a name="preparation-failed"></a>Error de preparación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al intentar instalar la actualización 1905 de Azure Stack, es posible que se produzca un error en el estado de esa actualización y dicho estado se cambie a **PreparationFailed**. La causa es que el proveedor de recursos de actualización (URP) no se puede transferir correctamente desde el contenedor de almacenamiento a un recurso compartido de infraestructura interno para su procesamiento. El paquete de actualización 1905 es más grande que los paquetes de actualización anteriores, lo que puede hacer que este problema sea más probable.
- Corrección: A partir de la versión 1901 (1.1901.0.95), para solucionar este problema, puede hacer clic en **Actualizar ahora** de nuevo (en lugar de en **Reanudar**). A continuación, URP limpia los archivos del intento anterior y vuelve a iniciar la descarga. Si el problema persiste, recomendamos cargar manualmente el paquete de actualización siguiendo la sección [Importación e instalación de actualizaciones](azure-stack-apply-updates.md#import-and-install-updates).
- Repetición: Común

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Suscripciones administrativas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Las dos suscripciones administrativas que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**.
- Corrección: Estas suscripciones se suspenderán a partir de la versión 1906 y, posteriormente, se eliminarán. Si tiene recursos que se ejecutan en estas dos suscripciones, vuelva a crearlos en las suscripciones de usuario anteriores a la versión 1906.
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

- Aplicable a: Este problema se aplica a las versiones 1904 y 1905.
- Causa: La pantalla de administración de Marketplace no se ve al iniciar sesión en el portal de administradores.
- Corrección: Actualice el explorador o vaya a **Settings** (Configuración) y seleccione la opción **Reset to default settings** (Restablecer configuración predeterminada).
- Repetición: Intermitente

### <a name="docker-extension"></a>Extensión de Docker

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En los portales de administrador y de usuario, si se busca **Docker**, el elemento se devuelve de forma incorrecta. No está disponible en Azure Stack. Si intenta crearlo, aparece un error.
- Corrección: Sin mitigación.
- Repetición: Común

### <a name="upload-blob"></a>Carga de blob

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, al intentar cargar un blob mediante la opción **OAuth(preview)** , la tarea genera un mensaje de error.
- Corrección: Cargue el blob mediante la opción SAS.
- Repetición: Común

## <a name="networking"></a>Redes

### <a name="load-balancer"></a>Equilibrador de carga

#### <a name="add-backend-pool"></a>Agregar grupo de back-end

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, si intenta agregar un **grupo de back-end** a una **instancia de Load Balancer**, se produce un error en la operación con el mensaje **Failed to update Load Balancer...** (No se pudo actualizar Load Balancer...).
- Corrección: Use PowerShell, la CLI o una plantilla de Resource Manager para asociar el grupo de back-end a un recurso de equilibrador de carga.
- Repetición: Común

#### <a name="create-inbound-nat"></a>Crear una NAT de entrada

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, si intenta crear una **regla NAT de entrada** para una instancia de **Load Balancer**, se produce un error en la operación con el mensaje **Failed to update Load Balancer...** (No se pudo actualizar Load Balancer...).
- Corrección: Use PowerShell, la CLI o una plantilla de Resource Manager para asociar el grupo de back-end a un recurso de equilibrador de carga.
- Repetición: Común

#### <a name="create-load-balancer"></a>Creación de un equilibrador de carga

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la ventana **Crear equilibrador de carga** muestra una opción para crear una SKU **estándar** del equilibrador de carga. En Azure Stack no se admite esta opción.
- Corrección: Use las opciones del equilibrador de carga **básico** en su lugar.
- Repetición: Común

### <a name="public-ip-address"></a>Dirección IP pública

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la ventana **Crear dirección IP pública** muestra una opción para crear una SKU **estándar**. En Azure Stack no se admite la SKU **estándar**.
- Corrección: Utilice la SKU **básica** para la dirección IP pública.
- Repetición: Común

## <a name="compute"></a>Proceso

### <a name="vm-boot-diagnostics"></a>Diagnósticos de arranque de VM

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al crear una nueva máquina virtual Windows, puede aparecer el siguiente error: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** (No se pudo iniciar la máquina virtual "vm-name". No se pudo actualizar la configuración de salida de serie de la VM 'vm-name').
El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque.
- Corrección: Vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.
- Repetición: Común

### <a name="virtual-machine-scale-set"></a>Conjunto de escalado de máquina virtual

#### <a name="centos"></a>CentOS

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La experiencia de creación de conjuntos de escalado de máquinas virtuales proporciona la versión 7.2 basada en CentOS como una opción para la implementación. CentOS 7.2 no está disponible en Azure Stack Marketplace, lo que causará errores en la implementación que indican que la imagen no se encuentra.
- Corrección: Seleccione otro sistema operativo para la implementación o use una plantilla de Azure Resource Manager en la que se especifique otra imagen de CentOS que el operador haya descargado de Marketplace antes de la implementación.
- Repetición: Común

#### <a name="remove-scale-set"></a>Quitar el conjunto de escalado

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede quitar un conjunto de escalado desde la hoja **Conjunto de escalado de máquinas virtuales**.
- Corrección: Seleccione el conjunto de escalado que quiere quitar y, a continuación, haga clic en el botón **Eliminar** del panel **Información general**.
- Repetición: Común

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Crear errores durante la revisión y actualización de entornos de Azure Stack de 4 nodos

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La creación de VM en un conjunto de disponibilidad de 3 dominios de error y la creación de una instancia de conjunto de escalado de máquinas virtuales genera un error **FabricVmPlacementErrorUnsupportedFaultDomainSize** durante el proceso de actualización en un entorno de Azure Stack de 4 nodos.
- Corrección: Se pueden crear VM únicas en un conjunto de disponibilidad con 2 dominios de error correctamente. Sin embargo, la creación de instancias del conjunto de escalado todavía no está disponible durante el proceso de actualización en una instancia de Azure Stack de 4 nodos.

#### <a name="vmss-instance-view-blade-doesnt-load"></a>La hoja de la vista de la instancia de Virtual Machine Scale Sets no se carga
 
- Aplicable a: Este problema se aplica a las versiones 1904 y 1905.
- Causa: La hoja de la vista de la instancia de un conjunto de escala situado en Azure Stack Portal -> Dashboard  ->  Virtual Machine Scale Sets  -> AnyScaleSet - Instances  ->  AnyScaleSetInstance no se puede cargar.
- Corrección: Actualmente no hay ninguna corrección y estamos trabajando en una solución. Hasta entonces, utilice el comando de la CLI az vmss get-instance-view para obtener la vista de la instancia de VMSS

### <a name="ubuntu-ssh-access"></a>Acceso a SSH en Ubuntu

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Una VM de Ubuntu 18.04 creada con la autorización de SSH habilitada no le permite usar las claves SSH para iniciar sesión.
- Corrección: Utilice el acceso a la VM para la extensión de Linux a fin de implementar las claves SSH después del aprovisionamiento o utilice la autenticación basada en contraseña.
- Repetición: Común

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Pasos siguientes

- [Revisión de la lista de comprobación de actividades de actualización](azure-stack-release-notes-checklist.md)
- [Revisión de la lista de actualizaciones de seguridad](azure-stack-release-notes-security-updates-1905.md)
