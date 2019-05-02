---
title: Delegación de ofertas en Azure Stack | Microsoft Docs
description: Obtenga información acerca de cómo delegar a otras personas sus tareas de crear ofertas y registrar a usuarios.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: 68ba971ada087bed31a65db037963b601953f1c6
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64309567"
---
# <a name="delegate-offers-in-azure-stack"></a>Delegación de ofertas en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Como operador de Azure Stack, es posible que a menudo desee delegar en otras personas las tareas de registro de usuarios y creación de suscripciones. Por ejemplo, si es un proveedor de servicios puede desear que los distribuidores registren a los clientes y los administren en su nombre. Si forma parte de un grupo central de TI en una empresa, puede que quiera delegar el registro de usuarios a otro miembro de TI.

La delegación permite que sea más fácil llegar a más usuarios y administrarlos, de lo que podría hacer por sí solo, como se muestra en la ilustración siguiente:

![Niveles de delegación](media/azure-stack-delegated-provider/image1.png)

Con la delegación, el proveedor delegado administra una oferta (oferta delegada) y los clientes finales obtienen suscripciones bajo dicha oferta sin intervención del administrador del sistema.

## <a name="understand-delegation-roles-and-steps"></a>Descripción de los roles y pasos de delegación

### <a name="delegation-roles"></a>Roles de delegación

Los roles siguientes forman parte de la delegación:

* El *operador de Azure Stack* administra la infraestructura de Azure Stack y crea una plantilla de oferta. El operador delega en otras personas para que puedan proporcionar ofertas a su inquilino.

* Los operadores de Azure Stack delegados son usuarios con derechos de *propietario* o de *colaborador* en las suscripciones que se denominan *proveedores delegados*. Pueden pertenecer a otras organizaciones, por ejemplo, a otros inquilinos de Azure Active Directory (Azure AD).

* Los *usuarios* se suscriben a las ofertas y las usan para administrar las cargas de trabajo, crear máquinas virtuales, almacenar datos, etcétera.

### <a name="delegation-steps"></a>Pasos de delegación

Hay dos pasos básicos para configurar la delegación:

1. **Crear una suscripción de proveedor delegado**: suscribir un usuario a una oferta que solo contenga el servicio de suscripción. Los usuarios que se suscriben a esta oferta, pueden ampliar las ofertas delegadas a otros usuarios si los suscriben a dichas ofertas.

2. **Delegar una oferta en el proveedor delegado**: Esta oferta permite al proveedor delegado crear suscripciones o ampliar la oferta a sus usuarios. El proveedor delegado puede aprovechar ahora la oferta y ofrecerla a otros usuarios.

En la siguiente ilustración se muestra los pasos para configurar la delegación:

![Crear el proveedor delegado y habilitarlo para que puedan suscribir a los usuarios](media/azure-stack-delegated-provider/image2.png)

#### <a name="delegated-provider-requirements"></a>Requisitos del proveedor delegado

Para actuar como proveedor delegado, un usuario debe establecer una relación con el proveedor principal mediante la creación de una suscripción. Esta suscripción proporciona a los proveedores delegados el derecho a presentar las ofertas delegadas en nombre del proveedor principal.

Una vez establecida esta relación, el operador de Azure Stack puede delegar una oferta al proveedor delegado. El proveedor delegado puede aceptar la oferta, asignarle un nombre (pero sin cambiar el contenido) y ofrecerla a sus clientes.

## <a name="delegation-walkthrough"></a>Tutorial de delegación

Las secciones siguientes ofrecen un tutorial práctico para configurar un proveedor delegado, delegar una oferta y comprobar que los usuarios pueden suscribirse a la oferta delegada.

### <a name="set-up-roles"></a>Configurar los roles

Para usar este tutorial, necesita dos cuentas de Azure AD además de su cuenta de operador de Azure Stack. Si no tiene estas dos cuentas, deberá crearlas. Las cuentas pueden pertenecer a cualquier usuario de Azure AD y se conocen como el usuario y el proveedor delegado.

| **Rol** | **Derechos organizativos** |
| --- | --- |
| Proveedor delegado |Usuario |
| Usuario |Usuario |

 > [!NOTE]
 > En el caso de un revendedor de CSP, para crear este proveedor delegado, se requieren estos usuarios en el directorio del inquilino (instancia de AAD del usuario). El operador de Azure Stack debe [incorporar primero](azure-stack-enable-multitenancy.md) la instancia de AAD de ese inquilino y, luego, configurar el uso y la facturación mediante [estos pasos](azure-stack-csp-howto-register-tenants.md).

### <a name="identify-the-delegated-provider"></a>Identificación de los proveedores delegados

1. Inicie sesión en el portal de administración como operador de Azure Stack.

1. Para crear una oferta que permita a un usuario convertirse en un proveedor delegado:

    a.  [Cree un plan](azure-stack-create-plan.md).
       Este plan debe incluir solo el servicio de suscripción. En este artículo se usa un plan denominado **PlanForDelegation** como ejemplo.

   b.  [Cree una oferta](azure-stack-create-offer.md) en función de este plan. En este artículo se usa una oferta denominada **OfferToDP** como ejemplo.

   c.  Agregue el proveedor delegado como un suscriptor a esta oferta; para ello, seleccione **Suscripciones**, **Agregar** y **Nueva suscripción de inquilino**.

   ![Agregue el proveedor delegado como un suscriptor](media/azure-stack-delegated-provider/image3.png)

   > [!NOTE]
   > Como con todas las ofertas de Azure Stack, tiene la opción de hacer la oferta pública y permitir que los usuarios se subscriban, o mantenerla privada y dejar que el operador de Azure Stack administre la suscripción. Los proveedores delegados suelen ser un grupo pequeño. Con el fin de controlar a quién se admite en este grupo, mantener esta oferta en privado es lo más lógico en la mayoría de los casos.

