---
title: Ofrecer servicios de Azure Stack para la suscripción.
description: Aprenda a crear una oferta de servicio mediante ofertas, planes y servicios.
author: BryanLa
ms.author: bryanla
ms.service: azure-stack
ms.topic: tutorial
ms.date: 10/16/2019
ms.reviewer: shriramnat
ms.lastreviewed: 10/16/2019
ms.openlocfilehash: 03be52d653fe6340a88a9c466fef8c9b1ccc9256
ms.sourcegitcommit: b9d520f3b7bc441d43d489e3e32f9b89601051e6
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/08/2020
ms.locfileid: "75727435"
---
# <a name="tutorial-offer-a-service-to-users"></a>Tutorial: Oferta de un servicio a los usuarios

En este tutorial se muestra un operador acerca de cómo crear una oferta. Una oferta hace que los servicios estén disponibles para los usuarios en función de una suscripción. Una vez que se suscribe a una oferta, el usuario tiene derecho a crear e implementar recursos dentro de los servicios que haya especificado esa oferta.

En este tutorial, aprenderá a:

> [!div class="checklist"]
> * Creación de una oferta
> * Creación de un plan
> * Asignación de servicios y cuotas a un plan
> * Asignación de un plan a una oferta

## <a name="overview"></a>Información general

Una oferta consta de uno o varios planes. Un plan otorga el derecho de acceso a uno o más servicios, siempre que se especifique el proveedor de recursos correspondiente y una cuota de cada servicio. Asimismo, los planes se pueden agregar a una oferta como plan base o se puede extender la oferta como un plan complementario. Para obtener más información, consulte [Introducción a los servicios, planes, ofertas y suscripciones](service-plan-offer-subscription-overview.md).

![Planes, ofertas y suscripciones](media/azure-stack-key-features/image4.png)

### <a name="resource-providers"></a>Proveedores de recursos

Un proveedor de recursos admite la creación, implementación y administración de sus recursos como servicios. Un ejemplo común es el proveedor de recursos Microsoft. compute, que ofrece la posibilidad de crear e implementar máquinas virtuales (VM). Para ver una introducción sobre Azure Resource Manager, consulte [Azure Resource Manager](/azure/azure-resource-manager/resource-group-overview).

En Azure Stack, hay dos categorías generales de proveedores de recursos: las que implementan recursos como servicios fundamentales y las que se implementan como servicios de valor agregado.

### <a name="foundational-services"></a>Servicios fundamentales

>[!NOTE]
> En este tutorial, aprenderá a crear una oferta basada en servicios fundamentales. 

Los siguientes proveedores de recursos son compatibles con los servicios fundamentales, que están disponibles de forma nativa con cada instalación de Azure Stack:

| Proveedor de recursos | Recursos de ejemplo |
| ----------------- | ------------------|
| Microsoft.Compute | Virtual Machines y Virtual Machine Scale Sets |
| Microsoft.KeyVault | Almacenes de claves, secretos |
| Microsoft.Network | Redes virtuales, direcciones IP públicas, equilibradores de carga |
| Microsoft.Storage | Cuentas de almacenamiento, blobs, colas, tablas |

### <a name="value-add-services"></a>Servicios de valor agregado

>[!NOTE]
> Para ofrecer un servicio de valor agregado, primero se debe instalar el proveedor de recursos correspondiente en Marketplace de Azure Stack. Una vez instalados, sus recursos se ofrecen a los usuarios de la misma manera que los servicios fundamentales. Consulte la sección de **guías paso a paso** de la tabla de contenido, para ver el conjunto actual de proveedores de recursos que admiten ofertas de servicios de valor agregado.

Los proveedores de recursos que se instalan después de que se ha implementado Azure Stack admiten los servicios de valor agregado. Algunos ejemplos son:

| Proveedor de recursos | Recursos de ejemplo |
| ----------------- | ------------------------- |
| Microsoft.Web | Aplicaciones de función, aplicaciones web y aplicaciones de API de App Service | 
| Microsoft.MySqlAdapter | Servidor de hospedaje de MySQL, base de datos de MySQL | 
| Microsoft.SqlAdapter | Servidor de hospedaje de SQL Server, base de datos de SQL Server |

::: moniker range=">=azs-1902"
## <a name="create-an-offer"></a>Creación de una oferta

Durante el proceso de creación de la oferta, creará una oferta y un plan. El plan se usa como plan base de la oferta. Durante la creación del plan, se especifican los servicios disponibles en el plan y sus respectivas cuotas.

