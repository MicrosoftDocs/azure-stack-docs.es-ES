---
title: Creación de planes complementarios
titleSuffix: Azure Stack Hub
description: Aprenda a crear planes complementarios en Azure Stack Hub.
author: bryanla
ms.topic: conceptual
ms.date: 10/05/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 06/11/2019
ms.openlocfilehash: 2fd22acfe6f47d3a12502d5528e1f3fd96294460
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77696488"
---
# <a name="create-add-on-plans-in-azure-stack-hub"></a>Creación de planes complementarios en Azure Stack Hub

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
3. En el portal de administración, seleccione **Ofertas** y, a continuación, seleccione la oferta que desea actualizar con un plan complementario.

   ![Selección de la oferta que quiere actualizar con el plan complementario en el portal de administración de Azure Stack](media/create-add-on-plan/add-on1.png)

4. En la parte inferior de las propiedades de la oferta seleccione **Add-on plans** (Planes complementarios). Seleccione **Agregar**.

    ![Selección de planes complementarios en el portal de administración de Azure Stack](media/create-add-on-plan/add-on2.png)

5. Seleccione el plan que se va a agregar. En este ejemplo, el plan se denomina **20-storageaccounts**. Después de seleccionar el plan, haga clic en **Seleccionar** para agregar el plan a la oferta. Debería recibir una notificación de que el plan se agregó correctamente a la oferta.

    ![Selección del plan complementario que desea agregar en el portal de administración de Azure Stack](media/create-add-on-plan/add-on3.png)

6. Revise la lista de planes complementarios incluidos con la oferta para comprobar que el nuevo plan complementario aparezca en la lista.

    ![[Revisión de la lista de planes complementarios en el portal de administración de Azure Stack](media/create-add-on-plan/add-on4.png "Create add-on plan")](media/create-add-on-plan/add-on4lg.png#lightbox)
::: moniker-end

::: moniker range="<=azs-1901"

## <a name="create-an-add-on-plan-1901-and-earlier"></a>Crear un plan complementario (1901 y anteriores)

1. Inicie sesión en el portal del administrador de Azure Stack Hub como administrador de nube.
2. Siga los mismos pasos utilizados para [crear un plan base](azure-stack-create-plan.md) para crear un nuevo plan que ofrezca servicios que no se ofrecían anteriormente. En este ejemplo, los servicios de Key Vault (**Microsoft.KeyVault**) se incluirán en el nuevo plan.
3. En el portal de administración, seleccione **Ofertas** y, a continuación, seleccione la oferta que desea actualizar con un plan complementario.

   ![Selección de la oferta que quiere actualizar con el plan complementario en el portal de administración de Azure Stack](media/create-add-on-plan/1.PNG)

4. Desplácese hasta el final de las propiedades de la oferta y seleccione **Planes complementarios**. Seleccione **Agregar**.

    ![Selección de planes complementarios en el portal de administración de Azure Stack](media/create-add-on-plan/2.PNG)

5. Seleccione el plan que se va a agregar. En este ejemplo, el plan se denomina **Key vault plan** (Plan de almacén de claves). Después de seleccionar el plan, haga clic en **Seleccionar** para agregar el plan a la oferta. Debería recibir una notificación de que el plan se agregó correctamente a la oferta.

    ![Selección del plan complementario que desea agregar en el portal de administración de Azure Stack](media/create-add-on-plan/3.PNG)

6. Revise la lista de planes complementarios incluidos con la oferta para comprobar que el nuevo plan complementario aparezca en la lista.

    ![Revisión de la lista de planes complementarios en el portal de administración de Azure Stack](media/create-add-on-plan/4.PNG)

::: moniker-end

## <a name="next-steps"></a>Pasos siguientes

* [Creación de una oferta](azure-stack-create-offer.md)
