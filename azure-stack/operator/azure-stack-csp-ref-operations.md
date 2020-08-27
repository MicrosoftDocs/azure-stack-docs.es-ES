---
title: Registro de inquilinos en Azure Stack Hub para el seguimiento de uso
description: Aprenda a registrar inquilinos y cómo se realiza el seguimiento del uso de estos en Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 08/24/2020
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/14/2019
ms.openlocfilehash: ac318c1352963a7ee0fb873adaae4b30fa58949b
ms.sourcegitcommit: d73637146daaba0ef0ab1729683bb52c65466099
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/25/2020
ms.locfileid: "88857517"
---
# <a name="register-tenants-for-usage-tracking-in-azure-stack-hub"></a>Registro de inquilinos en Azure Stack Hub para el seguimiento de uso

En este artículo se incluye información acerca de las operaciones de registro. Puede usar estas operaciones para:

- Administrar los registros de inquilinos.
- Administrar el seguimiento del uso de los inquilinos.

## <a name="add-tenant-to-registration"></a>Adición de inquilinos al registro

Utilice la operación cuando desee agregar un nuevo inquilino a su registro. Se informa del uso de inquilinos en una suscripción de Azure conectada con el inquilino de Azure Active Directory (Azure AD).

También puede usar la operación para cambiar la suscripción asociada a un inquilino. Llame a PUT o al cmdlet **New-AzureRMResource** de PowerShell para sobrescribir la asignación anterior.

Puede asociar una sola suscripción de Azure a un inquilino. Si intenta agregar una segunda suscripción a un inquilino existente, la primera suscripción se sobrescribe.

### <a name="use-api-profiles"></a>Uso de perfiles de API

Los cmdlets de registro siguientes requieren que se especifique un perfil de API cuando se ejecuta PowerShell. Los perfiles de API representan un conjunto de proveedores de recursos de Azure y sus versiones de API. Le ayudan a usar la versión correcta de la API cuando interactúa con varias nubes de Azure. Por ejemplo, si trabaja con varias nubes al trabajar con la plataforma global de Azure y Azure Stack Hub, los perfiles de API especifican un nombre que coincide con la fecha de la versión. Use el perfil **2017-09-03**.

Para más información sobre los perfiles de API y Azure Stack Hub, consulte [Administración de perfiles de versión de API en Azure Stack Hub](../user/azure-stack-version-profiles.md).

### <a name="parameters"></a>Parámetros

| Parámetro                  | Descripción |
|---                         | --- |
| registrationSubscriptionID | La suscripción de Azure que se ha usado para el registro inicial. |
| customerSubscriptionID     | La suscripción de Azure (no Azure Stack Hub) a la que pertenece el cliente que se va a registrar. Debe crearse en la oferta del proveedor de soluciones en la nube (CSP) mediante el Centro de partners. Si un cliente tiene más de un inquilino, cree una suscripción para que el inquilino inicie sesión en Azure Stack Hub. El identificador de suscripción del cliente distingue mayúsculas y minúsculas. |
| resourceGroup              | El grupo de recursos de Azure en el que se almacena el registro. |
| registrationName           | El nombre del registro de Azure Stack Hub. Es un objeto almacenado en Azure. El nombre suele ser **azurestack-CloudID**, donde **CloudID** es el identificador de nube de su implementación de Azure Stack Hub. |

> [!NOTE]  
> Los inquilinos se deben registrar en cada implementación de Azure Stack Hub que utilicen. Si un inquilino usa más de una implementación de Azure Stack Hub, actualice los registros iniciales de cada implementación con la suscripción del inquilino.

### <a name="powershell"></a>PowerShell

Use el cmdlet **New-AzureRmResource** para agregar un inquilino. [Conéctese a Azure Stack Hub](azure-stack-powershell-configure-admin.md) y, después, desde un símbolo del sistema con privilegios elevados, use el siguiente cmdlet:

```powershell  
New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Llamada a la API

**Operation**: PUT  
**URI de solicitud**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Respuesta**: 201 Creado  
**Cuerpo de respuesta**: Vacío  

## <a name="list-all-registered-tenants"></a>Lista de todos los inquilinos registrados

Obtenga una lista de todos los inquilinos que se han agregado a un registro.

 > [!NOTE]  
 > Si no hay inquilinos registrados, no recibirá respuesta.

### <a name="parameters"></a>Parámetros

| Parámetro                  | Descripción          |
|---                         | ---                  |
| registrationSubscriptionId | La suscripción de Azure que se ha usado para el registro inicial.   |
| resourceGroup              | El grupo de recursos de Azure en el que se almacena el registro.    |
| registrationName           | El nombre del registro de su implementación de Azure Stack Hub. Es un objeto almacenado en Azure. El nombre suele ser **azurestack-CloudID**, donde **CloudID** es el identificador de nube de su implementación de Azure Stack Hub.   |

### <a name="powershell"></a>PowerShell

Use el cmdlet **Get-AzureRmResource** para enumerar todos los inquilinos registrados. [Conéctese a Azure Stack Hub](azure-stack-powershell-configure-admin.md) y, después, desde un símbolo del sistema con privilegios elevados, ejecute el siguiente cmdlet:

```powershell
Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Llamada a la API

Puede obtener una lista de todas las asignaciones de inquilinos mediante la operación GET.

**Operation**: GET  
**URI de solicitud**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?api-version=2017-06-01 HTTP/1.1`  
**Respuesta**: 200  
**Cuerpo de respuesta**:

```json
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}",
            "name": " cspSubscriptionId 1",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}",
            "name": " cspSubscriptionId2 ",
            "type": "Microsoft.AzureStack\customerSubscriptions",
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Eliminación de una asignación de inquilinos

Puede quitar un inquilino que se haya agregado a un registro. Si ese inquilino todavía está usando recursos en Azure Stack Hub, su uso se cobra a la suscripción utilizada en el registro inicial de Azure Stack Hub.

### <a name="parameters"></a>Parámetros

| Parámetro                  | Descripción          |
|---                         | ---                  |
| registrationSubscriptionId | Identificador de suscripción para el registro.   |
| resourceGroup              | El grupo de recursos para el registro.   |
| registrationName           | Nombre del registro.  |
| customerSubscriptionId     | Identificador de suscripción del cliente. El identificador de suscripción del cliente distingue mayúsculas y minúsculas.  |

### <a name="powershell"></a>PowerShell

Use el cmdlet **Remove-AzureRmResource** para quitar un inquilino. [Conéctese a Azure Stack Hub](azure-stack-powershell-configure-admin.md) y, después, desde un símbolo del sistema con privilegios elevados, ejecute el siguiente cmdlet:

```powershell
Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Llamada a la API

Se pueden quitar las asignaciones de inquilinos mediante la operación DELETE.

**Operation**: Delete  
**URI de solicitud**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Respuesta**: 204 No Content  
**Cuerpo de respuesta**: Vacío

## <a name="next-steps"></a>Pasos siguientes

- [Recuperación de la información de uso de los recursos de Azure Stack Hub](azure-stack-billing-and-chargeback.md)
