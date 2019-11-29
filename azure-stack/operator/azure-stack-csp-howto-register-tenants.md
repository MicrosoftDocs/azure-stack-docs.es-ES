---
title: Adición de inquilinos en Azure Stack para uso y facturación | Microsoft Docs
description: Obtenga información sobre cómo agregar inquilinos para uso y facturación en Azure Stack.
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
ms.date: 09/25/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 09/17/2019
ms.openlocfilehash: a146a99476912e97c72e7a37ffc5224158feaffc
ms.sourcegitcommit: 0b783e262ac87ae67929dbd4c366b19bf36740f0
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/22/2019
ms.locfileid: "74310147"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack"></a>Adición de inquilinos en Azure Stack para uso y facturación

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se muestra cómo agregar un inquilino a una implementación de Azure Stack administrada por un proveedor de soluciones en la nube (CSP). Cuando el nuevo inquilino utiliza recursos, Azure Stack informa del uso a su suscripción de CSP.

Los CSP suelen ofrecen servicios a varios clientes finales (inquilinos) en su implementación de Azure Stack. Al agregar los inquilinos en el registro de Azure Stack se garantiza que el uso de cada inquilino se notifica y se factura a la suscripción del CSP correspondiente. Si no se completan los pasos descritos en este artículo, el uso realizado por los inquilinos se cargará en la suscripción utilizada en el registro inicial de Azure Stack. Antes de poder agregar a un cliente final a Azure Stack para el seguimiento de uso y para administrar el inquilino, debe configurar Azure Stack como un CSP. Para información sobre los pasos y recursos, consulte [Administración del uso y la facturación para Azure Stack como un proveedor de soluciones en la nube](azure-stack-add-manage-billing-as-a-csp.md).

La siguiente figura muestra los pasos que necesita seguir un CSP para habilitar un cliente final nuevo en Azure Stack y para configurar el seguimiento de uso del cliente. Al agregar al cliente final, también puede administrar los recursos en Azure Stack. Tiene dos opciones para administrar los recursos:

- Puede mantener el cliente final y proporcionar las credenciales para la suscripción de Azure Stack local al cliente final.  
- El cliente final puede trabajar con su suscripción de forma local y agregar al CSP como invitado con permisos de propietario.

## <a name="add-an-end-customer"></a>Agregar a un cliente final

Antes de agregar un cliente final, debe habilitar la facturación de varios inquilinos en el registro. Para habilitar la facturación de varios inquilinos, envíe el identificador de la suscripción del registro, el nombre del grupo de recursos y el nombre de registro a `azstcsp@microsoft.com`. El multiinquilino tarda normalmente 1 o 2 días hábiles en habilitarse.

Realice los pasos siguientes para agregar a un cliente final, como se muestra en la ilustración siguiente:

![Configuración del proveedor de soluciones en la nube para el seguimiento de uso y la administración de la cuenta del cliente final](media/azure-stack-csp-enable-billing-usage-tracking/process-csp-enable-billing.png)

### <a name="create-a-new-customer-in-partner-center"></a>Creación de un nuevo cliente en el centro de partners

En el centro de partners, cree una nueva suscripción de Azure para el cliente. Para obtener instrucciones, consulte [Adición de un nuevo cliente](/partner-center/add-a-new-customer).

### <a name="create-an-azure-subscription-for-the-end-customer"></a>Creación de una suscripción de Azure para el cliente final

Después de crear un registro del cliente en el centro de partners, le puede vender suscripciones a los productos del catálogo. Para obtener instrucciones, consulte [Creación, suspensión o cancelación de las suscripciones del cliente](/partner-center/create-a-new-subscription).

### <a name="create-a-guest-user-in-the-end-customer-directory"></a>Creación de un usuario invitado en el directorio de cliente final

