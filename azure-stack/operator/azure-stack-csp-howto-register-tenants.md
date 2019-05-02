---
title: Adición de inquilinos en Azure Stack para uso y facturación | Microsoft Docs
description: Los pasos necesarios agregan un usuario final a Azure Stack administrado por un proveedor de servicios en la nube (CSP).
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
ms.openlocfilehash: 8e177944a5f57c9475287325b705fac34ec513c0
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307827"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Adición de inquilinos en Azure Stack para uso y facturación

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se describen los pasos necesarios para agregar un usuario final a una implementación de Azure Stack administrada por un proveedor de servicios en la nube (CSP). Cuando el nuevo inquilino utiliza recursos, Azure Stack informa del uso a su suscripción de CSP.

Los CSP suelen ofrecen servicios a varios clientes finales (inquilinos) en su implementación de Azure Stack. Al agregar los inquilinos en el registro de Azure Stack se garantiza que el uso de cada inquilino se notifica y se factura a la suscripción del CSP correspondiente. Si no se completan los pasos descritos en este artículo, el uso realizado por los inquilinos se cargará en la suscripción utilizada en el registro inicial de Azure Stack. Antes de poder agregar a un cliente final a Azure Stack para el seguimiento de uso y para administrar el inquilino, debe configurar Azure Stack como un CSP. Para información sobre los pasos y recursos, consulte [Administración del uso y la facturación para Azure Stack como un proveedor de servicios en la nube](azure-stack-add-manage-billing-as-a-csp.md).

La siguiente figura muestra los pasos que necesita seguir un CSP para habilitar un cliente nuevo en Azure Stack y para configurar el seguimiento de uso del cliente. Al agregar al cliente final, también puede administrar los recursos en Azure Stack. Tiene dos opciones para administrar los recursos:

1. Puede mantener el cliente final y proporcionar las credenciales para la suscripción de Azure Stack local al cliente final.  
2. El cliente final puede trabajar con su suscripción de forma local y agregar al CSP como invitado con permisos de propietario.  

## <a name="steps-to-add-an-end-customer"></a>Pasos para agregar a un cliente final

![Configuración del proveedor de servicios en la nube para el seguimiento de uso y la administración de la cuenta del cliente final](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Creación de un nuevo cliente en el centro de partners

En el centro de partners, cree una nueva suscripción de Azure para el cliente. Para obtener instrucciones, consulte [Adición de un nuevo cliente](https://msdn.microsoft.com/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Creación de una suscripción de Azure para el cliente final

Después de haber creado un registro del cliente en el centro de partners, le puede vender suscripciones a los productos del catálogo. Para obtener instrucciones, consulte [Creación, suspensión o cancelación de las suscripciones del cliente](https://msdn.microsoft.com/partner-center/create-a-new-subscription).

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Creación de un usuario invitado en el directorio de cliente final

Si el cliente final administra su propia cuenta, cree un usuario invitado en su directorio y envíeles la información. El usuario final, a continuación, agregará al invitado y elevará los permisos del invitado a **Propietario** para la cuenta del CSP de Azure Stack.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Actualización del registro con la suscripción del cliente final

Actualice el registro con la suscripción del nuevo cliente. Azure informa acerca del uso del cliente con la identidad del cliente del centro de partners. Este paso garantiza que se informa del uso de cada cliente de esa suscripción del CSP individual del cliente. Esto facilita el seguimiento del uso del usuario y la facturación.

> [!NOTE]  
> Para realizar este paso, debe tener [Azure Stack registrado](azure-stack-registration.md ).

1. Abra Windows PowerShell con un símbolo del sistema con privilegios elevados y ejecute:  
    `Add-AzureRmAccount -EnvironmentName AzureChinaCloud`
2. Escriba sus credenciales de Azure.
3. En la sesión de PowerShell, ejecute:

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties <PSObject>
   ```

### <a name="new-azurermresource-powershell-parameters"></a>Parámetros del comando de PowerShell New-AzureRmResource

En la siguiente sección se describen los parámetros para el cmdlet **New-AzureRmResource**:

| Parámetro | DESCRIPCIÓN |
| --- | --- |
|registrationSubscriptionID | La suscripción de Azure que se ha usado para el registro inicial de Azure Stack.|
| customerSubscriptionID | La suscripción de Azure (no de Azure Stack) que pertenece al cliente que se va a registrar. Se debe crear en la oferta del CSP; en la práctica, esto significa mediante el centro de partners. Si un cliente tiene más de un inquilino de Azure Active Directory, esta suscripción debe crearse en el inquilino que se usará para iniciar sesión en Azure Stack. El id. de suscripción de clientes debe usar letras minúsculas. |
| resourceGroup | El grupo de recursos de Azure en el que se almacena el registro. |
| registrationName | El nombre del registro de Azure Stack. Es un objeto almacenado en Azure. |
| Properties (Propiedades) | Especifica las propiedades del recurso. Use este parámetro para especificar los valores de las propiedades que son específicos del tipo de recurso.

> [!NOTE]  
> Los inquilinos se deben registrar en cada instancia de Azure Stack que utilicen. Si tiene dos implementaciones de Azure Stack y un inquilino utiliza ambas, debe actualizar los registros iniciales de cada implementación con la suscripción del inquilino.

### <a name="onboard-tenant-to-azure-stack"></a>Incorporación de un inquilino a Azure Stack

Puede configurar Azure Stack para admitir usuarios de varios inquilinos de Azure AD para el uso de los servicios de Azure Stack. Para obtener instrucciones, consulte [Habilitación de varios inquilinos en Azure Stack](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Creación de un recurso local en el inquilino del cliente final en Azure Stack

Una vez que haya agregado al nuevo cliente a Azure Stack o el inquilino del cliente final haya habilitado su cuenta de invitado con privilegios de propietario, compruebe que puede crear un recurso en el inquilino. Por ejemplo, pueden [crear una máquina virtual Windows con el portal de Azure Stack](../user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Pasos siguientes

- Para revisar los mensajes de error que se desencadenen en el proceso de registro, consulte [Mensajes de error del registro de inquilinos](azure-stack-csp-ref-infrastructure.md#usage-and-billing-error-codes).
- Para más información sobre cómo recuperar la información de utilización de recursos de Azure Stack, consulte [Uso y facturación en Azure Stack](azure-stack-billing-and-chargeback.md).
- Para revisar cómo un cliente final puede agregarle, en calidad de CSP, como el administrador de su inquilino de Azure Stack, consulte [Habilitación de un proveedor de servicios en la nube para administrar la suscripción de Azure Stack](../user/azure-stack-csp-enable-billing-usage-tracking.md).
