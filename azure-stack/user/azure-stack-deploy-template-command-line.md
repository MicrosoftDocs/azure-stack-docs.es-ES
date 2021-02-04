---
title: Implementación de una plantilla con la línea de comandos en Azure Stack Hub
description: Aprenda a usar la interfaz de la línea de comandos (CLI) multiplataforma de Azure para implementar plantillas en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 2/1/2021
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 38327d2e23fd74c1a5e0a197ccc2a32b65b3fa7a
ms.sourcegitcommit: a6f62a6693e48eb05272c01efb5ca24372875173
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/02/2021
ms.locfileid: "99247819"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack-hub"></a>Implementación de una plantilla con la línea de comandos en Azure Stack Hub

Puede usar la interfaz de la línea de comandos (CLI) de Azure para implementar plantillas de Azure Resource Manager en Azure Stack Hub. Las plantillas de Azure Resource Manager implementan y configuran los recursos para su aplicación en una única operación coordinada.

## <a name="deploy-template"></a>Implementar plantilla

1. Examine el [repositorio AzureStack-QuickStart-Templates](https://aka.ms/AzureStackGitHub) y busque la plantilla **101-create-storage-account**. Guarde la plantilla (`azuredeploy.json`) y los archivos de parámetros (`(azuredeploy.parameters.json`) en una ubicación de la unidad local, por ejemplo, `C:\templates\`.
2. Vaya a la carpeta en la que descargó los archivos. 
3. [Instale Azure Stack Hub y conéctese](azure-stack-version-profiles-azurecli2.md) a la CLI de Azure.
4. Actualice la región y la ubicación en el siguiente comando. Utilice `local` para el parámetro de ubicación si usa ASDK. Para implementar la plantilla:
    ```azurecli
    az group create --name testDeploy --location local
    az group deployment create --resource-group testDeploy --template-file ./azuredeploy.json --parameters ./azuredeploy.parameters.json
    ```

Este comando implementa la plantilla en el grupo de recursos **testDeploy** de la instancia de Azure Stack Hub.

## <a name="validate-template-deployment"></a>Validar la implementación de la plantilla

Para revisar el grupo de recursos y la cuenta de almacenamiento, ejecute los siguientes comandos de la CLI:

```azurecli
az group list
az storage account list
```

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [implementar plantillas mediante PowerShell](azure-stack-deploy-template-powershell.md).
