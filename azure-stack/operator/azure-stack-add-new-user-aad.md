---
title: Adición de una nueva cuenta de usuario de Azure Stack Hub en Azure Active Directory
description: Obtenga información sobre cómo crear una cuenta de usuario en Azure Active Directory, de modo que pueda explorar el portal de usuarios.
author: JustinHall
ms.topic: article
ms.date: 05/20/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: 435b2bfdd2de9a232379190a0fe3db0dca642def
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77700568"
---
# <a name="add-a-new-azure-stack-hub-user-account-in-azure-active-directory-azure-ad"></a>Adición de una nueva cuenta de usuario de Azure Stack Hub en Azure Active Directory (Azure AD)

Para poder probar ofertas y planes y crear recursos, necesitará una cuenta de usuario. Puede crear una cuenta de usuario en el inquilino de Azure AD mediante Azure Portal o PowerShell.

## <a name="create-user-account-using-the-azure-portal"></a>Creación de una cuenta de usuario mediante Azure Portal

Debe tener una suscripción de Azure para usar el Portal de Azure.

1. Inicie sesión en [Azure](https://portal.azure.com).
2. En la barra de navegación izquierda, seleccione **Active Directory** y cambie al directorio que quiere usar para Azure Stack Hub o cree uno nuevo.
3. Seleccione **Azure Active Directory** > **Usuarios** > **Nuevo usuario**.

    ![Página Users - All users (Usuarios: Todos los usuarios) con Nuevo usuario resaltado](media/azure-stack-add-new-user-aad/new-user-all-users.png)

4. En la página **Usuario**, rellene la información necesaria.

    ![Página Agregar nuevo usuario, Usuario con información de usuario](media/azure-stack-add-new-user-aad/new-user-user.png)

   - **Nombre (obligatorio)** : Nombre y apellidos del nuevo usuario. Por ejemplo, Mary Parker.
   - **Nombre de usuario (obligatorio)** : Nombre de usuario del nuevo usuario. Por ejemplo, mary@contoso.com.
       La parte de dominio del nombre de usuario debe usar el nombre de dominio predeterminado inicial <_nombre de dominio_>.onmicrosoft.com o un nombre de dominio personalizado, como contoso.com. Para obtener más información sobre cómo crear un nombre de dominio personalizado, consulte [Agregar un nombre de dominio personalizado a Azure AD](/azure/active-directory/fundamentals/add-custom-domain).
   - **Perfil**: Si quiere, puede agregar más información sobre el usuario. También puede agregar información del usuario más adelante. Para obtener más información sobre cómo agregar información de usuario, consulte [Agregar o cambiar la información del perfil de usuario](/azure/active-directory/fundamentals/active-directory-users-profile-azure-portal).
   - **Rol del directorio**: elija **Usuario**.

5. Marque **Mostrar contraseña** y copie la contraseña generada automáticamente proporcionada en el cuadro **Contraseña**. Necesitará esta contraseña para el proceso inicial de inicio de sesión.

6. Seleccione **Crear**.

    El usuario se crea y se agrega al inquilino de Azure AD.

7. Inicie sesión en Microsoft Azure Portal con la nueva cuenta. Cambie la contraseña cuando se le solicite.
8. Inicie sesión en `https://portal.local.azurestack.external` con la nueva cuenta para ver el portal de usuarios.

## <a name="create-a-user-account-using-powershell"></a>Creación de una cuenta de usuario con PowerShell

Si no tiene una suscripción de Azure, no puede usar Azure Portal para agregar una cuenta de usuario de inquilino. En este caso, puede usar el módulo de Azure AD para Windows PowerShell.

> [!NOTE]
> Si usa una cuenta Microsoft para implementar el ASDK, no puede usar Azure AD PowerShell para crear una cuenta de inquilino.

1. Instale la versión de **64-bits** del [Ayudante para el inicio de sesión de Microsoft Online Services para profesionales de TI (RTW)](https://go.microsoft.com/fwlink/p/?LinkId=286152).

2. Instale el Módulo Microsoft Azure AD para Windows PowerShell con estos pasos:

    - Abra un símbolo del sistema con privilegios elevadps de Windows PowerShell (ejecute Windows PowerShell como administrador).
    - Ejecute el comando **Install-Module MSOnline**.
    - Si se le pide que instale el proveedor de NuGet, seleccione **Y** (Sí) y **ENTRAR**.
    - Si se le pide que instale el módulo desde PSGallery, seleccione **Y** (Sí) y **ENTRAR**.

3. Ejecute los siguientes cmdlets:

    ```powershell
    # Provide the Azure AD credential you use to deploy the ASDK.

            $msolcred = get-credential

    # Add a user account "Tenant Admin <username>@<yourdomainname>" with the initial password "<password>".

            connect-msolservice -credential $msolcred
            $user = new-msoluser -DisplayName "Tenant Admin" -UserPrincipalName <username>@<yourdomainname> -Password <password>
            Add-MsolRoleMember -RoleName "Company Administrator" -RoleMemberType User -RoleMemberObjectId $user.ObjectId

    ```

1. Inicie sesión en Azure con la nueva cuenta. Cambie la contraseña cuando se le solicite.
2. Inicie sesión en `https://portal.local.azurestack.external` con la nueva cuenta para ver el portal de usuarios.

## <a name="next-steps"></a>Pasos siguientes

[Adición de usuarios de Azure Stack Hub a AD FS](azure-stack-add-users-adfs.md)
