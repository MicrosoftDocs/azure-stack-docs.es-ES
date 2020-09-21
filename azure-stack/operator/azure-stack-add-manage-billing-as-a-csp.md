---
title: Administración del uso y la facturación de Azure Stack Hub como proveedor de soluciones en la nube
description: Obtenga información sobre cómo registrar Azure Stack Hub como proveedor de soluciones en la nube (CSP) y cómo agregar clientes para la facturación.
author: sethmanheim
ms.topic: article
ms.date: 07/27/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/15/2019
ms.openlocfilehash: 03206007c60d438149baad267117e447bd5b0a14
ms.sourcegitcommit: 3e2460d773332622daff09a09398b95ae9fb4188
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/15/2020
ms.locfileid: "90573486"
---
# <a name="manage-usage-and-billing-for-azure-stack-hub-as-a-cloud-solution-provider"></a>Administración del uso y la facturación de Azure Stack Hub como proveedor de soluciones en la nube

En este artículo se describe cómo registrar Azure Stack Hub como proveedor de soluciones en la nube (CSP) y cómo agregar a clientes.

Como CSP, trabaja con diversos clientes mediante su instancia de Azure Stack Hub. Cada cliente tiene una suscripción de CSP en Azure. Tendrá que dirigir el uso de su recurso Azure Stack Hub a cada suscripción de usuario.

En la siguiente ilustración se muestran los pasos necesarios para elegir su cuenta de servicios compartidos y registrar la cuenta de Azure con la cuenta de Azure Stack Hub. Una vez registrado, podrá incorporar los clientes finales:

[![Proceso para habilitar el uso y la administración como un proveedor de soluciones en la nube](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.svg "Proceso para habilitar el uso y la administración como un proveedor de soluciones en la nube")](media/azure-stack-add-manage-billing-as-a-csp/process-add-useage-as-a-csp.svg)

## <a name="create-a-csp-or-apss-subscription"></a>Creación de una suscripción de CSP o APSS

### <a name="csp-subscription-types"></a>Tipos de suscripción a CSP

Elija el tipo de cuenta de servicios compartidos que utiliza con Azure Stack Hub. Estos son los tipos de suscripciones que se pueden usar para el registro de una instancia de multiinquilino de Azure Stack Hub:

- Proveedor de soluciones en la nube
- Suscripción de servicios compartidos de asociados

Hemos creado un vídeo de tutorial que le ayudará a comprender cómo administrar sus derechos multiinquilino:

> [!VIDEO https://www.youtube.com/embed/ZP6jkbLeS34]

#### <a name="azure-partner-shared-services"></a>Servicios compartidos para asociados de Azure

Las suscripciones de los servicios compartidos para asociados de Azure (APSS) son la opción preferida para el registro cuando un distribuidor de CSP o CSP directo opera en Azure Stack Hub.

Las suscripciones de APSS están asociadas a un inquilino de servicios compartidos. Cuando registra Azure Stack Hub, proporciona credenciales para una cuenta que es propietaria de la suscripción. La cuenta que usa para registrar Azure Stack Hub puede ser diferente de la cuenta de administrador que usa para la implementación. Además, no es necesario que las dos cuentas pertenezcan al mismo dominio; puede realizar la implementación mediante el inquilino que ya utiliza. Por ejemplo, puede usar `ContosoCSP.onmicrosoft.com` y, después, registrarse con un inquilino diferente; por ejemplo, `IURContosoCSP.onmicrosoft.com`. No olvide iniciar sesión con `ContosoCSP.onmicrosoft.com` al realizar la administración diaria de Azure Stack Hub. Inicie sesión en Azure mediante `IURContosoCSP.onmicrosoft.com` cuando necesite realizar operaciones de registro.

Para una descripción de las suscripciones de APSS y cómo crearlas, consulte [Adición de servicios compartidos para asociados de Azure](/partner-center/shared-services).

#### <a name="csp-subscriptions"></a>Suscripciones de CSP

Las suscripciones a CSP son la opción preferida para el registro cuando un revendedor de CSP o un cliente final opera en Azure Stack Hub.

## <a name="register-azure-stack-hub"></a>Registro de Azure Stack Hub

Use la suscripción de APSS creada con la información de la sección anterior para registrar Azure Stack Hub con Azure. Para más información, consulte [Registro de Azure Stack Hub con una suscripción de Azure](azure-stack-registration.md).

## <a name="add-end-customer"></a>Adición a un cliente final

Para configurar Azure Stack Hub de modo que se notifique un nuevo uso de recursos del inquilino a su suscripción de CSP, consulte [Adición de inquilinos en Azure Stack Hub para uso y facturación](azure-stack-csp-howto-register-tenants.md).

## <a name="charge-the-right-subscriptions"></a>Carga de las suscripciones correctas

Azure Stack Hub utiliza una característica denominada *registro*. Un registro es un objeto almacenado en Azure. El objeto de registro documenta qué suscripciones de Azure hay que usar para cobrar por una instancia determinada de Azure Stack Hub. En esta sección se explica la importancia del registro.

Mediante el uso del registro Azure Stack Hub puede:

- Desviar los [datos de uso de Azure Stack Hub](azure-stack-billing-and-chargeback.md) a Azure Commerce y facturar una suscripción a Azure.
- Notificar el uso de cada cliente en una suscripción diferente con una implementación multiinquilino de Azure Stack Hub. La arquitectura multiinquilino permite que Azure Stack Hub admita distintas organizaciones en la misma instancia de Azure Stack Hub.

Para cada instancia de Azure Stack Hub, hay una suscripción predeterminada y varias suscripciones de inquilinos. La suscripción predeterminada es una suscripción de Azure que se cobra si no hay una suscripción específica de inquilino. Debe ser la primera suscripción que se registre. Para que los informes de uso multiinquilinos funcionen, la suscripción debe ser un plan CSP o APSS.

Después, el registro se actualiza con una suscripción de Azure para cada inquilino que use Azure Stack Hub. Las suscripciones de inquilinos deben ser de tipo CSP y deben trasferirse al asociado que posee la suscripción predeterminada. No puede registrar los clientes de otra persona.

Cuando Azure Stack Hub reenvía información de uso a Azure global, un servicio de Azure consulta el registro y asigna el uso de cada inquilino a la suscripción del inquilino correspondiente. Si no se ha registrado un inquilino, ese uso se transfiere a la suscripción predeterminada para la instancia Azure Stack Hub de la que se originó.

Como las suscripciones de inquilinos son suscripciones a CSP, se enviará la factura al asociado de CSP y la información de uso no será visible para el cliente final.

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca del programa CSP, consulte [Programa Proveedor de soluciones en la nube](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Para más información sobre cómo recuperar la información de utilización de recursos de Azure Stack Hub, consulte [Uso y facturación en Azure Stack Hub](azure-stack-billing-and-chargeback.md).
