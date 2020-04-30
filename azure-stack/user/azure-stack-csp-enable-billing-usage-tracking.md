---
title: Administración de una suscripción de Azure Stack Hub por parte de un proveedor de soluciones en la nube
description: Aprenda a dejar que un proveedor de soluciones en la nube (CSP) administre una suscripción de Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 04/24/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: 1d325f8c1b48db33c8607dbbc04c480c00c54de0
ms.sourcegitcommit: e5b587216a137819444680ec619281c90f37bad9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/27/2020
ms.locfileid: "82167133"
---
# <a name="let-your-cloud-solution-provider-manage-your-azure-stack-hub-subscription"></a>Administración de una suscripción de Azure Stack Hub por parte de un proveedor de soluciones en la nube

Si utiliza Azure Stack Hub con un proveedor de soluciones en la nube (CSP), puede elegir administrar su propia suscripción para acceder a los recursos en Azure y Azure Stack Hub. También puede permitir que el proveedor administre la suscripción automáticamente. En este artículo aprenderá a:

* Otorgar al proveedor de servicios acceso a su suscripción.
* Asegúrese de que el proveedor de servicios pueda administrar el servicio.

> [!NOTE]
> Si el CSP no administra su cuenta y usted omite los pasos siguientes, el CSP no podrá administrar automáticamente la suscripción a Azure Stack Hub.

## <a name="manage-your-subscription-with-a-csp"></a>Administración de la suscripción con un CSP

Agregue el CSP como **usuario** a su suscripción.

1. Agregue el CSP como usuario invitado con el rol de **usuario** a su directorio de inquilino. Para obtener ayuda para agregar un usuario, consulte [Adición de nuevos usuarios a Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. El CSP crea automáticamente la suscripción local de Azure Stack Hub. Ya está listo para empezar a usar Azure Stack Hub.

3. El CSP debe crear un recurso en la suscripción para comprobar que también puede administrar sus recursos. Por ejemplo, pueden [crear una máquina virtual Windows con el portal de Azure Stack Hub](azure-stack-quick-windows-portal.md).

## <a name="let-the-csp-manage-your-subscription-using-rbac-rights"></a>Permitir que el CSP administre la suscripción mediante derechos de RBAC

Agregue el CSP como **propietario** a su suscripción.

1. Agregue el CSP como usuario invitado a su directorio de inquilino. Para obtener información sobre cómo agregar un usuario, consulte [Incorporación de nuevos usuarios a Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Agregue el rol de **propietario** al usuario invitado de CSP. Para obtener información sobre la adición de un usuario de CSP a su suscripción, consulte [Uso del control de acceso basado en rol para administrar el acceso a los recursos de la suscripción de Azure](/azure/role-based-access-control/role-assignments-portal). El CSP crea automáticamente la suscripción local de Azure Stack Hub. Ya está listo para empezar a usar Azure Stack Hub.
3. El CSP debe crear un recurso en la suscripción para comprobar que puede administrar sus recursos.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre cómo recuperar la información del uso de recursos de Azure Stack Hub, consulte [Uso y facturación en Azure Stack Hub](../operator/azure-stack-billing-and-chargeback.md).
