---
title: Cómo crear un rol de registro para Azure Stack
description: Cómo crear un rol personalizado para evitar el uso de un administrador global para el registro.
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
ms.reviewer: rtiberiu
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 50c15403ec4bf41f7513af30d2dca53310d45298
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828246"
---
# <a name="create-a-custom-role-for-azure-stack-registration"></a>Creación de un rol personalizado para el registro de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

> [!WARNING]
> Esto no es una característica de la postura de seguridad. Úsela en escenarios donde quiera que las restricciones eviten cambios accidentales en la suscripción a Azure. Cuando un usuario tiene derechos delegados a este rol personalizado, también tendrá derechos para modificar los permisos y elevar derechos. Solo asigne usuarios en los que confía al rol personalizado.

Durante el registro de Azure Stack, debe iniciar sesión con una cuenta de Azure Active Directory. La cuenta requiere los siguientes permisos de Azure Active Directory y permisos de suscripción a Azure:

* **Permisos de registro de aplicación en el inquilino de Azure Active Directory:** Los administradores tienen permisos de registro de aplicaciones. El permiso para los usuarios es una configuración global para todos los usuarios en el inquilino. Para ver o cambiar esta opción de configuración, vea [cómo crear una aplicación de Azure AD y una entidad de servicio con acceso a los recursos](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions).

    La opción de configuración *Los usuarios pueden registrar aplicaciones* debe establecerse en **Sí** para habilitar una cuenta de usuario para registrar Azure Stack. Si la opción de configuración de registro de la aplicación se establece en **No**, no se puede usar una cuenta de usuario y debe usar una cuenta de administrador global para registrar Azure Stack.

* **Conjunto de permisos suficientes de suscripción a Azure:** Los usuarios que pertenecen al rol de propietario tienen permisos suficientes. Para otras cuentas, puede asignar el conjunto de permisos mediante la asignación de un rol personalizado tal como se describe en las secciones siguientes.

En lugar de usar una cuenta que tenga permisos de propietario en la suscripción de Azure, puede crear un rol personalizado para asignar permisos a una cuenta de usuario menos privilegiada. Esta cuenta se puede utilizar entonces para registrar la instancia de Azure Stack.

## <a name="create-a-custom-role-using-powershell"></a>Creación de un rol personalizado con PowerShell

Para crear un rol personalizado, debe tener el permiso `Microsoft.Authorization/roleDefinitions/write` en todos los `AssignableScopes`, como [Propietario](/azure/role-based-access-control/built-in-roles#owner) o [Administrador de acceso de usuario](/azure/role-based-access-control/built-in-roles#user-access-administrator). Use la siguiente plantilla JSON para simplificar la creación del rol personalizado. La plantilla crea un rol personalizado que permite el acceso de lectura y escritura necesarios para el registro de Azure Stack.

1. Cree un archivo JSON. Por ejemplo: `C:\CustomRoles\registrationrole.json`
2. Agregue el siguiente JSON al archivo. Reemplace `<SubscriptionID>` con la identificación de su suscripción de Azure.

    ```json
    {
      "Name": "Azure Stack registration role",
      "Id": null,
      "IsCustom": true,
      "Description": "Allows access to register Azure Stack",
      "Actions": [
        "Microsoft.Resources/subscriptions/resourceGroups/write",
        "Microsoft.Resources/subscriptions/resourceGroups/read",
        "Microsoft.AzureStack/registrations/*",
        "Microsoft.AzureStack/register/action",
        "Microsoft.Authorization/roleAssignments/read",
        "Microsoft.Authorization/roleAssignments/write",
        "Microsoft.Authorization/roleAssignments/delete",
        "Microsoft.Authorization/permissions/read"
      ],
      "NotActions": [
      ],
      "AssignableScopes": [
        "/subscriptions/<SubscriptionID>"
      ]
    }
    ```

3. En PowerShell, conéctese a Azure para usar Azure Resource Manager. Cuando se le solicite, autentíquese con una cuenta con permisos suficientes como [Propietario](/azure/role-based-access-control/built-in-roles#owner) o [Administrador de acceso de usuario](/azure/role-based-access-control/built-in-roles#user-access-administrator).

    ```azurepowershell
    Connect-AzureRmAccount
    ```

4. Para crear el rol personalizado, utilice **New-AzureRmRoleDefinition** mediante la especificación del archivo de plantilla JSON.

    ``` azurepowershell
    New-AzureRmRoleDefinition -InputFile "C:\CustomRoles\registrationrole.json"
    ```

## <a name="assign-a-user-to-registration-role"></a>Asignación de un usuario al rol de registro

Después de crear el rol personalizado de registro, asigne el rol a la cuenta de usuario que se usará para registrar Azure Stack.

1. Inicie sesión con la cuenta con permisos suficientes en la suscripción a Azure para delegar derechos, como [Propietario](/azure/role-based-access-control/built-in-roles#owner) o [Administrador de acceso de usuario](/azure/role-based-access-control/built-in-roles#user-access-administrator) .
2. En **Suscripciones**, seleccione **Control de acceso (IAM) > Agregar asignación de roles**.
3. En **Rol**, elija el rol personalizado que creó en el *rol de registro de Azure Stack*.
4. Seleccione los usuarios que quiere asignar al rol.
5. Seleccione **Guardar** para asignar los usuarios seleccionados al rol.

    ![Selección de usuarios para asignar al rol](media/azure-stack-registration-role/assign-role.png)

Para saber más sobre los roles personalizados, vea [Administración del acceso mediante RBAC y Azure Portal](/azure/role-based-access-control/role-assignments-portal).

## <a name="next-steps"></a>Pasos siguientes

[Registro de Azure Stack en Azure](azure-stack-registration.md)
