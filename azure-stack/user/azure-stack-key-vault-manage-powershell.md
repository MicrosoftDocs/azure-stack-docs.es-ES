---
title: Administración de Key Vault en Azure Stack mediante PowerShell | Microsoft Docs
description: Aprenda a administrar Key Vault en Azure Stack mediante PowerShell
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 22B62A3B-B5A9-4B8C-81C9-DA461838FAE5
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/09/2019
ms.author: sethm
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 613fec37e1677698e72ff09d3ffdc35502a57de5
ms.sourcegitcommit: c755c7eac0f871960f9290591421cf5990b9e734
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/09/2019
ms.locfileid: "65506091"
---
# <a name="manage-key-vault-in-azure-stack-using-powershell"></a>Administrar Key Vault en Azure Stack mediante PowerShell

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede administrar Key Vault en Azure Stack mediante PowerShell. Aprenda a usar los cmdlets de PowerShell en Key Vault para:

* Cree un almacén de claves.
* Almacenar y administrar claves criptográficas y secretos.
* Autorizar usuarios o aplicaciones para invocar operaciones en el almacén.

>[!NOTE]
>Los cmdlets de PowerShell para Key Vault que se describen en este artículo se ofrecen como parte del SDK de Azure PowerShell.

## <a name="prerequisites"></a>Requisitos previos

* Debe suscribirse a una oferta que incluya el servicio Azure Key Vault.
* [Instale PowerShell para Azure Stack](../operator/azure-stack-powershell-install.md).
* [Configure el entorno de PowerShell del usuario de Azure Stack](azure-stack-powershell-configure-user.md).

## <a name="enable-your-tenant-subscription-for-key-vault-operations"></a>Habilitación de la suscripción de inquilino para operaciones de Key Vault

Para poder emitir cualquier operación en un almacén de claves, debe asegurarse de que su suscripción de inquilino esté habilitada para operaciones de almacén. Para comprobar si están habilitadas las operaciones de almacén, ejecute el siguiente comando:

```powershell  
Get-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault | ft -Autosize
```

Si la suscripción está habilitada para operaciones de almacén, la salida muestra el valor **Registrado** para **RegistrationState** para todos los tipos de recursos de un almacén de claves.

![Estado de registro de almacén de claves](media/azure-stack-key-vault-manage-powershell/image1.png)

Si las operaciones de almacén no están habilitadas, emita el comando siguiente para registrar el servicio Key Vault en su suscripción:

```powershell
Register-AzureRmResourceProvider -ProviderNamespace Microsoft.KeyVault
```

Si el registro se realiza correctamente, se devuelve la siguiente salida:

![Register ](media/azure-stack-key-vault-manage-powershell/image2.png)

Al invocar los comandos de Key Vault, podría recibir un error, como "La suscripción no está registrada para usar el espacio de nombres Microsoft.KeyVault". Si se produce un error, confirme que tiene habilitado el proveedor de recursos de Key Vault siguiendo las instrucciones anteriores.

## <a name="create-a-key-vault"></a>Creación de un Almacén de claves

Antes de crear un almacén de claves, cree un grupo de recursos para que todos los recursos relacionados con dicho almacén de claves se encuentren en un grupo de recursos. Utilice el comando siguiente para crear un nuevo grupo de recursos:

```powershell
New-AzureRmResourceGroup -Name "VaultRG" -Location local -verbose -Force
```

![Nuevo grupo de recursos](media/azure-stack-key-vault-manage-powershell/image3.png)

Ahora, utilice el comando **New-AzureRMKeyVault** para crear un almacén de claves en el grupo de recursos que creó anteriormente. Este comando lee tres parámetros obligatorios: el nombre del grupo de recursos, el nombre del almacén de claves y la ubicación geográfica.

Ejecute el siguiente comando para crear un almacén de claves:

```powershell
New-AzureRmKeyVault -VaultName "Vault01" -ResourceGroupName "VaultRG" -Location local -verbose
```

![Almacén de claves nuevo](media/azure-stack-key-vault-manage-powershell/image4.png)

La salida de este comando muestra las propiedades del almacén de claves que ha creado. Cuando una aplicación accede a este almacén, debe utilizar la propiedad **Vault URI** (URI de almacén) que es `https://vault01.vault.local.azurestack.external` en este ejemplo.

### <a name="active-directory-federation-services-ad-fs-deployment"></a>Implementación de Servicios de federación de Active Directory (AD FS)

