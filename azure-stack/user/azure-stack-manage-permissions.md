---
title: Administración del acceso a los recursos de Azure Stack con control de acceso basado en rol | Microsoft Docs
description: Obtenga información para administrar los permisos de control de acceso basado en rol (RBAC) como administrador o inquilino de Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: cccac19a-e1bf-4e36-8ac8-2228e8487646
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2019
ms.author: bryanla
ms.reviewer: fiseraci
ms.lastreviewed: 03/11/2019
ms.openlocfilehash: a784da0d16f6ec92a105d9360430f4e8da2817ef
ms.sourcegitcommit: 245a4054a52e54d5989d6148fbbe386e1b2aa49c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/13/2019
ms.locfileid: "70974931"
---
# <a name="manage-access-to-resources-in-azure-stack-with-role-based-access-control"></a>Administración del acceso a los recursos de Azure Stack con control de acceso basado en rol

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Azure Stack admite el control de acceso basado en rol (RBAC), que es el mismo [modelo de seguridad para la administración de acceso](/azure/role-based-access-control/overview) que usa Microsoft Azure. Puede usar RBAC para administrar el acceso de usuarios, grupos o aplicaciones a las suscripciones, los recursos y los servicios.

## <a name="basics-of-access-management"></a>Aspectos básicos de la administración de acceso

El control de acceso basado en rol proporciona control de acceso muy detallado que puede usar para proteger su entorno. Se conceden a los usuarios los permisos exactos que necesitan mediante la asignación de un rol RBAC en un ámbito determinado. El ámbito de la asignación de roles puede ser una suscripción, un grupo de recursos o un único recurso. Consulte el artículo [Control de acceso basado en rol en Azure Portal](/azure/role-based-access-control/overview) para obtener información más detallada sobre la administración del acceso.

> [!NOTE]
> Cuando Azure Stack se implementa mediante Servicios de federación de Active Directory (AD FS) como proveedor de identidades, solo se admiten grupos universales para escenarios de RBAC.

### <a name="built-in-roles"></a>Roles integrados

Azure Stack cuenta con tres roles básicos que puede aplicar a todos los tipos de recurso:

* **Propietario**: puede administrar todo, incluido el acceso a los recursos.
* **Colaborador**: puede administrar todo, excepto el acceso a los recursos.
* **Lector**: puede ver todo, pero no puede realizar cambios.

### <a name="resource-hierarchy-and-inheritance"></a>Jerarquía y herencia de recursos

Azure Stack tiene la siguiente jerarquía de recursos:

* Cada suscripción pertenece a un directorio.
* Cada grupo de recursos pertenece a una suscripción.
* Cada recurso pertenece a un grupo de recursos.

El acceso que se concede en un ámbito principal se hereda en los ámbitos secundarios. Por ejemplo:

* Asigne el rol de **lector** a un grupo de Azure AD en el ámbito de la suscripción. Los miembros de ese grupo pueden ver todos los grupos de recursos y los recursos de la suscripción.
* Asigne el rol **Colaborador** a una aplicación en el ámbito del grupo de recursos. La aplicación puede administrar recursos de todo tipo de ese grupo de recursos, pero no de otros grupos de recursos de la suscripción.

### <a name="assigning-roles"></a>Asignación de roles

Puede asignar más de un rol a un usuario y cada rol puede estar asociado con un ámbito diferente. Por ejemplo:

* Asigne a TestUser-A el rol **Lector** en Subscription-1.
* Asigne a TestUser-A el rol **Propietario** en TestVM-1.

En el artículo sobre [asignaciones de roles](/azure/role-based-access-control/role-assignments-portal) de Azure, se proporciona información detallada sobre cómo ver, asignar y eliminar roles.

## <a name="set-access-permissions-for-a-user"></a>Establecimiento de los permisos de acceso de un usuario

Los siguientes pasos describen cómo configurar permisos para un usuario.

1. Inicie sesión con una cuenta que tenga permisos de propietario en el recurso que desea administrar.
2. En el panel de la izquierda, seleccione **Grupos de recursos**.
3. Elija el nombre del grupo de recursos en el que quiera establecer los permisos.
4. En el panel de navegación del grupo de recursos, elija **Control de acceso (IAM)** .<BR> En la vista **Asignaciones de roles**, se muestran los elementos que tienen acceso al grupo de recursos. Puede filtrar y agrupar los resultados.
5. En la barra de menús **Control de acceso**, elija **Agregar**.
6. En el panel **Agregar permisos**:

   * Elija el rol que quiera asignar de la lista desplegable **Rol**.
   * Elija el recurso que quiera asignar de la lista desplegable **Asignar acceso a**.
   * Seleccione el usuario, el grupo o la aplicación del directorio al que quiere conceder acceso. Puede buscar en el directorio con los nombres para mostrar, direcciones de correo electrónico e identificadores de objeto.

7. Seleccione **Guardar**.

## <a name="next-steps"></a>Pasos siguientes

[Creación de entidades de servicio](../operator/azure-stack-create-service-principals.md)
