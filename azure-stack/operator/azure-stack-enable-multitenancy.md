---
title: Configuración de los servicios multiinquilino en Azure Stack Hub
description: Aprenda a habilitar y deshabilitar varios inquilinos de Azure Active Directory en Azure Stack Hub.
author: BryanLa
ms.topic: how-to
ms.date: 10/16/2020
ms.author: bryanla
ms.reviewer: bryanr
ms.lastreviewed: 10/16/2020
ms.openlocfilehash: 923c430291c742069a29806449b45d4fc9cdef07
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94544231"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>Configuración de los servicios multiinquilino en Azure Stack Hub

Puede configurar Azure Stack Hub para admitir usuarios de varios inquilinos de Azure Active Directory (Azure AD) y permitirles usar servicios de Azure Stack Hub. Por ejemplo, tenga en cuenta el siguiente caso:

- Es el administrador de servicios de contoso.onmicrosoft.com, donde está instalado Azure Stack Hub.
- Mary es la administradora de directorios de fabrikam.onmicrosoft.com, donde se encuentran los usuarios invitados.
- La empresa de Mary recibe servicios IaaS y PaaS de la suya y debe permitir que los usuarios del directorio invitado (fabrikam.onmicrosoft.com) inicien sesión y usen los recursos de Azure Stack Hub en contoso.onmicrosoft.com.

En esta guía encontrará los pasos necesarios, en el contexto de este escenario, para configurar los servicios multiinquilino en Azure Stack Hub. En este escenario, usted y Mary deben realizar los pasos para permitir que los usuarios de Fabrikam inicien sesión y consuman servicios de la implementación de Azure Stack Hub en Contoso.

Si es un proveedor de soluciones en la nube (CSP), tiene maneras adicionales de [configurar y administrar una instancia multiinquilino de Azure Stack Hub](azure-stack-add-manage-billing-as-a-csp.md). 

## <a name="enable-multi-tenancy"></a>Habilitación de servicios multiinquilino

Hay algunos requisitos previos que se deben tener en cuenta antes de configurar los servicios multiinquilino en Azure Stack Hub:
  
 - Usted y Mary deben coordinar los procedimientos administrativos tanto en el directorio donde está instalado Azure Stack Hub (Contoso) como en el directorio de invitados (Fabrikam).
 - Asegúrese de que ha [instalado](powershell-install-az-module.md) y [configurado](azure-stack-powershell-configure-admin.md) PowerShell para Azure Stack Hub.
 - [Descargue Azure Stack Hub Tools](azure-stack-powershell-download.md) e importe los módulos Connect e Identity:

    ```powershell
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

#### <a name="register-azure-stack-hub-with-the-guest-directory"></a>Registro de Azure Stack Hub en el directorio invitado

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
> Si el Administrador de Azure Stack Hub instala nuevos servicios o actualizaciones en el futuro, puede que tenga que volver a ejecutar este script.
>
> Ejecute este script de nuevo en cualquier momento para comprobar el estado de las aplicaciones de Azure Stack Hub en su directorio.
>
> Si ha tenido problemas al crear máquinas virtuales en discos administrados (característica incorporada en la actualización 1808), es que se ha agregado un nuevo **proveedor de recursos de disco** que exige que este script se vuelva a ejecutar.

### <a name="direct-users-to-sign-in"></a>Instruir a los usuarios para iniciar sesión

Ahora que usted y Mary han completado los pasos para incorporar el directorio de Mary, Mary puede instruir a los usuarios de Fabrikam para que inicien sesión. Para iniciar sesión, los usuarios de Fabrikam (es decir, los usuarios con el sufijo fabrikam.onmicrosoft.com) visitan https\://portal.local.azurestack.external.

Mary indica a todas las [entidades de seguridad externas](/azure/role-based-access-control/rbac-and-directory-admin-roles) del directorio de Fabrikam (los usuarios del directorio de Fabrikam sin el sufijo fabrikam.onmicrosoft.com) que inicien sesión con https\://portal.local.azurestack.external/fabrikam.onmicrosoft.com. Si no se utiliza esta dirección URL, se envían al directorio predeterminado (Fabrikam) y reciben un error que indica que el administrador no ha dado su consentimiento.

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

2. Como administrador de servicios de Azure Stack Hub (usted en este caso), ejecute *Unregister-AzSGuestDirectoryTenant*.

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

## <a name="retrieve-azure-stack-hub-identity-health-report"></a>Recuperación del informe de estado de identidades de Azure Stack Hub 

Reemplace los marcadores de posición `<region>`, `<domain>` y `<homeDirectoryTenant>` y, a continuación, ejecute el siguiente cmdlet como administrador de Azure Stack Hub.

```powershell

$AdminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"
$DirectoryName = "<homeDirectoryTenant>.onmicrosoft.com"
$healthReport = Get-AzsHealthReport -AdminResourceManagerEndpoint $AdminResourceManagerEndpoint -DirectoryTenantName $DirectoryName
Write-Host "Healthy directories: "
$healthReport.directoryTenants | Where status -EQ 'Healthy' | Select -Property tenantName,tenantId,status | ft


Write-Host "Unhealthy directories: "
$healthReport.directoryTenants | Where status -NE 'Healthy' | Select -Property tenantName,tenantId,status | ft
```

### <a name="update-azure-ad-tenant-permissions"></a>Actualización de los permisos de inquilino de Azure AD

Esta acción desactivará la alerta en Azure Stack Hub, lo cual indica que un directorio requiere una actualización. Ejecute el siguiente comando desde la carpeta **Azurestack-tools-master/identity** :

```powershell
Import-Module ..\Identity\AzureStack.Identity.psm1

$adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"

# This is the primary tenant Azure Stack is registered to:
$homeDirectoryTenantName = "<homeDirectoryTenant>.onmicrosoft.com"

Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
   -DirectoryTenantName $homeDirectoryTenantName -Verbose
```

El script le pide credenciales administrativas en el inquilino de Azure AD y tarda varios minutos en ejecutarse. La alerta se debería desactivar después de ejecutar el cmdlet.

## <a name="next-steps"></a>Pasos siguientes

- [Administrar proveedores delegados](azure-stack-delegated-provider.md)
- [Conceptos clave de Azure Stack Hub](azure-stack-overview.md)
- [Administración del uso y la facturación de Azure Stack Hub como proveedor de soluciones en la nube](azure-stack-add-manage-billing-as-a-csp.md)
- [Adición de inquilinos a Azure Stack Hub para uso y facturación](azure-stack-csp-howto-register-tenants.md)
