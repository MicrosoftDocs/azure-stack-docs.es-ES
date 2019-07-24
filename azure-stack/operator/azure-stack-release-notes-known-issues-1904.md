---
title: Problemas conocidos de Azure Stack 1904 | Microsoft Docs
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
ms.date: 05/31/2019
ms.author: sethm
ms.reviewer: hectorl
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 3a92a142181b9e2268948d430b0a62d6ffa189d8
ms.sourcegitcommit: 90ed5aa051d0756b2432c8aca0e2232c8ec493a4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2019
ms.locfileid: "68239447"
---
# <a name="azure-stack-1904-known-issues"></a>Problemas conocidos de Azure Stack 1904

En este artículo se enumeran los problemas conocidos de la versión de 1904 de Azure Stack. La lista se actualiza conforme se identifican nuevos problemas.

> [!IMPORTANT]  
> Revise esta sección antes de aplicar la actualización.

## <a name="update-process"></a>Proceso de actualización

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al intentar instalar una actualización de Azure Stack, es posible que se produzca un error en el estado de esa actualización y dicho estado se cambie a **PreparationFailed**. La causa es que el proveedor de recursos de actualización (URP) no se puede transferir correctamente desde el contenedor de almacenamiento a un recurso compartido de infraestructura interno para su procesamiento.
- Corrección: A partir de la versión 1901 (1.1901.0.95), para solucionar este problema, puede hacer clic en **Actualizar ahora** de nuevo (en lugar de en **Reanudar**). A continuación, URP limpia los archivos del intento anterior y vuelve a iniciar la descarga.
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

### <a name="docker-extension"></a>Extensión de Docker

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En los portales de administrador y de usuario, si se busca **Docker**, el elemento se devuelve de forma incorrecta. No está disponible en Azure Stack. Si intenta crearlo, aparece un error.
- Corrección: Sin mitigación.
- Repetición: Común

### <a name="marketplace-management"></a>Administración de Marketplace

- Aplicable a: se trata de un nuevo problema con la versión 1904.
- Causa: La pantalla de administración de Marketplace no se ve al iniciar sesión en el portal de administradores.
- Corrección: Actualice el explorador.
- Repetición: Intermitente

### <a name="marketplace-management"></a>Administración de Marketplace

- Aplicable a: este problema se aplica a la versión 1904.
- Causa: Al filtrar los resultados en la hoja **Add from Azure** (Agregar desde Azure), en la pestaña Marketplace management (Administración de Marketplace) del portal de administradores, puede ver los resultados filtrados incorrectos.
- Corrección: Ordene los resultados por la columna Nombre y se corregirán.
- Repetición: Intermitente

### <a name="marketplace-management"></a>Administración de Marketplace

- Aplicable a: este problema se aplica a la versión 1904.
- Causa: Al filtrar los resultados de Al filtrar los resultados de Marketplace management (Administración de Marketplace) en el portal de administradores, verá nombres de publicadores duplicados en la lista desplegable de publicadores. 
- Corrección: Seleccione todos los duplicados para tener la lista correcta de todos los productos de Marketplace que están disponibles en ese publicador.
- Repetición: Intermitente

### <a name="docker-extension"></a>Extensión de Docker

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En los portales de administrador y de usuario, si se busca **Docker**, el elemento se devuelve de forma incorrecta. No está disponible en Azure Stack. Si intenta crearlo, aparece un error.
- Corrección: Sin mitigación.
- Repetición: Común

### <a name="upload-blob"></a>Carga de blob

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, al intentar cargar un blob mediante la opción OAuth(preview), la tarea genera un mensaje de error.
- Corrección: Cargue el blob mediante la opción SAS.
- Repetición: Común

### <a name="template"></a>Plantilla

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la interfaz de usuario de implementación de plantillas no rellena los parámetros de los nombres de plantilla que empiezan por "_" (carácter de subrayado).
- Corrección: Quite el "_" (carácter de subrayado) del nombre de la plantilla.
- Repetición: Común

## <a name="networking"></a>Redes

### <a name="load-balancer"></a>Equilibrador de carga