1. Inicie sesión en el portal de administrador con una cuenta de administrador en la nube.

   - En un sistema integrado, la dirección URL varía en función de la región y el nombre de dominio externo del operador y tendrá el formato https://adminportal.&lt;*región*&gt;.&lt;*FQDN*&gt;.
   - Si usa el Kit de desarrollo de Azure Stack, la dirección URL es https://adminportal.local.azurestack.external.

   Seleccione **+ Crear un recurso** > **Ofertas y planes** > **Oferta**.

   ![Nueva oferta](media/tutorial-offer-services/1-create-resource-offer.png)

1. En **Create a new offer** (Crear una nueva oferta) en la pestaña **Basics** (Datos básicos), escriba un **nombre para mostrar**, un **nombre de recurso** y seleccione un **grupo de recursos** existente o cree uno. El nombre para mostrar es el nombre descriptivo de la oferta. Solo el operador en la nube puede ver el nombre del recurso, que es el que usan los administradores para trabajar con la oferta como un recurso de Azure Resource Manager.

   ![Nombre para mostrar](media/tutorial-offer-services/2-create-new-offer.png)

1. Seleccione la pestaña **Base plans** (Planes base) y **Create new plan** (Crear nuevo plan) para crear un nuevo plan. También se agregará el plan a la oferta como un plan base.

   ![Agregar un plan](media/tutorial-offer-services/3-create-new-offer-base-plans.png)

1. En **New plan** (Nuevo plan) en la pestaña **Basics** (Datos básicos), escriba un **nombre para mostrar** y **nombre del recurso**. El nombre para mostrar es el nombre descriptivo del plan que ven los usuarios. Solo el operador en la nube puede ver el nombre del recurso, que es el que usan los operadores en la nube para trabajar con el plan como un recurso de Azure Resource Manager. El **grupo de recursos** se establecerá en el que se especificó en la oferta.

   ![Nombre para mostrar del plan](media/tutorial-offer-services/4-create-new-plan-basics.png)

1. Seleccione la pestaña **Services** (Servicios) y verá una lista de servicios disponibles de los proveedores de recursos instalados. Seleccione **Microsoft.Compute**, **Microsoft.Network** y **Microsoft.Storage**. 

   ![Servicios del plan](media/tutorial-offer-services/5-create-new-plan-services.png)

1. Seleccione la pestaña de **cuotas** y verá la lista de servicios que habilitó para este plan. Haga clic en **Create New** (Crear nuevo) para especificar una cuota personalizada para **Microsoft.Compute**. Es necesaria la cuota **Name** (Nombre), aunque puede aceptar o cambiar cada valor de cuota. Seleccione **OK** (Aceptar) cuando haya terminado y repita estos pasos para los servicios restantes.

   ![Crear la cuota del proceso](media/tutorial-offer-services/6-create-new-plan-quotas.png)

1. Seleccione la pestaña **Revisar y crear**. Debería ver un banner de color verde que dice "Validación superada" en la parte superior, y que indica que el nuevo plan base está listo para crearse. Seleccione **Crear**. También debería ver una notificación que indica que se ha creado el plan.

   ![Creación de un nuevo plan](media/tutorial-offer-services/7-create-new-plan-review-create.png)

1. Después de volver a la pestaña **Base plans** (Planes base) de la página **Create a new offer** (Crear una nueva oferta), compruebe que el plan se haya creado. Asegúrese de seleccionar el nuevo plan para incluirlo en la oferta como plan base, y seleccione **Review + create** (Revisar y crear).

   ![Agregar plan base](media/tutorial-offer-services/8-create-new-offer-base-plans-done.png)

1. En la pestaña **Review + create** (Revisar y crear), debería ver en la parte superior un banner de color verde que dice "Validación aprobada". Revise la información "Básica" y de los "Planes base" y seleccione **Create** (Crear) cuando esté listo. 

   ![Creación de nueva oferta](media/tutorial-offer-services/9-create-new-offer-review-create.png)

1. La página "Su implementación está en curso" se muestra inicialmente, seguida del mensaje "Su implementación se ha completado" una vez implementada la oferta. Haga clic en el nombre de la oferta, en la columna **Resource** (Recurso).

   ![Oferta de implementación completa](media/tutorial-offer-services/10-offer-deployment-complete.png)


1. Observe el banner, que muestra que la oferta todavía es privada, lo que impide que los usuarios se suscriban a ella. Cámbiela a pública seleccionando **Change State** (Cambiar estado) y elija **Public** (Pública).

    ![Estado público](media/tutorial-offer-services/11-offer-change-state.png)
