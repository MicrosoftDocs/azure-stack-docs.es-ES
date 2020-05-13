---
title: Eliminación de cuotas, planes, ofertas y suscripciones
description: Aprenda a eliminar suscripciones, planes, ofertas y cuotas de Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 08/13/2019
ms.author: bryanla
ms.reviewer: efemmano
ms.lastreviewed: 04/25/2019
ms.openlocfilehash: 3c8c8b424d6ce555b19f4a77dbb8275b731f488e
ms.sourcegitcommit: 70c344b3c9c63f8c12867b2cdfdd1794fcc518dc
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/05/2020
ms.locfileid: "82836145"
---
# <a name="delete-quotas-plans-offers-and-subscriptions"></a>Eliminación de cuotas, planes, ofertas y suscripciones

En este artículo se describe cómo eliminar las cuotas, los planes, las ofertas y las suscripciones que ya no necesite. Como principio general, solo se puede eliminar lo que no esté en uso. Por ejemplo, solo se puede eliminar una oferta si no hay suscripciones que pertenezcan a ella.

Las suscripciones son la excepción a este principio general; puede eliminar suscripciones que contengan recursos y estos se eliminarán junto con la suscripción.

Si desea eliminar una cuota, debe volver a los planes y ofertas que usan la cuota. Empiece por las ofertas, asegúrese de que no tienen suscripciones, elimine cada oferta, elimine los planes que usan la cuota, etc.

## <a name="delete-a-subscription"></a>Eliminación de una suscripción

Para eliminar una suscripción, seleccione **Todos los servicios** y **Suscripciones de usuario** para mostrar una lista de todas las suscripciones en el sistema. Si trabaja en una oferta, también puede seleccionar **Suscripciones** desde allí.

Puede eliminar suscripciones de esta lista, o bien usar PowerShell para escribir un script que elimine todas las suscripciones automáticamente. Estos comandos se documentan en la referencia [ Suscripciones - Eliminar](/rest/api/azurestack/subscriptions/delete).

> [!CAUTION]
> Al eliminar una suscripción, también se eliminan los datos y recursos que contiene.

## <a name="delete-an-offer"></a>Eliminación de una oferta

Para eliminar una oferta, vaya a **Todos los servicios** y **Ofertas** en el portal del administrador. Seleccione la oferta que desee eliminar y seleccione **Eliminar**.

![Eliminación de suscripciones en el portal de administración de Azure Stack Hub](media/azure-stack-delete-offer/delsub1.png)

Solo puede eliminar las ofertas cuando no haya ninguna suscripción que las utilice. Si existen suscripciones basadas en la oferta, la opción **Eliminar** no estará disponible. En este caso, consulte la sección [Eliminación de una suscripción](#delete-a-subscription).

## <a name="delete-a-plan"></a>Eliminación de un plan

Para eliminar un plan, vaya a **Todos los servicios** y **Planes** en el portal del administrador. Seleccione el plan que desee eliminar y seleccione **Eliminar**.

![Eliminación de planes en el portal de administración de Azure Stack Hub](media/azure-stack-delete-offer/delsub2.png)

Solo puede eliminar los planes cuando no haya ninguna oferta ni suscripción que las utilice. Si hay ofertas que usen el plan, elimínelo, deje que se produzca un error y recibirá un mensaje de error. Puede seleccionar **Ofertas principales** para mostrar una lista de las ofertas que usan el plan. Para más información acerca de la eliminación de ofertas, consulte [Eliminación de una oferta](#delete-an-offer).

Los planes se pueden haber agregado directamente a una suscripción como planes complementarios, aunque no formen parte de la oferta. En este caso, para poder eliminarlos, deben quitarse primero de las suscripciones que los utilizan.

Además, no se puede quitar un plan de una suscripción si es la única fuente de un recurso determinado de esa suscripción. Por ejemplo, si se ha agregado el plan A a la suscripción 1 y es el único plan que proporciona una cuota de red a la suscripción, el plan no se puede quitar de la suscripción. Por consiguiente, no se puede eliminar.

## <a name="edit-and-delete-a-quota"></a>Edición y eliminación de una cuota

Desde el portal de administración puede ver y editar las cuotas existentes. Para ello, seleccione **Region Management** (Administración de regiones) y el proveedor de recursos apropiado, y seleccione **Quotas** (Cuotas). También puede eliminar cuotas para determinados proveedores de recursos.

![Eliminación de cuotas desde el portal de administración de Azure Stack Hub](media/azure-stack-delete-offer/delsub3.png)

También se pueden eliminar algunas cuotas mediante las siguientes API REST:

- [Proceso](/rest/api/azurestack/quotas%20(compute)/delete)
- [Network](/rest/api/azurestack/quotas%20(network)/delete)

> [!NOTE]
> Si cualquiera de los planes actuales utiliza alguna cuota, esta no se puede eliminar. Primero debe eliminar el plan al que hace referencia la cuota.

## <a name="next-steps"></a>Pasos siguientes

- [Creación de suscripciones](azure-stack-subscribe-plan-provision-vm.md)
- [Aprovisionamiento de una máquina virtual](../user/azure-stack-create-vm-template.md)
