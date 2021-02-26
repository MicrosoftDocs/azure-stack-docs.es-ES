---
title: Configuración de los servicios multiinquilino en Azure Stack Hub
description: Aprenda a configurar el multiinquilinato para inquilinos invitados de Azure Active Directory en Azure Stack Hub.
author: BryanLa
ms.topic: how-to
ms.date: 01/26/2021
ms.author: bryanla
ms.reviewer: bryanr
ms.lastreviewed: 01/26/2021
ms.openlocfilehash: 3de6c5db42285f90e1d4ce6c1ebf6736d7ce4863
ms.sourcegitcommit: d542b68b299b73e045f30916afb6018e365e9db6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/09/2021
ms.locfileid: "99975952"
---
# <a name="configure-multi-tenancy-in-azure-stack-hub"></a>Configuración de los servicios multiinquilino en Azure Stack Hub

Puede configurar Azure Stack Hub para admitir inicios de sesión de usuarios que residen en otros directorios de Azure Active Directory (Azure AD) y permitirles usar servicios de Azure Stack Hub. Estos directorios tienen una relación de "invitado" con el entorno de Azure Stack Hub y, por tanto, se consideran inquilinos invitados de Azure AD. Por ejemplo, tenga en cuenta el siguiente caso:

- Supongamos que es el administrador de servicios de contoso.onmicrosoft.com, el inquilino principal de Azure AD que proporciona servicios de administración de identidades y acceso al entorno de Azure Stack Hub.
- Mary es la administradora de directorios de fabrikam.onmicrosoft.com, el inquilino invitado de Azure AD en el que se encuentran los usuarios invitados.
- La empresa de Mary (Fabrikam) usa los servicios de IaaS y PaaS de su empresa. Fabrikam quiere permitir que los usuarios del directorio invitado (fabrikam.onmicrosoft.com) inicien sesión y usen los recursos de Azure Stack Hub protegidos por contoso.onmicrosoft.com.

En esta guía se proporcionan los pasos necesarios, en el contexto de este escenario, para habilitar o deshabilitar el multiinquilinato en Azure Stack Hub para un inquilino de un directorio invitado. Para realizar este proceso, usted y Mary registran y anulan el registro del inquilino del directorio de los invitados, lo que habilita o deshabilita los inicios de sesión de Azure Stack Hub y el consumo de servicios por parte de los usuarios de fabrikam. 

Si es un proveedor de soluciones en la nube (CSP), tiene maneras adicionales de [configurar y administrar una instancia multiinquilino de Azure Stack Hub](azure-stack-add-manage-billing-as-a-csp.md). 

## <a name="prerequisites"></a>Requisitos previos

Antes de registrar o anular el registro de un directorio invitado, usted y Mary deben completar los pasos administrativos para los inquilinos de Azure AD respectivos: el directorio principal de Azure Stack Hub (contoso) y el directorio invitado (Fabrikam):

 - [Instale](powershell-install-az-module.md) y [configure](azure-stack-powershell-configure-admin.md) PowerShell para Azure Stack Hub.
 - [Descargue Azure Stack Hub Tools](azure-stack-powershell-download.md) e importe los módulos Connect e Identity:

    ```powershell
    Import-Module .\Identity\AzureStack.Identity.psm1
    ```

## <a name="register-a-guest-directory"></a>Registro de un directorio invitado

Para registrar un directorio invitado para el multiinquilinato, es necesario configurar el directorio principal de Azure Stack Hub y el directorio invitado.

#### <a name="configure-azure-stack-hub-directory"></a>Configuración del directorio de Azure Stack Hub

Como administrador de servicios de contoso.onmicrosoft.com, primero debe incorporar el inquilino del directorio invitado de Fabrikam a Azure Stack Hub. El siguiente script configurará Azure Resource Manager para aceptar inicios de sesión de usuarios y entidades de servicio del inquilino de fabrikam.onmicrosoft.com:

