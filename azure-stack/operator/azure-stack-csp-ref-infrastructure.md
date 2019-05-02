---
title: Infraestructura de informes de uso para proveedores de servicios en la nube | Microsoft Docs
description: Azure Stack incluye la infraestructura necesaria para realizar el seguimiento del uso para inquilinos a los que proporciona servicio un proveedor de servicios en la nube (CSP) cuando se produce y lo desvía a Azure.
services: azure-stack
documentationcenter: ''
author: WenJason
manager: digimobile
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
origin.date: 01/05/2019
ms.date: 04/29/2019
ms.author: v-jay
ms.reviewer: alfredop
ms.lastreviewed: 01/05/2019
ms.openlocfilehash: 1b8b83491211ae26493a6bb41c7f0f219f47f620
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307197"
---
# <a name="usage-reporting-infrastructure-for-cloud-service-providers"></a>Infraestructura de informes de uso para proveedores de servicios en la nube

Azure Stack incluye la infraestructura necesaria para realizar el seguimiento del uso cuando se produce y lo desvía a Azure. En Azure, Azure Commerce procesa los datos de uso y cobra el uso a las suscripciones apropiadas de Azure. Esto sucede de la misma manera que se supervisa el seguimiento del uso que tiene lugar en la nube global de Azure.

Debe tener en cuenta que ciertos conceptos son coherentes entre Azure global y Azure Stack. Azure Stack tiene suscripciones locales, que cumplen un rol similar al de una suscripción de Azure. Las suscripciones locales solo son válidas localmente. Las suscripciones locales se asignan a las suscripciones de Azure cuando uso se desvía a Azure.

Azure Stack tiene medidores de uso local. El uso local se asigna a los medidores utilizados en Azure Commerce. Sin embargo, los identificadores de los medidores son diferentes. Hay más medidores disponibles de forma local que el que usa Microsoft para facturación.

Hay algunas diferencias entre cómo los servicios están valorados en Azure Stack y Azure. Por ejemplo, en Azure Stack, el cargo por las máquinas virtuales solo se basa en núcleo virtual/horas, con la misma tasa para todas las series de máquinas virtuales, a diferencia de Azure. El motivo es que en Azure global los distintos precios reflejan hardware diferente. En Azure Stack, el cliente proporciona el hardware, así que no hay ninguna razón para cobrar distintas tasas para diferentes clases de máquinas virtuales.

Puede informarse sobre los medidores de Azure Stack utilizados en Commerce y sus precios en el centro de partners, de la misma manera que lo haría para los servicios de Azure:

1. En el centro de partners, vaya al **menú del panel** > **Pricing and offers** (Precios y ofertas).
2. En **Usage-based services** (Servicios basados en uso), seleccione **Current** (Actual).
3. Abra la hoja de cálculo **Azure in Global CSP price list** (Lista de precios de Azure en CSP global).
4. Filtre por **Region = Azure Stack** (Región = Azure Stack).

## <a name="usage-and-billing-error-codes"></a>Códigos de error de uso y facturación

Los siguientes mensajes de error se pueden encontrar al agregar inquilinos a un registro.

