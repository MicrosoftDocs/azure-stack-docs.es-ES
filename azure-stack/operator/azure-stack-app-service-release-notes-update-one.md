---
title: Notas de la versión de la actualización 1 de App Service en Azure Stack | Microsoft Docs
description: Conozca el contenido de la actualización 1 para App Service en Azure Stack, los problemas conocidos y la ubicación dónde debe descargarse.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digmobile
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 03/25/2018
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: sethm
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: 86eb23e57516bc108557bbdc4f2d131153f5e855
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64306489"
---
# <a name="app-service-on-azure-stack-update-1-release-notes"></a>Notas de la versión de App Service en la actualización 1 de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Estas notas de la versión describen las mejoras y correcciones de la actualización 1 de Azure App Service en Azure Stack y todos los problemas conocidos. Los problemas conocidos se dividen en aquellos que están relacionados directamente con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Aplique la actualización 1802 al sistema integrado de Azure Stack o implemente el kit de desarrollo de Azure Stack más reciente antes de implementar Azure App Service.
>
>

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 1 de App Service en Azure Stack es **69.0.13698.9**

### <a name="prerequisites"></a>Requisitos previos

> [!IMPORTANT]
> Las nuevas implementaciones de Azure App Service en Azure Stack requieren ahora un [certificado comodín con tres firmantes](azure-stack-app-service-before-you-get-started.md#get-certificates) debido a las mejoras en la forma en que se trata SSO para Kudu en este momento en Azure App Service. El nuevo firmante es **\*.sso.appservice.\<region\>.\<domainname\>.\<extension\>**
>
>

Remítase a la [documentación de introducción](azure-stack-app-service-before-you-get-started.md) antes de comenzar la implementación.

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

La actualización 1 de Azure App Service en Azure Stack incluye las siguientes correcciones y mejoras:

- **Alta disponibilidad de Azure App Service**: la actualización 1802 de Azure Stack permitía que las cargas de trabajo se implementaran en dominios con errores. Por lo tanto, la infraestructura de App Service es tolerante a errores ya que se implementará en dominios con error. De forma predeterminada, todas las nuevas implementaciones de Azure App Service tienen esta funcionalidad. Para las implementaciones completadas antes de aplicarse la actualización 1802 de Azure Stack, consulte la [documentación del dominio de error de App Service](azure-stack-app-service-before-you-get-started.md ).

- **Implementación en la red virtual existente**: los clientes ahora pueden implementar App Service en Azure Stack dentro de una red virtual existente. La implementación de una red virtual existente permite a los clientes conectarse al servidor de archivos y a SQL Server, lo que es necesario para Azure App Service, a través de puertos privados. Durante la implementación, los clientes pueden seleccionar llevarla a cabo en una red virtual existente, sin embargo [deben crear subredes para que App Service las use](azure-stack-app-service-before-you-get-started.md#virtual-network) antes de la implementación.

- Actualizaciones de las **herramientas de Kudu, los portales de Functions, Admin e inquilino de App Service**. Es coherente con la versión del SDK de Azure Stack Portal.

- Actualiza el **entorno de ejecución de Azure Functions** a **v1.0.11388**.

- **Actualizaciones de las herramientas y plataformas de aplicaciones siguientes**:
    - Se agregó compatibilidad con **.Net Core 2.0**
    - Versiones de **Node.JS** agregadas:
        - 6.11.2
        - 6.11.5
        - 7.10.1
        - 8.0.0
        - 8.1.4
        - 8.4.0
        - 8.5.0
        - 8.7.0
        - 8.8.1
        - 8.9.0
    - Versiones de **NPM** agregadas:
        - 3.10.10
        - 4.2.0
        - 5.0.0
        - 5.0.3
        - 5.3.0
        - 5.4.2
        - 5.5.1
    - Actualizaciones de **PHP** agregadas:
        - 5.6.32
        - 7.0.26 (x86 y x64)
        - 7.1.12 (x86 y x64)
    - **Git para Windows** actualizado a v 2.14.1
    - **Mercurial** actualizado a v4.5.0

  - Se agregó compatibilidad con la característica **solo para HTTPS**  dentro de la característica de dominio personalizado en el portal de inquilinos de App Service. 

  - Se agregó la validación de la conexión de almacenamiento en el selector de almacenamiento personalizado para Azure Functions 

#### <a name="fixes"></a>Correcciones

- Al crear un paquete de implementación sin conexión, los clientes ya no recibirán un mensaje de error de acceso denegado al abrir la carpeta desde el instalador de App Service

- Se resuelven los problemas que se producen cuando se trabaja en la característica de dominios personalizados en el portal de inquilinos de App Service.

- Se evita que los clientes usen durante la instalación nombres de administrador reservados

- Se habilita la implementación de App Service con un servidor de archivos **unido a un dominio**

- Se mejora la recuperación del certificado raíz de Azure Stack en un script y se comprueba ahora el certificado raíz en el instalador de App Service.

- Se devuelve un estado incorrecto fijo a Azure Resource Manager cuando se elimina una suscripción que contenía recursos en el espacio de nombres Microsoft.Web.

### <a name="known-issues-with-the-deployment-process"></a>Problemas conocidos relacionados con el proceso de implementación

- Errores de validación de certificados

Algunos clientes han experimentado problemas al proporcionar los certificados al instalador de App Service al realizar la implementación sobre un sistema integrado, debido a la validación demasiado restrictiva del instalador. El instalador de App Service se ha lanzado de nuevo, así que los clientes deben [descargar la versión actualizada](https://aka.ms/appsvconmasinstaller). Si sigue experimentando problemas al validar los certificados con el instalador actualizado, póngase en contacto con el soporte técnico.

- Problema al recuperar el certificado raíz de Azure Stack del sistema integrado

Un error en Get-AzureStackRootCert.ps1 hizo que los clientes no pudieran recuperar el certificado raíz de Azure Stack al ejecutar el script en una máquina que no tiene instalado dicho certificado. El script también se ha vuelto a lanzar, lo que resuelve el problema, y se solicita a los clientes que [descarguen los scripts auxiliares actualizados](https://aka.ms/appsvconmashelpers). Si sigue experimentando problemas al recuperar el certificado raíz con el script actualizado, póngase en contacto con el soporte técnico.

### <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización

- No hay ningún problema conocido para la actualización 1 de Azure App Service en Azure Stack.

### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

- El intercambio de espacios no funciona

El intercambio de espacios del sitio se ha interrumpido en esta versión. Para restaurar la funcionalidad, siga estos pasos:

1. Modifique el grupo de seguridad de red ControllersNSG para **permitir** conexiones de Escritorio remoto a las instancias de controlador de App Service. Reemplace AppService.local por el nombre del grupo de recursos en el que implementó App Service.

    ```powershell
      Add-AzureRmAccount -EnvironmentName AzureStackAdmin

      $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

      $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

      Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
        -Name $RuleConfig_Inbound_Rdp_3389.Name `
        -Description "Inbound_Rdp_3389" `
        -Access Allow `
        -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
        -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
        -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
        -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
        -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
        -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
        -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

      # Commit the changes back to NSG
      Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

2. Vaya a **CN0-VM** en Virtual Machines en el portal del administrador de Azure Stack y haga clic en **Connect** (Conectar) para abrir una sesión de Escritorio remoto con la instancia de controlador. Use las credenciales especificadas durante la implementación de App Service.
3. Inicie **PowerShell como administrador** y ejecute el siguiente script:

    ```powershell
        Import-Module appservice

        $sm = new-object Microsoft.Web.Hosting.SiteManager

        if($sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$true)
        {
          $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus=$false
        #  'Slot swap mode reverted'
        }
        
        # Confirm new setting is false
        $sm.HostingConfiguration.SlotsPollWorkerForChangeNotificationStatus
        
        # Commit Changes
        $sm.CommitChanges()

        Get-AppServiceServer -ServerType ManagementServer | ForEach-Object Repair-AppServiceServer
        
    ```

4. Cierre la sesión de Escritorio remoto.
5. Revierta el grupo de seguridad de red ControllersNSG para **denegar** las conexiones de Escritorio remoto a las instancias de controlador de App Service. Reemplace AppService.local por el nombre del grupo de recursos en el que implementó App Service.

    ```powershell

        Add-AzureRmAccount -EnvironmentName AzureStackAdmin

        $nsg = Get-AzureRmNetworkSecurityGroup -Name "ControllersNsg" -ResourceGroupName "AppService.local"

        $RuleConfig_Inbound_Rdp_3389 =  $nsg | Get-AzureRmNetworkSecurityRuleConfig -Name "Inbound_Rdp_3389"

        Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
          -Name $RuleConfig_Inbound_Rdp_3389.Name `
          -Description "Inbound_Rdp_3389" `
          -Access Deny `
          -Protocol $RuleConfig_Inbound_Rdp_3389.Protocol `
          -Direction $RuleConfig_Inbound_Rdp_3389.Direction `
          -Priority $RuleConfig_Inbound_Rdp_3389.Priority `
          -SourceAddressPrefix $RuleConfig_Inbound_Rdp_3389.SourceAddressPrefix `
          -SourcePortRange $RuleConfig_Inbound_Rdp_3389.SourcePortRange `
          -DestinationAddressPrefix $RuleConfig_Inbound_Rdp_3389.DestinationAddressPrefix `
          -DestinationPortRange $RuleConfig_Inbound_Rdp_3389.DestinationPortRange

        # Commit the changes back to NSG
        Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg
    ```

6. Los trabajos no pueden llegar al servidor de archivos cuando App Service está implementada en una red virtual existente y el servidor de archivos solo está disponible en la red privada.

Si decide realizar una implementación en una red virtual existente y en una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. De ese modo, permite que exista tráfico SMB entre la subred del rol de trabajo y el servidor de archivos. Para ello, vaya a WorkersNsg en el Portal de administración y agregue una regla de seguridad de salida con las siguientes propiedades:

- Origen: Cualquiera
- Intervalo de puertos de origen: *
- Destino: Direcciones IP
- Intervalo de direcciones IP de destino: Intervalo de direcciones IP del servidor de archivos
- Intervalo de puertos de destino: 445
- Protocolo: TCP
- Acción: PERMITIR
- Prioridad: 700
- Nombre: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conocidos para los administradores en la nube que trabajan con Azure App Service en Azure Stack

Consulte la documentación de las [notas de la versión de Azure Stack 1802](azure-stack-update-1903.md)

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de Azure App Service, consulte la [Información general de Azure App Service en Azure Stack](azure-stack-app-service-overview.md).
- Para obtener más información acerca de cómo prepararse para implementar App Service en Azure Stack, consulte [Antes de empezar a trabajar con App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md).

<!-- Update_Description: wording update -->