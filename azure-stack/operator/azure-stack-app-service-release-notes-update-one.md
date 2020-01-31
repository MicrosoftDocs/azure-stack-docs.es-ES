---
title: Notas de la versión de la actualización 1 de App Service en Azure Stack Hub
description: Aprenda sobre las mejoras, las correcciones y los problemas conocidos de la actualización 1 para App Service en Azure Stack Hub.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/20/2018
ms.openlocfilehash: d81c0387e26a72865b74a88062c5b7b7588b3281
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76875970"
---
# <a name="app-service-on-azure-stack-hub-update-1-release-notes"></a>Notas de la versión de la actualización 1 de App Service en Azure Stack Hub

En estas notas de la versión se describen las mejoras, las correcciones y los problemas conocidos de la actualización 1 de Azure App Service en Azure Stack Hub. Los problemas conocidos se dividen tres secciones: los problemas directamente relacionados con la implementación, los problemas relacionados con el proceso de actualización y los problemas relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Aplique la actualización 1802 al sistema integrado de Azure Stack Hub o implemente el Kit de desarrollo de Azure Stack (ASDK) más reciente antes de implementar Azure App Service.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 1 de App Service en Azure Stack Hub es **69.0.13698.9**.

### <a name="prerequisites"></a>Prerequisites

> [!IMPORTANT]
> Las nuevas implementaciones de Azure App Service en Azure Stack Hub requieren ahora un [certificado comodín con tres firmantes](azure-stack-app-service-before-you-get-started.md#get-certificates) debido a las mejoras en la forma en que se trata el inicio de sesión único para Kudu en Azure App Service. El nuevo firmante es **\*.sso.appservice.\<region\>.\<domainname\>.\<extension\>**

Consulte [Requisitos previos para implementar App Service en Azure Stack Hub](azure-stack-app-service-before-you-get-started.md) antes de comenzar la implementación.

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

La actualización 1 de Azure App Service en Azure Stack Hub incluye las siguientes correcciones y mejoras:

- **Alta disponibilidad de Azure App Service**: la actualización 1802 de Azure Stack Hub permitía que se implementaran las cargas de trabajo en los dominios de error, de forma que la infraestructura de App Service era tolerante a errores. De forma predeterminada, todas las implementaciones nuevas de Azure App Service tienen esta funcionalidad. Sin embargo, para las implementaciones realizadas antes de aplicarse la actualización 1802 de Azure Stack Hub, consulte la [documentación de dominios de error de App Service](azure-stack-app-service-before-you-get-started.md).

- **Implementación en la red virtual existente**: los clientes ahora pueden implementar App Service en Azure Stack Hub dentro de una red virtual existente. La implementación de una red virtual existente permite a los clientes conectarse al servidor de archivos y a SQL Server, lo que es necesario para Azure App Service, a través de puertos privados. Durante la implementación, los clientes pueden seleccionar llevarla a cabo en una red virtual existente; sin embargo, [deben crear subredes para que App Service las use](azure-stack-app-service-before-you-get-started.md#virtual-network) antes de la implementación.

- Actualizaciones de las **herramientas de Kudu, los portales de Functions, Admin e inquilino de App Service**. Es coherente con la versión del SDK del portal de Azure Stack Hub.

- Actualiza el **entorno de ejecución de Azure Functions** a **v1.0.11388**.

- **Actualizaciones de las herramientas y plataformas de aplicaciones siguientes**:
    - Se ha agregado compatibilidad con **.Net Core 2.0**.
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
    - Se ha agregado actualizaciones de **PHP**:
        - 5.6.32
        - 7.0.26 (x86 y x64)
        - 7.1.12 (x86 y x64)
    - **Git para Windows** actualizado a v 2.14.1
    - **Mercurial** actualizado a v4.5.0

  - Se agregó compatibilidad con la característica **Solo HTTPS**  dentro de la característica Dominio personalizado del portal de usuarios de App Service.

  - Se ha agregado la validación de la conexión de almacenamiento en el selector de almacenamiento personalizado para Azure Functions.

#### <a name="fixes"></a>Correcciones

- Al crear un paquete de implementación sin conexión, los clientes ya no recibirán un mensaje de error de acceso denegado al abrir la carpeta desde el instalador de App Service.

- Se han resuelto los problemas que se producían cuando se trabaja en la característica de dominios personalizados en el portal de usuarios de App Service.

- Se evita que los clientes usen durante la instalación nombres de administrador reservados.

- Se ha habilitado la implementación de App Service con un servidor de archivos **unido a un dominio**.

- Se ha mejorado la recuperación del certificado raíz de Azure Stack Hub en un script y se ha agregado la capacidad de validar el certificado raíz en el instalador de App Service.

- Se devuelve un estado incorrecto fijo a Azure Resource Manager cuando se elimina una suscripción que contenía recursos en el espacio de nombres Microsoft.Web.

### <a name="known-issues-with-the-deployment-process"></a>Problemas conocidos relacionados con el proceso de implementación

- Errores de validación de certificados.

    Algunos clientes han experimentado problemas al proporcionar los certificados al instalador de App Service al realizar la implementación sobre un sistema integrado, debido a la validación demasiado restrictiva del instalador. El instalador de App Service se ha lanzado de nuevo, así que los clientes deben [descargar la versión actualizada](https://aka.ms/appsvconmasinstaller). Si sigue experimentando problemas al validar los certificados con el instalador actualizado, póngase en contacto con el soporte técnico.

- Problema al recuperar el certificado raíz de Azure Stack Hub del sistema integrado.

    Un error en Get-AzureStackRootCert.ps1 ha causado que los clientes no pudieran recuperar el certificado raíz de Azure Stack Hub al ejecutar el script en una máquina que no tiene instalado dicho certificado. El script también se ha vuelto a publicar, lo que resuelve el problema. [Descargue aquí los scripts de la aplicación auxiliar actualizada](https://aka.ms/appsvconmashelpers). Si sigue experimentando problemas al recuperar el certificado raíz con el script actualizado, póngase en contacto con el soporte técnico.

### <a name="known-issues-with-the-update-process"></a>Problemas conocidos relacionados con el proceso de actualización

- No hay ningún problema conocido en la actualización 1 de Azure App Service en Azure Stack Hub.

### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

- El intercambio de espacios no funciona.

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

2. Vaya a **CN0-VM** en Virtual Machines en el portal del administrador de Azure Stack Hub y haga clic en **Connect** (Conectar) para abrir una sesión de Escritorio remoto con la instancia del controlador. Use las credenciales especificadas durante la implementación de App Service.
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

Si decide realizar una implementación en una red virtual existente y en una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. De ese modo, permite que exista tráfico SMB entre la subred del rol de trabajo y el servidor de archivos. Vaya a WorkersNsg en el portal del administrador y agregue una regla de seguridad de salida con las siguientes propiedades:

- Origen: Any
- Intervalo de puertos de origen: *
- Destino: Direcciones IP
- Intervalo de direcciones IP de destino: Intervalo de direcciones IP del servidor de archivos
- Intervalo de puertos de destino: 445
- Protocolo: TCP
- Acción: Allow
- Prioridad: 700
- Nombre: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Problemas conocidos para los administradores de la nube que usan Azure App Service en Azure Stack Hub

Consulte la documentación de las [notas de la versión 1802 de Azure Stack Hub](azure-stack-update-1903.md).

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre Azure App Service, consulte [Introducción a Azure App Service en Azure Stack Hub](azure-stack-app-service-overview.md).
- Para más información sobre cómo prepararse para implementar App Service en Azure Stack Hub, consulte [Requisitos previos para implementar App Service en Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
