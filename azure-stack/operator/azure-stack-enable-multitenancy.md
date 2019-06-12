---
title: Servicio multiinquilino en Azure Stack
description: Aprenda cómo admitir varios directorios de Azure Active Directory en Azure Stack
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: bryanr
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 8547c1aea70d7b72538b5a681e7c8dd4b4d10a02
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828307"
---
# <a name="multi-tenancy-in-azure-stack"></a>Servicio multiinquilino en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede configurar Azure Stack para admitir usuarios de varios inquilinos de Azure Active Directory (Azure AD) para usar los servicios en Azure Stack. Por ejemplo, tenga en cuenta el siguiente caso:

- Usted es el administrador de servicios de contoso.onmicrosoft.com, donde está instalado Azure Stack.
- Mary es la administradora de directorios de fabrikam.onmicrosoft.com, donde se encuentran los usuarios invitados.
- La empresa de Mary recibe servicios IaaS y PaaS de su empresa y debe permitir que los usuarios del directorio de invitados (fabrikam.onmicrosoft.com) inicien sesión y usen los recursos de Azure Stack en contoso.onmicrosoft.com.

En esta guía encontrará los pasos necesarios, en el contexto de este escenario, para configurar los servicios multiinquilino en Azure Stack. En este escenario, usted y Mary deben completar los pasos para permitir que los usuarios de Fabrikam inicien sesión y consuman servicios de la implementación de Azure Stack en Contoso.  

## <a name="enable-multi-tenancy"></a>Habilitación de servicios multiinquilino

Hay algunos requisitos previos que debe tener en cuenta antes de configurar los servicios multiinquilino en Azure Stack:
  
 - Usted y Mary deben coordinar los procedimientos administrativos tanto en el directorio donde está instalado Azure Stack (Contoso) como en el directorio de invitados (Fabrikam).  
 - Asegúrese de que ha [instalado](azure-stack-powershell-install.md) y [configurado](azure-stack-powershell-configure-admin.md) PowerShell para Azure Stack.
 - [Descargue Azure Stack Tools](azure-stack-powershell-download.md) e importe los módulos Connect e Identity:

    ```powershell  
    Import-Module .\Connect\AzureStack.Connect.psm1
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

### <a name="configure-azure-stack-directory"></a>Configurar el directorio de Azure Stack

En esta sección, configurará Azure Stack para permitir inicios de sesión de inquilinos del directorio de Azure AD de Fabrikam.

Incorpore el inquilino de directorio invitado (Fabrikam) a Azure Stack y configure Azure Resource Manager para aceptar usuarios y entidades de servicio de dicho inquilino.

El administrador de servicios de contoso.onmicrosoft.com ejecuta los comandos siguientes.

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantToBeOnboarded = "fabrikam.onmicrosoft.com"

## Replace the value below with the name of the resource group in which the directory tenant registration resource should be created (resource group must already exist).
$ResourceGroupName = "system.local"

## Replace the value below with the region location of the resource group. 
$location = "local"

Register-AzSGuestDirectoryTenant -AdminResourceManagerEndpoint $adminARMEndpoint `
 -DirectoryTenantName $azureStackDirectoryTenant `
 -GuestDirectoryTenantName $guestDirectoryTenantToBeOnboarded `
 -Location $location `
 -ResourceGroupName $ResourceGroupName
```

### <a name="configure-guest-directory"></a>Configurar el directorio de invitados

Una vez que el administrador u operador de Azure Stack ha habilitado el directorio de Fabrikam para su uso con Azure Stack, Mary debe registrar Azure Stack con el inquilino del directorio de Fabrikam.

#### <a name="registering-azure-stack-with-the-guest-directory"></a>Registrar Azure Stack en el directorio de invitados

Mary, la administradora del directorio de Fabrikam, ejecuta los comandos siguientes en el fabrikam.onmicrosoft.com del directorio invitado.

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest tenant directory. 
$guestDirectoryTenantName = "fabrikam.onmicrosoft.com"

Register-AzSWithMyDirectoryTenant `
 -TenantResourceManagerEndpoint $tenantARMEndpoint `
 -DirectoryTenantName $guestDirectoryTenantName `
 -Verbose 
```

> [!IMPORTANT]
> Si el Administrador de Azure Stack instala nuevos servicios o las actualizaciones en el futuro, puede que tenga que volver a ejecutar este script.
>
> Ejecute este script de nuevo en cualquier momento para comprobar el estado de las aplicaciones de Azure Stack en su directorio.
>
> Si ha tenido problemas al crear máquinas virtuales en discos administrados (característica incorporada en la actualización 1808), es que se ha agregado un nuevo **proveedor de recursos de disco** que exige que este script se vuelva a ejecutar.

### <a name="direct-users-to-sign-in"></a>Instruir a los usuarios para iniciar sesión

Ahora que usted y Mary han completado los pasos para incorporar el directorio de Mary, Mary puede instruir a los usuarios de Fabrikam para que inicien sesión.  Para iniciar sesión, los usuarios de Fabrikam (es decir, los usuarios con el sufijo fabrikam.onmicrosoft.com) van a https://portal.local.azurestack.external.  

Mary instruirá a todas las [entidades de seguridad externas](/azure/role-based-access-control/rbac-and-directory-admin-roles) en el directorio de Fabrikam (es decir, los usuarios en el directorio de Fabrikam sin el sufijo fabrikam.onmicrosoft.com) para que inicien sesión con https://portal.local.azurestack.external/fabrikam.onmicrosoft.com.  Si no utilizan esta dirección URL, se envían a su directorio predeterminado (Fabrikam) y reciben un error que indica que su administrador no ha dado su consentimiento.

## <a name="disable-multi-tenancy"></a>Deshabilitación del servicio multiinquilino

Si ya no desea varios inquilinos en Azure Stack, puede deshabilitar el servicio multiinquilino siguiendo estos pasos en orden:

1. Como administrador del directorio invitado (Mary en este escenario), ejecute *Unregister-AzsWithMyDirectoryTenant*. El cmdlet desinstala todas las aplicaciones de Azure Stack del nuevo directorio.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest tenant directory. 
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Como administrador de servicios de Azure Stack (usted en este escenario), ejecute *Unregister-AzSGuestDirectoryTenant*. 

    ``` PowerShell  
    ## The following Azure Resource Manager endpoint is for the ASDK. If you are in a multinode environment, contact your operator or service provider to get the endpoint.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack directory
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
- [Conceptos clave de Azure Stack](azure-stack-overview.md)
- [Administración del uso y la facturación de Azure Stack como proveedor de servicios en la nube](azure-stack-add-manage-billing-as-a-csp.md)
- [Adición de inquilinos para uso y facturación en Azure Stack](azure-stack-csp-howto-register-tenants.md)
