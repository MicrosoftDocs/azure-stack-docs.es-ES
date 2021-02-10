---
title: Adición de inquilinos a Azure Stack Hub para uso y facturación
description: Aprenda a agregar inquilinos a Azure Stack Hub para uso y facturación.
author: sethmanheim
ms.topic: article
ms.date: 11/17/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 11/17/2020
ms.openlocfilehash: 7db231e1bca513bf3755f8e9c078fb40d00cfa09
ms.sourcegitcommit: e88f0a1f2f4ed3bb8442bfb7b754d8b3a51319b4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/04/2021
ms.locfileid: "99533848"
---
# <a name="add-tenant-for-usage-and-billing-to-azure-stack-hub"></a>Adición de inquilinos a Azure Stack Hub para uso y facturación

En este artículo se indica cómo agregar un inquilino a una implementación de Azure Stack Hub administrada por un proveedor de soluciones en la nube (CSP). Cuando el nuevo inquilino usa recursos, Azure Stack Hub informa del uso a su suscripción de CSP.

Los CSP suelen ofrecen servicios a varios clientes finales (inquilinos) en su implementación de Azure Stack Hub. Al agregar inquilinos al registro de Azure Stack Hub se garantiza que el uso de cada inquilino se notifica en la suscripción del CSP correspondiente y se factura a esta. Si no se completan los pasos descritos en este artículo, el uso realizado por los inquilinos se cargará en la suscripción usada en el registro inicial de Azure Stack Hub. Antes de poder agregar a un cliente final a Azure Stack Hub para el seguimiento del uso y para administrar el inquilino, debe configurar Azure Stack Hub como un CSP. Para información sobre los pasos y los recursos, consulte [Administración del uso y la facturación de Azure Stack Hub como proveedor de soluciones en la nube](azure-stack-add-manage-billing-as-a-csp.md).

En la siguiente ilustración se muestran los pasos que necesita seguir un CSP para permitir que un nuevo cliente final use Azure Stack Hub y para configurar el seguimiento del uso del cliente. Al agregar al cliente final, también puede administrar los recursos de Azure Stack Hub. Tiene dos opciones para administrar los recursos:

- Puede mantener el cliente final y proporcionar las credenciales para la suscripción de Azure Stack Hub local al cliente final.  
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

De forma predeterminada, como CSP, no tiene acceso a la suscripción de Azure Stack Hub del cliente final. Sin embargo, si el cliente quiere que usted administre sus recursos, puede agregar su cuenta como propietario o colaborador a su suscripción a Azure Stack Hub. Para ello, deberán agregar la cuenta como usuario invitado a su inquilino de Azure AD. Se aconseja que use una cuenta diferente a la cuenta de Azure CSP para administrar la suscripción de Azure Stack Hub del cliente a fin de asegurarse de que no pierda el acceso a la suscripción de Azure del cliente.

### <a name="update-the-registration-with-the-end-customer-subscription"></a>Actualización del registro con la suscripción del cliente final

Actualice el registro con la suscripción del nuevo cliente. Azure informa acerca del uso del cliente con la identidad del cliente del Centro de partners. Este paso garantiza que se informa del uso de cada cliente de esa suscripción del CSP individual del cliente. Esto facilita el seguimiento del uso y la facturación. Para realizar este paso, debe [registrar primero Azure Stack Hub](azure-stack-registration.md).

### <a name="az-modules"></a>[Modules de Az](#tab/az)

1. Abra Windows PowerShell en un símbolo del sistema con privilegios elevados y ejecute:  

   ```powershell
   Connect-AzAccount
   ```

   >[!NOTE]
   > Si la sesión expira, la contraseña ha cambiado o simplemente desea cambiar de cuenta, ejecute el siguiente cmdlet antes de iniciar sesión con **Connect-AzAccount**: `Remove-AzAccount-Scope Process`.

2. Escriba sus credenciales de Azure.
3. En la sesión de PowerShell, ejecute:

   ```powershell
   New-AzResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
   ```

**Parámetros del comando de PowerShell de New-AzResource**

En la siguiente sección se describen los parámetros para el cmdlet **New-AzResource**:

