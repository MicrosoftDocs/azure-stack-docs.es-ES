---
title: Códigos de error de registro de uso y facturación en Azure Stack Hub
description: Obtenga información sobre los códigos de error de registro de uso y facturación en Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 08/27/2020
ms.author: sethm
ms.reviewer: avishwan
ms.lastreviewed: 06/27/2019
ms.openlocfilehash: 0c143a2a997a044e102eef63018dba5a492a3e11
ms.sourcegitcommit: 03aad17afe8519536066c735c59ad1bdfe8de083
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/27/2020
ms.locfileid: "89041611"
---
# <a name="usage-and-billing-registration-error-codes"></a>Códigos de error de registro de uso y facturación

A los proveedores de soluciones en la nube (CSP), les pueden aparecer los siguientes mensajes de error al [agregar inquilinos](azure-stack-csp-ref-operations.md#add-tenant-to-registration) a un registro para el uso de informes en el identificador de la suscripción de Azure del cliente.

## <a name="list-of-registration-error-codes"></a>Lista de códigos de error de registro

| Error   | Detalles  | Comentarios  |
|---|---|---|
| RegistrationNotFound | No se encontró el registro proporcionado. Asegúrese de que la información siguiente se proporcionó correctamente:<br>1. Identificador de la suscripción (valor proporcionado: **identificador de suscripción**),<br>2. Grupo de recursos (valor proporcionado: **grupo de recursos**),<br>3. Nombre de registro (valor proporcionado: **nombre de registro**). | Este error suele producirse cuando la información que apunta al registro inicial no es correcta. Si necesita comprobar el grupo de recursos y el nombre del registro, puede encontrarlo en Azure Portal al enumerar todos los recursos. Si encuentra más de un recurso de registro, busque **CloudDeploymentID** en las propiedades y seleccione el registro cuyo **CloudDeploymentID** coincida con el de su nube. Para buscar el objeto **CloudDeploymentID**, puede usar este comando de PowerShell en Azure Stack Hub:<br>`$azureStackStampInfo = Invoke-Command -Session $session -ScriptBlock { Get-AzureStackStampInformation }` |
| BadCustomerSubscriptionId | El **identificador de la suscripción del cliente** proporcionado y el identificador de suscripción del **nombre de registro** no pertenecen al mismo proveedor de soluciones en la nube de Microsoft. Compruebe que el identificador de la suscripción del cliente es correcto. El identificador de suscripción del cliente distingue mayúsculas y minúsculas. Si el problema persiste, póngase en contacto con el soporte técnico. | Este error aparece cuando la suscripción del cliente es una suscripción de proveedor de soluciones en la nube, pero se transfiere a un asociado de CSP que no es al que se transfiere la suscripción utilizada en el registro inicial. Esta comprobación se realiza para evitar una situación cuya consecuencia podría ser que se facturara a un asociado de CSP que no es responsable del uso de Azure Stack Hub. |
| InvalidCustomerSubscriptionId  | El **identificador de la suscripción del cliente** no es válido. Asegúrese de que se proporciona una suscripción válida de Azure. |   |
| CustomerSubscriptionNotFound  | El **identificador de la suscripción del cliente** no se encuentra en el **nombre del registro**. Asegúrese de que se está usando una suscripción válida a Azure y de que el identificador de suscripción se ha agregado al registro mediante la operación PUT. | Este error se produce al intentar comprobar que se ha agregado un inquilino a una suscripción, pero no se detecta que la suscripción del cliente está asociada al registro. El cliente no se ha agregado al registro o el identificador de suscripción se ha escrito incorrectamente. |
| UnauthorizedCspRegistration | El **nombre de registro** proporcionado no tiene la aprobación necesaria para usar una arquitectura multiinquilino. Envíe un correo electrónico a azstCSP@microsoft.com e incluya el nombre de registro, grupo de recursos e identificador de suscripción que se utilizó en el registro. | Microsoft debe aprobar un registro para el modo multiinquilino para que pueda empezar a agregarle inquilinos. |
| CustomerSubscriptionsNotAllowed | No se admiten operaciones de suscripción de clientes en clientes desconectados. Para usar esta característica, vuelva a registrarse con licencias de pago por uso. | El registro al que intenta agregar inquilinos es de capacidad. Por tanto, cuando se creó el registro, se usó el parámetro `BillingModel Capacity`. Solo se permiten registros de pago por uso para agregar inquilinos. Debe volver a realizar el registro con el parámetro `BillingModel PayAsYouUse`. |
| InvalidCSPSubscription | El **identificador de la suscripción del cliente** proporcionado no es una suscripción de CSP válida. Asegúrese de que se proporciona una suscripción válida de Azure. | Es muy probable que el error se deba a que la suscripción del cliente no se ha escrito correctamente. |
| MetadataResolverBadGatewayError | Uno de los servidores ascendentes devolvió un error inesperado. Vuelva a intentarlo más tarde. Si el problema persiste, póngase en contacto con el soporte técnico. |

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [Infraestructura de informes de uso para proveedores de soluciones en la nube](azure-stack-csp-ref-infrastructure.md).
- Para más información acerca del programa CSP, consulte [Cloud Solutions](https://partner.microsoft.com/solutions/microsoft-cloud-solutions) (Soluciones en la nube).
- Para más información sobre cómo recuperar la información del uso de recursos de Azure Stack Hub, consulte [Uso y facturación en Azure Stack Hub](azure-stack-billing-and-chargeback.md).
