---
title: Implementación con Visual Studio Code en Azure Stack | Microsoft Docs
description: Como usuario, quiero crear una plantilla de Azure Resource Manager en Visual Studio Code y usar el esquema de implementación para preparar una plantilla que sea compatible con mi versión de Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/30/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 09/30/2019
ms.openlocfilehash: 914e3e8db57009d58a14aa87d24ff86a8291e52b
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2019
ms.locfileid: "71711106"
---
# <a name="deploy-with-visual-studio-code-to-azure-stack"></a>Implementación con Visual Studio Code en Azure Stack

Puede usar Visual Studio Code y la extensión de Herramientas de Azure Resource Manager para crear y editar las plantillas correspondientes que funcionen con la versión de Azure Stack. Puede crear plantillas de Resource Manager en Visual Studio Code sin la extensión, pero la extensión proporciona opciones de autocompletar que simplifican el desarrollo de la plantilla. Además, puede especificar un esquema de implementación que le ayudará a conocer los recursos disponibles en Azure Stack.

En este artículo, implementará una máquina virtual Windows.

## <a name="concepts-for-azure-stack-resource-manager"></a>Conceptos de Resource Manager para Azure Stack

### <a name="azure-stack-resource-manager"></a>Administrador de recursos de Azure Stack

Para entender los conceptos asociados a la implementación y administración de sus soluciones de Azure en Azure Stack, consulte [Uso de plantillas de Azure Resource Manager en Azure Stack](azure-stack-arm-templates.md).

### <a name="api-profiles"></a>Perfiles de API
Para comprender los conceptos asociados a la coordinación de proveedores de recursos en Azure Stack consulte [Administración de perfiles de la versión de API en Azure Stack](azure-stack-version-profiles.md).

### <a name="the-deployment-schema"></a>El esquema de implementación

El esquema de implementación de Azure Stack admite perfiles híbridos mediante las plantillas de Azure Resource Manager en Visual Studio Code. Puede cambiar una línea en la plantilla JSON para hacer referencia al esquema y luego puede usar IntelliSense para revisar el recurso compatible con Azure. Con el esquema, revise los proveedores de recursos, los tipos y las versiones de API compatibles con su versión de Azure Stack. El esquema depende del perfil de API para recuperar las versiones específicas de los puntos de conexión de API en los proveedores de recursos admitidos en su versión de Azure Stack. Puede usar la finalización de palabra para los tipos y los valores de apiVersion y, posteriormente, se limitará a los tipos de recursos y valores de apiVersion disponibles para el perfil de API.

## <a name="prerequisites"></a>Requisitos previos

- [Visual Studio Code](https://code.visualstudio.com/)
- Acceso a Azure Stack
- [PowerShell para Azure Stack instalado](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install?toc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fuser%2FTOC.json&bc=https%3A%2F%2Fdocs.microsoft.com%2Fen-us%2Fazure-stack%2Fbreadcrumb%2Ftoc.json) en una máquina que alcanza los puntos de conexión de administración

## <a name="install-resource-manager-tools-extension"></a>Instalación de la extensión Herramientas de Resource Manager

Para instalar la extensión de Herramientas de Resource Manager, realice estos pasos:

1. Abra Visual Studio Code.
2. Presione CTRL+MAYÚS+X para abrir el panel de extensiones.
3. Busque `Azure Resource Manager Tools` y, después, seleccione **Instalar**.
4. Seleccione **Recargar** para finalizar la instalación de la extensión.

## <a name="get-a-template"></a>Obtención de una plantilla

En lugar de crear una plantilla desde cero, abra una plantilla de AzureStack-QuickStart-Templates (https://github.com/Azure/AzureStack-QuickStart-Templates). AzureStack-QuickStart-Templates es un repositorio de plantillas de Resource Manager que implementa recursos en Azure Stack. 

La plantilla de este artículo se llama `101-vm-windows-create`. La plantilla define una implementación básica de una máquina virtual Windows en Azure Stack.  Esta plantilla también implementa una red virtual (con DNS), un grupo de seguridad de red y una interfaz de red.

1. Abra Visual Studio Code y vaya a una carpeta de trabajo de la máquina.
2. En Visual Studio Code, abra una ventana de terminal de Git Bash.
3. Ejecute el siguiente comando para recuperar el repositorio de inicio rápido de Azure Stack.
    ```bash  
    Git clone https://github.com/Azure/AzureStack-QuickStart-Templates.git
    ```
4. Abra el directorio que contiene el repositorio.
    ```bash  
    CD AzureStack-QuickStart-Templates
    ```
5. Seleccione **Abrir** para abrir el archivo de `/101-vm-windows-create/azuredeploy.json` en el repositorio.
6. Guarde el archivo en su propia área de trabajo, o si ha creado una rama del repositorio puede trabajar allí en su lugar.
7. Con el archivo todavía abierto, cambie el campo `$Schema` a `https://schema.management.azure.com/schemas/2019-03-01-hybrid/deploymentTemplate.json#`.
8. Puede comprobar que el esquema de implementación funciona borrando el valor del campo apiProfile.
    ```JSON  
    "apiProfile": ""
    ```
9. Coloque el cursor entre las comillas vacías y presione CTRL + BARRA ESPACIADORA. Puede elegir entre los perfiles de API válidos en el esquema de implementación de Azure Stack. Puede realizar esta operación con cada uno de los proveedores de recursos de la plantilla.

    ![Esquema de implementación de Resource Manager para Azure Stack](./media/azure-stack-resource-manager-deploy-template-vscode/azure-stack-resource-manager-vscode-schema.png)

10. Cuando esté listo, puede implementar la plantilla mediante PowerShell. Siga las instrucciones que se indican en [Implementación con PowerShell](azure-stack-deploy-template-powershell.md). Especifique la ubicación de la plantilla en el script.
11. Después de haber implementado la máquina virtual Windows, vaya al portal de Azure Stack y busque el grupo de recursos. Si desea borrar el resultado de este ejercicio de Azure Stack, elimine el grupo de recursos.

## <a name="next-steps"></a>Pasos siguientes

- Más información acerca de las [plantillas de Resource Manager para Azure Stack](azure-stack-arm-templates.md).  
- Más información acerca de los [perfiles de API en Azure Stack](azure-stack-version-profiles.md).