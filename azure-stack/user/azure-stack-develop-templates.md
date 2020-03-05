---
title: Desarrollo de plantillas para Azure Stack Hub
description: Aprenda a desarrollar plantillas de Azure Resource Manager para realizar la portabilidad de aplicaciones entre Azure y Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: unknown
ms.lastreviewed: 05/21/2019
ms.openlocfilehash: ce9ee8a982ade764947af3c6e2fb2f880cefc217
ms.sourcegitcommit: 4ac711ec37c6653c71b126d09c1f93ec4215a489
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/27/2020
ms.locfileid: "77702982"
---
# <a name="develop-templates-for-azure-stack-hub-with-azure-resource-manager"></a>Desarrollo de plantillas para Azure Stack Hub con Azure Resource Manager

Al desarrollar una aplicación, es importante garantizar la portabilidad de las plantillas entre Azure y Azure Stack Hub. En este artículo se proporcionan consideraciones para desarrollar [plantillas de Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf). Con estas plantillas, puede crear un prototipo de la aplicación y probar la implementación en Azure sin necesidad de acceder a un entorno de Azure Stack Hub.

## <a name="resource-provider-availability"></a>Disponibilidad del proveedor de recursos

La plantilla que planee implementar debe utilizar solo servicios de Microsoft Azure que ya estén disponibles, o bien que estén en versión preliminar, en Azure Stack Hub.

## <a name="public-namespaces"></a>Espacios de nombres públicos

Como Azure Stack Hub se hospeda en un centro de datos, tiene espacios de nombres de punto de conexión de servicio diferentes que los de la nube pública de Azure. Como resultado, se producirá un error en los puntos de conexión públicos codificados de forma rígida de las plantillas de Azure Resource Manager al tratar de implementarlos en Azure Stack Hub. Puede compilar dinámicamente puntos de conexión de servicio con las funciones `reference` y `concatenate` para recuperar valores del proveedor de recursos durante la implementación. Por ejemplo, en lugar de codificar de forma rígida `blob.core.windows.net` en la plantilla, recupere [primaryEndpoints.blob ](https://github.com/Azure/AzureStack-QuickStart-Templates/blob/master/101-vm-windows-create/azuredeploy.json#L175)para establecer dinámicamente el punto de conexión *osDisk.URI*:

```json
"osDisk": {"name": "osdisk","vhd": {"uri":
"[concat(reference(concat('Microsoft.Storage/storageAccounts/', variables('storageAccountName')), '2015-06-15').primaryEndpoints.blob, variables('vmStorageAccountContainerName'),
 '/',variables('OSDiskName'),'.vhd')]"}}
```

## <a name="api-versioning"></a>Control de versiones de la API

Las versiones de los servicios de Azure pueden diferir entre Azure y Azure Stack Hub. Cada recurso requiere el atributo **apiVersion**, que define las funcionalidades que ofrece. Para garantizar la compatibilidad de la versión de API en Azure Stack Hub, estas son las versiones de API válidas para cada proveedor de recursos:

| Proveedor de recursos | apiVersion |
| --- | --- |
| Proceso |**2015-06-15** |
| Red |**2015-06-15**, **2015-05-01-preview** |
| Storage |**2016-01-01**, **2015-06-15**, **2015-05-01-preview** |
| KeyVault | **2015-06-01** |
| App Service |**2015-08-01** |

## <a name="template-functions"></a>Funciones de plantillas

Las [funciones](/azure/azure-resource-manager/resource-group-template-functions) de Azure Resource Manager proporcionan las funcionalidades necesarias para crear plantillas dinámicas. Por ejemplo, puede utilizar funciones para tareas como las siguientes:

* Concatenar o recortar cadenas.
* Hacer referencia a valores de otros recursos.
* Efectuar una iteración en los recursos para implementar varias instancias.

Estas funciones no están disponibles en Azure Stack Hub:

* Omitir
* Take

## <a name="resource-location"></a>Ubicación de los recursos

Las plantillas de Azure Resource Manager usan un atributo `location` para incluir recursos durante la implementación. En Azure, las ubicaciones hacen referencia a una región, como Oeste de EE. UU. o Sudamérica. En Azure Stack Hub, las ubicaciones son diferentes porque Azure Stack Hub está en el centro de datos. Para asegurarse de que las plantillas pueden transferirse entre Azure y Azure Stack Hub, res preciso hacer referencia a la ubicación del grupo de recursos al implementar recursos individuales. Puede hacerlo mediante `[resourceGroup().Location]` para garantizar que todos los recursos hereden la ubicación del grupo de recursos. El siguiente código es un ejemplo de uso de esta función al implementar una cuenta de almacenamiento:

```json
"resources": [
{
  "name": "[variables('storageAccountName')]",
  "type": "Microsoft.Storage/storageAccounts",
  "apiVersion": "[variables('apiVersionStorage')]",
  "location": "[resourceGroup().location]",
  "comments": "This storage account is used to store the VM disks",
  "properties": {
  "accountType": "Standard_GRS"
  }
}
]
```

## <a name="next-steps"></a>Pasos siguientes

* [Implementación de plantillas con PowerShell](azure-stack-deploy-template-powershell.md)
* [Implementación de plantillas con la CLI de Azure](azure-stack-deploy-template-command-line.md)
* [Implementación de plantillas con Visual Studio](azure-stack-deploy-template-visual-studio.md)