En una implementación de AD FS, podría obtener esta advertencia: "Access policy is not set. No user or application has access permission to use this vault" (La directiva de acceso no está definida. Ningún usuario ni ninguna aplicación tienen permiso de acceso para utilizar este almacén). Para resolver este problema, establezca una directiva de acceso para el almacén mediante el comando [Set-AzureRmKeyVaultAccessPolicy](#authorize-an-application-to-use-a-key-or-secret):

```powershell
# Obtain the security identifier(SID) of the active directory user
$adUser = Get-ADUser -Filter "Name -eq '{Active directory user name}'"
$objectSID = $adUser.SID.Value

# Set the key vault access policy
Set-AzureRmKeyVaultAccessPolicy -VaultName "{key vault name}" -ResourceGroupName "{resource group name}" -ObjectId "{object SID}" -PermissionsToKeys {permissionsToKeys} -PermissionsToSecrets {permissionsToSecrets} -BypassObjectIdValidation
```

## <a name="manage-keys-and-secrets"></a>Administración de claves y secretos

Después de crear un almacén, realice estos pasos para crear y administrar claves y secretos en el almacén.

### <a name="create-a-key"></a>Crear una clave

Use el comando **Add-AzureKeyVaultKey** para crear o importar una clave protegida mediante software en un almacén de claves:

```powershell
Add-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01" -verbose -Destination Software
```

El parámetro **Destination** (Destino) se utiliza para especificar que la clave está protegida mediante software. Cuando la clave se crea correctamente, el comando da como salida los detalles de la clave creada.

![Nueva clave](media/azure-stack-key-vault-manage-powershell/image5.png)

Ahora puede hacer referencia a la clave creada utilizando su URI. Si crea o importa una clave con el mismo nombre que una clave existente, la clave original se actualiza con los valores que se especifican en la nueva clave. Puede acceder a la versión anterior utilizando el URI específico de la versión de la clave. Por ejemplo: 

* Use `https://vault10.vault.local.azurestack.external:443/keys/key01` para obtener siempre la versión actual.
* Use `https://vault010.vault.local.azurestack.external:443/keys/key01/d0b36ee2e3d14e9f967b8b6b1d38938a` para obtener esta versión específica.

### <a name="get-a-key"></a>Obtener una clave

Use el comando **Get-AzureKeyVaultKey** para leer una clave y sus detalles:

```powershell
Get-AzureKeyVaultKey -VaultName "Vault01" -Name "Key01"
```

### <a name="create-a-secret"></a>Crear un secreto

Use el comando **Set-AzureKeyVaultSecret** para crear o actualizar un secreto en un almacén. Si aún no existe, se crea un secreto. Si ya existe, se crea una nueva versión del secreto:

```powershell
$secretvalue = ConvertTo-SecureString "User@123" -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01" -SecretValue $secretvalue
```

![Crear un secreto](media/azure-stack-key-vault-manage-powershell/image6.png)

### <a name="get-a-secret"></a>Obtener un secreto

Use el comando **Get-AzureKeyVaultSecret** para leer un secreto en un almacén de claves. Este comando puede devolver todas las versiones de un secreto o algunas versiones concretas:

```powershell
Get-AzureKeyVaultSecret -VaultName "Vault01" -Name "Secret01"
```

Después de crear claves y secretos, puede autorizar aplicaciones externas para que los usen.

## <a name="authorize-an-application-to-use-a-key-or-secret"></a>Autorización de una aplicación para que use una clave o un secreto

Para autorizar una aplicación a acceder a una clave o a un secreto del almacén de claves, use el comando **Set-AzureRmKeyVaultAccessPolicy**.

En el siguiente ejemplo, el nombre del almacén es *ContosoKeyVault* y la aplicación que desea autorizar tiene el identificador de cliente *8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed*. Ejecute el comando siguiente para autorizar la aplicación. Si lo desea, puede especificar el parámetro **PermissionsToKeys** para establecer permisos para un usuario, una aplicación o un grupo de seguridad.

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed -PermissionsToKeys decrypt,sign
```

Si desea autorizar a esa misma aplicación a leer los secretos del almacén, ejecute el siguiente cmdlet:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -ServicePrincipalName 8f8c4bbd-485b-45fd-98f7-ec6300 -PermissionsToKeys Get
```

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de una máquina virtual con una contraseña almacenada en Key Vault](azure-stack-key-vault-deploy-vm-with-secret.md)
* [Implementación de una máquina virtual con un certificado almacenado en Key Vault](azure-stack-key-vault-push-secret-into-vm.md)
