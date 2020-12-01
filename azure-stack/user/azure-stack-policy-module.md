---
title: Uso del módulo de directivas de Azure Stack Hub
description: Aprenda a restringir una suscripción de Azure para que se comporte como una suscripción de Azure Stack Hub
author: sethmanheim
ms.topic: article
ms.date: 11/22/2020
ms.author: sethm
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: 13d3e006d676e7e24f94741c59cb8837d5200d1d
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518133"
---
# <a name="manage-azure-policy-using-the-azure-stack-hub-policy-module"></a>Administración de la directiva de Azure con el módulo de directivas de Azure Stack Hub

El módulo de directivas de Azure Stack Hub le permite configurar una suscripción de Azure con la misma disponibilidad de servicios y control de versiones que Azure Stack Hub. El módulo utiliza el cmdlet de PowerShell [**New-AzPolicyDefinition**](/powershell/module/Az.resources/new-Azpolicydefinition) para crear una directiva de Azure. Esta directiva limita los tipos de recursos y los servicios que están disponibles en una suscripción. Después, puede crear una asignación de la directiva dentro del ámbito que corresponda utilizando el cmdlet [**New-AzPolicyAssignment**](/powershell/module/Az.resources/new-Azpolicyassignment). Después de configurar la directiva, puede usar su suscripción de Azure para desarrollar aplicaciones destinadas a Azure Stack Hub.

## <a name="install-the-module"></a>Instalación del módulo

1. Instale la versión apropiada del módulo Az PowerShell, tal y como se describe en el paso 1 de [Instalación de PowerShell para Azure Stack Hub](../operator/powershell-install-az-module.md).
2. [Descargue las herramientas de Azure Stack Hub desde GitHub](../operator/azure-stack-powershell-download.md).
3. [Configure PowerShell para utilizarlo con Azure Stack Hub](azure-stack-powershell-configure-user.md).
4. Importe el módulo **AzureStack.Policy.psm1**:

   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Aplicación de la directiva a la suscripción de Azure

Puede utilizar los comandos siguientes para aplicar una directiva predeterminada de Azure Stack Hub en su suscripción a Azure. Antes de ejecutar estos comandos, reemplace `Azure subscription name` por el nombre de la suscripción a Azure.

### <a name="az-modules"></a>[Modules de Az](#tab/az1)

```powershell
Add-AzAccount
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```
### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm1)

```powershell
Add-AzureRMAccount
$s = Select-AzureRMSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRMPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRMPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

---

## <a name="apply-policy-to-a-resource-group"></a>Aplicación de la directiva a un grupo de recursos

Quizá desee aplicar directivas más detalladas. Por ejemplo, puede tener otros recursos que se ejecuten en la misma suscripción. Puede delimitar la aplicación de directivas a un grupo de recursos específico, lo que le permite probar sus aplicaciones para Azure Stack Hub con recursos de Azure. Antes de ejecutar los comandos siguientes, reemplace `Azure subscription name` por el nombre de la suscripción a Azure:

### <a name="az-modules"></a>[Modules de Az](#tab/az2)

```powershell
Add-AzAccount
$rgName = 'myRG01'
$s = Select-AzSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```
### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm2)
 
```powershell
Add-AzureRMAccount
$rgName = 'myRG01'
$s = Select-AzureRMSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRMPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRMPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

---

## <a name="policy-in-action"></a>Directiva en acción

Una vez que haya implementado la directiva de Azure, recibirá un error si intenta implementar un recurso prohibido por la directiva:

![Resultado del error de implementación de un recurso debido a la restricción de la directiva](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
* [Implementación de plantillas con la CLI de Azure](azure-stack-deploy-template-command-line.md)
* [Implementación de plantillas con Visual Studio](azure-stack-deploy-template-visual-studio.md)
