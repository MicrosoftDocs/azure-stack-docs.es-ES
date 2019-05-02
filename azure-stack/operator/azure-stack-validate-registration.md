---
title: Validación del registro de Azure en Azure Stack | Microsoft Docs
description: Use Azure Stack Readiness Checker para validar el registro de Azure.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/23/2019
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: a777fc1d9052eb58bbebd319fe6cc7f42a09cb9a
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64307323"
---
# <a name="validate-azure-registration"></a>Validación del registro de Azure

Use la herramienta Azure Stack Readiness Checker (**AzsReadinessChecker**) para validar que su suscripción de Azure está lista para usarse con Azure Stack. Valide el registro antes de empezar la implementación de Azure Stack. La herramienta Readiness Checker valida que:

- La suscripción de Azure que usa es de tipo compatible. Las suscripciones deben ser un proveedor de servicios en la nube (CSP) o el contrato Enterprise (EA).
- La cuenta que se usa para registrar la suscripción en Azure puede iniciar sesión en Azure y es propietaria de una suscripción.

Para obtener más información sobre el registro de Azure Stack, vea [Registro de Azure Stack en Azure](azure-stack-registration.md).

## <a name="get-the-readiness-checker-tool"></a>Obtención de la herramienta Readiness Checker

Descargue la versión más reciente de **AzsReadinessChecker** desde [Galería de PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Requisitos previos

Se necesitan los siguientes requisitos previos:

**El equipo en el que se ejecuta la herramienta:**

- Windows 10 o Windows Server 2016, con conectividad a internet.
- Azure PowerShell 5.1 o posterior. Para comprobar la versión, ejecute el siguiente cmdlet de PowerShell y luego revise la versión **principal** y las versiones **secundarias**:  

  ```powershell
  $PSVersionTable.PSVersion
  ```

- [PowerShell configurado para Azure Stack](azure-stack-powershell-install.md).
- Versión más reciente de [Microsoft Azure Stack Readiness Checker](https://aka.ms/AzsReadinessChecker).  

**Entorno de Azure Active Directory:**

- Identifique el nombre de usuario y la contraseña de una cuenta que sea propietario de la suscripción de Azure que va a usar con Azure Stack.  
- Indique el identificador de suscripción de la suscripción de Azure que va a usar.
- Identifique la clase **AzureEnvironment** que va a usar. Los valores admitidos para el parámetro de nombre de entorno son **AzureCloud**, **AzureChinaCloud** o **AzureUSGovernment**, dependiendo de la suscripción a Azure que esté utilizando.

## <a name="steps-to-validate-azure-registration"></a>Pasos para validar el registro de Azure

1. En un equipo que cumpla los requisitos previos, abra un símbolo del sistema de PowerShell con privilegios elevados y ejecute el siguiente comando para instalar **AzsReadinessChecker**:

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Desde el símbolo del sistema de PowerShell, ejecute el siguiente comando para establecer `$registrationCredential` como la cuenta propietaria de la suscripción. Reemplace `subscriptionowner@contoso.onmicrosoft.com` por la cuenta y nombre de inquilino:

   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```

   > [!NOTE]
   > Como CSP, cuando se usa una suscripción de servicios compartidos o IUR, debe proporcionar las credenciales de un usuario desde ese AAD correspondiente. Normalmente, será similar a `subscriptionowner@iurcontoso.onmicrosoft.com`. Ese usuario debe tener las credenciales adecuadas, tal como se describe en el paso anterior.

3. Desde el símbolo del sistema de PowerShell, ejecute l siguiente para establecer `$subscriptionID` como la suscripción de Azure que se va a usar. Reemplace `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` por su propio identificador de suscripción:

   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ```

4. Desde el símbolo del sistema de PowerShell, ejecute el siguiente comando para iniciar la validación de la suscripción:

   - Especifique el valor de `AzureEnvironment` como **AzureCloud**, **AzureGermanCloud** o **AzureChinaCloud**.  
   - Indique el administrador de Azure Active Directory y el nombre del inquilino de Azure Active Directory.

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. Después de ejecutar la herramienta, revise el resultado. Confirme que el estado del inicio de sesión y los requisitos de instalación es correcto. Una salida de validación correcta tiene un aspecto similar al del siguiente ejemplo:

   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>Informe y archivo de registro

Cada vez que se ejecuta la validación, los resultados se registran en **AzsReadinessChecker.log** y **AzsReadinessCheckerReport.json**. La ubicación de estos archivos se muestra con los resultados de validación de PowerShell.

Estos archivos pueden ayudar a compartir el estado de validación antes de implementar Azure Stack o investigar problemas de validación. Ambos archivos conservan los resultados de cada comprobación de validación posterior. El informe ofrece la confirmación del equipo de implementación de la configuración de identidad. El archivo de registro puede ayudar al equipo de implementación o de soporte técnico a investigar los problemas de validación.

De forma predeterminada, los dos archivos se escriben en **C:\Users\<nombre de usuario>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json**.  

- Use el parámetro **-OutputPath** ***&lt;ruta de acceso&gt;***  al final de la línea de comandos de ejecución para especificar otra ubicación para el informe.
- Use el parámetro **-CleanReport** al final del comando de ejecución para borrar la información de ejecuciones anteriores de la herramienta desde **AzsReadinessCheckerReport.json**.

Para obtener más información, consulte el artículo [Informe de validación de Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errores de validación

Si se producen errores en una comprobación de validación, los detalles sobre los errores se muestran en la ventana de PowerShell. La herramienta también registra información en el archivo **AzsReadinessChecker.log**.

Los ejemplos siguientes ofrecen instrucciones sobre errores de validación comunes:

### <a name="user-must-be-an-owner-of-the-subscription"></a>El usuario que llama debe ser propietario de la suscripción.

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Causa**: la cuenta no es un administrador de la suscripción de Azure.

**Resolución**: use una cuenta que sea de administrador de la suscripción de Azure que se facture por uso de la implementación de Azure Stack.

### <a name="expired-or-temporary-password"></a>Contraseña temporal o expirada

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change Password.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Causa**: la cuenta no puede iniciar sesión porque la contraseña es temporal o ha expirado.

**Resolución**: en PowerShell, ejecute el siguiente comando y siga los avisos para restablecer la contraseña.

```powershell
Login-AzureRMAccount
```

O bien, inicie sesión en [Azure Portal](https://portal.azure.com) con el propietario de la cuenta y el usuario se verá obligado a cambiar la contraseña.

### <a name="unknown-user-type"></a>Tipo de usuario desconocido  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Causa**: la cuenta no puede iniciar sesión en el entorno de Azure Active Directory especificado. En este ejemplo, **AzureChinaCloud** se especifica como valor de **AzureEnvironment**.  

**Resolución**: confirme que la cuenta es válida para el entorno de Azure especificado. En PowerShell, ejecute el siguiente comando para comprobar que la cuenta es válida para un entorno concreto:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>Pasos siguientes

- [Validación de la identidad de Azure](azure-stack-validate-identity.md)
- [Vista del informe de preparación](azure-stack-validation-report.md)
- [Consideraciones generales sobre la integración de Azure Stack](azure-stack-datacenter-integration.md)
