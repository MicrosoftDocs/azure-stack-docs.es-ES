---
title: Problemas conocidos de Azure Stack 1907
description: Obtenga información sobre los problemas conocidos de las versiones 1907 de Azure Stack.
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
ms.date: 10/29/2019
ms.author: sethm
ms.reviewer: prchint
ms.lastreviewed: 10/29/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 435f761633b3513be39cd5912cbcee697003e952
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99248783"
---
# <a name="azure-stack-1907-known-issues"></a>Problemas conocidos de Azure Stack 1907

En este artículo se enumeran los problemas conocidos de las versiones de Azure Stack. La lista se actualiza conforme se identifican nuevos problemas.

Para tener acceso a los problemas conocidos de una versión diferente, use la lista desplegable del selector de versiones encima de la tabla de contenido de la izquierda.

> [!IMPORTANT]  
> Revise esta sección antes de aplicar la actualización.

> [!IMPORTANT]  
> Si a su instancia le faltan más de dos actualizaciones de Azure Stack, se considera que no cumple los requisitos. [Para recibir soporte técnico, deberá actualizarla al menos a la versión mínima admitida.](../azure-stack-servicing-policy.md#keep-your-system-under-support)

## <a name="1907-update-process"></a>Proceso de actualización de 1907

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al intentar instalar la actualización 1907 de Azure Stack, es posible que se produzca un error en el estado de esa actualización y dicho estado cambie a **PreparationFailed**. La causa es que el proveedor de recursos de actualización (URP) no se puede transferir correctamente desde el contenedor de almacenamiento a un recurso compartido de infraestructura interno para su procesamiento.
- Corrección: A partir de la versión 1901 (1.1901.0.95), para solucionar este problema, puede hacer clic en **Actualizar ahora** de nuevo (en lugar de en **Reanudar**). A continuación, URP limpia los archivos del intento anterior y vuelve a iniciar la descarga. Si el problema persiste, recomendamos cargar manualmente el paquete de actualización siguiendo la sección [Importación e instalación de actualizaciones](../azure-stack-apply-updates.md).
- Repetición: Comunes

## <a name="portal"></a>Portal

### <a name="administrative-subscriptions"></a>Suscripciones administrativas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Las dos suscripciones administrativas que se incluyeron con la versión 1804 no deberían usarse. Los tipos de suscripción son **suscripción de medición** y **suscripción de consumo**.
- Corrección: Si tiene recursos que se ejecutan en estas dos suscripciones, vuelva a crearlos en las suscripciones de usuario.
- Repetición: Comunes

### <a name="subscriptions-properties-blade"></a>Hoja de propiedades de suscripciones

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: en el portal de administrador, la hoja **Propiedades** de las suscripciones no se carga correctamente.
- Corrección: puede ver estas propiedades de suscripciones en el panel **Essentials** (Información esencial) de la hoja **Introducción a las suscripciones**.
- Repetición: Comunes

### <a name="subscription-permissions"></a>Permisos de suscripción

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No puede ver los permisos de la suscripción mediante los portales de Azure Stack.
- Corrección: Use [PowerShell para verificar los permisos](/powershell/module/azurerm.resources/get-azurermroleassignment).
- Repetición: Comunes

### <a name="storage-account-settings"></a>Configuración de cuentas de almacenamiento

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Configuración** de la cuenta de almacenamiento muestra una opción para cambiar el **tipo de transferencia de seguridad**. La característica no se admite actualmente en Azure Stack.
- Repetición: Comunes

### <a name="upload-blob"></a>Carga de blob

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, al intentar cargar un blob mediante la opción **OAuth(preview)** , la tarea genera un mensaje de error.
- Corrección: Cargue el blob mediante la opción SAS.
- Repetición: Comunes

## <a name="networking"></a>Redes

### <a name="service-endpoints"></a>Puntos de conexión del servicio

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Red virtual** muestra una opción para usar **puntos de conexión de servicio**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Comunes

### <a name="network-interface"></a>interfaz de red

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se puede agregar una nueva interfaz de red a una VM cuyo estado es **En ejecución**.
- Corrección: Detenga la máquina virtual antes de agregar o quitar una interfaz de red.
- Repetición: Comunes

### <a name="virtual-network-gateway"></a>Puerta de enlace de red virtual

#### <a name="alerts"></a>Alertas

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Puerta de enlace de red virtual** muestra una opción para usar **alertas**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Comunes

#### <a name="active-active"></a>Activo-activo

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, durante la creación, y en el menú de recursos de **Puerta de enlace de red virtual**, verá una opción para habilitar la configuración **activa-activa**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Comunes

#### <a name="vpn-troubleshooter"></a>Solucionador de problemas de VPN

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la hoja **Conexiones** muestra una característica denominada **Solucionador de problemas de VPN**. Esta característica no se admite actualmente en Azure Stack.
- Repetición: Comunes

### <a name="network-connection-type"></a>Tipo de conexión de red

- Aplicable a: Este problema se aplica a cualquier entorno de 1906 o 1907. 
- Causa: En el portal de usuarios, la hoja **AddConnection** muestra una opción para usar la opción **De red virtual a red virtual**. Esta característica no se admite actualmente en Azure Stack. 
- Repetición: Comunes 

#### <a name="documentation"></a>Documentación

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Los vínculos de la documentación de la página de información general de Puerta de enlace de red virtual enlazan a la documentación específica de Azure en lugar de a la de Azure Stack. Use los vínculos siguientes para la documentación de Azure Stack:

  - [SKU de puerta de enlace](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-skus)
  - [Conexiones de alta disponibilidad](../../user/azure-stack-vpn-gateway-about-vpn-gateways.md#gateway-availability)
  - [Configuración de BGP en Azure Stack](../../user/azure-stack-vpn-gateway-settings.md#gateway-requirements)
  - [Circuitos ExpressRoute](../azure-stack-connect-expressroute.md)
  - [Especificación de directivas de IPsec o IKE personalizadas](../../user/azure-stack-vpn-gateway-settings.md#ipsecike-parameters)

## <a name="compute"></a>Proceso

### <a name="vm-boot-diagnostics"></a>Diagnósticos de arranque de VM

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al crear una nueva máquina virtual Windows, puede aparecer el siguiente error: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** (No se pudo iniciar la máquina virtual "vm-name". No se pudo actualizar la configuración de salida de serie de la VM 'vm-name'). El error se produce si habilita el diagnóstico de arranque en una VM, pero elimina la cuenta de almacenamiento de diagnósticos de arranque.
- Corrección: Vuelva a crear la cuenta de almacenamiento con el mismo nombre que usó anteriormente.
- Repetición: Comunes

### <a name="virtual-machine-scale-set"></a>Conjunto de escalado de máquina virtual

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Crear errores durante la revisión y actualización de entornos de Azure Stack de 4 nodos

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La creación de VM en un conjunto de disponibilidad de 3 dominios de error y la creación de una instancia de conjunto de escalado de máquinas virtuales genera un error **FabricVmPlacementErrorUnsupportedFaultDomainSize** durante el proceso de actualización en un entorno de Azure Stack de 4 nodos.
- Corrección: Se pueden crear VM únicas en un conjunto de disponibilidad con 2 dominios de error correctamente. Sin embargo, la creación de instancias del conjunto de escalado todavía no está disponible durante el proceso de actualización en una instancia de Azure Stack de 4 nodos.

### <a name="ubuntu-ssh-access"></a>Acceso a SSH en Ubuntu

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Una VM de Ubuntu 18.04 creada con la autorización de SSH habilitada no le permite usar las claves SSH para iniciar sesión.
- Corrección: Utilice el acceso a la VM para la extensión de Linux a fin de implementar las claves SSH después del aprovisionamiento o utilice la autenticación basada en contraseña.
- Repetición: Comunes

### <a name="virtual-machine-scale-set-reset-password-does-not-work"></a>La contraseña de restablecimiento del conjunto de escalado de máquinas virtuales no funciona

- Aplicable a: Este problema se aplica a las versiones 1906 y 1907.
- Causa: Aparece una nueva hoja para restablecer la contraseña en la interfaz de usuario del conjunto de escalado, pero Azure Stack todavía no admite el restablecimiento de contraseña en un conjunto de escalado.
- Corrección: Ninguno.
- Repetición: Comunes

### <a name="rainy-cloud-on-scale-set-diagnostics"></a>Nube con lluvia en el diagnóstico del conjunto de escalado

- Aplicable a: Este problema se aplica a las versiones 1906 y 1907.
- Causa: La página de información general del conjunto de escalado de máquinas virtuales muestra un gráfico vacío. Al hacer clic en el gráfico vacío, se abre una hoja de "nube de lluvia". Este es el gráfico de información de diagnóstico del conjunto de escalado, como el porcentaje de CPU, y no es una característica admitida en la compilación actual de Azure Stack.
- Corrección: Ninguno.
- Repetición: Comunes

### <a name="virtual-machine-diagnostic-settings-blade"></a>Hoja de configuración de diagnóstico de la máquina virtual

- Aplicable a: Este problema se aplica a las versiones 1906 y 1907.    
- Causa: La hoja de configuración de diagnóstico de la máquina virtual tiene una pestaña **Receptor**, que solicita una **Cuenta de Application Insights**. Este es el resultado de una nueva hoja y aún no se admite en Azure Stack.
- Corrección: Ninguno.
- Repetición: Comunes