| Parámetro | Descripción |
| --- | --- |
|registrationSubscriptionID | La suscripción de Azure que se ha usado para el registro inicial de Azure Stack Hub.|
| customerSubscriptionID | La suscripción de Azure (no Azure Stack Hub) a la que pertenece el cliente que se va a registrar. Debe crearse en la oferta de CSP. En la práctica, esto significa mediante el centro de partners. Si un cliente tiene más de un inquilino de Azure Active Directory, esta suscripción debe crearse en el inquilino que se usará para iniciar sesión en Azure Stack Hub. El identificador de suscripción del cliente distingue mayúsculas y minúsculas. |
| resourceGroup | El grupo de recursos de Azure en el que se almacena el registro. |
| registrationName | El nombre del registro de Azure Stack Hub. Es un objeto almacenado en Azure.

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm)

1. Abra Windows PowerShell en un símbolo del sistema con privilegios elevados y ejecute:  

   ```powershell
   Add-AzureRMAccount
   ```

   >[!NOTE]
   > Si la sesión expira, la contraseña ha cambiado o simplemente desea cambiar de cuenta, ejecute el siguiente cmdlet antes de iniciar sesión con **Connect-AzAccount**: `Remove-AzAccount-Scope Process`.

2. Escriba sus credenciales de Azure.
3. En la sesión de PowerShell, ejecute:

   ```powershell
   New-AzureRMResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
   ```

**Parámetros del comando de PowerShell New-AzureRMResource**

En la siguiente sección se describen los parámetros para el cmdlet **New-AzureRMResource**:

| Parámetro | Descripción |
| --- | --- |
|registrationSubscriptionID | La suscripción de Azure que se ha usado para el registro inicial de Azure Stack Hub.|
| customerSubscriptionID | La suscripción de Azure (no Azure Stack Hub) a la que pertenece el cliente que se va a registrar. Debe crearse en la oferta de CSP. En la práctica, esto significa mediante el centro de partners. Si un cliente tiene más de un inquilino de Azure Active Directory, esta suscripción debe crearse en el inquilino que se usará para iniciar sesión en Azure Stack Hub. El identificador de suscripción del cliente distingue mayúsculas y minúsculas. |
| resourceGroup | El grupo de recursos de Azure en el que se almacena el registro. |
| registrationName | El nombre del registro de Azure Stack Hub. Es un objeto almacenado en Azure.

---


> [!NOTE]  
> Los inquilinos se deben registrar en cada instancia de Azure Stack Hub que usen. Si tiene dos implementaciones de Azure Stack Hub y un inquilino usa ambas, debe actualizar los registros iniciales de cada implementación con la suscripción del inquilino.

### <a name="onboard-tenant-to-azure-stack-hub"></a>Incorporación de un inquilino a Azure Stack Hub

Configure Azure Stack Hub para permitir que los usuarios de varios inquilinos de Azure AD usen los servicios de Azure Stack Hub. Si necesita instrucciones, consulte [Habilitación de los servicios multiinquilino en Azure Stack Hub](azure-stack-enable-multitenancy.md).

### <a name="create-a-local-resource-in-the-end-customer-tenant-in-azure-stack-hub"></a>Creación de un recurso local en el inquilino del cliente final en Azure Stack Hub

Cuando haya agregado el nuevo cliente a Azure Stack Hub o el inquilino del cliente final haya habilitado su cuenta de invitado con privilegios de propietario, compruebe que puede crear un recurso en su inquilino. Por ejemplo, pueden [crear una máquina virtual Windows con el portal de Azure Stack Hub](../user/azure-stack-quick-windows-portal.md).

## <a name="next-steps"></a>Pasos siguientes

- Para revisar los mensajes de error que se desencadenen en el proceso de registro, consulte [Mensajes de error del registro de inquilinos](azure-stack-registration-errors.md).
- Para más información sobre cómo recuperar la información del uso de recursos de Azure Stack Hub, consulte [Uso y facturación en Azure Stack Hub](azure-stack-billing-and-chargeback.md).
- Para ver cómo puede agregarle un cliente final, en calidad de CSP, como administrador de su inquilino de Azure Stack Hub, consulte [Habilitación de un proveedor de soluciones en la nube para administrar la suscripción de Azure Stack Hub](../user/azure-stack-csp-enable-billing-usage-tracking.md).
