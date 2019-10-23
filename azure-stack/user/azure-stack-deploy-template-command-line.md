---
title: Implementación de una plantilla con la línea de comandos en Azure Stack | Microsoft Docs
description: Obtenga información acerca de cómo usar la interfaz de la línea de comandos (CLI) multiplataforma de Azure para implementar plantillas en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 10/07/2019
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 7b3daaefd8fa7e7bce9c6d5708e664911fc906fe
ms.sourcegitcommit: 7226979ece29d9619c959b11352be601562b41d3
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/14/2019
ms.locfileid: "72304088"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>Implementación de una plantilla con la línea de comandos en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Puede usar la interfaz de la línea de comandos (CLI) de Azure para implementar plantillas de Azure Resource Manager en Azure Stack. Las plantillas de Azure Resource Manager implementan y configuran los recursos para su aplicación en una única operación coordinada.

## <a name="deploy-template"></a>Implementar plantilla

1. Examine el [repositorio AzureStack-QuickStart-Templates ](https://aka.ms/AzureStackGitHub) y busque la plantilla **101-create-storage-account**. Guarde la plantilla (`azuredeploy.json`) y los archivos de parámetros (`(azuredeploy.parameters.json`) en una ubicación de la unidad local, por ejemplo, `C:\templates\`.
2. Vaya a la carpeta en la que descargó los archivos. 
3. [Instale Azure Stack y conéctese](azure-stack-version-profiles-azurecli2.md) con la Interfaz de la línea de comandos de Azure.
4. Actualice la región y la ubicación en el siguiente comando. Utilice `local` para el parámetro de ubicación si usa ASDK. Para implementar la plantilla:
    ```azurecli
    az group create --name testDeploy --location local
    az group deployment create --resource-group testDeploy --template-file ./azuredeploy.json --parameters ./azuredeploy.parameters.json
    ```

Este comando implementa la plantilla en el grupo de recursos **testDeploy** en la instancia de Azure Stack.

## <a name="validate-template-deployment"></a>Validar la implementación de la plantilla

Para revisar el grupo de recursos y la cuenta de almacenamiento, ejecute los siguientes comandos de la CLI:

```azurecli
az group list
az storage account list
```

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [implementar plantillas mediante PowerShell](azure-stack-deploy-template-powershell.md).
