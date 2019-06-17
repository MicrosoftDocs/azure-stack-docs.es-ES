---
title: Implementación de una plantilla con la línea de comandos en Azure Stack | Microsoft Docs
description: Obtenga información acerca de cómo usar la interfaz de la línea de comandos (CLI) multiplataforma para implementar plantillas en Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: CLI
ms.topic: article
ms.date: 05/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: 92c9189f8144804f36e551ab89d8b4fc4c1f8598
ms.sourcegitcommit: 7f39bdc83717c27de54fe67eb23eb55dbab258a9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/05/2019
ms.locfileid: "66691368"
---
# <a name="deploy-a-template-with-the-command-line-in-azure-stack"></a>Implementación de una plantilla con la línea de comandos en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Use la interfaz de la línea de comandos (CLI) de Azure para implementar plantillas de Azure Resource Manager en Azure Stack. Las plantillas de Azure Resource Manager implementan y aprovisionan recursos para su aplicación en una única operación coordinada.

## <a name="before-you-begin"></a>Antes de empezar

- [Instale Azure Stack y conéctese](azure-stack-version-profiles-azurecli2.md) con la Interfaz de la línea de comandos de Azure.
- Descargue los archivos *azuredeploy.json* y *azuredeploy.parameters.json* desde la [plantilla de ejemplo para crear una cuenta de almacenamiento](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-create-storage-account).

## <a name="deploy-template"></a>Implementar plantilla

Navegue hasta la carpeta en la que se descargaron estos archivos y ejecute el siguiente comando para implementar la plantilla:

```azurecli
az group create "cliRG" "local" -f azuredeploy.json -d "testDeploy" -e azuredeploy.parameters.json
```

Este comando implementará la plantilla en el grupo de recursos **cliRG** en la ubicación predeterminada de la POC de Azure Stack.

## <a name="validate-template-deployment"></a>Validar la implementación de la plantilla

Para ver este grupo de recursos y esta cuenta de almacenamiento, use los siguientes comandos de la CLI:

```azurecli
az group list

az storage account list
```

## <a name="next-steps"></a>Pasos siguientes

Obtenga más información sobre la [implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md).
