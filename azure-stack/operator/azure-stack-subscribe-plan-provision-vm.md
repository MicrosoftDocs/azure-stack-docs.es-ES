---
title: Creación de suscripciones para ofertas en Azure Stack Hub.
titleSuffix: Azure Stack
description: Aprenda a crear suscripciones para ofertas en Azure Stack Hub.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
ms.service: azure-stack
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: bryanla
ms.lastreviewed: 05/10/2019
ms.openlocfilehash: 357b44e5a9860f8626c7b17e215ee773e4e4ebca
ms.sourcegitcommit: c4368652f0dd68c432aa1dabddbabf161a4a6399
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/13/2020
ms.locfileid: "75914790"
---
# <a name="create-subscriptions-to-offers-in-azure-stack-hub"></a>Creación de suscripciones para ofertas en Azure Stack Hub

Después de [crear una oferta](azure-stack-create-offer.md), los usuarios necesitan una suscripción a dicha oferta antes de que puedan utilizarla. Hay dos formas en las que los usuarios pueden suscribirse a una oferta:

- Como operador de nube, puede crear una suscripción para un usuario en el portal de administración. Las suscripciones que cree pueden ser para ofertas tanto públicas como privadas.
- Como usuario inquilino, puede suscribirse a una oferta pública a través del portal de usuarios.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Creación de una suscripción como operador de nube

Los operadores de nube usan el portal del administrador para crear una suscripción a una oferta para un usuario. Se pueden crear suscripciones para los miembros de su propio inquilino de directorio. Cuando los [servicios multiinquilino](azure-stack-enable-multitenancy.md) están habilitados, también puede crear suscripciones para usuarios de inquilinos de directorio adicionales.

Si no desea que los inquilinos creen sus propias suscripciones, haga ofertas privadas y, después, cree suscripciones para los inquilinos. Esta estrategia es habitual cuando se integra Azure Stack Hub con sistemas de catálogo de servicios o de facturación externa.

Después de crear una suscripción para un usuario, podrá iniciar sesión en el portal de usuarios y ver que está suscrito a la oferta.  

### <a name="to-create-a-subscription-for-a-user"></a>Para crear una suscripción para un usuario

1. En el portal del administrador, vaya a **User subscriptions** (Suscripciones de usuario).
2. Seleccione **Agregar**. En **Nueva suscripción de usuario**, escriba la siguiente información:  

   - **Nombre para mostrar**: un nombre descriptivo para identificar la suscripción que aparece como *Nombre de suscripción de usuario*.
   - **Usuario**: especifique un usuario de un inquilino de directorio disponible para esta suscripción. El nombre de usuario se muestra como *Propietario*.  El formato del nombre de usuario depende de la solución de identidad. Por ejemplo:

     - **Azure AD:** `<user1>@<contoso.onmicrosoft.com>`

     - **AD FS:** `<user1>@<azurestack.local>`

   - **Inquilino de directorio**: seleccione el inquilino de directorio al que pertenece la cuenta de usuario. Si no ha habilitado los servicios multiinquilino, solo estará disponible el inquilino de directorio local.

3. Seleccione **Oferta**. En **Ofertas**, elija una **Oferta** para esta suscripción. Dado que crea la suscripción para un usuario, seleccione **Privado** como estado de accesibilidad.

4. Seleccione **Crear** para crear la suscripción. La nueva suscripción aparece en **Suscripción de usuario**. Cuando el usuario inicia sesión en el portal de usuario, puede ver los detalles de la suscripción.

### <a name="to-make-an-add-on-plan-available"></a>Para añadir un plan complementario

Un operador en la nube puede agregar un plan a una suscripción creada anteriormente en cualquier momento:

1. En el portal del administrador, seleccione **All Services** y, después, en la categoría **ADMINISTRATIVE RESOURCES**, seleccione **User subscriptions** (Todos los servicios > Recursos administrativos > Suscripciones de usuario). Seleccione la suscripción que desea cambiar.

2. Seleccione **Complementos** y, después, seleccione **+Agregar**.  

3. En **Agregar plan**, seleccione el plan que desea agregar como complemento.

## <a name="create-a-subscription-as-a-user"></a>Creación de una suscripción como usuario

Como usuario, puede iniciar sesión en el portal de usuarios para buscar y suscribirse a ofertas públicas y planes complementarios para su inquilino de directorio (organización).

>[!NOTE]
>Cuando el entorno de Azure Stack Hub admite [servicios multiinquilino](azure-stack-enable-multitenancy.md), también puede suscribirse a ofertas de un inquilino de directorio remoto.

### <a name="to-subscribe-to-an-offer"></a>Para suscribirse a una oferta

1. Inicie sesión en el portal de usuarios de Azure Stack Hub y seleccione **Obtener una suscripción**.

   ![Obtención de una suscripción en el portal de usuarios de Azure Stack Hub](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. En **Obtener una suscripción**, escriba el nombre descriptivo de la suscripción en **Nombre para mostrar**. Seleccione **Oferta** y en **Elegir una oferta**, elija una. Seleccione **Crear** para crear la suscripción.

   ![Selección de una oferta en el portal de usuarios de Azure Stack Hub](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Después de suscribirse a una oferta, actualice el portal para ver los servicios que forman parte de la nueva suscripción.

4. Para ver la suscripción que ha creado, seleccione **Todos los servicios** y, después, en la categoría **GENERAL** seleccione **Suscripciones**. Seleccione la suscripción para ver sus detalles.  

### <a name="to-enable-an-add-on-plan-in-your-subscription"></a>Para habilitar un plan complementario en su suscripción

Si la oferta a la que se suscribe tiene un plan complementario, puede agregarlo a la suscripción en cualquier momento.  

1. En el portal de usuarios, seleccione **todos los servicios**. A continuación, en la categoría **GENERAL** seleccione **Suscripciones** y, después, seleccione la suscripción que desea cambiar. Si hay planes complementarios disponibles, la opción **+Agregar plan** está activa y muestra un icono para los **Planes complementarios**.

   Si **+ Agregar plan** no está activo, no hay ningún plan complementario para la oferta asociado a esa suscripción.

1. Seleccione **+ Agregar plan** o el icono **Planes complementarios**. En **Planes complementarios**, seleccione el plan que desea agregar.

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre cómo un usuario puede implementar recursos en su suscripción:

- [Varios inicios rápidos](../user/azure-stack-quick-windows-portal.md) muestran cómo aprovisionar máquinas virtuales Windows y Linux con PowerShell, la CLI de Azure y el portal de usuarios.
- [Un tutorial que usa una plantilla de Azure Resource Manager](../user/azure-stack-create-vm-template.md) muestra cómo implementar una máquina virtual de Ubuntu 16.04 que ejecute Minikube para administrar un clúster de Kubernetes.
