---
title: Cambio del propietario de facturación de las suscripciones de usuario de Azure Stack Hub
description: Aprenda a cambiar el propietario de facturación de las suscripciones de usuario de Azure Stack Hub.
author: PatAltimore
ms.topic: conceptual
ms.date: 11/16/2020
ms.author: patricka
ms.reviewer: shnatara
ms.lastreviewed: 11/16/2020
ms.openlocfilehash: c5438aaf928efeee0b71470e0965c6b18b5a7c0a
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97871317"
---
# <a name="change-the-billing-owner-for-an-azure-stack-hub-user-subscription"></a>Cambio del propietario de facturación de las suscripciones de usuario de Azure Stack Hub

Los operadores de Azure Stack Hub pueden usar PowerShell para cambiar el propietario de facturación de una suscripción de usuario. Por ejemplo, una razón para cambiar el propietario es reemplazar un usuario que deja la organización.

Hay dos tipos de *propietarios* que se asignan a una suscripción:

- **Propietario de la facturación**: de forma predeterminada, es la cuenta de usuario que obtiene la suscripción de una oferta y, a continuación, posee la relación de facturación para esa suscripción. Esta cuenta también es la del administrador de la suscripción. Solo una cuenta de usuario puede tener esta designación en una suscripción. Un propietario de facturación a menudo es el responsable de una organización o equipo.

  Puede usar el cmdlet de PowerShell [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) para cambiar el propietario de la facturación.  

- **Propietarios agregados con roles RBAC**: se puede conceder el rol de **Propietario** a otros usuarios mediante el [control de acceso basado en rol](azure-stack-manage-permissions.md) (RBAC). Se puede agregar cualquier número de cuentas de usuario adicionales como propietarios para complementar al propietario de la facturación. Los propietarios adicionales también son administradores de la suscripción y tienen todos los privilegios para ella, excepto los permisos para eliminar al propietario de la facturación.

  Puede usar PowerShell para administrar los propietarios adicionales. Para obtener más información, consulte [este artículo](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>Cambio del propietario de la facturación

Ejecute el script siguiente para cambiar el propietario de la facturación de una suscripción de usuario. El equipo que se usa para ejecutar el script debe conectarse a Azure Stack Hub y ejecutar el módulo de Azure Stack PowerShell 1.3.0 u otro posterior. Para más información, consulte [Instalación de Azure Stack Hub PowerShell](powershell-install-az-module.md).

>[!NOTE]
>En una instancia multiinquilino de Azure Stack Hub, el nuevo propietario debe estar en el mismo directorio que el actual. Para poder proporcionar la propiedad de la suscripción a un usuario que se encuentre en otro directorio, primero debe [invitar a ese usuario como invitado en su directorio](/azure/active-directory/b2b/add-users-administrator).

Reemplace los valores siguientes en el script antes de que se ejecute:

- **$ArmEndpoint**: punto de conexión de Resource Manager para su entorno.
- **$TenantId**: El identificador de inquilino.
- **$SubscriptionId**: El identificador de suscripción.
- **$OwnerUpn**: una cuenta como **alguien\@example.com** para agregar como el nuevo propietario de la facturación.

### <a name="az-modules"></a>[Modules de Az](#tab/az)

```powershell
# Set up Azure Stack Hub admin environment
Add-AzEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

[!include[Remove Account](../includes/remove-account-az.md)]

### <a name="az-modules"></a>[Modules de Az](#tab/azurerm)

```powershell
# Set up AzureRMure Stack Hub admin environment
Add-AzureRMEnvironment -ARMEndpoint $ArmEndpoint -Name AzureRMureStack-admin
Add-AzureRMAccount -Environment AzureRMureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRMSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId"
Set-AzureRMContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzureRMsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzureRMsUserSubscription -InputObject $subscription
```
[!include[Remove Account](../includes/remove-account-azurerm.md)]
---




## <a name="next-steps"></a>Pasos siguientes

- [Administración del control de acceso basado en rol](azure-stack-manage-permissions.md)
