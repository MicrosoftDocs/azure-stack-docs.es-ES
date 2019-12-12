---
title: Automatización de la validación de Azure Stack con PowerShell | Microsoft Docs
description: Puede automatizar la validación de Azure Stack con PowerShell.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/26/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 2f048ea1feb7a707c3c52b83fcf7c9e65a1ea58a
ms.sourcegitcommit: 08d2938006b743b76fba42778db79202d7c3e1c4
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 12/09/2019
ms.locfileid: "74954424"
---
# <a name="automate-azure-stack-validation-with-powershell"></a>Automatización de la validación de Azure Stack con PowerShell

La validación como servicio (VaaS) permite automatizar el inicio de las pruebas con el script **RunVaaSAutomation.ps1**.

Este script se puede utilizar para:

> [!div class="checklist"]
> * Instalar los requisitos previos.
> * Instalar e iniciar el agente local.
> * Iniciar pruebas de VaaS en flujos de trabajo de ejecución de pruebas, validación de soluciones y validación de paquetes.
> * Notificar resultados de la prueba.

Los vínculos siguientes contienen información sobre cómo ejecutar pruebas mediante el portal de VaaS. Antes de usar el script, debe obtener información sobre los parámetros necesarios y sus valores:

* Flujo de trabajo SolutionValidation: [Validación de una nueva solución de Azure Stack](azure-stack-vaas-validate-solution-new.md)
* Flujo de trabajo PackageValidation: [Validación de paquetes de OEM](azure-stack-vaas-validate-oem-package.md)
* Flujo de trabajo TestPass: [Programación de una prueba](azure-stack-vaas-schedule-test-pass.md)

## <a name="download-the-automation-scripts"></a>Descarga de los scripts de automatización

1. Abra un símbolo del sistema de PowerShell con privilegios elevados.

2. Ejecute el script siguiente para descargar el script de automatización:

```powershell
# Review and update the $RootFolder parameter
$RootFolder = "c:\VaaS"

if (-not(Test-Path($RootFolder))) {
    mkdir $RootFolder
}
Invoke-WebRequest -Uri https://storage.azurestackvalidation.com/packages/Microsoft.VaaS.Scripts.latest.nupkg -OutFile "$RootFolder\RunVaaSAutomation.zip"
Expand-Archive -Path "$RootFolder\RunVaaSAutomation.zip" -DestinationPath "$RootFolder\RunVaaSAutomation" -Force
Set-Location "$RootFolder\RunVaaSAutomation"
```

## <a name="launch-the-solution-validation-workflow"></a>Inicio del flujo de trabajo de validación de soluciones

Para aprender a ejecutar el flujo de trabajo de validación de soluciones mediante el portal de VaaS, consulte [Validación de paquetes de OEM](azure-stack-vaas-validate-oem-package.md).

Ejecute el siguiente script con los valores de parámetros adecuados:

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''
$VaaSProject_SolutionValidation_Configuration='Min' # enter 'Min' or 'Max'

# No need to modify the following lines
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outputFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$testParameters = @{}
$projectParameters = @{
    "Configuration" = $VaaSProject_SolutionValidation_Configuration;
}
$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId;
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'SolutionValidation';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable'= $projectParameters;
    'VaaSTestFilter' = 'Test';
    'VaaSTestFilterValue' = '';
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName'= $AgentName;
    'MaxScriptWaitTimeInHours'=48;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## <a name="launch-package-validation-workflow"></a>Inicio del flujo de trabajo de validación de paquetes

Para aprender a ejecutar el flujo de trabajo de validación de paquetes mediante el portal de VaaS, consulte [Validación de paquetes de OEM](azure-stack-vaas-validate-oem-package.md).

Ejecute el siguiente script con los valores de parámetros adecuados:

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''
$VaaSProject_PackageValidation_PackageBlobUri=''
$VaaSProject_PackageValidation_RequireDigitalSignature = "false" # enter 'true' or 'false' string
$VaaSProject_PackageValidation_LocalPathtoAzureStackUpdatePkgs = ""
$VaaSProject_PackageValidation_LocalPathtoOEMUpdatePkgs = ""

# No need to modify the following lines
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outputFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$testParameters = @{
    "RequireDigitalSignature" = $VaaSProject_PackageValidation_RequireDigitalSignature;
    "LocalPathtoAzureStackUpdatePkgs" = $VaaSProject_PackageValidation_LocalPathtoAzureStackUpdatePkgs;
    "LocalPathtoOEMUpdatePkgs" = $VaaSProject_PackageValidation_LocalPathtoOEMUpdatePkgs;
}

$projectParameters = @{
    "PackageBlobUri" = $VaaSProject_PackageValidation_PackageBlobUri;
}
$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId;
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'PackageValidation';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable' = $projectParameters;
    'VaaSTestFilter' = 'Test';
    'VaaSTestFilterValue' = '';
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName'= $AgentName;
    'MaxScriptWaitTimeInHours'=96;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## <a name="launch-the-test-pass-workflow"></a>Inicio del flujo de trabajo de la ejecución de la prueba

Para aprender a ejecutar el flujo de trabajo de ejecución de pruebas mediante el portal de VaaS, consulte [Programación de una prueba](azure-stack-vaas-schedule-test-pass.md).

Ejecute el siguiente script con los valores de parámetros adecuados:

