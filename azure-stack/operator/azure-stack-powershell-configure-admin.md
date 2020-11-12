---
title: Conexión a Azure Stack Hub con PowerShell
description: Aprenda a conectarse a Azure Stack Hub con PowerShell.
author: mattbriggs
ms.topic: article
ms.date: 10/19/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: d99212c63e33060fbbb8eb483dd32e7c01d54ba1
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94545149"
---
# <a name="connect-to-azure-stack-hub-with-powershell"></a>Conexión a Azure Stack Hub con PowerShell

Puede configurar Azure Stack Hub para usar PowerShell para administrar recursos, como la creación de ofertas, planes, cuotas y alertas. Este tema le ayuda a configurar el entorno de operador.

## <a name="prerequisites"></a>Prerrequisitos

Implemente los siguientes requisitos previos desde el [Kit de desarrollo de Azure Stack (ASDK)](../asdk/asdk-connect.md#connect-with-rdp) o desde un cliente externo basado en Windows si está [conectado al ASDK por VPN](../asdk/asdk-connect.md#connect-with-vpn).

- Instale los [módulos de Azure PowerShell compatibles con Azure Stack Hub](powershell-install-az-module.md).  
- Descargue las [herramientas necesarias para trabajar con Azure Stack Hub](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Conexión con Azure AD

Para configurar el entorno de operador de Azure Stack Hub con PowerShell, ejecute uno de los scripts siguientes. Reemplace los valores de tenantName de Azure Active Directory (Azure AD) y el punto de conexión de Azure Resource Manager por la configuración de su propio entorno.

[!include[Remove Account](../../includes/remove-account.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzureRmEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRmAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```

## <a name="connect-with-ad-fs"></a>Conexión con AD FS

Conéctese al entorno de operador de Azure Stack Hub con PowerShell con Azure Active Directory Federated Services (Azure AD FS). Para el ASDK, este punto de conexión de Azure Resource Manager se establece en `https://adminmanagement.local.azurestack.external`. Para obtener el punto de conexión de Azure Resource Manager para los sistemas integrados de Azure Stack Hub, póngase en contacto con su proveedor de servicios.

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
  ```

[!Include [AD FS only supports interactive authentication with user identities](../includes/note-powershell-adfs.md)]

## <a name="test-the-connectivity"></a>Prueba de la conectividad

Ahora que todo está configurado, use PowerShell para crear recursos en Azure Stack Hub. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado **MyResourceGroup**.

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

## <a name="next-steps"></a>Pasos siguientes

- [Desarrollo de plantillas para Azure Stack Hub](../user/azure-stack-develop-templates.md).
- [Implementación de plantillas con PowerShell](../user/azure-stack-deploy-template-powershell.md).
  - [Referencia sobre el módulo de Azure Stack Hub](/powershell/azure/azure-stack/overview).
