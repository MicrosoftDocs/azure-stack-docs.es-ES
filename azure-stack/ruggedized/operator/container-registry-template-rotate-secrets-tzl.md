---
title: Rotación de secretos del registro de contenedor en Azure Stack Hub | Microsoft Docs
titleSuffix: Azure Stack Hub
description: Aprenda a rotar los secretos del registro de contenedor en Azure Stack Hub.
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
ms.date: 1/8/2020
ms.author: mabrigg
ms.reviewer: chasat
ms.lastreviewed: 12/17/2019
ms.openlocfilehash: ed1809d44f54ec65c60c5a1e17ca472ee401b5ba
ms.sourcegitcommit: 50b362d531c2d35a3a935811fee71252971bd5d8
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2020
ms.locfileid: "96941273"
---
# <a name="rotate-container-registry-secrets-in-azure-stack-hub"></a>Rotación de secretos del registro de contenedor en Azure Stack Hub

Los usuarios de Azure Stack Hub pueden rotar los secretos (certificados, nombre de usuario y contraseña) de una implementación de una plantilla del registro de contenedor. Puede ejecutar un script para rellenar los nuevos valores del secreto en Microsoft Azure Key Vault y **volver a implementar** la instancia de la plantilla del registro de contenedor existente. La rotación de secretos en sí no requiere una nueva implementación.