```powershell  
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as adminmanagement.<region>.<FQDN>.
$adminARMEndpoint = "https://adminmanagement.local.azurestack.external"

## Replace the value below with the Azure Stack Hub directory
$azureStackDirectoryTenant = "contoso.onmicrosoft.com"

## Replace the value below with the guest directory tenant. 
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

#### <a name="configure-guest-directory"></a>Configurar el directorio de invitados

A continuación, Mary (administradora de directorios de Fabrikam) debe registrar Azure Stack Hub con el directorio invitado fabrikam.onmicrosoft.com; para ello, ejecuta el siguiente script:

```powershell
## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as management.<region>.<FQDN>.
$tenantARMEndpoint = "https://management.local.azurestack.external"
    
## Replace the value below with the guest directory tenant.
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

Por último, Mary puede dirigir a los usuarios de Fabrikam que tienen cuentas @fabrikam.onmicrosoft.com para que inicien sesión visitando el [portal de usuarios de Azure Stack Hub](../user/azure-stack-use-portal.md). En los sistemas multinodo, la dirección URL del portal de usuarios tiene el formato `https://management.<region>.<FQDN>`. En el caso de una implementación de ASDK, la dirección URL es `https://portal.local.azurestack.external`.

Mary también debe dirigir a las entidades de seguridad externas (los usuarios del directorio de Fabrikam sin el sufijo fabrikam.onmicrosoft.com) para que inicien sesión mediante `https://<user-portal-url>/fabrikam.onmicrosoft.com`. Si no especifican el inquilino del directorio `/fabrikam.onmicrosoft.com` en la dirección URL, se les envía al directorio predeterminado y reciben un error que indica que el administrador no ha dado su consentimiento.

## <a name="unregister-a-guest-directory"></a>Anulación del registro de un directorio invitado

Si ya no desea permitir inicios de sesión en los servicios de Azure Stack Hub desde un inquilino del directorio invitado, puede anular el registro del directorio. De nuevo, se deben configurar tanto el directorio principal de Azure Stack Hub como el directorio invitado:

1. Como administrador del directorio invitado (Mary en este escenario), ejecute `Unregister-AzsWithMyDirectoryTenant`. El cmdlet desinstala todas las aplicaciones de Azure Stack Hub del nuevo directorio.

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as management.<region>.<FQDN>.
    $tenantARMEndpoint = "https://management.local.azurestack.external"
        
    ## Replace the value below with the guest directory tenant.
    $guestDirectoryTenantName = "fabrikam.onmicrosoft.com"
    
    Unregister-AzsWithMyDirectoryTenant `
     -TenantResourceManagerEndpoint $tenantARMEndpoint `
     -DirectoryTenantName $guestDirectoryTenantName `
     -Verbose 
    ```

2. Como administrador de servicios de Azure Stack Hub (usted en este escenario), ejecute el cmdlet `Unregister-AzSGuestDirectoryTenant`:

    ``` PowerShell
    ## The following Azure Resource Manager endpoint is for the ASDK. If you're in a multinode environment, contact your operator or service provider to get the endpoint, formatted as adminmanagement.<region>.<FQDN>.
    $adminARMEndpoint = "https://adminmanagement.local.azurestack.external"
    
    ## Replace the value below with the Azure Stack Hub directory
    $azureStackDirectoryTenant = "contoso.onmicrosoft.com"
    
    ## Replace the value below with the guest directory tenant. 
    $guestDirectoryTenantToBeDecommissioned = "fabrikam.onmicrosoft.com"
    
    ## Replace the value below with the name of the resource group in which the directory tenant resource was created (resource group must already exist).
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

## <a name="update-azure-ad-tenant-permissions"></a>Actualización de los permisos de inquilino de Azure AD

Esta acción desactivará una alerta en Azure Stack Hub, lo cual indica que un directorio requiere una actualización. Ejecute el siguiente comando desde la carpeta **Azurestack-tools-master/identity**:

```powershell
Import-Module ..\Identity\AzureStack.Identity.psm1

$adminResourceManagerEndpoint = "https://adminmanagement.<region>.<domain>"

# This is the primary tenant Azure Stack Hub is registered to:
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
