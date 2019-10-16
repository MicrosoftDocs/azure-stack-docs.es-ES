---
title: Uso de la herramienta de validación de plantillas en Azure Stack | Microsoft Docs
description: Compruebe las plantillas que se van a implementar en Azure Stack con una herramienta de validación de plantillas.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: d9e6aee1-4cba-4df5-b5a3-6f38da9627a3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: a5e86f0f0719e30ef693c736ac4c70f05830c3bb
ms.sourcegitcommit: b2d19e12a50195bb8925879ee75c186c9604f313
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/04/2019
ms.locfileid: "71961661"
---
# <a name="use-the-template-validation-tool-in-azure-stack"></a>Uso de la herramienta de validación de plantillas en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Use la herramienta de validación de plantillas para comprobar si las [plantillas](azure-stack-arm-templates.md) de Azure Resource Manager están listas para implementarse en Azure Stack. La herramienta de validación de plantillas está disponible como parte del repositorio de GitHub de herramientas de Azure Stack. Descargue las herramientas de Azure Stack mediante los pasos descritos en el artículo [Descarga de herramientas de Azure Stack desde GitHub](../operator/azure-stack-powershell-download.md).

## <a name="overview"></a>Información general

Para validar una plantilla, primero tiene que crear un archivo con funcionalidades de la nube y, a continuación, ejecutar la herramienta de validación. Use los siguientes módulos de PowerShell desde las herramientas de Azure Stack:

- En la carpeta **CloudCapabilities**: **AzureRM.CloudCapabilities.psm1** crea un archivo JSON de funcionalidades de la nube que representa los servicios y las versiones en una nube de Azure Stack.
- En la carpeta **TemplateValidator**: **AzureRM.TemplateValidator.psm1** utiliza un archivo JSON de funcionalidades de la nube para probar plantillas para la implementación en Azure Stack.

## <a name="build-the-cloud-capabilities-file"></a>Genere el archivo de funcionalidades de la nube

Para poder usar el validador de plantillas, ejecute el módulo de PowerShell **AzureRM.CloudCapabilities** para generar un archivo JSON.

>[!NOTE]
> Si actualiza el sistema integrado o agrega los nuevos servicios o extensiones virtuales, también debe volver a ejecutar ese módulo.

1. Asegúrese de que dispone de conectividad a Azure Stack. Estos pasos se pueden realizar desde el host del Kit de desarrollo de Azure Stack (ASDK), o se puede usar una [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) para conectarse desde la estación de trabajo.
2. Importe el módulo de PowerShell **AzureRM.CloudCapabilities**:

    ```powershell
    Import-Module .\CloudCapabilities\AzureRM.CloudCapabilities.psm1
    ```

3. Use el cmdlet **Get-CloudCapabilities** para recuperar las versiones de servicio y crear un archivo JSON de funcionalidades de la nube. Si no se especifica `-OutputPath`, el archivo **AzureCloudCapabilities.json** se crea en el directorio actual. Use la ubicación real de Azure:

    ```powershell
    Get-AzureRMCloudCapability -Location <your location> -Verbose
    ```

## <a name="validate-templates"></a>Validar plantillas

Siga estos pasos para validar las plantillas mediante el módulo de PowerShell **AzureRM.TemplateValidator**. Puede usar sus propias plantillas o usar las [plantillas de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates).

1. Importe el módulo de PowerShell **AzureRM.TemplateValidator.psm1**:

    ```powershell
    cd "c:\AzureStack-Tools-master\TemplateValidator"
    Import-Module .\AzureRM.TemplateValidator.psm1
    ```

2. Ejecute el validador de plantillas:

    ```powershell
    Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
    -CapabilitiesPath <path to cloudcapabilities.json> `
    -Verbose
    ```

Los errores o las advertencias de validación de plantillas se muestran en la consola de PowerShell y se escriben en un archivo HTML del directorio de origen. La captura de pantalla siguiente es un ejemplo de un informe de validación:

![Informe de comprobación de plantilla](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parámetros

El cmdlet del validador de plantillas admite los siguientes parámetros.

| Parámetro | DESCRIPCIÓN | Obligatorio |
| ----- | -----| ----- |
| `TemplatePath` | Especifica la ruta de acceso para buscar de forma recursiva las plantillas de Azure Resource Manager. | Sí |
| `TemplatePattern` | Especifica el nombre de los archivos de plantilla para que coincida. | Sin |
| `CapabilitiesPath` | Especifica la ruta de acceso al archivo JSON con funcionalidades de la nube. | Sí |
| `IncludeComputeCapabilities` | Incluye la evaluación de recursos de IaaS, como tamaños de máquina virtual y extensiones de máquina virtual. | Sin |
| `IncludeStorageCapabilities` | Incluye la evaluación de recursos de almacenamiento, como los tipos de SKU. | Sin |
| `Report` | Especifica el nombre del informe HTML generado. | Sin |
| `Verbose` | Registra los errores y las advertencias en la consola. | Sin|

### <a name="examples"></a>Ejemplos

En este ejemplo, se validan todas las [plantillas de inicio rápido de Azure Stack](https://github.com/Azure/AzureStack-QuickStart-Templates) descargadas en el almacenamiento local. Además, en el ejemplo se validan las extensiones y los tamaños de máquina virtual (VM) con respecto a las funcionalidades de ASDK:

```powershell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```

## <a name="next-steps"></a>Pasos siguientes

- [Implementar plantillas en Azure Stack](azure-stack-arm-templates.md)
- [Desarrollo de plantillas para Azure Stack](azure-stack-develop-templates.md)
