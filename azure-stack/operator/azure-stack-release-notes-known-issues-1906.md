---
title: Problemas conocidos de Azure Stack 1906 | Microsoft Docs
description: Obtenga información sobre los problemas conocidos de Azure Stack 1906.
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
ms.date: 06/28/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 06/28/2019
ms.openlocfilehash: 96a29ab11ffc15b35c7035576fa68544caef594b
ms.sourcegitcommit: 1c4eda123857d714109e38bb853eb1ce49af5f5c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/08/2019
ms.locfileid: "67648042"
---
# <a name="azure-stack-1906-known-issues"></a>Problemas conocidos de Azure Stack 1906

En este artículo se enumeran los problemas conocidos de la versión 1906 de Azure Stack. La lista se actualiza conforme se identifican nuevos problemas.

> [!IMPORTANT]  
> Revise esta sección antes de aplicar la actualización.

## <a name="update-process"></a>Proceso de actualización

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al intentar instalar la actualización 1906 de Azure Stack, es posible que se produzca un error en el estado de esa actualización y dicho estado cambie a **PreparationFailed**. La causa es que el proveedor de recursos de actualización (URP) no se puede transferir correctamente desde el contenedor de almacenamiento a un recurso compartido de infraestructura interno para su procesamiento. 
- Corrección: A partir de la versión 1901 (1.1901.0.95), para solucionar este problema, puede hacer clic en **Actualizar ahora** de nuevo (en lugar de en **Reanudar**). A continuación, URP limpia los archivos del intento anterior y vuelve a iniciar la descarga. Si el problema persiste, recomendamos cargar manualmente el paquete de actualización siguiendo la sección [Importación e instalación de actualizaciones](azure-stack-apply-updates.md#import-and-install-updates).
- Repetición: Común

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Suscripciones administrativas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Las dos suscripciones administrativas que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**.
- Corrección: Si tiene recursos que se ejecutan en estas dos suscripciones, vuelva a crearlos en las suscripciones de usuario.
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

### <a name="storage-account-settings"></a>Configuración de cuentas de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Configuración** de la cuenta de almacenamiento muestra una opción para cambiar el **tipo de transferencia de seguridad**. La característica no se admite actualmente en Azure Stack.
- Repetición: Común

### <a name="upload-blob"></a>Carga de blob

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, al intentar cargar un blob mediante la opción **OAuth(preview)** , la tarea genera un mensaje de error.
- Corrección: Cargue el blob mediante la opción SAS.
- Repetición: Común

### <a name="template"></a>Plantilla

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la interfaz de usuario de implementación de plantillas no rellena los parámetros de los nombres de plantilla que empiezan por "_" (carácter de subrayado).
- Corrección: Quite el "_" (carácter de subrayado) del nombre de la plantilla.
- Repetición: Común

## <a name="networking"></a>Redes

### <a name="service-endpoints"></a>Puntos de conexión de servicio

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Red virtual** muestra una opción para usar **puntos de conexión de servicio**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

### <a name="network-interface"></a>interfaz de red

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede agregar una nueva interfaz de red a una VM cuyo estado es **En ejecución**.
- Corrección: Detenga la máquina virtual antes de agregar o quitar una interfaz de red.
- Repetición: Común

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

#### <a name="alerts"></a>Alertas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Puerta de enlace de red virtual** muestra una opción para usar **alertas**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="active-active"></a>Activo-activo

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, durante la creación, y en el menú de recursos de **Puerta de enlace de red virtual**, verá una opción para habilitar la configuración **activa-activa**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="vpn-troubleshooter"></a>Solucionador de problemas de VPN

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Conexiones** muestra una característica denominada **Solucionador de problemas de VPN**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Común

#### <a name="documentation"></a>Documentación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Los vínculos de la documentación de la página de información general de Puerta de enlace de red virtual enlazan a la documentación específica de Azure en lugar de a la de Azure Stack. Use los vínculos siguientes para la documentación de Azure Stack:

  - [SKU de puerta de enlace](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Conexiones de alta disponibilidad](../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configuración de BGP en Azure Stack](../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuitos ExpressRoute](azure-stack-connect-expressroute.md)
  - [Especificación de directivas de IPsec o IKE personalizadas](../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

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

## <a name="compute"></a>Proceso

### <a name="vm-boot-diagnostics"></a>Diagnósticos de arranque de VM

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al crear una nueva máquina virtual Windows, puede aparecer el siguiente error: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** (No se pudo iniciar la máquina virtual "vm-name". No se pudo actualizar la configuración de salida de serie de la VM 'vm-name'). El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque.
- Corrección: Vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.
- Repetición: Común

### <a name="virtual-machine-scale-set"></a>Conjunto de escalado de máquina virtual

#### <a name="centos"></a>CentOS

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La experiencia de creación de conjuntos de escalado de máquinas virtuales proporciona la versión 7.2 basada en CentOS como una opción para la implementación. CentOS 7.2 no está disponible en Azure Stack.
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

#### <a name="scale-set-instance-view-blade-does-not-load"></a>La hoja de la vista de la instancia del conjunto de escalado no se carga

- Aplicable a: Este problema se aplica a las versiones 1904, 1905 y 1906.
- Causa: La hoja de la vista de la instancia de un conjunto de escalado de máquinas virtuales que se encuentra en el portal de Azure Stack, en **Panel > Virtual Machine Scale Sets > AnyScaleSet > Instancias > AnyScaleSetInstance** no se puede cargar y muestra la imagen de una nube de lluvia.
- Corrección: Actualmente no hay ninguna corrección y estamos trabajando en una solución. Hasta entonces, use el comando `az vmss get-instance-view` de la CLI para obtener la vista de instancia de un conjunto de escalado.

### <a name="ubuntu-ssh-access"></a>Acceso a SSH en Ubuntu

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Una VM de Ubuntu 18.04 creada con la autorización de SSH habilitada no le permite usar las claves SSH para iniciar sesión.
- Corrección: Utilice el acceso a la VM para la extensión de Linux a fin de implementar las claves SSH después del aprovisionamiento o utilice la autenticación basada en contraseña.
- Repetición: Común

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>La contraseña de restablecimiento del conjunto de escalado de máquinas virtuales no funciona

- Aplicable a: Este problema se aplica a la versión 1906.
- Causa: Aparece una nueva hoja para restablecer la contraseña en la interfaz de usuario del conjunto de escalado, pero Azure Stack todavía no admite el restablecimiento de contraseña en un conjunto de escalado.
- Corrección: Ninguno.
- Repetición: Común

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Nube con lluvia en el diagnóstico del conjunto de escalado

- Aplicable a: Este problema se aplica a la versión 1906.
- Causa: La página de información general del conjunto de escalado de máquinas virtuales muestra un gráfico vacío. Al hacer clic en el gráfico vacío, se abre una hoja de "nube de lluvia". Este es el gráfico de información de diagnóstico del conjunto de escalado, como el porcentaje de CPU, y no es una característica admitida en la compilación actual de Azure Stack.
- Corrección: Ninguno.
- Repetición: Común

### <a name="virtual-machine-diagnostic-settings-blade"></a>Hoja de configuración de diagnóstico de la máquina virtual

- Aplicable a: Este problema se aplica a la versión 1906.
- Causa: La hoja de configuración de diagnóstico de la máquina virtual tiene una pestaña **Receptor**, que solicita una **Cuenta de Application Insights**. Este es el resultado de una nueva hoja y aún no se admite en Azure Stack.
- Corrección: Ninguno.
- Repetición: Común

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## App Service -->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Pasos siguientes

- [Revisión de la lista de comprobación de actividades de actualización](azure-stack-release-notes-checklist.md)
- [Revisión de la lista de actualizaciones de seguridad](azure-stack-release-notes-security-updates-1906.md)
