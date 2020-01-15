---
title: Establecimiento de permisos de acceso mediante el control de acceso basado en rol | Microsoft Docs
description: Obtenga información sobre cómo establecer los permisos de acceso mediante el control de acceso basado en rol (RBAC) en Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/23/2019
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 12/23/2019
ms.openlocfilehash: ae5b167ca807fc1e28c13c553e13ea89c7e6fe49
ms.sourcegitcommit: dc3d0b77ee77742525fa0cd72d8547d25393022f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/26/2019
ms.locfileid: "75492783"
---
# <a name="set-access-permissions-using-role-based-access-control"></a>Establecimiento de permisos de acceso mediante el control de acceso basado en rol

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En Azure Stack, un usuario puede ser un lector, propietario o colaborador de cada una de las instancias de una suscripción, grupo de recursos o servicio. Por ejemplo, el Usuario A puede tener permisos de lector en la Suscripción 1, pero tener permisos de propietario en la Máquina virtual 7.

 - Lector: El usuario puede ver todo, pero no puede hacer cambios.
 - Colaborador: El usuario puede administrar todo, excepto el acceso a los recursos.
 - Propietario: El usuario puede administrar todo, incluido el acceso a los recursos.
 - Personalizado: El usuario tiene acceso limitado y específico a los recursos.

 Para más información sobre la creación de un rol personalizado, consulte [Roles personalizados en los recursos de Azure](https://docs.microsoft.com/azure/role-based-access-control/custom-roles).

## <a name="set-access-permissions-for-a-user"></a>Establecimiento de los permisos de acceso de un usuario

1. Inicie sesión con una cuenta que tenga permisos de propietario en el recurso que desea administrar.
2. En la hoja para el recurso, haga clic en el icono **Acceso**![](media/azure-stack-manage-permissions/image1.png).
3. En la hoja **Usuarios**, haga clic en **Roles**.
4. En la hoja **Roles**, haga clic en **Agregar** para agregar permisos para el usuario.

## <a name="set-access-permissions-for-a-universal-group"></a>Establecimiento de los permisos de acceso de un grupo universal 

> [!Note]
> Aplicable solo a Servicios de federación de Active Directory (AD FS).

1. Inicie sesión con una cuenta que tenga permisos de propietario en el recurso que desea administrar.
2. En la hoja para el recurso, haga clic en el icono **Acceso**![](media/azure-stack-manage-permissions/image1.png).
3. En la hoja **Usuarios**, haga clic en **Roles**.
4. En la hoja **Roles**, haga clic en **Agregar** para agregar permisos para el grupo de Active Directory del grupo universal.

## <a name="next-steps"></a>Pasos siguientes

[Adición de una nueva cuenta de inquilino de Azure Stack en Azure Active Directory](azure-stack-add-new-user-aad.md)