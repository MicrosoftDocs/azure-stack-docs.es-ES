---
title: Uso de PowerShell para administrar suscripciones, planes y ofertas en Azure Stack Hub
description: Cómo administrar suscripciones, planes y ofertas en Azure Stack Hub con PowerShell.
author: PatAltimore
ms.topic: how-to
ms.date: 12/18/2020
ms.author: patricka
ms.lastreviewed: 12/18/2020
ms.reviewer: bganapa
ms.openlocfilehash: 3dcc3eae565f2e2a41078c2696a09f60ffa618b9
ms.sourcegitcommit: e13f27291bab236aac5d8b05401056961e9cc1e9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/19/2020
ms.locfileid: "97697870"
---
# <a name="use-powershell-to-manage-subscriptions-plans-and-offers-in-azure-stack-hub"></a>Uso de PowerShell para administrar suscripciones, planes y ofertas en Azure Stack Hub

Puede usar PowerShell para configurar y entregar servicios por medio de ofertas, planes y suscripciones. mediante PowerShell. Para obtener instrucciones sobre cómo configurar PowerShell en Azure Stack Hub, consulte [Instalación del módulo Az de PowerShell para Azure Stack Hub](powershell-install-az-module.md). Para más información sobre cómo conectarse a Azure Stack Hub con PowerShell, consulte [Conexión a Azure Stack Hub con PowerShell](azure-stack-powershell-configure-admin.md).

Antes de empezar, compruebe que el módulo de PowerShell para Azure Stack Hub está cargado. En una consola de PowerShell, escriba `Import-Module AzureStack`.

## <a name="create-a-plan"></a>Creación de un plan

Las cuotas son necesarias al crear un plan. Puede usar las cuotas existentes o crear unas nuevas. Por ejemplo, para crear una cuota de almacenamiento, proceso y red, puede usar los cmdlets [New-AzsStorageQuota](/powershell/module/azs.storage.admin/new-azsstoragequota), [New-AzsComputeQuota](/powershell/module/azs.compute.admin/new-azscomputequota) y [New-AzsNetworkQuota](/powershell/module/azs.network.admin/new-azsnetworkquota).

```powershell
$serviceQuotas  = @()
$serviceQuotas += (New-AzsStorageQuota -Name "Example storage quota with defaults").Id
$serviceQuotas += (New-AzsComputeQuota -Name "Example compute quota with defaults").Id
$serviceQuotas += (New-AzsNetworkQuota -Name "Example network quota with defaults").Id
```

Para crear o actualizar una base o un plan complementario, use [New-AzsPlan](/powershell/module/azs.subscriptions.admin/new-azsplan).

```powershell
$testPlan = New-AzsPlan -Name "testplan" -ResourceGroupName "testrg" -QuotaIds $serviceQuotas -Description "Test plan"
```

## <a name="create-an-offer"></a>Creación de una oferta

Para crear una oferta, use [New-AzsOffer](/powershell/module/azs.subscriptions.admin/new-azsoffer).

```powershell
New-AzsOffer -Name "testoffer" -ResourceGroupName "testrg" -BasePlanIds @($testPlan.Id)
```

Una vez que tenga una oferta, puede agregarle planes. Use [Add-AzsPlanToOffer](/powershell/module/azs.subscriptions.admin/add-azsplantooffer). El parámetro **-PlanLinkType** distingue el tipo de plan.

```powershell
Add-AzsPlanToOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg" -MaxAcquisitionCount 18
```

Si desea cambiar el estado de una oferta, use el cmdlet [Set-AzsOffer](/powershell/module/azs.subscriptions.admin/set-azsoffer).

```powershell
$offer = Get-AzsAdminManagedOffer -Name "testoffer" -ResourceGroupName "testrg"
$offer.state = "Public"
$offer | Set-AzsOffer -Confirm:$false
```

## <a name="create-subscription-to-an-offer"></a>Creación de una suscripción a una oferta

Después de crear una oferta, los usuarios necesitan una suscripción a dicha oferta antes de que puedan utilizarla. Hay dos formas en las que los usuarios pueden suscribirse a una oferta:

* Como operador de nube, puede crear una suscripción para un usuario. Las suscripciones que cree pueden ser para ofertas tanto públicas como privadas.
* Como usuario, puede suscribirse a una oferta pública.

Para crear una suscripción para un usuario como operador de nube, use [New-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/new-azsusersubscription).

```powershell
New-AzsUserSubscription -Owner "user@contoso.com" -DisplayName "User subscription" -OfferId "/subscriptions/<Subscription ID>/resourceGroups/testrg/providers/Microsoft.Subscriptions.Admin/offers/testoffer"
```

Para suscribirse a una oferta pública como usuario, utilice [New-AzsSubscription](/powershell/module/azs.subscriptions/new-azssubscription). *New-AzsSubscription* requiere una conexión al entorno de Azure Resource Manager del usuario. Siga los pasos descritos en [Conexión a Azure Stack Hub con PowerShell](azure-stack-powershell-configure-admin.md), pero utilice el punto de conexión de Azure Resource Manager del usuario. Por ejemplo, `Add-AzEnvironment -Name "AzureStackUser" -ArmEndpoint "https://management.local.azurestack.external"`.

```powershell
$testOffer = Get-AzsOffer | Where-Object Name -eq "testoffer"
New-AzsSubscription -OfferId "User subscription" -OfferId $testOffer.Id -DisplayName "My subscription"
```

## <a name="delete-quotas-plans-offers-and-subscriptions"></a>Eliminación de cuotas, planes, ofertas y suscripciones

Hay cmdlets de PowerShell complementarios para eliminar suscripciones, planes, ofertas y cuotas de Azure Stack Hub. A continuación, se muestran ejemplos de cada uno.

Use [Remove-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/remove-azsusersubscription) para quitar una suscripción de una oferta.

```powershell
Remove-AzsUserSubscription -TargetSubscriptionId "c90173b1-de7a-4b1d-8600-b8325ca1eab1e"
```

Para quitar un plan de una oferta, utilice [Remove-AzsPlanFromOffer](/powershell/module/azs.subscriptions.admin/remove-azsplanfromoffer).

```powershell
Remove-AzsPlanFromOffer -PlanName "addonplan" -PlanLinkType Addon -OfferName "testoffer" -ResourceGroupName "testrg"
Remove-AzsPlanFromOffer -PlanName "testplan" -PlanLinkType Base -OfferName "testoffer" -ResourceGroupName "testrg"
```

Use [Remove-AzsPlan](/powershell/module/azs.subscriptions.admin/remove-azsplan) para quitar un plan.

```powershell
Remove-AzsPlan -Name "testplan" -ResourceGroupName "testrg"
```

Use [Remove-AzsOffer](/powershell/module/azs.subscriptions.admin/remove-azsoffer) para quitar una oferta.

```powershell
Remove-AzsOffer -Name "testoffer" -ResourceGroupName "testrg"
```

Para quitar cuotas, use [Remove-AzsStorageQuota](/powershell/module/azs.storage.admin/remove-azsstoragequota), [Remove-AzsComputeQuota](/powershell/module/azs.compute.admin/remove-azscomputequota), [Remove-AzsNetworkQuota](/powershell/module/azs.network.admin/remove-azsnetworkquota).

```powershell
Remove-AzsStorageQuota -Name "Example storage quota with defaults"
Remove-AzsComputeQuota -Name "Example compute quota with defaults"
Remove-AzsNetworkQuota -Name "Example network quota with defaults"
```

## <a name="next-steps"></a>Pasos siguientes

- [Introducción a la administración de actualizaciones en Azure Stack Hub](./azure-stack-updates.md)
