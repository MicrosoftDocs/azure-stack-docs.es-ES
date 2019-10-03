---
title: Conexión a Azure Stack con PowerShell como usuario | Microsoft Docs
description: Aprenda a conectarse a Azure Stack con PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/02/2019
ms.openlocfilehash: 6a75eb788afd84b6619326293ae2399d8ed5b0e1
ms.sourcegitcommit: 28c8567f85ea3123122f4a27d1c95e3f5cbd2c25
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/02/2019
ms.locfileid: "71824219"
---
# <a name="connect-to-azure-stack-with-powershell-as-a-user"></a>Conexión a Azure Stack con PowerShell como usuario

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede conectarse a Azure Stack con PowerShell para administrar los recursos de Azure Stack. Por ejemplo, puede usar PowerShell para suscribirse a ofertas, crear máquinas virtuales e implementar plantillas de Azure Resource Manager.

Para establecer la configuración:
  - Asegúrese de que cumple los requisitos.
  - Conéctese a Azure Active Directory (Azure AD) o a Servicios de federación de Active Directory (AD FS). 
  - Registre los proveedores de recursos.
  - Pruebe la conectividad.

## <a name="prerequisites-to-connecting-with-powershell"></a>Requisitos previos para la conexión con PowerShell

Configure estos requisitos previos desde el [kit de desarrollo](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) o desde un cliente externo basado en Windows si está [conectado a través de VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Instale los [módulos de Azure PowerShell compatibles con Azure Stack](../operator/azure-stack-powershell-install.md).
* Descargue las [herramientas necesarias para trabajar con Azure Stack](../operator/azure-stack-powershell-download.md).

Asegúrese de reemplazar las siguientes variables de script con los valores de la configuración de Azure Stack:

- **Nombre del inquilino de Azure AD**  
  Nombre del inquilino de Azure AD que se usa para administrar Azure Stack. Por ejemplo, yourdirectory.onmicrosoft.com.
- **Punto de conexión de Azure Resource Manager**  
  En el kit de desarrollo de Azure Stack, este valor se establece en https://management.local.azurestack.external. Para obtener este valor para los sistemas integrados de Azure Stack, póngase en contacto con su proveedor de servicios.

## <a name="connect-to-azure-stack-with-azure-ad"></a>Conexión a Azure Stack con Azure AD

```powershell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack cmdlets
    # can be easily targeted at your Azure Stack instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

## <a name="connect-to-azure-stack-with-ad-fs"></a>Conexión a Azure Stack con AD FS

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRmAccount -EnvironmentName "AzureStackUser"
  ```

## <a name="register-resource-providers"></a>Registro de proveedores de recursos

Los proveedores de recursos no se registran automáticamente para nuevas suscripciones de usuario que no tienen ningún recurso implementado a través del portal. Puede registrar explícitamente un proveedor de recursos ejecutando el script siguiente:

```powershell  
foreach($s in (Get-AzureRmSubscription)) {
        Select-AzureRmSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRmResourceProvider -ListAvailable | Register-AzureRmResourceProvider
    }
```

## <a name="test-the-connectivity"></a>Prueba de la conectividad

Cuando tenga todo configurado, pruebe la conectividad con PowerShell para crear recursos en Azure Stack. Como prueba, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Ejecute el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Pasos siguientes

- [Desarrollo de plantillas para Azure Stack](azure-stack-develop-templates.md)
- [Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
- [Referencia del módulo de PowerShell de Azure Stack](https://docs.microsoft.com/powershell/azure/azure-stack/overview)
- Si desea configurar PowerShell para el entorno de operadores en la nube, consulte el artículo [Configuración del entorno de PowerShell del operador de Azure Stack](../operator/azure-stack-powershell-configure-admin.md).