#### <a name="add-backend-pool"></a>Agregar grupo de back-end

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, si intenta agregar un **grupo de back-end** a una **instancia de Load Balancer**, se produce un error en la operación con el mensaje **Failed to update Load Balancer...** (No se pudo actualizar Load Balancer...).
- Corrección: Use PowerShell, la CLI o una plantilla de Azure Resource Manager para asociar el grupo de back-end a un recurso del equilibrador de carga.
- Repetición: Común

#### <a name="create-inbound-nat"></a>Crear una NAT de entrada

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, si intenta crear una **regla NAT de entrada** para una instancia de **Load Balancer**, se produce un error en la operación con el mensaje **Failed to update Load Balancer...** (No se pudo actualizar Load Balancer...).
- Corrección: Use PowerShell, la CLI o una plantilla de Azure Resource Manager para asociar el grupo de back-end a un recurso del equilibrador de carga.
- Repetición: Común

#### <a name="create-load-balancer"></a>Creación de un equilibrador de carga

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la ventana **Crear equilibrador de carga** muestra una opción para crear una SKU **estándar** del equilibrador de carga. En Azure Stack no se admite esta opción.
- Corrección: Use las opciones del equilibrador de carga básico en su lugar.
- Repetición: Común

#### <a name="public-ip-address"></a>Dirección IP pública

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: En el portal de usuarios, la ventana **Crear dirección IP pública** muestra una opción para crear una SKU **estándar**. En Azure Stack no se admite la SKU **estándar**.
- Corrección: Utilice en su lugar la SKU **básica** para las direcciones IP públicas.
- Repetición: Común

## <a name="compute"></a>Proceso

### <a name="vm-boot-diagnostics"></a>Diagnósticos de arranque de VM

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Al crear una nueva máquina virtual (VM) Windows, puede aparecer el siguiente error: **Failed to start virtual machine 'vm-name'. Error: Failed to update serial output settings for VM 'vm-name'** (No se pudo iniciar la máquina virtual "vm-name". No se pudo actualizar la configuración de salida de serie de la VM 'vm-name').
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
- Causa: No se puede quitar un conjunto de escalado desde la hoja **Virtual Machine Scale Sets**.
- Corrección: Seleccione el conjunto de escalado que quiere quitar y, a continuación, haga clic en el botón **Eliminar** del panel **Información general**.
- Repetición: Común

#### <a name="create-failures-during-patch-and-update-on-4-node-azure-stack-environments"></a>Crear errores durante la revisión y actualización de entornos de Azure Stack de 4 nodos

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: La creación de VM en un conjunto de disponibilidad de 3 dominios de error y la creación de una instancia de conjunto de escalado de máquinas virtuales genera un error **FabricVmPlacementErrorUnsupportedFaultDomainSize** durante el proceso de actualización en un entorno de Azure Stack de 4 nodos.
- Corrección: Se pueden crear VM únicas en un conjunto de disponibilidad con 2 dominios de error correctamente. Sin embargo, la creación de instancias del conjunto de escalado todavía no está disponible durante el proceso de actualización en una instancia de Azure Stack de 4 nodos.

### <a name="ubuntu-ssh-access"></a>Acceso a SSH en Ubuntu

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: Una VM de Ubuntu 18.04 creada con la autorización de SSH habilitada no le permite usar las claves SSH para iniciar sesión.
- Corrección: Utilice el acceso a la VM para la extensión de Linux a fin de implementar las claves SSH después del aprovisionamiento o utilice la autenticación basada en contraseña.
- Repetición: Común

### <a name="compute-host-agent-alert"></a>Alerta del agente de host de proceso

- Aplicable a: se trata de un nuevo problema con la versión 1904.
- Causa: Aparece la advertencia **Compute host agent** (Agente de host de proceso) después de reiniciar un nodo en la unidad de escalado. El reinicio cambia la configuración de inicio predeterminada del servicio del agente de host de proceso. Esta alerta es similar a la siguiente:

   ```shell
   NAME  
   Compute Host Agent is not responding to calls.
   SEVERITY  
   Warning
   STATE  
   Active
   CREATED TIME  
   5/16/2019, 10:08:23 AM
   UPDATED TIME  
   5/22/2019, 12:27:27 PM
   COMPONENT  
   M#####-NODE02
   DESCRIPTION  
   Could not communicate with the Compute Host Agent running on node: M#####-NODE02
   REMEDIATION  
   Please disable Compute Host Agent feature flag and collect logs for further diagnosis.
   ```

