---
title: Validación de la integración de Azure Graph
titleSuffix: Azure Stack Hub
description: Use Azure Stack Hub Readiness Checker para validar la integración de Graph con Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: jerskine
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 541be4667328f6ac3906c785329b8816558c8b84
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2020
ms.locfileid: "78366645"
---
# <a name="validate-graph-integration-for-azure-stack-hub"></a>Validación de la integración de Graph con Azure Stack Hub

Use la herramienta Azure Stack Hub Readiness Checker (AzsReadinessChecker) para validar que su entorno está listo para la integración de Graph con Azure Stack Hub. Valide la integración de Graph antes de comenzar la integración en el centro de datos o antes de una implementación de Azure Stack Hub.

La herramienta Readiness Checker valida:

* Las credenciales para la cuenta de servicio que se creó para la integración de Graph tienen los derechos adecuados para consultar Active Directory.
* El *catálogo global* se puede resolver y está disponible.
* KDC se puede resolver y está disponible.
* La conectividad de red necesaria está preparada.

Para más información sobre los requisitos de la integración de Azure Stack Hub en el centro de datos, consulte [Integración de Azure Stack Hub en el centro de datos: identidad](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Obtención de la herramienta Readiness Checker

Descargue la versión más reciente de la herramienta Azure Stack Hub Readiness Checker (AzsReadinessChecker) desde la [Galería de PowerShell](https://aka.ms/AzsReadinessChecker).

## <a name="prerequisites"></a>Prerrequisitos

Deben cumplirse los siguientes requisitos previos.

**Equipo donde se ejecuta la herramienta:**

* Windows 10 o Windows Server 2016 con conectividad de dominio.
* Azure PowerShell 5.1 o posterior. Para comprobar la versión, ejecute el siguiente comando de PowerShell y luego revise la versión *principal* y las versiones *secundarias*:
    ```powershell
    $PSVersionTable.PSVersion
    ```
* Módulo PowerShell de Active Directory.
* La versión más reciente de la herramienta [Microsoft Azure Stack Hub Readiness Checker](https://aka.ms/AzsReadinessChecker).

**Entorno de Active Directory:**

* Identifique un nombre de usuario y una contraseña para una cuenta del servicio Graph en la instancia de Active Directory existente.
* Identifique el FQDN de la raíz del bosque de Active Directory.

## <a name="validate-the-graph-service"></a>Validar el servicio Graph

1. En un equipo que cumpla los requisitos previos, abra un símbolo del sistema administrativo de PowerShell y ejecute el siguiente comando para instalar AzsReadinessChecker:

    ```powershell
    Install-Module Microsoft.AzureStack.ReadinessChecker -Force
    ```

1. Desde el símbolo del sistema de PowerShell, ejecute el comando siguiente para establecer la variable *$graphCredential* en la cuenta del gráfico. Reemplace `contoso\graphservice` con su cuenta mediante el formato `domain\username`.

    ```powershell
    $graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"
    ```

1. Desde el símbolo del sistema de PowerShell, ejecute el comando siguiente para iniciar la validación del servicio Graph. Especifique el valor de `-ForestFQDN` como el FQDN de la raíz del bosque.

    ```powershell
    Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential
    ```

1. Después de ejecutar la herramienta, revise el resultado. Confirme que el estado es correcto para los requisitos de integración. Una validación correcta tiene un aspecto similar al del siguiente ejemplo:

    ```powershell
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Hub Stamp requires prior to Datacenter Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

En entornos de producción, la prueba de la conectividad de red desde la estación de trabajo de un operador no es totalmente indicativa de la conectividad disponible para Azure Stack Hub. La red pública VIP del sello de Azure Stack Hub necesita la conectividad del tráfico LDAP para realizar la integración de identidades.

## <a name="report-and-log-file"></a>Informe y archivo de registro

Cada vez que se ejecuta la validación, los resultados se registran en **AzsReadinessChecker.log** y **AzsReadinessCheckerReport.json**. La ubicación de estos archivos aparece con los resultados de validación de PowerShell.

Los archivos de validación pueden ayudarle a compartir el estado antes de implementar Azure Stack Hub o investigar problemas de validación. Ambos archivos conservan los resultados de cada comprobación de validación posterior. El informe ofrece la confirmación del equipo de implementación de la configuración de identidad. El archivo de registro puede ayudar al equipo de implementación o de soporte técnico a investigar los problemas de validación.

De forma predeterminada, ambos archivos se escriben en `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Uso:

* `-OutputPath`: el parámetro de *ruta de acceso* al final del comando de ejecución para especificar otra ubicación para el informe.
* `-CleanReport`: el parámetro al final del comando de ejecución para borrar*AzsReadinessCheckerReport.json* de la información del informe previo. Para más información, consulte [Informe de validación para Azure Stack Hub](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errores de validación

Si se producen errores en una comprobación de validación, los detalles sobre los errores se muestran en la ventana de PowerShell. La herramienta también registra información en *AzsGraphIntegration.log*.

## <a name="next-steps"></a>Pasos siguientes

[Vista del informe de preparación](azure-stack-validation-report.md)  
[Consideraciones generales sobre la integración de Azure Stack Hub](azure-stack-datacenter-integration.md)  
