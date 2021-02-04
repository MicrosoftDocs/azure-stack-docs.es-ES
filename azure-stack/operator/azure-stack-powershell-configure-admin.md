---
title: Conexión a Azure Stack Hub con PowerShell
description: Aprenda a conectarse a Azure Stack Hub con PowerShell.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 2/1/2021
ms.openlocfilehash: ab38cc4b00da140a529df43ac49789d7eab80327
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/04/2021
ms.locfileid: "99534188"
---
# <a name="connect-to-azure-stack-hub-with-powershell"></a>Conexión a Azure Stack Hub con PowerShell

Puede configurar Azure Stack Hub para usar PowerShell para administrar recursos, como la creación de ofertas, planes, cuotas y alertas. Este tema le ayuda a configurar el entorno de operador.

## <a name="prerequisites"></a>Prerrequisitos

Implemente los siguientes requisitos previos desde el [Kit de desarrollo de Azure Stack (ASDK)](../asdk/asdk-connect.md#connect-with-rdp) o desde un cliente externo basado en Windows si está [conectado al ASDK por VPN](../asdk/asdk-connect.md#connect-with-vpn).

- Instale los [módulos de Azure PowerShell compatibles con Azure Stack Hub](powershell-install-az-module.md).  
- Descargue las [herramientas necesarias para trabajar con Azure Stack Hub](azure-stack-powershell-download.md).  

## <a name="connect-with-azure-ad"></a>Conexión con Azure AD

Para configurar el entorno de operador de Azure Stack Hub con PowerShell, ejecute uno de los scripts siguientes. Reemplace los valores de tenantName de Azure Active Directory (Azure AD) y el punto de conexión de Azure Resource Manager por la configuración de su propio entorno.

### <a name="az-modules"></a>[Modules de Az](#tab/az1)

[!include[Remove Account](../includes/remove-account-az.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

    # Set your tenant name.
    $AuthEndpoint = (Get-AzEnvironment -Name "AzureStackAdmin").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Connect-AzAccount -EnvironmentName "AzureStackAdmin" -TenantId $TenantId
```
### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm1)

[!include[Remove Account](../includes/remove-account-azurerm.md)]

```powershell  
    # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
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

---


## <a name="connect-with-ad-fs"></a>Conexión con AD FS

Conéctese al entorno de operador de Azure Stack Hub con PowerShell con Azure Active Directory Federated Services (Azure AD FS). Para el ASDK, este punto de conexión de Azure Resource Manager se establece en `https://adminmanagement.local.azurestack.external`. Para obtener el punto de conexión de Azure Resource Manager para los sistemas integrados de Azure Stack Hub, póngase en contacto con su proveedor de servicios.

### <a name="az-modules"></a>[Modules de Az](#tab/az2)

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
    Add-AzEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
      -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
      -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

  # Sign in to your environment.
  Connect-AzAccount -EnvironmentName "AzureStackAdmin"
  ```

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm2)

```powershell  
# Register an Azure Resource Manager environment that targets your Azure Stack Hub instance. Get your Azure Resource Manager endpoint value from your service provider.
  Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint "https://adminmanagement.local.azurestack.external" `
    -AzureKeyVaultDnsSuffix adminvault.local.azurestack.external `
    -AzureKeyVaultServiceEndpointResourceId https://adminvault.local.azurestack.external

# Sign in to your environment.
Login-AzureRmAccount -EnvironmentName "AzureStackAdmin"
```

---

[!Include [AD FS only supports interactive authentication with user identities](../includes/note-powershell-adfs.md)]

## <a name="test-the-connectivity"></a>Prueba de la conectividad

Ahora que todo está configurado, use PowerShell para crear recursos en Azure Stack Hub. Por ejemplo, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Use el comando siguiente para crear un grupo de recursos denominado **MyResourceGroup**.

### <a name="az-modules"></a>[Modules de Az](#tab/az3)
```powershell  
New-AzResourceGroup -Name "MyResourceGroup" -Location "Local"
```

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm3)

```powershell  
New-AzureRmResourceGroup -Name "MyResourceGroup" -Location "Local"
```

---


## <a name="next-steps"></a>Pasos siguientes

- [Uso de PowerShell para administrar suscripciones, planes y ofertas en Azure Stack Hub](azure-stack-powershell-plan-offer.md)
- [Desarrollo de plantillas para Azure Stack Hub](../user/azure-stack-develop-templates.md).
- [Implementación de plantillas con PowerShell](../user/azure-stack-deploy-template-powershell.md).
- [Referencia sobre el módulo de Azure Stack Hub](/powershell/azure/azure-stack/overview).