De forma predeterminada, como CSP, no tendrá acceso a la suscripción de Azure Stack para clientes finales. Sin embargo, si el cliente quiere que usted administre sus recursos, puede agregar su cuenta como propietario o colaborador a su suscripción a Azure Stack. Para ello, deberán agregar la cuenta como usuario invitado a su inquilino de Azure AD. Se aconseja que utilice una cuenta diferente de la cuenta de Azure CSP para administrar la suscripción del cliente a Azure Stack y asegurarse de que no pierda el acceso a la suscripción de Azure del cliente.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Actualización del registro con la suscripción del cliente final

Actualice el registro con la suscripción del nuevo cliente. Azure informa acerca del uso del cliente con la identidad del cliente del Centro de partners. Este paso garantiza que se informa del uso de cada cliente de esa suscripción del CSP individual del cliente. Esto facilita el seguimiento del uso y la facturación. Para realizar este paso, debe [registrar primero Azure Stack](azure-stack-registration.md).

1. Abra Windows PowerShell con un símbolo del sistema con privilegios elevados y ejecute:  

   ```powershell
   Add-AzureRmAccount
   ```

   >[!Note]
   > Si la sesión expira, la contraseña ha cambiado o simplemente desea cambiar de cuenta, ejecute el siguiente cmdlet antes de iniciar sesión con Add-AzureRmAccount: `Remove-AzureRmAccount-Scope Process`

2. Escriba sus credenciales de Azure.
3. En la sesión de PowerShell, ejecute:

   ```powershell
   New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
   ```

### <a name="new-azurermresource-powershell-parameters"></a>Parámetros del comando de PowerShell New-AzureRmResource

En la siguiente sección se describen los parámetros para el cmdlet **New-AzureRmResource**:

| Parámetro | DESCRIPCIÓN |
| --- | --- |
|registrationSubscriptionID | La suscripción de Azure que se ha usado para el registro inicial de Azure Stack.|
| customerSubscriptionID | La suscripción de Azure (no de Azure Stack) que pertenece al cliente que se va a registrar. Debe crearse en la oferta de CSP. En la práctica, esto significa mediante el centro de partners. Si un cliente tiene más de un inquilino de Azure Active Directory, esta suscripción debe crearse en el inquilino que se usará para iniciar sesión en Azure Stack. El id. de suscripción de clientes debe usar letras minúsculas. |
| resourceGroup | El grupo de recursos de Azure en el que se almacena el registro. |
| registrationName | El nombre del registro de Azure Stack. Es un objeto almacenado en Azure. 

> [!NOTE]  
> Los inquilinos se deben registrar en cada instancia de Azure Stack que utilicen. Si tiene dos implementaciones de Azure Stack y un inquilino utiliza ambas, debe actualizar los registros iniciales de cada implementación con la suscripción del inquilino.

### <a name="onboard-tenant-to-azure-stack"></a>Incorporación de un inquilino a Azure Stack

Puede configurar Azure Stack para admitir usuarios de varios inquilinos de Azure AD para el uso de los servicios de Azure Stack. Para obtener instrucciones, consulte [Habilitación de varios inquilinos en Azure Stack](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack"></a>Creación de un recurso local en el inquilino del cliente final en Azure Stack

Una vez que haya agregado el nuevo cliente a Azure Stack o el inquilino del cliente final haya habilitado su cuenta de invitado con privilegios de propietario, compruebe que puede crear un recurso en el inquilino. Por ejemplo, pueden [crear una máquina virtual Windows con el portal de Azure Stack](../user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Pasos siguientes

- Para revisar los mensajes de error que se desencadenen en el proceso de registro, consulte [Mensajes de error del registro de inquilinos](azure-stack-registration-errors.md).
- Para más información sobre cómo recuperar la información de utilización de recursos de Azure Stack, consulte [Uso y facturación en Azure Stack](azure-stack-billing-and-chargeback.md).
- Para ver cómo puede agregarle un cliente final, en calidad de CSP, como administrador de su inquilino de Azure Stack, consulte [Habilitación de un proveedor de soluciones en la nube para administrar la suscripción de Azure Stack](../user/azure-stack-csp-enable-billing-usage-tracking.md).
