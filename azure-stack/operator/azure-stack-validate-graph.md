---
title: Validar la integración de Azure Graph para Azure Stack
description: Use Azure Stack Readiness Checker para validar la integración de Graph para Azure Stack.
services: azure-stack
documentationcenter: ''
author: PatAltimore
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2019
ms.author: patricka
ms.reviewer: jerskine
ms.lastreviewed: 06/10/2019
ms.openlocfilehash: 75943b3259db736a3dfcd4b30b76f434eaf19111
ms.sourcegitcommit: af63214919e798901399fdffef09650de4176956
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/11/2019
ms.locfileid: "66828432"
---
# <a name="validate-graph-integration-for-azure-stack"></a>Validar la integración de Graph para Azure Stack

Use la herramienta Azure Stack Readiness Checker (AzsReadinessChecker) para validar que su entorno está listo para la integración de Graph con Azure Stack. Valide la integración de Graph antes de comenzar la integración del centro de datos o antes de una implementación de Azure Stack.

La herramienta Readiness Checker valida:

* Las credenciales para la cuenta de servicio que se creó para la integración de Graph tienen los derechos adecuados para consultar Active Directory.
* El *catálogo global* se puede resolver y está disponible.
* KDC se puede resolver y está disponible.
* La conectividad de red necesaria está preparada.

Para obtener más información acerca de los requisitos de la integración del centro de datos de Azure Stack, consulte [Integración del centro de datos de Azure Stack: identidad](azure-stack-integrate-identity.md).

## <a name="get-the-readiness-checker-tool"></a>Obtención de la herramienta Readiness Checker

Descargue la versión más reciente de Azure Stack Readiness Checker (AzsReadinessChecker) desde [Galería de PowerShell](https://aka.ms/AzsReadinessChecker).

## <a name="prerequisites"></a>Requisitos previos

Deben cumplirse los siguientes requisitos previos.

**Equipo donde se ejecuta la herramienta:**

* Windows 10 o Windows Server 2016, con conectividad de dominio.
* Azure PowerShell 5.1 o posterior. Para comprobar la versión, ejecute el siguiente comando de PowerShell y luego revise la versión *principal* y las versiones *secundarias*:  
   > `$PSVersionTable.PSVersion`
* Módulo PowerShell de Active Directory.
* La versión más reciente de la herramienta [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).

**Entorno de Active Directory:**

* Identifique un nombre de usuario y una contraseña para una cuenta del servicio Graph en la instancia de Active Directory existente.
* Identifique el FQDN de la raíz del bosque de Active Directory.

## <a name="validate-the-graph-service"></a>Validar el servicio Graph

1. En un equipo que cumpla los requisitos previos, abra un símbolo del sistema administrativo de PowerShell y ejecute el siguiente comando para instalar AzsReadinessChecker:

     `Install-Module Microsoft.AzureStack.ReadinessChecker -Force`

1. Desde el símbolo del sistema de PowerShell, ejecute el comando siguiente para establecer la variable *$graphCredential* en la cuenta del gráfico. Reemplace `contoso\graphservice` con su cuenta mediante el formato `domain\username`.

    `$graphCredential = Get-Credential contoso\graphservice -Message "Enter Credentials for the Graph Service Account"`

1. Desde el símbolo del sistema de PowerShell, ejecute el comando siguiente para iniciar la validación del servicio Graph. Especifique el valor de **-ForestFQDN** como el FQDN de la raíz del bosque.

     `Invoke-AzsGraphValidation -ForestFQDN contoso.com -Credential $graphCredential`

1. Después de ejecutar la herramienta, revise el resultado. Confirme que el estado es correcto para los requisitos de integración. Una validación correcta tiene un aspecto similar al del siguiente ejemplo:

    ```
    Testing Graph Integration (v1.0)
            Test Forest Root:            OK
            Test Graph Credential:       OK
            Test Global Catalog:         OK
            Test KDC:                    OK
            Test LDAP Search:            OK
            Test Network Connectivity:   OK

    Details:

    [-] In standalone mode, some tests should not be considered fully indicative of connectivity or readiness the Azure Stack Stamp requires prior to Data Center Integration.

    Additional help URL: https://aka.ms/AzsGraphIntegration

    AzsReadinessChecker Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log

    AzsReadinessChecker Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json

    Invoke-AzsGraphValidation Completed
    ```

En entornos de producción, la prueba de conectividad de red desde una estación de trabajo de un operador no es totalmente indicativa de la conectividad disponible para Azure Stack. La red pública VIP de la marca de Azure Stack necesitará la conectividad del tráfico LDAP para realizar la integración de identidad.

## <a name="report-and-log-file"></a>Informe y archivo de registro

Cada vez que se ejecuta la validación, los resultados se registran en **AzsReadinessChecker.log** y **AzsReadinessCheckerReport.json**. La ubicación de estos archivos aparece con los resultados de validación de PowerShell.

Estos archivos de validación pueden ayudarle a compartir el estado antes de implementar Azure Stack o investigar problemas de validación. Ambos archivos conservan los resultados de cada comprobación de validación posterior. El informe ofrece la confirmación del equipo de implementación de la configuración de identidad. El archivo de registro puede ayudar al equipo de implementación o de soporte técnico a investigar los problemas de validación.

De forma predeterminada, ambos archivos se escriben en `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\`.

Uso:

* **-OutputPath**: el parámetro de *ruta de acceso* al final del comando de ejecución para especificar otra ubicación para el informe.
* **-CleanReport**: el parámetro al final del comando de ejecución para borrar *AzsReadinessCheckerReport.json* de la información del informe previo. Para obtener más información, consulte el artículo [Informe de validación de Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errores de validación

Si se producen errores en una comprobación de validación, los detalles sobre los errores se muestran en la ventana de PowerShell. La herramienta también registra información en *AzsGraphIntegration.log*.

## <a name="next-steps"></a>Pasos siguientes

[Vista del informe de preparación](azure-stack-validation-report.md)  
[Consideraciones generales sobre la integración de Azure Stack](azure-stack-datacenter-integration.md)  
