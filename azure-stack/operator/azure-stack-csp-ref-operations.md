---
title: Registro de inquilinos en Azure Stack para el seguimiento de uso | Microsoft Docs
description: Aprenda a registrar inquilinos y cómo se realiza el seguimiento del uso de estos en Azure Stack.
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
ms.date: 10/14/2019
ms.author: sethm
ms.reviewer: alfredop
ms.lastreviewed: 10/14/2019
ms.openlocfilehash: 72310e813d0dd0a64575f1b2452bf4a5191638ef
ms.sourcegitcommit: 97d41b3ebed07aa85a50087b6076671fd37e08c5
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/15/2019
ms.locfileid: "72350175"
---
# <a name="register-tenants-for-usage-tracking-in-azure-stack"></a>Registro de inquilinos en Azure Stack para el seguimiento de uso

*Se aplica a: Sistemas integrados de Azure Stack*

En este artículo se incluye información acerca de las operaciones de registro. Puede usar estas operaciones para:

- Administrar el registro de inquilinos
- Administrar el seguimiento del uso de inquilinos

## <a name="add-tenant-to-registration"></a>Adición de inquilinos al registro

Utilice la operación cuando desee agregar un nuevo inquilino a su registro. Se informa del uso de inquilinos en una suscripción de Azure conectada con el inquilino de Azure Active Directory (Azure AD).

También puede usar la operación para cambiar la suscripción asociada a un inquilino. Llame a PUT o a **New-AzureRMResource** para sobrescribir la asignación anterior.

Puede asociar una sola suscripción de Azure a un inquilino. Si intenta agregar una segunda suscripción a un inquilino existente, la primera suscripción se sobrescribe.

### <a name="use-api-profiles"></a>Uso de perfiles de API

Los cmdlets de registro siguientes requieren que se especifique un perfil de API cuando se ejecuta PowerShell. Los perfiles de API representan un conjunto de proveedores de recursos de Azure y sus versiones de API. Le ayudan a usar la versión correcta de la API cuando interactúa con varias nubes de Azure. Por ejemplo, si trabaja con varias nubes al trabajar con la plataforma global de Azure y Azure Stack, los perfiles de API especifican un nombre que coincide con la fecha de la versión. Use el perfil **2017-09-03**.

Para más información sobre los perfiles de API y Azure Stack, consulte [Administración de perfiles de versión de API en Azure Stack](../user/azure-stack-version-profiles.md).

### <a name="parameters"></a>Parámetros

| Parámetro                  | DESCRIPCIÓN |
|---                         | --- |
| registrationSubscriptionID | La suscripción de Azure que se ha usado para el registro inicial. |
| customerSubscriptionID     | La suscripción de Azure (no Azure Stack) a la que pertenece el cliente que se va a registrar. Debe crearse en la oferta del proveedor de soluciones en la nube (CSP) mediante el Centro de partners. Si un cliente tiene más de un inquilino, cree una suscripción para que el inquilino inicie sesión en Azure Stack. |
| resourceGroup              | El grupo de recursos de Azure en el que se almacena el registro. |
| registrationName           | El nombre del registro de Azure Stack. Es un objeto almacenado en Azure. El nombre suele ser **azurestack-CloudID**, donde **CloudID** es el identificador de nube de su implementación de Azure Stack. |

> [!NOTE]  
> Los inquilinos se deben registrar en cada implementación de Azure Stack que utilicen. Si un inquilino usa más de una implementación de Azure Stack, actualice los registros iniciales de cada implementación con la suscripción del inquilino.

### <a name="powershell"></a>PowerShell

Use el cmdlet **New-AzureRmResource** para agregar un inquilino. [Conéctese a Azure Stack](azure-stack-powershell-configure-admin.md) y, después, desde un símbolo del sistema con privilegios elevados, use el siguiente cmdlet:

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

| Parámetro                  | DESCRIPCIÓN          |
|---                         | ---                  |
| registrationSubscriptionId | La suscripción de Azure que se ha usado para el registro inicial.   |
| resourceGroup              | El grupo de recursos de Azure en el que se almacena el registro.    |
| registrationName           | El nombre del registro de su implementación de Azure Stack. Es un objeto almacenado en Azure. El nombre suele ser **azurestack-CloudID**, donde **CloudID** es el identificador de nube de su implementación de Azure Stack.   |

### <a name="powershell"></a>PowerShell

Use el cmdlet **Get-AzureRmResource** para enumerar todos los inquilinos registrados. [Conéctese a Azure Stack](azure-stack-powershell-configure-admin.md) y, después, desde un símbolo del sistema con privilegios elevados, ejecute el siguiente cmdlet:

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

Puede quitar un inquilino que se haya agregado a un registro. Si ese inquilino todavía está usando recursos en Azure Stack, su uso se cobra a la suscripción utilizada en el registro inicial de Azure Stack.

### <a name="parameters"></a>Parámetros

| Parámetro                  | DESCRIPCIÓN          |
|---                         | ---                  |
| registrationSubscriptionId | Identificador de suscripción para el registro.   |
| resourceGroup              | El grupo de recursos para el registro.   |
| registrationName           | Nombre del registro.  |
| customerSubscriptionId     | Identificador de suscripción del cliente.  |

### <a name="powershell"></a>PowerShell

Use el cmdlet **Remove-AzureRmResource** para quitar un inquilino. [Conéctese a Azure Stack](azure-stack-powershell-configure-admin.md) y, después, desde un símbolo del sistema con privilegios elevados, ejecute el siguiente cmdlet:

```powershell
Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Llamada a la API

Se pueden quitar las asignaciones de inquilinos mediante la operación DELETE.

**Operation**: DELETE  
**URI de solicitud**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Respuesta**: 204 No Content  
**Cuerpo de respuesta**: Vacío

## <a name="next-steps"></a>Pasos siguientes

- [Cómo recuperar la información de uso de los recursos de Azure Stack](azure-stack-billing-and-chargeback.md)
