---
title: Eliminación de cuotas, planes, ofertas y suscripciones
description: Aprenda a eliminar suscripciones, planes, ofertas y cuotas de Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 08/13/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: 0e5f8963b2cdca8f116e1fdd889e1e8087265b0f
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "80806893"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>Eliminación de cuotas, planes, ofertas y suscripciones

En este artículo se describe cómo eliminar las cuotas, los planes, las ofertas y las suscripciones que ya no necesite. Como norma general, solo puede eliminar lo que no esté en uso. Por ejemplo, solo se puede eliminar una oferta si no hay suscripciones que pertenezcan a ella.

Las suscripciones son la excepción a esta norma general; puede eliminar suscripciones que contengan recursos y estos se eliminarán junto con la suscripción.

Por lo tanto, si desea eliminar una cuota, debe volver a los planes y las ofertas que usen esa cuota; empezando por las ofertas, asegúrese de que no tienen ninguna suscripción, elimine todas las ofertas, después, los planes que usen la cuota, etc.

## <a name="delete-a-subscription"></a>Eliminación de una suscripción

Para eliminar una suscripción, seleccione **Todos los servicios** y **Suscripciones de usuario** para mostrar una lista de todas las suscripciones en el sistema. Si está trabajando en una oferta, también puede seleccionar **Suscripciones** desde allí.

Puede eliminar suscripciones de esta lista o usar PowerShell para escribir un script que elimine todas las suscripciones automáticamente con los comandos documentados en la [referencia Suscriptions - Delete](/rest/api/azurestack/subscriptions/delete) (Suscripciones: eliminar).

> [!CAUTION]
> Al eliminar una suscripción, también se eliminan los datos y recursos que contiene.

## <a name="delete-an-offer"></a>Eliminación de una oferta

Para eliminar una oferta, vaya a **Todos los servicios** y **Ofertas** en el portal del administrador. Seleccione la oferta que desee eliminar y seleccione **Eliminar**.

![delsub1](media/azure-stack-delete-offer/delsub1.png)

Solo puede eliminar las ofertas cuando no haya ninguna suscripción que las utilice. Si existen suscripciones basadas en la oferta, la opción **Eliminar** no estará disponible. En este caso, consulte la sección [Eliminación de una suscripción](#delete-a-subscription).

## <a name="delete-a-plan"></a>Eliminación de un plan

Para eliminar un plan, vaya a **Todos los servicios** y **Planes** en el portal del administrador. Seleccione el plan que desee eliminar y seleccione **Eliminar**.

![delsub2](media/azure-stack-delete-offer/delsub2.png)

Solo puede eliminar los planes cuando no haya ninguna oferta ni suscripción que las utilice. Si no hay ofertas que usen el plan, elimínelo; permita que se produzca un error y recibirá un mensaje. Puede seleccionar **Ofertas principales** para mostrar una lista de las ofertas que usan el plan. Para más información acerca de la eliminación de ofertas, consulte [Eliminación de una oferta](#delete-an-offer).

Los planes pueden haberse agregado directamente a una suscripción como planes complementarios, aunque no formen parte de la oferta. En este caso, para poder eliminarlos, deben quitarse primero de las suscripciones que los utilizan.

Además, no se pueden quitar de una suscripción si son el único origen de un recurso determinado de esa suscripción. Por ejemplo, supongamos que el Plan A se ha agregado a la Suscripción 1 y que es el único plan que proporciona una cuota de red a la suscripción; no se puede quitar de la suscripción. Por lo tanto, no se puede eliminar.

## <a name="edit-and-delete-a-quota"></a>Edición y eliminación de una cuota

Puede ver y editar las cuotas existentes mediante el portal del administrador. Para ello, seleccione **Region Management** (Administración de regiones) y el proveedor de recursos correspondiente, y haga clic en **Cuotas**. También puede eliminar cuotas para determinados proveedores de recursos.

![delsub3](media/azure-stack-delete-offer/delsub3.png)

Como alternativa, puede eliminar algunas cuotas con estas API REST:

- [Proceso](/rest/api/azurestack/quotas%20(compute)/delete)
- [Network](/rest/api/azurestack/quotas%20(network)/delete)

> [!NOTE]
> No se puede eliminar una cuota si existen planes actuales que la usan. Primero debe eliminar el plan al que hace referencia la cuota.

## <a name="next-steps"></a>Pasos siguientes

- [Creación de suscripciones](azure-stack-subscribe-plan-provision-vm.md)
- [Aprovisionamiento de una máquina virtual](../user/azure-stack-create-vm-template.md)