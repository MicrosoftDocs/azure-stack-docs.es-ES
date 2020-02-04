---
title: En este artículo, aprenderá a actualizar ofertas y planes de Azure Stack Hub
description: En este artículo se describe cómo ver y modificar ofertas y planes de Azure Stack Hub.
author: bryanla
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: c4c01db560bbcd0f8312d205ad4643d4af48fed5
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76880016"
---
# <a name="azure-stack-hub-add-on-plans"></a>Planes complementarios de Azure Stack Hub

Como operador de Azure Stack Hub, puede crear planes complementarios para modificar un [plan base](azure-stack-create-plan.md) cuando quiera ofrecer servicios adicionales o ampliar las cuotas de *equipo*, *almacenamiento* o *red* más allá de la oferta inicial del plan base. Los planes complementarios modifican el plan base y son extensiones opcionales que los usuarios pueden elegir habilitar en su suscripción.

Hay ocasiones en las que combinar todo en un único plan es la solución óptima. En otras ocasiones puede interesarle más tener un plan base y ofrecer los servicios adicionales utilizando planes complementarios. Por ejemplo, podría decidir ofrecer servicios IaaS como parte de un plan base, con todos los servicios PaaS tratados como planes complementarios.

Otra razón para usar los planes complementarios es ayudar a supervisar el uso de los recursos. Para ello, puede empezar con un plan base que incluye cuotas relativamente pequeñas (dependiendo de los servicios requeridos). Después, a medida que los usuarios vayan utilizando la capacidad disponible, se les avisaría de que han consumido la asignación de recursos en función a su plan asignado. A partir de ese momento, los usuarios pueden seleccionar un plan complementario que proporcione los recursos adicionales.

> [!NOTE]
> Cuando no desee usar un plan complementario para ampliar una cuota, también puede elegir [editar la configuración original de la cuota](azure-stack-quota-types.md#edit-a-quota).

Los planes complementarios se [crean de la misma manera](azure-stack-create-plan.md) que un plan base. La diferencia entre ambos se determina cuando se agrega el plan a una oferta. Se designa como plan base o plan complementario. Al agregar un plan complementario a una oferta existente, los recursos adicionales pueden tardar hasta una hora en aparecer en la suscripción.

::: moniker range=">=azs-1902"
## <a name="create-an-add-on-plan-1902-and-later"></a>Crear un plan complementario (1902 y posteriores)

1. Inicie sesión en el portal del administrador de Azure Stack Hub como administrador de nube.
2. Siga los mismos pasos utilizados para [crear un plan base](azure-stack-create-plan.md) para crear un nuevo plan que ofrezca servicios que no se ofrecían anteriormente.
3. En el portal de administrador, haga clic en **Ofertas** y, a continuación, seleccione la oferta que desea actualizar con un plan complementario.

   ![Creación de un plan complementario](media/create-add-on-plan/add-on1.png)

4. En la parte inferior de las propiedades de la oferta seleccione **Add-on plans** (Planes complementarios). Haga clic en **Agregar**.

    ![Creación de un plan complementario](media/create-add-on-plan/add-on2.png)

5. Seleccione el plan que se va a agregar. En este ejemplo, el plan se denomina **20-storageaccounts**. Después de seleccionar el plan, haga clic en **Seleccionar** para agregar el plan a la oferta. Debería recibir una notificación de que el plan se agregó correctamente a la oferta.

    ![Creación de un plan complementario](media/create-add-on-plan/add-on3.png)

6. Revise la lista de planes complementarios incluidos con la oferta para comprobar que el nuevo plan complementario aparezca en la lista.

    [![Creación de un plan complementario](media/create-add-on-plan/add-on4.png "Creación de un plan complementario")](media/create-add-on-plan/add-on4lg.png#lightbox)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-add-on-plan-1901-and-earlier"></a>Crear un plan complementario (1901 y anteriores)

1. Inicie sesión en el portal del administrador de Azure Stack Hub como administrador de nube.
2. Siga los mismos pasos utilizados para [crear un plan base](azure-stack-create-plan.md) para crear un nuevo plan que ofrezca servicios que no se ofrecían anteriormente. En este ejemplo, los servicios de Key Vault (**Microsoft.KeyVault**) se incluirán en el nuevo plan.
3. En el portal de administrador, haga clic en **Ofertas** y, a continuación, seleccione la oferta que desea actualizar con un plan complementario.

   ![Creación de un plan complementario](media/create-add-on-plan/1.PNG)

4. Desplácese hasta el final de las propiedades de la oferta y seleccione **Planes complementarios**. Haga clic en **Agregar**.

    ![Creación de un plan complementario](media/create-add-on-plan/2.PNG)

5. Seleccione el plan que se va a agregar. En este ejemplo, el plan se denomina **Key vault plan** (Plan de almacén de claves). Después de seleccionar el plan, haga clic en **Seleccionar** para agregar el plan a la oferta. Debería recibir una notificación de que el plan se agregó correctamente a la oferta.

    ![Creación de un plan complementario](media/create-add-on-plan/3.PNG)

6. Revise la lista de planes complementarios incluidos con la oferta para comprobar que el nuevo plan complementario aparezca en la lista.

    ![Creación de un plan complementario](media/create-add-on-plan/4.PNG)
::: moniker-end

## <a name="next-steps"></a>Pasos siguientes

* [Creación de una oferta](azure-stack-create-offer.md)
