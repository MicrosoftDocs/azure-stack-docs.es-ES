---
title: Uso de la herramienta de validación de plantillas en Azure Stack Hub
description: Compruebe las plantillas que se van a implementar en Azure Stack Hub con una herramienta de validación de plantillas.
author: sethmanheim
ms.topic: article
ms.date: 11/22/2020
ms.author: sethm
ms.reviewer: sijuman
ms.lastreviewed: 11/22/2020
ms.openlocfilehash: 0631058a3eade431769a5651bb37441b835eb3e6
ms.sourcegitcommit: 8c745b205ea5a7a82b73b7a9daf1a7880fd1bee9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/24/2020
ms.locfileid: "95518065"
---
# <a name="use-the-template-validation-tool-in-azure-stack-hub"></a>Uso de la herramienta de validación de plantillas en Azure Stack Hub

Use la herramienta de validación de plantillas para comprobar si las [plantillas](azure-stack-arm-templates.md) de Azure Resource Manager están listas para implementarse en Azure Stack Hub. La herramienta de validación de plantillas está disponible en el repositorio de GitHub de herramientas de Azure Stack Hub. Para descargar las herramientas de Azure Stack Hub, siga los pasos que se describen en el artículo [Descarga de herramientas de Azure Stack Hub desde GitHub](../operator/azure-stack-powershell-download.md).

## <a name="overview"></a>Información general

Para validar una plantilla, primero tiene que crear un archivo con funcionalidades de la nube y, a continuación, ejecutar la herramienta de validación. Use los siguientes módulos de PowerShell desde las herramientas de Azure Stack Hub:

- En la carpeta **CloudCapabilities**: **Az.CloudCapabilities.psm1** crea un archivo JSON de funcionalidades de la nube que representa los servicios y las versiones en una nube de Azure Stack Hub.
- En la carpeta **TemplateValidator**: **Az.TemplateValidator.psm1** utiliza un archivo JSON de funcionalidades de la nube para probar plantillas para la implementación en Azure Stack Hub.

## <a name="build-the-cloud-capabilities-file"></a>Genere el archivo de funcionalidades de la nube

Para poder usar el validador de plantillas, ejecute el módulo de PowerShell **Az.CloudCapabilities** para generar un archivo JSON.

> [!NOTE]
> Si actualiza el sistema integrado o agrega los nuevos servicios o extensiones virtuales, también debe volver a ejecutar ese módulo.


### <a name="az-modules"></a>[Modules de Az](#tab/az1)

1. Asegúrese de que dispone de conectividad con Azure Stack Hub. Estos pasos se pueden realizar desde el host del Kit de desarrollo de Azure Stack (ASDK), o se puede usar una [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) para conectarse desde la estación de trabajo.
2. Importe el módulo de PowerShell **Az.CloudCapabilities**:

    ```powershell
    Import-Module .\CloudCapabilities\Az.CloudCapabilities.psm1
    ```

3. Use el cmdlet **Get-CloudCapabilities** para recuperar las versiones de servicio y crear un archivo JSON de funcionalidades de la nube. Si no se especifica `-OutputPath`, el archivo **AzureCloudCapabilities.json** se crea en el directorio actual. Use la ubicación real de Azure:

```powershell
Get-AzCloudCapability -Location <your location> -Verbose
```

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm1)

1. Asegúrese de que dispone de conectividad con Azure Stack Hub. Estos pasos se pueden realizar desde el host del Kit de desarrollo de Azure Stack (ASDK), o se puede usar una [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn) para conectarse desde la estación de trabajo.
2. Importe el módulo de PowerShell **Az.CloudCapabilities**:

    ```powershell
    Import-Module .\CloudCapabilities\Az.CloudCapabilities.psm1
    ```

3. Use el cmdlet **Get-CloudCapabilities** para recuperar las versiones de servicio y crear un archivo JSON de funcionalidades de la nube. Si no se especifica `-OutputPath`, el archivo **AzureCloudCapabilities.json** se crea en el directorio actual. Use la ubicación real de Azure:

