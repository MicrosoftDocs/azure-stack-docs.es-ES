---
title: Creación de una entidad de servicio de Azure Stack | Microsoft Docs
description: Aprenda a crear una entidad de servicio que puede usarse con el control de acceso basado en roles en Azure Resource Manager para administrar el acceso a los recursos.
services: azure-resource-manager
documentationcenter: na
author: PatAltimore
manager: femila
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/15/2019
ms.author: patricka
ms.reviewer: thoroet
ms.lastreviewed: 12/12/2018
ms.openlocfilehash: 8b3d0ad1f0854f7028e9dfff2d9114df141394a6
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269603"
---
# <a name="create-service-principals-to-give-applications-access-to-azure-stack-resources"></a>Creación de entidades de servicio para otorgar a las aplicaciones acceso a los recursos de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede permitir que una aplicación tenga acceso a los recursos de Azure Stack mediante la creación de un servicio principal que use Azure Resource Manager. Una entidad de servicio le permite delegar permisos específicos mediante el [control de acceso basado en roles](azure-stack-manage-permissions.md).

Como práctica recomendada, debe usar entidades de servicio para las aplicaciones. Las entidades de servicio se prefieren a la ejecución de una aplicación con sus propias credenciales por los siguientes motivos:

* Puede asignar permisos a la entidad de servicio diferentes a los de su cuenta. Normalmente, los permisos de una entidad de servicio están restringidos a exactamente aquello que la aplicación debe hacer.
* No es necesario cambiar las credenciales de la aplicación si los roles o las responsabilidades cambian.
* Puede usar un certificado para automatizar la autenticación al ejecutar un script desatendido.

## <a name="example-scenario"></a>Escenario de ejemplo

Tiene una aplicación de administración de configuración que necesita hacer un inventario de los recursos de Azure mediante Azure Resource Manager. Puede crear una entidad de servicio y asignarle el rol Lector. Este rol da a la aplicación acceso de solo lectura a los recursos de Azure.

## <a name="getting-started"></a>Introducción

Siga los pasos de este artículo como guía para:

* Crear una entidad de servicio para la aplicación.
* Registrar la aplicación y crear una clave de autenticación.
* Asignar la aplicación a un rol.

La manera en que ha configurado Active Directory para Azure Stack determina cómo crear a una entidad de servicio. Elija una de las siguientes opciones:

* Creación de una entidad de servicio para [Azure Active Directory (Azure AD)](azure-stack-create-service-principals.md#create-service-principal-for-azure-ad).
* Creación de una entidad de servicio para [Servicios de federación de Active Directory (AD FS)](azure-stack-create-service-principals.md#create-service-principal-for-ad-fs).

Los pasos para asignar una entidad de servicio a un rol son los mismos para Azure AD y AD FS. Después de crear la entidad de servicio, puede delegar permisos mediante la asignación a un rol.

## <a name="create-service-principal-for-azure-ad"></a>Crear una entidad de servicio para Azure AD

Si Azure Stack usa Azure AD como almacén de identidades, puede crear una entidad de servicio con los mismos pasos que en Azure, mediante Azure Portal.

> [!NOTE]
> Compruebe que tiene los [permisos de Azure AD requeridos](/azure/active-directory/develop/howto-create-service-principal-portal#required-permissions) antes de empezar a crear una entidad de servicio.

### <a name="create-service-principal"></a>Creación de una entidad de servicio

Para crear una entidad de servicio para la aplicación:

1. Inicie sesión en su cuenta de Azure mediante [Azure Portal](https://portal.azure.com).
2. Haga clic en **Azure Active Directory** > **Registros de aplicaciones** > **Nuevo registro**.
3. Proporcione un nombre.
4. Seleccione los **tipos de cuenta admitidos**.
5.  Agregue un identificador URI para la aplicación. Seleccione **Web** para indicar el tipo de aplicación que quiere crear. Después de configurar los valores, seleccione **Registrar**.

Ha creado una entidad de servicio para la aplicación.

### <a name="get-credentials"></a>Obtener credenciales

Al iniciar sesión mediante programación, deberá usar el identificador de la aplicación y una clave de autenticación. Para obtener estos valores:

1. En **App registrations** (Registros de aplicaciones), en Active Directory, seleccione su aplicación.

2. Copie el **id. de aplicación** y almacénelo en el código de la aplicación. Las aplicaciones en el ejemplo de aplicaciones usan el **id. de cliente** al hacer referencia al **id. de la aplicación**.

     ![Identificador de la aplicación para la aplicación](./media/azure-stack-create-service-principals/image12.png)
3. Para generar una clave de autenticación, seleccione **Certificados y secretos**.

4. Seleccione **Nuevo secreto de cliente**.

5. Proporcione una descripción de la clave y una duración, luego, seleccione **Agregar**. 

6. Cuando haya terminado, se muestra el valor del secreto. Escriba este valor porque no podrá recuperar la clave más adelante. Guarde el valor de clave donde la aplicación pueda recuperarlo.

![Advertencia del valor de clave para la clave guardada.](./media/azure-stack-create-service-principals/image15.png)

El paso final es [asignar un rol a la aplicación](azure-stack-create-service-principals.md).

## <a name="create-service-principal-for-ad-fs"></a>Crear una entidad de servicio para AD FS

Si ha implementado Azure Stack mediante AD FS como almacén de identidades, puede usar PowerShell para las siguientes tareas:

* Crear una entidad de servicio.
* Asignar un rol a la entidad de servicio.
* Iniciar sesión mediante la identidad de la entidad de servicio.

Para obtener más información acerca de cómo crear la entidad de servicio, consulte [Crear una entidad de servicio para AD FS](../operator/azure-stack-create-service-principals.md#manage-service-principal-for-ad-fs).

## <a name="assign-the-service-principal-to-a-role"></a>Asignación de un rol a la entidad de servicio

Para acceder a los recursos de la suscripción, debe asignarle a la aplicación un rol. Decida qué rol representa los permisos adecuados para la aplicación. Para obtener más información sobre los roles disponibles, vea [RBAC: Roles integrados](/azure/role-based-access-control/built-in-roles).

> [!NOTE]
> Puede establecer el ámbito de un rol en el nivel de suscripción, grupo de recursos o recurso. Los permisos se heredan en los niveles inferiores del ámbito. Por ejemplo, una aplicación con el rol Lector para un grupo de recursos significa que la aplicación puede leer cualquiera de los recursos del grupo de recursos.

Siga estos pasos como guía para asignar un rol a una entidad de servicio.

1. En el portal de Azure Stack, desplácese hasta el nivel de ámbito al que desea asignar la aplicación. Por ejemplo, para asignar un rol en el ámbito de suscripción, seleccione **Suscripciones**.

2. Seleccione la suscripción que se asignará a la aplicación. En este ejemplo, la suscripción es Visual Studio Enterprise.

     ![Selección de la suscripción de Visual Studio Enterprise para asignación](./media/azure-stack-create-service-principals/image16.png)

3. Seleccione **Control de acceso (IAM)** para la suscripción.

4. Seleccione **Agregar asignación de roles**.

5. Seleccione el rol que quiere asignar a la aplicación.

6. Busque la aplicación y selecciónela.

7. Seleccione **Aceptar** para finalizar la asignación del rol. Verá la aplicación en la lista de usuarios asignados a un rol para ese ámbito.

Ahora que ha creado una entidad de servicio y le ha asignado un rol, la aplicación puede tener acceso a los recursos de Azure Stack.

## <a name="next-steps"></a>Pasos siguientes

[Administración de permisos de usuario](azure-stack-manage-permissions.md)
