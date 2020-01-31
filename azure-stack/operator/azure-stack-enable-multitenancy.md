---
title: Configuración de los servicios multiinquilino en Azure Stack Hub
description: Aprenda a habilitar y deshabilitar varios inquilinos de Azure Active Directory en Azure Stack Hub.
author: ihenkel
ms.topic: article
ms.date: 06/10/2019
ms.author: inhenkel
ms.reviewer: bryanr
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: dd91cfed0dd4f2444371de43e7da8a48b6339069
ms.sourcegitcommit: 959513ec9cbf9d41e757d6ab706939415bd10c38
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/30/2020
ms.locfileid: "76890279"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>Configuración de los servicios multiinquilino en Azure Stack Hub

Puede configurar Azure Stack Hub para admitir usuarios de varios inquilinos de Azure Active Directory (Azure AD) y permitirles usar servicios de Azure Stack Hub. Por ejemplo, tenga en cuenta el siguiente caso:

- Es el administrador de servicios de contoso.onmicrosoft.com, donde está instalado Azure Stack Hub.
- Mary es la administradora de directorios de fabrikam.onmicrosoft.com, donde se encuentran los usuarios invitados.
- La empresa de Mary recibe servicios IaaS y PaaS de la suya y debe permitir que los usuarios del directorio invitado (fabrikam.onmicrosoft.com) inicien sesión y usen los recursos de Azure Stack Hub en contoso.onmicrosoft.com.

En esta guía encontrará los pasos necesarios, en el contexto de este escenario, para configurar los servicios multiinquilino en Azure Stack Hub. En este escenario, usted y Mary deben realizar los pasos para permitir que los usuarios de Fabrikam inicien sesión y consuman servicios de la implementación de Azure Stack Hub en Contoso.

## <a name="enable-multi-tenancy"></a>Habilitación de servicios multiinquilino

Hay algunos requisitos previos que se deben tener en cuenta antes de configurar los servicios multiinquilino en Azure Stack Hub:
  
 - Usted y Mary deben coordinar los procedimientos administrativos tanto en el directorio donde está instalado Azure Stack Hub (Contoso) como en el directorio de invitados (Fabrikam).
 - Asegúrese de que ha [instalado](azure-stack-powershell-install.md) y [configurado](azure-stack-powershell-configure-admin.md) PowerShell para Azure Stack Hub.
 - [Descargue Azure Stack Hub Tools](azure-stack-powershell-download.md) e importe los módulos Connect e Identity:

    ```powershell
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-hub-directory"></a>Configuración del directorio de Azure Stack Hub

En esta sección, configurará Azure Stack Hub para permitir inicios de sesión de los inquilinos del directorio de Azure AD de Fabrikam.

Incorpore el inquilino del directorio invitado (Fabrikam) a Azure Stack Hub y configure Azure Resource Manager para aceptar usuarios y entidades de servicio de dicho inquilino.

El administrador de servicios de contoso.onmicrosoft.com ejecuta los comandos siguientes:

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack Hub directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group.
$location = "local"

# Subscription Name
$SubscriptionName = "Default Provider Subscription"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName `
 -SubscriptionName $SubscriptionName
```

### <a name="configure-guest-directory"></a>Configurar el directorio de invitados

Cuando el operador de Azure Stack Hub haya habilitado el directorio de Fabrikam que se usará con Azure Stack Hub, Mary debe registrar Azure Stack Hub en el inquilino del directorio de Fabrikam.

#### <a name="registering-azure-stack-hub-with-the-guest-directory"></a>Registro de Azure Stack Hub en el directorio invitado

Mary (administradora del directorio de Fabrikam) ejecuta los comandos siguientes en el fabrikam.onmicrosoft.com del directorio invitado:

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory.
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose
```

> [!IMPORTANT]
> Si el administrador de Azure Stack Hub instala nuevos servicios o actualizaciones en el futuro, puede que tenga que volver a ejecutar este script.
>
> Ejecute este script de nuevo en cualquier momento para comprobar el estado de las aplicaciones de Azure Stack Hub en su directorio.
>
> Si ha tenido problemas al crear máquinas virtuales en discos administrados (característica incorporada en la actualización 1808), es que se ha agregado un nuevo **proveedor de recursos de disco** que exige que este script se vuelva a ejecutar.

### <a name="direct-users-to-sign-in"></a>Instruir a los usuarios para iniciar sesión

Ahora que usted y Mary han completado los pasos para incorporar el directorio de Mary, Mary puede instruir a los usuarios de Fabrikam para que inicien sesión. Para iniciar sesión, los usuarios de Fabrikam (es decir, los usuarios con el sufijo fabrikam.onmicrosoft.com) visitan https\://portal.local.azurestack.external.

Mary indica a todas las [entidades de seguridad externas](/azure/role-based-access-control/rbac-and-directory-admin-roles) del directorio de Fabrikam (los usuarios del directorio de Fabrikam sin el sufijo fabrikam.onmicrosoft.com) que inicien sesión con https\://portal.local.azurestack.external/fabrikam.onmicrosoft.com. Si no utilizan esta dirección URL, se envían a su directorio predeterminado (Fabrikam) y reciben un error que indica que su administrador no ha dado su consentimiento.

## <a name="disable-multi-tenancy"></a>Deshabilitación del servicio multiinquilino

Si ya no desea varios inquilinos en Azure Stack Hub, puede deshabilitar el servicio multiinquilino siguiendo estos pasos en orden:

1. Como administrador del directorio invitado (Mary en este escenario), ejecute *Unregister-AzsWithMyDirectoryTenant*. El cmdlet desinstala todas las aplicaciones de Azure Stack Hub del nuevo directorio.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory.
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Como administrador de servicios de Azure Stack Hub (usted en este escenario), ejecute *Unregister-AzSGuestDirectoryTenant*.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack Hub directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
    $ResourceGroupName = "system.local"
    
    Unregister-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
     -DirectoryTenantName $azureStackDirectoryTenant `
     -GuestDirectoryTenantName $guestDirectoryTenantToBeDecommissioned `
     -ResourceGroupName $ResourceGroupName
    ```

    > [!WARNING]
    > Los pasos para deshabilitar el servicio multiinquilino deben realizarse en orden. Si el paso 2 se realiza primero, el paso 1 generará errores.

## <a name="next-steps"></a>Pasos siguientes

- [Administrar proveedores delegados](azure-stack-delegated-provider.md)
- [Conceptos clave de Azure Stack Hub](azure-stack-overview.md)
- [Administración del uso y la facturación de Azure Stack Hub como proveedor de soluciones en la nube](azure-stack-add-manage-billing-as-a-csp.md)
- [Adición de inquilinos a Azure Stack Hub para uso y facturación](azure-stack-csp-howto-register-tenants.md)