```powershell
Get-AzureRMCloudCapability -Location <your location> -Verbose
```

---

## <a name="validate-templates"></a>Validar plantillas

Siga estos pasos para validar las plantillas mediante el módulo de PowerShell **Az.TemplateValidator**. Puede usar tanto sus propias plantillas como las [plantillas de inicio rápido de Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates).

### <a name="az-modules"></a>[Modules de Az](#tab/az2)

1. Importe el módulo de PowerShell **Az.TemplateValidator.psm1**:

    ```powershell
    cd "c:\AzureStack-Tools-az\TemplateValidator"
    Import-Module .\Az.TemplateValidator.psm1
    ```

2. Ejecute el validador de plantillas:

```powershell
Test-AzTemplate -TemplatePath <path to template.json or template folder> `
-CapabilitiesPath <path to cloudcapabilities.json> `
-Verbose
```

### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm2)

1. Importe el módulo de PowerShell **Az.TemplateValidator.psm1**:

    ```powershell
    cd "c:\AzureStack-Tools-az\TemplateValidator"
    Import-Module .\Az.TemplateValidator.psm1
    ```

2. Ejecute el validador de plantillas:

```powershell
Test-AzureRMTemplate -TemplatePath <path to template.json or template folder> `
-CapabilitiesPath <path to cloudcapabilities.json> `
-Verbose
```

---

Los errores o las advertencias de validación de plantillas se muestran en la consola de PowerShell y se escriben en un archivo HTML del directorio de origen. La captura de pantalla siguiente es un ejemplo de un informe de validación:

![Informe de comprobación de plantilla](./media/azure-stack-validate-templates/image1.png)

### <a name="parameters"></a>Parámetros

El cmdlet del validador de plantillas admite los siguientes parámetros.

| Parámetro | Descripción | Obligatorio |
| ----- | -----| ----- |
| `TemplatePath` | Especifica la ruta de acceso para buscar de forma recursiva las plantillas de Azure Resource Manager. | Sí |
| `TemplatePattern` | Especifica el nombre de los archivos de plantilla para que coincida. | No |
| `CapabilitiesPath` | Especifica la ruta de acceso al archivo JSON con funcionalidades de la nube. | Sí |
| `IncludeComputeCapabilities` | Incluye la evaluación de recursos de IaaS, como tamaños de máquina virtual y extensiones de máquina virtual. | No |
| `IncludeStorageCapabilities` | Incluye la evaluación de recursos de almacenamiento, como los tipos de SKU. | No |
| `Report` | Especifica el nombre del informe HTML generado. | No |
| `Verbose` | Registra los errores y las advertencias en la consola. | No|

### <a name="examples"></a>Ejemplos

En este ejemplo, se validan todas las [plantillas de inicio rápido de Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates) descargadas en el almacenamiento local. Además, en el ejemplo se validan las extensiones y los tamaños de máquina virtual (VM) con respecto a las funcionalidades de ASDK.

### <a name="az-modules"></a>[Modules de Az](#tab/az3)

```powershell
test-AzTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```
### <a name="azurerm-modules"></a>[Módulos de AzureRM](#tab/azurerm3)

```powershell
test-AzureRMTemplate -TemplatePath C:\AzureStack-Quickstart-Templates `
-CapabilitiesPath .\TemplateValidator\AzureStackCloudCapabilities_with_AddOns_20170627.json `
-TemplatePattern MyStandardTemplateName.json `
-IncludeComputeCapabilities `
-Report TemplateReport.html
```
---

## <a name="next-steps"></a>Pasos siguientes

- [Implementación de plantillas en Azure Stack Hub](azure-stack-arm-templates.md)
- [Desarrollo de plantillas para Azure Stack Hub](azure-stack-develop-templates.md)
