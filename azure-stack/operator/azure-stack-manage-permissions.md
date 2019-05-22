---
title: Administrar permisos para los recursos por usuario en Azure Stack (administrador de servicios e inquilino) | Microsoft Docs
description: Si es administrador de servicios o inquilino, aprenda a administrar los permisos RBAC.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/14/2019
ms.author: mabrigg
ms.reviewer: thoroet
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 96708a3c88b593f0d423b227d43c356ef38c1515
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985190"
---
# <a name="manage-role-based-access-control"></a>Administrar el control de acceso basado en roles

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En Azure Stack, un usuario puede ser un lector, propietario o colaborador de cada una de las instancias de una suscripción, grupo de recursos o servicio. Por ejemplo, el Usuario A puede tener permisos de lector en la Suscripción 1, pero tener permisos de propietario en la Máquina virtual 7.

 - Lector: El usuario puede ver todo, pero no puede hacer cambios.
 - Colaborador: El usuario puede administrar todo, excepto el acceso a los recursos.
 - Propietario: El usuario puede administrar todo, incluido el acceso a los recursos.

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
[Add a new Azure Stack tenant account in Azure Active Directory](azure-stack-add-new-user-aad.md)

