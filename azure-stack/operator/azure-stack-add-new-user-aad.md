---
title: Incorporación de una nueva cuenta de inquilino de Azure Stack en Azure Active Directory | Microsoft Docs
description: Después de implementar el Kit de desarrollo de Microsoft Azure Stack, tendrá que crear una cuenta de usuario de al menos un inquilino para poder explorar el portal del inquilino.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: a75d5c88-5b9e-4e9a-a6e3-48bbfa7069a7
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2018
ms.openlocfilehash: 83a927cd0198798b4b1beaf8b84ed0b7ad2a11a0
ms.sourcegitcommit: 914daff43ae0f0fc6673a06dfe2d42d9b4fbab48
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/23/2019
ms.locfileid: "66043107"
---
# <a name="add-a-new-azure-stack-tenant-account-in-azure-active-directory"></a>Adición de una nueva cuenta de inquilino de Azure Stack en Azure Active Directory

Después de [implementar el Kit de desarrollo de Azure Stack](../asdk/asdk-install.md), necesitará una cuenta de usuario inquilino para explorar el portal del inquilino y probar las ofertas y los planes. Puede crear una cuenta de inquilino con [Azure Portal](#create-an-azure-stack-tenant-account-using-the-azure-portal) o con PowerShell.

## <a name="create-an-azure-stack-tenant-account-using-the-azure-portal"></a>Creación de una cuenta de inquilino de Azure Stack con el Portal de Azure+

Debe tener una suscripción de Azure para usar el Portal de Azure.

1. Inicie sesión en [Azure](https://portal.azure.com).
2. En la barra de navegación izquierda, seleccione **Active Directory** y cambie al directorio que quiere usar para Azure Stack o cree uno nuevo.
3. Seleccione **Azure Active Directory** > **Usuarios** > **Nuevo usuario**.

    ![Página Usuarios - Todos los usuarios con Nuevo usuario resaltado](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. En la página **Usuario**, rellene la información necesaria.

    ![Página Agregar nuevo usuario, Usuario con información de usuario](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **Nombre (obligatorio).** Nombre y apellidos del nuevo usuario. Por ejemplo, Mary Parker.
   - **Nombre de usuario (obligatorio).** Nombre de usuario del nuevo usuario. Por ejemplo, mary@contoso.com.
       La parte de dominio del nombre de usuario debe usar el nombre de dominio predeterminado inicial <_nombre de dominio_>.onmicrosoft.com o un nombre de dominio personalizado, como contoso.com. Para más información sobre cómo crear un nombre de dominio personalizado, [Incorporación de un nombre de dominio personalizado a Azure Active Directory]((/azure/active-directory/fundamentals/add-custom-domain).
   - **Perfil.** Si quiere, puede agregar más información sobre el usuario. También puede agregar información del usuario posteriormente. Para más información acerca de cómo agregar información de usuario, consulte [Incorporación o modificación de información del perfil de usuario]((/azure/active-directory/fundamentals/active-directory-users-profile-azure-portal).
   - **Rol del directorio.**  elija **Usuario**.

5. Active **Mostrar contraseña** y copie la contraseña generada automáticamente proporcionada en el cuadro **Contraseña**. Necesitará esta contraseña para el proceso inicial de inicio de sesión.

6. Seleccione **Crear**.

    El usuario se crea y se agrega al inquilino de Azure AD.

7. Inicie sesión en Microsoft Azure Portal con la nueva cuenta. Cambie la contraseña cuando se le solicite.
8. Inicie sesión en `https://portal.local.azurestack.external` con la nueva cuenta para ver el portal del inquilino.

## <a name="create-an-azure-stack-user-account-using-powershell"></a>Crear una cuenta de usuario de Azure Stack con PowerShell

Si no tiene una suscripción de Azure, no puede usar Azure Portal para agregar una cuenta de usuario de inquilino. En este caso, puede usar el módulo Azure Active Directory para Windows PowerShell.

> [!NOTE]
> Si usa una cuenta Microsoft para implementar el Kit de desarrollo de Azure Stack, no se puede usar PowerShell de Azure AD para crear la cuenta de inquilino. 

1. Instale la versión de **64-bits** del [Ayudante para el inicio de sesión de Microsoft Online Services para profesionales de TI (RTW)](https://go.microsoft.com/fwlink/p/?LinkId=286152).

2. Instale el módulo Microsoft Azure Active Directory para Windows PowerShell mediante estos pasos:

    - Abra un símbolo del sistema con privilegios elevados de Windows PowerShell (ejecute Windows PowerShell como administrador).
    - Ejecute el comando **Install-Module MSOnline**.
    - Si se le pide que instale el proveedor de paquetes NuGet, seleccione **Y** (Sí) y **ENTRAR**.
    - Si se le pide que instale el módulo desde PSGallery, seleccione **Y** (Sí) y **ENTRAR**.

3. Ejecute los siguientes cmdlets:

    ```powershell
    # Provide the AAD credential you use to deploy Azure Stack Development Kit

            $msolcred = get-credential

    # Add a tenant account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Inicie sesión en Microsoft Azure con la nueva cuenta. Cambie la contraseña cuando se le solicite.
2. Inicie sesión en `https://portal.local.azurestack.external` con la nueva cuenta para ver el portal del inquilino.

## <a name="next-steps"></a>Pasos siguientes

[Agregar usuarios de Azure Stack a AD FS](azure-stack-add-users-adfs.md)