| Error                           | Detalles                                                                                                                                                                                                                                                                                                                           | Comentarios                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                            |
|---------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **RegistrationNotFound**            | No se encontró el registro proporcionado. Asegúrese de que la información siguiente se proporcionó correctamente:<br>1. Identificador de la suscripción (valor proporcionado: _identificador de suscripción_),<br>2. Grupo de recursos (valor proporcionado: _grupo de recursos_),<br>3. Nombre de registro (valor proporcionado: _nombre de registro_).                             | Este error suele ocurrir cuando la información que apunta al registro inicial no es correcta. Si necesita comprobar el grupo de recursos y el nombre del registro, puede encontrarlo en Azure Portal al enumerar todos los recursos. Si encuentra más de un recurso de registro, busque **CloudDeploymentID** en las propiedades y seleccione el registro cuyo **CloudDeploymentID** coincida con el de su nube. Para buscar el objeto **CloudDeploymentID**, puede usar este PowerShell en Azure Stack:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| **BadCustomerSubscriptionId**       | El _identificador de suscripción de cliente_ proporcionado y el identificador de suscripción del _nombre de registro_ no pertenecen al mismo proveedor de servicios en la nube de Microsoft. Comprueba que el identificador de la suscripción del cliente es correcta. Si el problema persiste, póngase en contacto con el soporte técnico. | Este error se produce cuando la suscripción del cliente es una suscripción CSP, pero se transfiere a un asociado de CSP diferente de aquel en el que se transfiere la suscripción utilizada en el registro inicial. Esta comprobación se realiza para evitar una situación que podría dar como resultado la facturación a un asociado de CSP que no es responsable del uso de Azure Stack.                                                                                                                                                                                                                                                                          |
| **InvalidCustomerSubscriptionId**   | El '_identificador de la suscripción del cliente_' no es válido. Asegúrese de que se proporciona una suscripción válida de Azure.                                                                                                                                                                         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
| **CustomerSubscriptionNotFound**    | El _identificador de la suscripción de cliente_ no se encuentra en el nombre _registration. Asegúrese de que se está usando una suscripción válida a Azure y que el identificador de suscripción se ha agregado al registro mediante la operación PUT.                                                   | Este error se produce al intentar comprobar que se ha agregado un inquilino a una suscripción, y no se encuentra la suscripción del cliente que se asociará al registro. El cliente no se ha agregado al registro o el identificador de suscripción se ha escrito incorrectamente.                                                                                                                                                                                                                                                                                                                                |
| **UnauthorizedCspRegistration**     | El _nombre de registro_ proporcionado no está aprobado para el uso de la arquitectura multiinquilino. Envíe un correo electrónico a azstCSP@microsoft.com e incluya el nombre de registro, grupo de recursos e identificador de suscripción que se utilizó en el registro.                                                                                    | Microsoft debe aprobar un registro para el modo multiinquilino antes de poder empezar a agregarle inquilinos.                                                                                                                                                                                                                                                                                                                                                                                             |
| **CustomerSubscriptionsNotAllowed** | No se admiten operaciones de suscripción de clientes en clientes desconectados. Para usar esta característica, vuelva a registrarse con licencias de pago por uso.                                                                                                                                                                    | El registro al que intenta agregar inquilinos es un registro de capacidad; es decir, cuando se creó el registro, se usó el parámetro `BillingModel Capacity`. Solo se permiten registros de pago por uso para agregar inquilinos. Debe volver a realizar el registro con el parámetro `BillingModel PayAsYouUse`.                                                                                                                                                                                                                                                                                          |
| **InvalidCSPSubscription**          | El _identificador de suscripción de cliente_ proporcionado no es una suscripción válida de CSP. Asegúrese de que se proporciona una suscripción válida de Azure.                                                                                                                                                        | Es muy probable que la suscripción del cliente esté mal escrita.                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                        |
| **MetadataResolverBadGatewayError** | Uno de los servidores ascendentes devolvió un error inesperado. Inténtelo de nuevo más tarde. Si el problema persiste, póngase en contacto con el soporte técnico.                                                                                                                                                                                                |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |

## <a name="terms-used-for-billing-and-usage"></a>Términos que se usan para la facturación y el uso

Los siguientes términos y conceptos se usan para el uso y la facturación en Azure Stack:

| Término | Definición |
| --- | --- |
| Asociado de CSP directo | Un asociado del proveedor de soluciones en la nube (CSP) directo recibe una factura directamente de Microsoft por el uso de Azure y de Azure Stack, y factura directamente a los clientes. |
| CSP indirecto | Los revendedores indirectos trabajan con un proveedor indirecto (también conocido como distribuidor). Los revendedores reclutan clientes finales; el proveedor indirecto mantiene la relación de facturación con Microsoft, administra la facturación de clientes y proporciona servicios adicionales como soporte técnico de productos. |
| Cliente final | Los clientes finales son las empresas y los organismos gubernamentales que poseen las aplicaciones y otras cargas de trabajo que se ejecutan en Azure Stack. |

## <a name="next-steps"></a>Pasos siguientes

- Para más información acerca del programa CSP, consulte [Programa Proveedor de soluciones en la nube](https://partner.microsoft.com/solutions/microsoft-cloud-solutions).
- Para más información sobre cómo recuperar la información de utilización de recursos de Azure Stack, consulte [Uso y facturación en Azure Stack](azure-stack-billing-and-chargeback.md).

<!-- Update_Description: wording update -->