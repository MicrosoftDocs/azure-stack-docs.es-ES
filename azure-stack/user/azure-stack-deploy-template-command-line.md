---
title: Implementación de plantillas con la línea de comandos en Azure Stack | Microsoft Docs
description: Obtenga información acerca de cómo usar la interfaz de la línea de comandos (CLI) multiplataforma para implementar plantillas en Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 9584177f-4af3-4834-864d-930b09ae0995
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: fcf84ee394c917e896bc50d5d6a97f42191451e9
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64985598"
---
# <a name="deploy-templates-in-azure-stack-using-the-command-line"></a>Implementación de plantillas en Azure Stack mediante la línea de comandos

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Use la línea de comandos para implementar plantillas de Azure Resource Manager en el entorno del Kit de desarrollo de Azure Stack. Las plantillas de Azure Resource Manager implementan y aprovisionan todos los recursos para su aplicación en una única operación coordinada.

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

- Para más información sobre cómo implementar plantillas, consulte:

[Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
