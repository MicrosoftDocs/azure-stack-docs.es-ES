---
title: Administración del uso y la facturación para Azure Stack como un proveedor de servicios en la nube | Microsoft Docs
description: Obtenga información sobre cómo registrar Azure Stack como proveedor en la nube (CSP) y cómo agregar clientes para la facturación.
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
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: a8ae60e9ee19f96196d1c3763a32be0842567c7a
ms.sourcegitcommit: e8f7fe07b32be33ef621915089344caf1fdca3fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/28/2019
ms.locfileid: "70118683"
---
# <a name="manage-usage-and-billing-for-azure-stack-as-a-cloud-service-provider"></a>Administración del uso y la facturación para Azure Stack como un proveedor de servicios en la nube

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describe cómo registrar Azure Stack como proveedor de servicios en la nube (CSP) y cómo agregar a clientes.

Como CSP, trabaja con diversos clientes mediante su instancia de Azure Stack. Cada cliente tiene una suscripción de CSP en Azure. Tendrá que dirigir el uso de su recurso Azure Stack a cada suscripción de usuario.

En la siguiente ilustración se muestran los pasos necesarios para elegir su cuenta de servicios compartidos y registrar la cuenta de Azure con la cuenta de Azure Stack. Una vez registrado, podrá incorporar los clientes finales:

[![Proceso para habilitar el uso y administración como Proveedor de servicios en la nube](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png "Process for enabling usage and management as a Cloud Service Provider")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.png#lightbox)

## <a name="create-a-csp-or-apss-subscription"></a>Creación de una suscripción de CSP o APSS

### <a name="csp-subscription-types"></a>Tipos de suscripción a CSP

Elija el tipo de cuenta de servicios compartidos que utiliza para Azure Stack. Estos son los tipos de suscripciones que se pueden usar para el registro de una instancia de Azure Stack multiinquilino:

- Proveedor de servicios en la nube
- Suscripción de servicios compartidos de asociados

#### <a name="azure-partner-shared-services"></a>Servicios compartidos para asociados de Azure

Las suscripciones de los servicios compartidos para asociados de Azure (APSS) son la opción preferida para el registro cuando un distribuidor de CSP o CSP directo opera en Azure Stack.

Las suscripciones de APSS están asociadas a un inquilino de servicios compartidos. Cuando registra Azure Stack, proporciona credenciales para una cuenta que es propietaria de la suscripción. La cuenta que usa para registrar Azure Stack puede ser diferente de la cuenta de administrador que usa para la implementación. Además, no es necesario que las dos cuentas pertenezcan al mismo dominio; puede realizar la implementación mediante el inquilino que ya utiliza. Por ejemplo, puede usar `ContosoCSP.onmicrosoft.com` y, después, registrarse con un inquilino diferente; por ejemplo, `IURContosoCSP.onmicrosoft.com`. No olvide iniciar sesión con `ContosoCSP.onmicrosoft.com` al realizar la administración diaria de Azure Stack. Inicie sesión en Azure mediante `IURContosoCSP.onmicrosoft.com` cuando necesite realizar operaciones de registro.

Para una descripción de las suscripciones de APSS y cómo crearlas, consulte [Adición de servicios compartidos para asociados de Azure](/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Suscripciones de CSP

Las suscripciones a CSP son la opción preferida para el registro cuando un revendedor de CSP o un cliente final opera en Azure Stack.

## <a name="register-azure-stack"></a>Registro de Azure Stack

Use la suscripción a APSS creada con la información de la sección anterior para registrar Azure Stack con Azure. Para obtener más información, consulte [Registro de Azure Stack con una suscripción a Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Adición a un cliente final

Para configurar Azure Stack de modo que cuando un nuevo inquilino utilice recursos su uso se notifique a su suscripción de CSP, consulte [Adición de inquilinos en Azure Stack para uso y facturación](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Carga de las suscripciones correctas

Azure Stack utiliza una característica denominada *registro*. Un registro es un objeto almacenado en Azure. El objeto de registro documenta qué suscripciones de Azure hay que usar para cobrar por una instancia determinada de Azure Stack. En esta sección se explica la importancia del registro.

Mediante el uso del registro Azure Stack puede:

- Desviar los datos de uso de Azure Stack a Azure Commerce y facture una suscripción a Azure.
- Notificar el uso de cada cliente en una suscripción diferente con una implementación multiinquilino de Azure Stack. La arquitectura multiinquilino permite que Azure Stack admita distintas organizaciones en la misma instancia de Azure Stack.

Para cada instancia de Azure Stack, hay una suscripción predeterminada y varias suscripciones de inquilinos. La suscripción predeterminada es una suscripción a Azure que se cobra si no hay ninguna suscripción específica de inquilino. Debe ser la primera suscripción que se registre. Para que los informes de uso multiinquilinos funcionen, la suscripción debe ser un plan CSP o APSS.

Después, el registro se actualiza con una suscripción de Azure para cada inquilino que use Azure Stack. Las suscripciones de inquilinos deben ser de tipo CSP y deben trasferirse al asociado que posee la suscripción predeterminada. No puede registrar los clientes de otra persona.

Cuando Azure Stack reenvía información de uso a Azure global, un servicio de Azure consulta el registro y asigna el uso de cada inquilino a la suscripción del inquilino correspondiente. Si no se ha registrado ningún inquilino, ese uso se transfiere a la suscripción predeterminada para la instancia de Azure Stack de la que se originó.

Dado que las suscripciones de inquilinos son suscripciones a CSP, se enviará la factura al asociado de CSP y la información de uso no será visible para el cliente final.

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca del programa CSP, consulte [Programa Proveedor de soluciones en la nube](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Para obtener más información sobre cómo recuperar la información de utilización de recursos de Azure Stack, consulte [Uso y facturación en Azure Stack](azure-stack-billing-and-chargeback.md).