::: moniker-end

::: moniker range="<=azs-1901"
## <a name="create-an-offer-1901-and-earlier"></a>Creación de una oferta (1901 y anteriores)

Durante el proceso de creación de la oferta, creará una oferta y un plan. El plan se usa como plan base de la oferta. Durante la creación del plan, se especifican los servicios disponibles en el plan y sus respectivas cuotas.

1. Inicie sesión en el portal de administrador con una cuenta de administrador en la nube.

   - En un sistema integrado, la dirección URL varía en función de la región y el nombre de dominio externo del operador y tendrá el formato https://adminportal.&lt;*región*&gt;.&lt;*FQDN*&gt;.
   - Si usa el Kit de desarrollo de Azure Stack, la dirección URL es https://adminportal.local.azurestack.external.
   
   Seleccione **+ Crear un recurso** > **Ofertas y planes** > **Oferta**.

   ![Nueva oferta](media/tutorial-offer-services/image01.png)

1. En **Nueva oferta**, escriba el **Nombre para mostrar** y el **Nombre de recurso** y, después, seleccione un **Grupo de recursos** nuevo o existente. El nombre para mostrar es el nombre descriptivo de la oferta. Solo el operador en la nube puede ver el nombre del recurso, que es el que usan los administradores para trabajar con la oferta como un recurso de Azure Resource Manager.

   ![Nombre para mostrar](media/tutorial-offer-services/image02.png)

1. Seleccione **Planes base** y, en la sección **Plan**, seleccione **Agregar** para agregar un nuevo plan a la oferta.

   ![Agregar un plan](media/tutorial-offer-services/image03.png)

1. En la sección **Nuevo plan**, rellene el **Nombre para mostrar** y el **Nombre del recurso**. El nombre para mostrar es el nombre descriptivo del plan que ven los usuarios. Solo el operador en la nube puede ver el nombre del recurso, que es el que usan los operadores en la nube para trabajar con el plan como un recurso de Azure Resource Manager.

   ![Nombre para mostrar del plan](media/tutorial-offer-services/image04.png)

1. Seleccione **Servicios**. En la lista Servicios, seleccione **Microsoft.Compute**, **Microsoft.Network** y **Microsoft.Storage**. Elija **Seleccionar** para agregar estos servicios al plan.

   ![Servicios del plan](media/tutorial-offer-services/image05.png)

1. Seleccione **Cuotas** y, a continuación, seleccione el primer servicio para el que desea crear una cuota. Para una cuota de IaaS, utilice el siguiente ejemplo como guía para configurar cuotas para los servicios Compute, Network y Storage.

   - Primero, cree una cuota para el servicio Compute. En la lista de espacio de nombres, seleccione **Microsoft.Compute** y, después, seleccione **Crear nueva cuota**.

     ![Crear nueva cuota](media/tutorial-offer-services/image06.png)

   - En **Crear cuota**, escriba un nombre para la cuota. Puede cambiar o aceptar cualquiera de los valores de cuota que se muestran. En este ejemplo, aceptamos la configuración predeterminada y seleccionamos **Aceptar**.

     ![Nombre de cuota](media/tutorial-offer-services/image07.png)

   - Elija **Microsoft.Compute** en la lista de espacio de nombres y, a continuación, seleccione la cuota que ha creado. Gracias a este paso se vincula la cuota con el servicio Compute.

     ![Seleccione la cuota](media/tutorial-offer-services/image08.png)

      Repita estos pasos para los servicios Network y Storage. Cuando haya terminado, seleccione **Aceptar** en **Cuotas** para guardar todas las cuotas.

1. En **Nuevo plan**, seleccione **Aceptar**.

1. En **Plan**, seleccione el nuevo plan y, después, **Seleccionar**.

1. En **Nueva oferta**, seleccione **Crear**. Verá una notificación cuando se crea la oferta.

1. En el menú del panel, seleccione **Ofertas** y, a continuación, haga clic en la oferta que ha creado.

1. Seleccione **Cambiar estado** y, después, elija **Público**.

    ![Estado público](media/tutorial-offer-services/image09.png)
::: moniker-end
 
## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Creación de una oferta
> * Creación de un plan
> * Asignación de servicios y cuotas a un plan
> * Asignación de un plan a una oferta

Prosiga con el siguiente tutorial para aprender a:
> [!div class="nextstepaction"]
> [Prueba de los servicios ofrecidos en este tutorial](tutorial-test-offer.md)
