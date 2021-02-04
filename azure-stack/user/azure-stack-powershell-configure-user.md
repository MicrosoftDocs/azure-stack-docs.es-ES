---
title: Conexión a Azure Stack Hub con PowerShell como usuario
description: Aprenda a conectarse a Azure Stack Hub con PowerShell para usar el símbolo del sistema interactivo o escribir scripts.
author: mattbriggs
ms.topic: article
ms.date: 11/22/2020
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: f89fa7e7e4e8e35b209f0b22a5994c45ede6a17c
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/04/2021
ms.locfileid: "99533984"
---
# <a name="connect-to-azure-stack-hub-with-powershell-as-a-user"></a>Conexión a Azure Stack Hub con PowerShell como usuario

Puede conectarse a Azure Stack Hub con PowerShell para administrar los recursos de Azure Stack Hub. Por ejemplo, puede usar PowerShell para suscribirse a ofertas, crear máquinas virtuales e implementar plantillas de Azure Resource Manager.

Para establecer la configuración:
  - Asegúrese de que cumple los requisitos.
  - Conéctese a Azure Active Directory (Azure AD) o a Servicios de federación de Active Directory (AD FS). 
  - Registre los proveedores de recursos.
  - Pruebe la conectividad.

## <a name="prerequisites-to-connecting-with-powershell"></a>Requisitos previos para la conexión con PowerShell

Configure estos requisitos previos desde el [kit de desarrollo](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) o desde un cliente externo basado en Windows si está [conectado a través de VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn):

* Instale los [módulos de Azure PowerShell compatibles con Azure Stack Hub](../operator/powershell-install-az-module.md).
* Descargue las [herramientas necesarias para trabajar con Azure Stack Hub](../operator/azure-stack-powershell-download.md).

Asegúrese de reemplazar las siguientes variables de script con los valores de la configuración de Azure Stack Hub:

- **Nombre del inquilino de Azure AD**  
  Nombre del inquilino de Azure AD que se usa para administrar Azure Stack Hub. Por ejemplo, yourdirectory.onmicrosoft.com.
- **Punto de conexión de Azure Resource Manager**  
  En el kit de desarrollo de Azure Stack, este valor se establece en `https://management.local.azurestack.external`. Para obtener este valor para los sistemas integrados de Azure Stack Hub, póngase en contacto con su proveedor de servicios.

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>Conexión a Azure Stack Hub con Azure AD

### <a name="az-modules"></a>[Modules de Az](#tab/az1)

```powershell  
    Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Connect-AzAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```
### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm1)
 
```powershell  
    Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"
    # Set your tenant name
    $AuthEndpoint = (Get-AzureRMEnvironment -Name "AzureStackUser").ActiveDirectoryAuthority.TrimEnd('/')
    $AADTenantName = "<myDirectoryTenantName>.onmicrosoft.com"
    $TenantId = (invoke-restmethod "$($AuthEndpoint)/$($AADTenantName)/.well-known/openid-configuration").issuer.TrimEnd('/').Split('/')[-1]

    # After signing in to your environment, Azure Stack Hub cmdlets
    # can be easily targeted at your Azure Stack Hub instance.
    Add-AzureRMAccount -EnvironmentName "AzureStackUser" -TenantId $TenantId
```

---


## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>Conexión a Azure Stack Hub con AD FS

### <a name="az-modules"></a>[Modules de Az](#tab/az2)

  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance
  Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Connect-AzAccount -EnvironmentName "AzureStackUser"
  ```
### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm2)
 
  ```powershell  
  # Register an Azure Resource Manager environment that targets your Azure Stack Hub instance
  Add-AzureRMEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"

  # Sign in to your environment
  Login-AzureRMAccount -EnvironmentName "AzureStackUser"
  ```

---


## <a name="register-resource-providers"></a>Registro de proveedores de recursos

Los proveedores de recursos no se registran automáticamente para nuevas suscripciones de usuario que no tienen ningún recurso implementado a través del portal. Puede registrar explícitamente un proveedor de recursos ejecutando el script siguiente:

### <a name="az-modules"></a>[Modules de Az](#tab/az3)

```powershell  
foreach($s in (Get-AzSubscription)) {
        Select-AzSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzResourceProvider -ListAvailable | Register-AzResourceProvider
    }
```
### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm3)
 
```powershell  
foreach($s in (Get-AzureRMSubscription)) {
        Select-AzureRMSubscription -SubscriptionId $s.SubscriptionId | Out-Null
        Write-Progress $($s.SubscriptionId + " : " + $s.SubscriptionName)
Get-AzureRMResourceProvider -ListAvailable | Register-AzureRMResourceProvider
    }
```

---


[!Include [AD FS only supports interactive authentication with user identities](../includes/note-powershell-adfs.md)]

## <a name="test-the-connectivity"></a>Prueba de la conectividad

Cuando tenga todo configurado, pruebe la conectividad con PowerShell para crear recursos en Azure Stack Hub. Como prueba, puede crear un grupo de recursos para una aplicación y agregar una máquina virtual. Ejecute el comando siguiente para crear un grupo de recursos denominado "MyResourceGroup":

### <a name="az-modules"></a>[Modules de Az](#tab/az4)
```powershell  
New-AzResourceGroup -Name "MyResourceGroup" -Location "Local"
```

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm4)
 
```powershell  
New-AzureRMResourceGroup -Name "MyResourceGroup" -Location "Local"
```

---


## <a name="next-steps"></a>Pasos siguientes

- [Desarrollo de plantillas para Azure Stack Hub](azure-stack-develop-templates.md)
- [Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
- [Referencia del módulo de PowerShell de Azure Stack Hub](/powershell/azure/azure-stack/overview)
- Si desea configurar PowerShell para el entorno de operadores en la nube, consulte el artículo [Configuración del entorno de PowerShell del operador de Azure Stack Hub](../operator/azure-stack-powershell-configure-admin.md).
