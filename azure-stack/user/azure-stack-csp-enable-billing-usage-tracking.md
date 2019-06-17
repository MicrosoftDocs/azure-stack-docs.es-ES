---
title: Administración de una suscripción de Azure Stack por parte de un proveedor de servicios en la nube | Microsoft Docs
description: Aprenda a dejar que sea su proveedor de servicios en la nube (CSP) quien administre la suscripción de Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/20/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 05/20/2019
ms.openlocfilehash: 0dc162dd1d4021323f1bf80ad4f89fc721e575a9
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691976"
---
# <a name="let-your-cloud-service-provider-manage-your-azure-stack-subscription"></a>Administración de una suscripción de Azure Stack por parte de un proveedor de servicios en la nube

*Se aplica a: Sistemas integrados de Azure Stack*

Si utiliza Azure Stack con un proveedor de servicios en la nube (CSP), puede administrar su propia suscripción para acceder a los recursos en Azure y Azure Stack. También puede permitir que el proveedor administre la suscripción automáticamente. Este artículo muestra cómo:

* Otorgar al proveedor de servicios acceso a su suscripción.
* Asegúrese de que el proveedor de servicios pueda administrar el servicio.

> [!NOTE]
> Si el CSP no administra su cuenta y no se realizan los siguientes pasos, el CSP no podrá administrar su suscripción de Azure Stack.

## <a name="manage-your-subscription-with-a-cloud-service-provider"></a>Administración de la suscripción con un proveedor de servicios en la nube

Agregue el CSP como **usuario** a su suscripción.

1. Agregue el CSP como usuario invitado con el rol de **usuario** a su directorio de inquilino. Para obtener ayuda para agregar un usuario, consulte [Adición de nuevos usuarios a Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. El CSP crea automáticamente la suscripción local de Azure Stack. Está listo para empezar a usar Azure Stack.
3. El CSP debe crear un recurso en la suscripción para comprobar que también puede administrar sus recursos. Por ejemplo, pueden [crear una máquina virtual Windows con el portal de Azure Stack](azure-stack-quick-windows-portal.md).

## <a name="let-the-cloud-service-provider-manage-your-subscription-using-rbac-rights"></a>Permitir que el proveedor de servicios en la nube administre la suscripción mediante derechos de RBAC

Agregue el CSP como **propietario** a su suscripción.

1. Agregue el CSP como usuario invitado a su directorio de inquilino. Para obtener ayuda para agregar un usuario, consulte [Adición de nuevos usuarios a Azure Active Directory](/azure/active-directory/add-users-azure-active-directory).

2. Agregue el rol de **propietario** al usuario invitado de CSP. Para obtener ayuda para agregar un usuario de CSP a su suscripción, consulte [Uso del control de acceso basado en rol para administrar el acceso a los recursos de la suscripción de Azure](/azure/role-based-access-control/role-assignments-portal). El CSP crea automáticamente la suscripción local de Azure Stack. Está listo para empezar a usar Azure Stack.
3. El CSP debe crear un recurso en la suscripción para comprobar que puede administrar sus recursos.

## <a name="next-steps"></a>Pasos siguientes

* Para más información sobre cómo recuperar la información de utilización de recursos de Azure Stack, consulte [Uso y facturación en Azure Stack](../operator/azure-stack-billing-and-chargeback.md).