### <a name="azure-stack-operator-creates-the-delegated-offer"></a>El operador de Azure Stack crea la oferta delegada

El siguiente paso es crear el plan y la oferta que va a delegar y que usarán los usuarios. Es conveniente definir esta oferta exactamente como desea que los usuarios la vean, porque el proveedor delegado no puede cambiar los planes ni las cuotas que incluye.

1. Como operador de Azure Stack, [cree un plan](azure-stack-create-plan.md) y [una oferta](azure-stack-create-offer.md) basada en él. En este artículo se usa una oferta denominada **DelegatedOffer** como ejemplo.

   > [!NOTE]
   > Esta oferta no tiene que ser pública, pero puede hacerla pública. No obstante, en la mayoría de los casos, solo deseará que los proveedores delegados tengan acceso a ella. Una vez que delegue una oferta privada tal como se describe en los pasos siguientes, el proveedor delegado tendrá acceso a la misma.

2. Delegue la oferta. Vaya a **DelegatedOffer**. En **Configuración**, seleccione **Proveedores delegados** y, luego, **Agregar**.

3. Seleccione la suscripción del proveedor delegado en el cuadro de lista desplegable y elija **Delegar**.

   ![Agregar proveedor delegado](media/azure-stack-delegated-provider/image4.png)

### <a name="delegated-provider-customizes-the-offer"></a>El proveedor delegado personaliza la oferta

Inicie sesión en el portal de usuarios como proveedor delegado y luego cree una oferta nueva usando la oferta delegada como plantilla.

1. Seleccione **+ Crear un recurso**, **Ofertas + Planes de inquilinos** y **Oferta**.

    ![Crear una nueva oferta](media/azure-stack-delegated-provider/image5.png)

2. Asigne un nombre a la oferta. En este ejemplo se usa **ResellerOffer**. Seleccione la oferta delegada en la que basarse y, después seleccione **Crear**.

   ![Asignar un nombre](media/azure-stack-delegated-provider/image6.png)

   >[!IMPORTANT]
   >Es importante saber que los proveedores delegados solo pueden elegir las ofertas que les hayan delegado. No pueden hacer cambios en dichas ofertas. Solo un operador de Azure Stack puede cambiarlas, por ejemplo, modificar sus planes y cuotas. Un proveedor delegado no construye una oferta a partir de planes base y de planes complementarios.

3. El proveedor delegado puede exponer estas ofertas mediante la dirección URL de su propio portal. Para hacer pública la oferta, seleccione **Examinar** y, a continuación, **Ofertas**. Seleccione la oferta y, a continuación, seleccione **Cambiar estado**.

4. Las ofertas delegadas públicas solo son visibles en el portal delegado. Para buscar y cambiar esta dirección URL:

     a.  Seleccione **Examinar**, **Todos los servicios** y, en la categoría **GENERAL**, seleccione **Suscripciones**. Seleccione la suscripción de proveedor delegado; por ejemplo, **DPSubscription** y, luego, **Propiedades**.

    b.  Copie la URL del portal en una ubicación diferente, como el Bloc de notas.

    ![Selección de la suscripción del proveedor delegado](media/azure-stack-delegated-provider/dpportaluri.png)  

   Ha terminado de crear una oferta delegada como proveedor delegado. Cierre sesión como proveedor delegado y cierre la ventana del explorador.

### <a name="sign-up-for-the-offer"></a>Suscribirse a la oferta

1. En una nueva ventana del explorador, vaya a la URL del portal de delegado que guardó en el paso anterior. Inicie sesión en el portal como usuario.

   >[!NOTE]
   >Las ofertas delegadas no son visibles, a menos que se use el portal delegado.

1. En el panel, haga clic en **Obtener una suscripción**. Verá que solo se presentan al usuario las ofertas delegadas que creó el proveedor delegado.

   ![Ver y seleccionar ofertas](media/azure-stack-delegated-provider/image8.png)

Ha finalizado el proceso de delegación de una oferta. Ahora, un usuario puede registrarse para esta oferta y obtener una suscripción a ella.

## <a name="move-subscriptions-between-delegated-providers"></a>Paso de suscripciones entre proveedores delegados

Si es necesario, se puede pasar una suscripción entre suscripciones de proveedor delegado nuevas o existentes que pertenezcan al mismo inquilino del directorio. Para ello, se usa el cmdlet [Move-AzsSubscription](/powershell/module/azs.subscriptions.admin) de PowerShell.

Resulta útil cuando:

* Incorpora un nuevo miembro del equipo que adoptará el rol de proveedor delegado y desea asignarle suscripciones de usuario que se crearon antes en la suscripción de proveedor predeterminada.
* Tiene varias suscripciones de los proveedores delegados en el mismo inquilino de directorio (Azure Active Directory) y tiene que mover suscripciones de usuario entre ellas. Este caso podría darse cuando un miembro del equipo se mueve entre los equipos y su suscripción debe asignarse al nuevo equipo.

## <a name="next-steps"></a>Pasos siguientes

* [Aprovisionar una máquina virtual](../user/azure-stack-create-vm-template.md)
