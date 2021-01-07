---
title: Establecimiento de permisos de acceso mediante el control de acceso basado en rol
description: Obtenga información sobre cómo establecer los permisos de acceso mediante el control de acceso basado en rol (RBAC) en Azure Stack Hub.
author: PatAltimore
ms.topic: article
ms.date: 12/23/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: 852aa0283e3bd88a196c4bcf9e56951142bc4d60
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870127"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>Establecimiento de permisos de acceso mediante el control de acceso basado en rol

En Azure Stack Hub, un usuario puede ser un lector, propietario o colaborador de cada una de las instancias de una suscripción, grupo de recursos o servicio. Por ejemplo, el Usuario A puede tener permisos de lector en la Suscripción 1, pero tener permisos de propietario en la Máquina virtual 7.

 - Lector: El usuario puede ver todo, pero no puede hacer cambios.
 - Colaborador: El usuario puede administrar todo, excepto el acceso a los recursos.
 - Propietario: El usuario puede administrar todo, incluido el acceso a los recursos.
 - Personalizado: El usuario tiene acceso limitado y específico a los recursos.

 Para más información sobre la creación de un rol personalizado, consulte [Roles personalizados en los recursos de Azure](/azure/role-based-access-control/custom-roles).

## <a name="set-access-permissions-for-a-user"></a>Establecimiento de los permisos de acceso de un usuario

1. Inicie sesión con una cuenta que tenga permisos de propietario en el recurso que desea administrar.
2. En la hoja del recurso, haga clic en el icono **Acceso** ![El icono de acceso es un contorno de la cabeza y los hombros de dos personas.](media/azure-stack-manage-permissions/image1.png)
3. En la hoja **Usuarios**, haga clic en **Roles**.
4. En la hoja **Roles**, haga clic en **Agregar** para agregar permisos para el usuario.

## <a name="set-access-permissions-for-a-universal-group"></a>Establecimiento de los permisos de acceso de un grupo universal 

> [!Note]
> Aplicable solo a Servicios de federación de Active Directory (AD FS).

1. Inicie sesión con una cuenta que tenga permisos de propietario en el recurso que desea administrar.
2. En la hoja del recurso, haga clic en el icono **Acceso** ![El icono de acceso es un contorno de la cabeza y los hombros de dos personas.](media/azure-stack-manage-permissions/image1.png)
3. En la hoja **Usuarios**, haga clic en **Roles**.
4. En la hoja **Roles**, haga clic en **Agregar** para agregar permisos para el grupo de Active Directory del grupo universal.

## <a name="next-steps"></a>Pasos siguientes

[Adición de un inquilino de Azure Stack Hub](azure-stack-add-new-user-aad.md)
