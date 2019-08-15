---
title: Suscripción a una oferta en Azure Stack | Microsoft Docs
description: Creación de suscripciones para ofertas en Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: sethm
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: 51b61eae1f2c00a959eded275c13c5c4b8d06e4c
ms.sourcegitcommit: aefcf9c61bd8089a0aaa569af7643e5e15f4947c
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/13/2019
ms.locfileid: "68991801"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Creación de suscripciones para ofertas en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Después de [crear una oferta](azure-stack-create-offer.md), los usuarios necesitan una suscripción a dicha oferta antes de que puedan utilizarla. Hay dos formas en las que los usuarios pueden suscribirse a una oferta:

- Como operador de nube, puede crear una suscripción para un usuario en el portal de administración. Las suscripciones que cree pueden ser para ofertas tanto públicas como privadas.
- Como usuario inquilino, puede suscribirse a una oferta pública a través del portal de usuarios.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Creación de una suscripción como operador de nube

Los operadores de nube pueden usar el portal de administración para crear una suscripción a una oferta para un usuario. Puede crear suscripciones para miembros de su propio inquilino de directorio. Cuando los [servicios multiinquilino](azure-stack-enable-multitenancy.md) están habilitados, también puede crear suscripciones para usuarios de inquilinos de directorio adicionales.

Si no desea que los inquilinos creen sus propias suscripciones, haga las ofertas privadas y, después, cree suscripciones para los inquilinos. Esta estrategia es habitual cuando se integra Azure Stack con sistemas de catálogo de servicios o de facturación externa.

Después de crear una suscripción para un usuario, podrá iniciar sesión en el portal de usuarios y ver que está suscrito a la oferta.  

### <a name="to-create-a-subscription-for-a-user"></a>Para crear una suscripción para un usuario

1. En el portal de administración, vaya a **Suscripciones de usuario**.
2. Seleccione **Agregar**. En **Nueva suscripción de usuario**, escriba la siguiente información:  

   - **Nombre para mostrar**: un nombre descriptivo para identificar la suscripción que aparece como *Nombre de suscripción de usuario*.
   - **Usuario**: especifique un usuario de un inquilino de directorio disponible para esta suscripción. El nombre de usuario se muestra como *Propietario*.  El formato del nombre de usuario depende de la solución de identidad. Por ejemplo:

     - **Azure AD:** `<user1>@<contoso.onmicrosoft.com>`

     - **AD FS:** `<user1>@<azurestack.local>`

   - **Inquilino de directorio**: seleccione el inquilino de directorio al que pertenece la cuenta de usuario. Si no ha habilitado los servicios multiinquilino, solo estará disponible el inquilino de directorio local.

3. Seleccione **Oferta**. En **Ofertas**, elija una **Oferta** para esta suscripción. Dado que crea la suscripción para un usuario, seleccione **Privado** como estado de accesibilidad.

4. Seleccione **Crear** para crear la suscripción. La nueva suscripción aparece en **Suscripción de usuario**. Cuando el usuario inicia sesión en el portal de usuario, puede ver los detalles de la suscripción.

### <a name="to-make-an-add-on-plan-available"></a>Para añadir un plan complementario

Un operador en la nube puede agregar un plan a una suscripción creada anteriormente en cualquier momento:

1. En el portal de administración, seleccione **Todos los servicios** y, después, en la categoría **RECURSOS ADMINISTRATIVOS**, seleccione **Suscripciones de usuario**. Seleccione la suscripción que desea cambiar.

2. Seleccione **Complementos** y, después, seleccione **+Agregar**.  

3. En **Agregar plan**, seleccione el plan que desea agregar como complemento.

## <a name="create-a-subscription-as-a-user"></a>Creación de una suscripción como usuario

Como usuario, puede iniciar sesión en el portal de usuarios para buscar y suscribirse a ofertas públicas y planes complementarios para su inquilino de directorio (organización).

>[!NOTE]
>Cuando el entorno de Azure Stack admite [servicios multiinquilino](azure-stack-enable-multitenancy.md), también puede suscribirse a ofertas de un inquilino de directorio remoto.

### <a name="to-subscribe-to-an-offer"></a>Para suscribirse a una oferta

1. [Inicie sesión](../asdk/asdk-connect.md) en el [portal de usuarios de Azure Stack](https://portal.local.azurestack.external) y seleccione **Obtener una suscripción**.

   ![Obtener una suscripción](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. En **Obtener una suscripción**, escriba el nombre descriptivo de la suscripción en **Nombre para mostrar**. Seleccione **Oferta** y en **Elegir una oferta**, elija una. Seleccione **Crear** para crear la suscripción.

   ![Creación de una oferta](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Después de suscribirse a una oferta, actualice el portal para ver los servicios que forman parte de la nueva suscripción.

4. Para ver la suscripción que ha creado, seleccione **Todos los servicios** y, después, en la categoría **GENERAL** seleccione **Suscripciones**. Seleccione la suscripción para ver sus detalles.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Para suscribirse a un plan complementario

Si la oferta tiene un plan complementario, puede añadirlo a su suscripción en cualquier momento.  

1. En el portal de usuarios, seleccione **todos los servicios**. A continuación, en la categoría **GENERAL** seleccione **Suscripciones** y, después, seleccione la suscripción que desea cambiar. Si hay planes complementarios disponibles, la opción **+ Agregar plan** está activa y hay un icono para **Planes complementarios**.

   Si **+ Agregar plan** no está activo, no hay ningún plan complementario para la oferta asociado a esa suscripción.

1. Seleccione **+ Agregar plan** o el icono **Planes complementarios**. En **Planes complementarios**, seleccione el plan que desea agregar.

## <a name="next-steps"></a>Pasos siguientes

- [Aprovisionamiento de una máquina virtual](../user/azure-stack-create-vm-template.md)
