---
title: Uso del módulo de directivas de Azure Stack Hub | Microsoft Docs
description: Aprenda a restringir una suscripción de Azure para que se comporte como una suscripción de Azure Stack Hub
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2020
ms.author: sethm
ms.lastreviewed: 03/26/2019
ms.openlocfilehash: aedc2915f3e2be473b74766c4274197112ecf6fe
ms.sourcegitcommit: d450dcf5ab9e2b22b8145319dca7098065af563b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/11/2020
ms.locfileid: "75878754"
---
# <a name="manage-azure-policy-using-the-azure-stack-hub-policy-module"></a>Administración de la directiva de Azure con el módulo de directivas de Azure Stack Hub

El módulo de directivas de Azure Stack Hub le permite configurar una suscripción de Azure con la misma disponibilidad de servicios y control de versiones que Azure Stack Hub. El módulo utiliza el cmdlet de PowerShell [**New-AzureRmPolicyDefinition**](/powershell/module/azurerm.resources/new-azurermpolicydefinition) para crear una directiva de Azure. Esta directiva limita los tipos de recursos y los servicios que están disponibles en una suscripción. Después, puede crear una asignación de la directiva dentro del ámbito que corresponda utilizando el cmdlet [**New-AzureRmPolicyAssignment**](/powershell/module/azurerm.resources/new-azurermpolicyassignment). Después de configurar la directiva, puede usar su suscripción de Azure para desarrollar aplicaciones destinadas a Azure Stack Hub.

## <a name="install-the-module"></a>Instalación del módulo

1. Instale la versión apropiada del módulo AzureRM PowerShell, tal y como se describe en el paso 1 de [Instalación de PowerShell para Azure Stack Hub](../operator/azure-stack-powershell-install.md).
2. [Descargue las herramientas de Azure Stack Hub desde GitHub](../operator/azure-stack-powershell-download.md).
3. [Configure PowerShell para utilizarlo con Azure Stack Hub](azure-stack-powershell-configure-user.md).
4. Importe el módulo **AzureStack.Policy.psm1**:

   ```powershell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Aplicación de la directiva a la suscripción de Azure

Puede utilizar los comandos siguientes para aplicar una directiva predeterminada de Azure Stack Hub con su suscripción a Azure. Antes de ejecutar estos comandos, reemplace `Azure subscription name` por el nombre de la suscripción a Azure:

```powershell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID
```

## <a name="apply-policy-to-a-resource-group"></a>Aplicación de la directiva a un grupo de recursos

Quizá desee aplicar directivas más detalladas. Por ejemplo, puede tener otros recursos que se ejecuten en la misma suscripción. Puede delimitar la aplicación de directivas a un grupo de recursos específico, lo que le permite probar sus aplicaciones para Azure Stack Hub con recursos de Azure. Antes de ejecutar los comandos siguientes, reemplace `Azure subscription name` por el nombre de la suscripción a Azure:

```powershell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "Azure subscription name"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName
```

## <a name="policy-in-action"></a>Directiva en acción

Una vez que haya implementado la directiva de Azure, recibirá un error si intenta implementar un recurso prohibido por la directiva:

![Resultado del error de implementación de un recurso debido a la restricción de la directiva](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
* [Implementación de plantillas con la CLI de Azure](azure-stack-deploy-template-command-line.md)
* [Implementación de plantillas con Visual Studio](azure-stack-deploy-template-visual-studio.md)