```powershell
# Review and update the following parameters
$VaaSAccountUserName = ""
$VaaSAccountPassword = ""
$VaaSAccountTenantId = ""
$ServiceAdminUserName = ""
$ServiceAdminPassword = ""
$TenantAdminUserName  = ""
$TenantAdminPassword  = ""
$CloudAdminUserName   = ""
$CloudAdminPassword   = ""
$SolutionName   = ''
$ProjectName    = ''
$DiagnosticsStorageConnection=''

# No need to modify the following lines
# The following code is an example of running the "Cloud Simulation Engine" test
Import-Module .\VaaSAutomation.psm1 -verbose -force
$stampInfo = Get-StampInfo -cloudAdminUserName $CloudAdminUserName -cloudAdminPassword $CloudAdminPassword -retryCount 3 -retryPeriod 30 -outputFolder "."
$AgentName = "$((Get-WmiObject win32_computersystem).DNSHostName).$((Get-WmiObject win32_computersystem).Domain)".ToLower()
$VaaSAccountCredentail = New-Object System.Management.Automation.PSCredential ($VaaSAccountUserName, (ConvertTo-SecureString $VaaSAccountPassword -AsPlainText -Force))

$VaaSTestFilter='Test'
$VaaSTestFilterValue='cloudsimulation'
$testParameters = @{
    'ServiceAdminUser' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUser' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUser' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DomainFQDN' = "";
    'DomainAdminUser' = "";
    'DomainAdminPassword' = "";
    'TenantId' = $stampInfo.AadTenantId;
    'ExternalFqdn' = $stampInfo.ExternalDomainFQDN;
    'NumberOfNodes' = "$($stampInfo.NumberOfNodes)";
    'Region' = $stampInfo.RegionName;
    'RunDurationInHours' = 24;
    'EnableSuBR' = "false";
    'Faults' = "";
    'Resources' = "";
    'FaultControllerSettings' = "default";
    'DiagnosticsStorageConnection' = $diagnosticsStorageConnection;
    'DiagnosticsContainerName' = "$(New-Guid).ToString().ToLower()";
    'MaxFiddlerCaptureSizeInMB' = "0";
    'PackageHashCode' = "";
}

$projectParameters = @{}

$scriptParameters = @{
    'VaaSAccountCredentail' = $VaaSAccountCredentail;
    'VaaSAccountTenantId' = $VaaSAccountTenantId
    'VaaSSolutionName' = $SolutionName;
    'VaaSProjectType' = 'TestPass';
    'VaaSProjectName' = $ProjectName;
    'VaaSProjectParameterHashTable'= $projectParameters;
    'VaaSTestFilter'= $VaaSTestFilter;
    'VaaSTestFilterValue' = $VaaSTestFilterValue;
    'VaaSTestParameterHashTable'= $testParameters;
    'VaaSOnPremAgentName' = $AgentName;
    'MaxScriptWaitTimeInHours' = 24;
    'ServiceAdminUserName' = $ServiceAdminUserName;
    'ServiceAdminPassword' = $ServiceAdminPassword;
    'TenantAdminUserName' = $TenantAdminUserName;
    'TenantAdminPassword' = $TenantAdminPassword;
    'CloudAdminUserName' = $CloudAdminUserName;
    'CloudAdminPassword' = $CloudAdminPassword;
    'DiagnosticsStorageConnection' = $DiagnosticsStorageConnection;
}
& .\RunVaaSAutomation.ps1 @scriptParameters
```

## <a name="parameter-table"></a>Tabla de parámetros

Para más información, consulte [Parámetros comunes de flujo de trabajo](azure-stack-vaas-parameters.md).

| Parámetro | DESCRIPCIÓN |
| --- | --- |
| VaaSAccountUserName | Su nombre de usuario para el portal de VaaS. |
| VaaSAccountPassword | Su contraseña para el portal de VaaS. |
| VaaSAccountTenantId | El GUID del inquilino de VaaS. |
| ServiceAdminUserName | Cuenta de administrador del servicio de Azure Stack.  |
| ServiceAdminPassword | Contraseña del servicio de Azure Stack.  |
| TenantAdminUserName | Administrador del inquilino principal.  |
| TenantAdminPassword | Contraseña del inquilino principal.  |
| CloudAdminUserName | El nombre de usuario del administrador de la nube.  |
| CloudAdminPassword | Contraseña del usuario administrador de la nube.  |
| SolutionName | El nombre de la solución de VaaS. |
| ProjectName | El nombre del flujo de trabajo de VaaS. |
| DiagnosticsStorageConnection | Una URL de SAS a una cuenta de Azure Storage donde se copiarán los registros de diagnóstico durante la ejecución de pruebas. Para obtener instrucciones sobre la generación de la dirección URL de SAS, consulte [Generación de la cadena de conexión de diagnóstico](azure-stack-vaas-parameters.md). |

## <a name="review-the-results"></a>Revisión del resultado

Los registros y los informes de las pruebas se guardan en la carpeta de trabajo actual. 

Para otras opciones, consulte [Supervisión y administración de pruebas en el portal de VaaS](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Pasos siguientes

Para más información sobre PowerShell en Azure Stack, revise los módulos más recientes.

- [Módulo Azure Stack](/powershell/azure/azure-stack/overview?view=azurestackps-1.6.0)
