---
title: Creación de una oferta en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo crear una oferta para los usuarios de Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 6ada971c092acbc1ebe8e83a784a5d1e392c4dea
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72283584"
---
# <a name="create-an-offer-in-azure-stack"></a>Creación de una oferta en Azure Stack

Las [ofertas](azure-stack-overview.md) son grupos de uno o varios planes que los proveedores presentan a los usuarios para que estos los compren o se suscriban a ellos. Este artículo describe cómo crear una oferta que incluye el [plan que ha creado](azure-stack-create-plan.md). Esta oferta da a los suscriptores la posibilidad de configurar máquinas virtuales.

::: moniker range=">=azs-1902"
## <a name="create-an-offer-1902-and-later"></a>Creación de una oferta (1902 y posteriores)

1. Inicie sesión en el [portal de administrador de Azure Stack](https://adminportal.local.azurestack.external) y seleccione **+ Crear un recurso**, **Ofertas y planes** y, finalmente, **Oferta**.

   ![Creación de una oferta en Azure Stack](media/azure-stack-create-offer/offers.png)

2. Aparece una interfaz de usuario con pestañas que permite definir el nombre de la oferta. También puede agregar nuevos planes base y planes complementarios. Lo más importante es que puede revisar los detalles de la oferta que crea, antes de decidirse a crearla.

   En la pestaña **Basics** (Básico), escriba un **nombre para mostrar** y un **nombre de recurso** y, después, en **Resource Group** (Grupo de recursos), seleccione **Create new** (Crear nuevo) o **Use existing** (Usar existente). El nombre para mostrar es el nombre descriptivo de la oferta. Este nombre descriptivo es la única información acerca de la oferta que verán los usuarios cuando se suscriban a una oferta en el portal de usuarios. Utilice un nombre intuitivo que ayude al usuario a entender lo que incluye la oferta. Solo el administrador puede ver el nombre del recurso. Es el nombre que usan los administradores para trabajar con la oferta como un recurso de Azure Resource Manager. En esta pestaña, también puede elegir si quiere que esta oferta sea pública o mantenerla privada. El valor predeterminado es privada. Puede [cambiar el estado público o privado de la oferta](#change-the-state-of-an-offer) en cualquier momento.

   ![Nueva oferta en Azure Stack](media/azure-stack-create-offer/new-offer.png)
  
3. Seleccione la pestaña **Base plans** (Planes base) o haga clic en el botón **Next: Base plans >** (Siguiente: Planes base). Seleccione los planes que quiere incluir en la oferta.

   ![Seleccionar el plan que quiere incluir en la oferta de Azure Stack](media/azure-stack-create-offer/select-plan.png)

4. En este momento puede crear un plan complementario para modificar el plan base, pero es un paso opcional. Tiene la oportunidad de crear un plan complementario en el siguiente artículo, [Planes complementarios de Azure Stack](create-add-on-plan.md).

5. Seleccione la pestaña **Revisar y crear**. Revise el resumen de la oferta para asegurarse de que todos los valores son correctos. La interfaz le permite expandir las cuotas de los planes elegidos, de una en una, para ver los detalles de cada cuota en un plan. También puede volver atrás para realizar las modificaciones necesarias.

6. Seleccione **Crear** para crear la oferta.

   ![Revisar y crear una oferta en Azure Stack](media/azure-stack-create-offer/review-offer.png)

### <a name="change-the-state-of-an-offer"></a>Cambio del estado de una oferta

Después de crear la oferta puede cambiar su estado. Las ofertas deben hacerse **públicas** para que los usuarios tengan la vista completa de ellas cuando se suscriban. Las ofertas pueden ser:

- **Públicas:** son visibles para los usuarios.
- **Privado**: solo las ven los administradores de la nube. Esta configuración es útil durante la elaboración del plan o la oferta, o si el administrador de la nube desea [crear cada una de las suscripciones para los usuarios](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
- **Retiradas**: cerradas a nuevos suscriptores. El administrador de la nube puede retirar las ofertas para impedir futuras suscripciones, pero sin que esto afecte a los suscriptores actuales.

  > [!TIP]  
  > Los cambios en la oferta no son inmediatamente visibles para el usuario. Para ver los cambios, es posible que los usuarios tengan que cerrar sesión y volver a iniciarla en el portal de usuarios para ver la nueva oferta.

Hay dos formas de cambiar el estado de una oferta:

1. En **Todos los recursos**, seleccione el nombre de la oferta. En la pantalla **Información general** de la oferta, seleccione **Cambiar estado**. Elija el estado que quiere utilizar (por ejemplo, **Público**).

   ![Cambiar el estado de la oferta de Azure Stack](media/azure-stack-create-offer/change-state.png)

2. Seleccione **Offer settings** (Configuración de la oferta). Elija el estado que quiere utilizar (por ejemplo, **Público**) y, después, seleccione **Guardar**.

   ![Configuración de la oferta de Azure Stack](media/azure-stack-create-offer/offer-settings.png)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-offer-1901-and-earlier"></a>Creación de una oferta (1901 y anteriores)

1. Inicie sesión en el [portal de administrador de Azure Stack](https://adminportal.local.azurestack.external) y seleccione **+ Crear un recurso**, **Ofertas + Planes de inquilinos** y, finalmente, **Oferta**.

   ![Creación de una oferta en Azure Stack](media/azure-stack-create-offer/image01.png)
  
2. En **Nueva oferta**, escriba un **Nombre para mostrar** y un **Nombre de recurso** y, después, en **Grupo de recursos** seleccione **Crear nuevo** o **Usar existente**. El nombre para mostrar es el nombre descriptivo de la oferta. Este nombre descriptivo es la única información acerca de la oferta que verán los usuarios cuando se suscriban a ella. Utilice un nombre intuitivo que ayude al usuario a entender lo que incluye la oferta. Solo el administrador puede ver el nombre del recurso. Es el nombre que usan los administradores para trabajar con la oferta como un recurso de Azure Resource Manager.

   ![Nueva oferta en Azure Stack](media/azure-stack-create-offer/image01a.png)
  
3. Seleccione **Planes base** para abrir el **plan**. Seleccione los planes que desea incluir en la oferta y, después, elija **Seleccionar**. Para crear la oferta, seleccione **Crear**.

   ![Seleccionar el plan que quiere incluir en la oferta de Azure Stack](media/azure-stack-create-offer/image02.png)
  
4. Después de crear la oferta puede cambiar su estado. Las ofertas deben hacerse **públicas** para que los usuarios tengan la vista completa de ellas cuando se suscriban. Las ofertas pueden ser:

   - **Públicas:** son visibles para los usuarios.
   - **Privado**: solo las ven los administradores de la nube. Esta configuración es útil durante la elaboración del plan o la oferta, o si el administrador de la nube desea [crear cada una de las suscripciones para los usuarios](azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator).
   - **Retiradas**: cerradas a nuevos suscriptores. El administrador de la nube puede retirar las ofertas para impedir futuras suscripciones, pero sin que esto afecte a los suscriptores actuales.

   > [!TIP]  
   > Los cambios en la oferta no son inmediatamente visibles para el usuario. Para ver los cambios, es posible que los usuarios tengan que cerrar sesión y volver a iniciarla en el portal de usuarios para ver la nueva oferta.

   En la pantalla de información general de la oferta, seleccione **Estado de accesibilidad**. Elija el estado que desea utilizar (por ejemplo, **Público**) y, después, seleccione **Guardar**.

     ![Cambiar el estado de la oferta de Azure Stack](media/azure-stack-create-offer/change-stage-1807.png)

     Como alternativa, seleccione **Cambiar estado** y, a continuación, elija un estado.

    ![Seleccionar el estado de accesibilidad de la oferta de Azure Stack](media/azure-stack-create-offer/change-stage-select-1807.png)

> [!NOTE]
> También puede usar PowerShell para crear ofertas, planes y cuotas predeterminados. Para más información, consulte [Azure Stack PowerShell Module 1.4.0](/powershell/azure/azure-stack/overview?view=azurestackps-1.4.0).
::: moniker-end

## <a name="next-steps"></a>Pasos siguientes

- Para más información sobre cómo modificar una oferta y proporcionar a los usuarios un plan complementario, continúe con [Create un plan complementario ](create-add-on-plan.md) (opcional).
- En caso contrario, vaya a [Suscribirse a una oferta](azure-stack-subscribe-plan-provision-vm.md).