## <a name="prerequisites-for-the-user"></a>Requisitos previos del usuario

 - El usuario debe tener instalados los módulos de PowerShell para Azure Stack Hub. Para más información, consulte [Instalación de PowerShell de Azure Stack](../../operator/azure-stack-powershell-install.md?toc=https%3A%2F%2Fdocs.microsoft.com%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fazure-stack%2Fbreadcrumb%2Ftoc.json).

 - Obtenga los secretos de actualización de la plantilla del registro de contenedor. Puede usar un nuevo certificado SSL o una nueva combinación de nombre de usuario y contraseña para acceder a Docker Registry.

 - Obtenga los scripts que se encuentran en `\registry\scripts` después de descargar el archivo zip del repositorio [msazurestackworkloads/azurestack-gallery](https://github.com/msazurestackworkloads/azurestack-gallery/archive/master.zip) de GitHub.

## <a name="import-new-secrets-into-key-vault"></a>Importación de nuevos secretos en Key Vault

Siga estas instrucciones para configurar los nuevos secretos en Key Vault.

### <a name="set-updated-registry-user-password-for-existing-username"></a>Establecimiento de una contraseña de usuario del registro actualizada para el nombre de usuario existente

1.  Abra un símbolo del sistema de PowerShell con privilegios elevados y ejecute `Import-Module .\\pre-reqs.ps1` desde la carpeta de scripts.

2.  Para actualizar el valor del usuario del registro existente, ejecute el cmdlet:

    ```powershell  
    Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName <username> `
        -RegistryUserPassword <newpassword> `
        -SkipExistCheck $true
    ```
    Por ejemplo, el cmdlet devuelve la siguiente salida:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName admin `
        -RegistryUserPassword password1 `
        -SkipExistCheck $true 
    
    Check if key vault secret name (admin) exists.
    Creating key vault secret name (admin) as it does not exist.
    ```

1.  Para validar que se ha especificado un nuevo valor para este registro, abra un símbolo del sistema de PowerShell con privilegios elevados y ejecute el siguiente cmdlet:

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName newregkv -Name admin -IncludeVersions
    ```
    Por ejemplo, el cmdlet devuelve la siguiente salida:
    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name admin -IncludeVersions
    
    
    Vault Name   : newregkv
    Name         : admin
    Version      : 2a1495372c474cc890c888518f02b19f
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/
                   admin/2a1495372c474cc890c888518f02b19f
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 7:05:56 PM
    Updated      : 12/18/2019 7:05:56 PM
    Content Type : 
    Tags         : 
    
    Vault Name   : newregkv
    Name         : admin
    Version      : 3fd65c1719c74997984648de18a1fa0e
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/
                   admin/3fd65c1719c74997984648de18a1fa0e
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/17/2019 5:05:56 AM
    Updated      : 12/17/2019 5:05:56 AM
    Content Type : user credentials
    Tags         : 
    ```

### <a name="set-new-registry-username-and-password"></a>Establecimiento de un nuevo nombre de usuario y contraseña del registro

1.  Abra un símbolo del sistema de PowerShell con privilegios elevados y `Import-Module .\pre-reqs.ps1` desde la carpeta de scripts.

2.  Para crear un nuevo secreto para el nuevo nombre de usuario y contraseña, abra un símbolo del sistema de PowerShell con privilegios elevados y ejecute el siguiente cmdlet:

    ```powershell  
    Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName <newusername> `
        -RegistryUserPassword <newpassword> 
    ```
    Por ejemplo, el cmdlet devuelve la siguiente salida:
    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-RegistryAccessSecret -KeyVaultName newregkv `
        -RegistryUserName admin1 `
        -RegistryUserPassword password1
    
    Check if key vault secret name (admin1) exists.
    Creating key vault secret name (admin1) as it does not exist. 
    ```

1.  Para comprobar que se ha creado un nuevo secreto, abra un símbolo del sistema de PowerShell con privilegios elevados y ejecute el siguiente cmdlet:

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName \<KeyVaultName> -Name \<username>
    ```

    Por ejemplo, el cmdlet devuelve la siguiente salida:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name admin1
    
    
    Vault Name   : newregkv
    Name         : admin1
    Version      : 2ae9a7239f4044be82ca9d1e9b80e85a
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/admin1/2ae9a7239f4044be82ca9d1e9b80e85a
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 11:28:18 PM
    Updated      : 12/18/2019 11:28:18 PM
    Content Type : user credentials
    Tags         : 
    ```

> [!Important]  
> Si va a crear un nuevo secreto (combinación de nombre de usuario y contraseña), deberá eliminar el secreto de Key Vault antiguo. Si vuelve a implementar la plantilla del registro de contenedor existente sin eliminar el secreto anterior, las combinaciones de nombre de usuario y contraseña antiguas y nuevas serán válidas para iniciar sesión en el registro.

### <a name="update-the-ssl-certificate-for-existing-key-vault-secret"></a>Actualización del certificado SSL para el secreto de Key Vault existente

1. Abra un símbolo del sistema de PowerShell con privilegios elevados y ejecute el siguiente cmdlet:

    ```powershell
    Set-CertificateSecret -KeyVaultName \<keyvaultname> `
     -CertificateSecretName \<originalsecretnameforcertificate> `
    Set-CertificateSecret -KeyVaultName <keyvaultname> `
        -CertificateSecretName <originalsecretnameforcertificate> `
        -CertificateFilePath <pathtonewcertificate> `
        -CertificatePassword <certificatepassword> `
        -SkipExistCheck $true
    ```

    Por ejemplo, el cmdlet devuelve la siguiente salida:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Set-CertificateSecret -KeyVaultName newregkv `
        -CertificateSecretName containersecret `
        -CertificateFilePath C:\crinstall\shanghairegcertnew.pfx `
        -CertificatePassword <certificatepassword> `
        -SkipExistCheck $true
    Check if key vault secret name (containersecret) exists.
    Creating key vault secret name (containersecret) as it does not exist.
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/997da68a-xxxx-xxxx-ad3d-ffeac81b02dc/resourceGroups/newregreg/providers/Microsoft.KeyVault/vaults/newregkv
    PFX KeyVaultSecretUrl        : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/a07ece6b9914408e8f20c516e15b66c9
    PFX Certificate Thumbprint   : 31810AA7FEF1173188691FB3F47208E5389FBA61
    ---------------------------------------------------------------- 
    ```

1.  Usará los valores generados por esta función al volver a implementar la plantilla del registro de contenedor existente.

2.  Para comprobar que se ha creado una nueva versión del secreto existente, abra un símbolo del sistema de PowerShell con privilegios elevados y ejecute el siguiente cmdlet:

    ```powershell  
    Get-AzureKeyVaultSecret -VaultName <KeyVaultName> -Name <secretname>
    ```

    Por ejemplo, el cmdlet devuelve la siguiente salida:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts> Get-AzureKeyVaultSecret -VaultName newregkv -Name containersecret -IncludeVersions
    
    
    Vault Name   : newregkv
    Name         : containersecret
    Version      : a07ece6b9914408e8f20c516e15b66c9
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/a07ece6b9914408e8f20c516e15b66c9
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/18/2019 11:46:28 PM
    Updated      : 12/18/2019 11:46:28 PM
    Content Type : 
    Tags         : 
    
    Vault Name   : newregkv
    Name         : containersecret
    Version      : 0199c7ec1d8d41bb9ddff0f39dca9931
    Id           : https://newregkv.vault.shanghai.azurestack.corp.microsoft.com:443/secrets/containersecret/0199c7ec1d8d41bb9ddff0f39dca9931
    Enabled      : True
    Expires      : 
    Not Before   : 
    Created      : 12/17/2019 5:06:03 AM
    Updated      : 12/17/2019 5:06:03 AM
    Content Type : pfx
    Tags         : 
    ```

### <a name="set-a-new-ssl-certificate-for-the-container-registry-template"></a>Establecimiento de un nuevo certificado SSL para la plantilla del registro de contenedor

1.  Abra un símbolo del sistema de PowerShell con privilegios elevados y ejecute el siguiente cmdlet:

    ```powershell  
    Set-CertificateSecret -KeyVaultName <keyvaultname> `
        -CertificateSecretName <newsecretnameforcertificate> `
        -CertificateFilePath <pathtonewcertificate> `
        -CertificatePassword <certificatepassword>
    ```

    Por ejemplo, el cmdlet devuelve la siguiente salida:

    ```powershell  
    PS C:\azurestack-gallery-master\registry\Scripts>    Set-CertificateSecret -KeyVaultName newregkv `
        -CertificateSecretName containersecret121719 `
        -CertificateFilePath C:\crinstall\shanghairegcertnew.pfx `
        -CertificatePassword <certificatepassword> 
    Check if key vault secret name (containersecret121719) exists.
    Creating key vault secret name (containersecret121719) as it does not exist.
    ----------------------------------------------------------------
    PFX KeyVaultResourceId       : /subscriptions/997da68a-xxxx-xxxx-ad3d-ffeac81b02dc/resourc
    eGroups/newregreg/providers/Microsoft.KeyVault/vaults/newregkv
    PFX KeyVaultSecretUrl        : https://newregkv.vault.shanghai.azurestack.corp.microsoft.c
    om:443/secrets/containersecret121719/bb2cfe4df7bc4fbe854a00799afa8566
    PFX Certificate Thumbprint   : 31810AA7FEF1173188691FB3F47208E5389FBA61 
    ```

## <a name="redeploy-existing-container-registry-template"></a>Volver a implementar la plantilla del registro de contenedor existente

1. Abra el portal de usuarios de Azure Stack Hub.

2.  Vaya al grupo de recursos en el que está implementada la plantilla de máquina virtual del registro de contenedor.

    ![Plantilla de registro de contenedor](./media/container-registry-template-rotating-secrets-tzl/image1.png)

3. Seleccione las implementaciones en **Implementaciones**.

    ![Plantilla de registro de contenedor](./media/container-registry-template-rotating-secrets-tzl/image2.png)

4.  Si es la primera rotación de los secretos, seleccione la implementación original. Si no es la primera rotación, seleccione la implementación más reciente y, a continuación, seleccione **Volver a implementar**.

    ![Plantilla de registro de contenedor](./media/container-registry-template-rotating-secrets-tzl/image3.png)

5.  En **Implementar plantilla de solución**, seleccione **Usar grupo de recursos existente** y seleccione el grupo de recursos que se usó para implementar originalmente la plantilla del registro de contenedor. Para que una reimplementación se realice correctamente, debe usar el mismo grupo de recursos.

    ![Plantilla de registro de contenedor](./media/container-registry-template-rotating-secrets-tzl/image4.png)

6.  En **Parámetros** compruebe que los parámetros coinciden con la implementación original. Será necesario agregar el identificador de cliente y el secreto de la entidad de servicio.

    - Si va a rotar únicamente el nombre de usuario y la contraseña del servicio de registro, solo tiene que agregar los parámetros de la entidad de servicio.

    - Si va a rotar el certificado, deberá especificar los nuevos valores de PFXKeyVaultSecretURL y PFXThumbprint que se generaron al establecer los nuevos secretos.

    ![Plantilla de registro de contenedor](./media/container-registry-template-rotating-secrets-tzl/image5.png)

7.  Seleccione **Aceptar** y, después, **Crear**. La reimplementación continuará. La funcionalidad del registro seguirá funcionando durante la reimplementación.

    - Si va a rotar el nombre de usuario y la contraseña, tendrá que volver a autenticarse en el registro una vez completada la reimplementación.

    - Si va a rotar el certificado, no debería experimentar ninguna pérdida de acceso al registro. Se supone que está utilizando un certificado de un proveedor de certificados de confianza. Si usa un certificado privado, este certificado deberá instalarse en los clientes para evitar la pérdida de acceso.

## <a name="next-steps"></a>Pasos siguientes

[Información general de Azure Stack Marketplace](../../operator/azure-stack-marketplace.md)