- Corrección:
  - Esta alerta puede omitirse. El agente que no responde no tiene ningún impacto en las operaciones del operador y el usuario o las aplicaciones del usuario. Esta alerta volverá a aparecer al cabo de 24 horas si se cierra manualmente.
  - El problema se ha corregido en la [última revisión de Azure Stack 1904](https://support.microsoft.com/help/4505688).
- Repetición: Común

### <a name="virtual-machine-scale-set-instance-view"></a>Vista de instancia del conjunto de escalado de máquinas virtuales

- Aplicable a: Este problema se aplica a las versiones 1904 y 1905.
- Causa: la hoja de la vista de la instancia de un conjunto de escalado situado en el portal de Azure Stack, en **Panel** > **Virtual Machine Scale Sets**  >  **AnyScaleSet: Instancias** > **AnyScaleSetInstance** no se puede cargar.
- Corrección: Actualmente no hay ninguna corrección y estamos trabajando en una solución. Hasta entonces, use el cmdlet de la CLI `az vmss get-instance-view` para obtener la vista de instancia de un conjunto de escalado de máquinas virtuales.

### <a name="user-image-service"></a>Servicio de imágenes de usuario
- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: El error en la creación de una imagen de usuario pondrá el servicio de imágenes de usuario en mal estado. Se empezarán a producir errores en las operaciones de creación y eliminación de imágenes de usuario. Pueden producirse errores al eliminar la imagen de usuario: "Error: Se ha producido un error interno de administración de discos."
- Corrección: Sin mitigación. Abra una incidencia de soporte técnico dirigida a Microsoft.

## <a name="storage"></a>Storage

- Aplicable a: este problema se aplica a todas las versiones admitidas.
- Causa: No se admite [ConvertTo-AzureRmVMManagedDisk](/powershell/module/azurerm.compute/convertto-azurermvmmanageddisk) en Azure Stack lo cual da como resultado la creación de un disco con el identificador **$null**. Esto le impide realizar operaciones en la máquina virtual como iniciarla o detenerla. El disco no aparece en la interfaz de usuario, ni aparece a través de la API. Una máquina virtual que ha alcanzado ese punto no se puede reparar y debe eliminarse.
- Corrección: Para convertir los discos correctamente, siga la guía [Conversión en discos administrados](../user/azure-stack-managed-disk-considerations.md#convert-to-managed-disks).

## <a name="app-service"></a>App Service

- Los inquilinos deben registrar el proveedor de recursos de almacenamiento antes de crear su primera instancia de Azure Functions en la suscripción.
- Algunas experiencias de usuario del portal de inquilinos se rompen debido a una incompatibilidad con la el marco del portal en 1903; principalmente, la experiencia de usuario en las ranuras de implementación, las pruebas en producción y las extensiones de sitio. Para solucionar este problema, utilice el [módulo de PowerShell para Azure App Service](/azure/app-service/deploy-staging-slots#automate-with-powershell) o la [CLI de Azure](/cli/azure/webapp/deployment/slot?view=azure-cli-latest). Para restaurar la experiencia del portal, actualice la implementación de [Azure App Service en Azure Stack a 1.6 (Actualización 6)](azure-stack-app-service-release-notes-update-six.md).

<!-- ## Storage -->
<!-- ## SQL and MySQL-->
<!-- ## Usage -->
<!-- ### Identity -->
<!-- ### Marketplace -->

## <a name="next-steps"></a>Pasos siguientes

- [Revisión de la lista de comprobación de actividades de actualización](azure-stack-release-notes-checklist.md)
- [Revisión de la lista de actualizaciones de seguridad](azure-stack-release-notes-security-updates-1904.md)
