---
title: Validación de la identidad de Azure
titleSuffix: Azure Stack Hub
description: Use Azure Stack Hub Readiness Checker para validar la identidad de Azure.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 3c2eb6b0efc0bc6f0111ed342ef16e19223f0a85
ms.sourcegitcommit: 1fa0140481a483e5c27f602386fe1fae77ad29f7
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/05/2020
ms.locfileid: "78366524"
---
# <a name="validate-azure-identity"></a>Validación de la identidad de Azure

Use la herramienta Azure Stack Readiness Checker (**AzsReadinessChecker**) para validar que su instancia de Azure Active Directory (Azure AD) está lista para usarse con Azure Stack Hub. Antes de empezar a implementar Azure Stack Hub, valide la solución de identidad de Azure.  

La herramienta Readiness Checker valida:

- Azure AD como proveedor de identidades de Azure Stack Hub.
- La cuenta de Azure AD que planea usar puede iniciar sesión como administrador global de Azure AD.

La validación garantiza que el entorno está listo para que Azure Stack Hub almacene información sobre los usuarios, las aplicaciones, los grupos y las entidades de servicio de Azure Stack Hub en la instancia de Azure AD.

## <a name="get-the-readiness-checker-tool"></a>Obtención de la herramienta Readiness Checker

Descargue la versión más reciente de la herramienta Azure Stack Hub Readiness Checker (AzsReadinessChecker) desde la [Galería de PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Prerrequisitos

Se necesitan los siguientes requisitos previos:

**El equipo en el que se ejecuta la herramienta:**

- Windows 10 o Windows Server 2016, con conectividad a Internet.
- Azure PowerShell 5.1 o posterior. Para comprobar la versión, ejecute el siguiente comando de PowerShell y luego revise la versión **principal** y las versiones **secundarias**:  
  ```powershell
  $PSVersionTable.PSVersion
  ```
- [PowerShell configurado para Azure Stack Hub](azure-stack-powershell-install.md).
- Versión más reciente de la herramienta [Microsoft Azure Stack Hub Readiness Checker](https://aka.ms/AzsReadinessChecker).

**Entorno de Azure AD:**

- Identifique la cuenta de Azure AD que va a usar con Azure Stack Hub y compruebe que es administrador global de Azure AD.
- Identifique el nombre del inquilino de Azure AD. El nombre del inquilino debe ser el nombre de dominio principal de Azure AD. Por ejemplo, **contoso.onmicrosoft.com**.
- Identifique el entorno de Azure que va a usar. Los valores admitidos para el parámetro de nombre de entorno son **AzureCloud**, **AzureChinaCloud** o **AzureUSGovernment**, dependiendo de la suscripción a Azure que se use.

## <a name="steps-to-validate-azure-identity"></a>Pasos para validar la identidad de Azure

1. En un equipo que cumpla los requisitos previos, abra un símbolo del sistema de comandos de PowerShell con privilegios elevados y ejecute el siguiente comando para instalar **AzsReadinessChecker**:  

   ```powershell
   Install-Module Microsoft.AzureStack.ReadinessChecker -Force
   ```

2. Desde el símbolo del sistema de PowerShell, ejecute el siguiente comando para establecer `$serviceAdminCredential` como administrador de servicios del inquilino de Azure AD.  Reemplace `serviceadmin\@contoso.onmicrosoft.com` por la cuenta y nombre de inquilino:

   ```powershell
   $serviceAdminCredential = Get-Credential serviceadmin@contoso.onmicrosoft.com -Message "Enter credentials for service administrator of Azure Active Directory tenant"
   ```

3. Desde el símbolo del sistema de PowerShell, ejecute el siguiente comando para iniciar la validación de Azure AD.

   - Especifique el valor de nombre de entorno para la clase **AzureEnvironment**. Los valores admitidos para el parámetro de nombre de entorno son **AzureCloud**, **AzureChinaCloud** o **AzureUSGovernment**, dependiendo de la suscripción a Azure que se use.
   - Reemplace `contoso.onmicrosoft.com` por el nombre del inquilino de Azure AD.

   ```powershell
   Invoke-AzsAzureIdentityValidation -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment <environment name> -AADDirectoryTenantName contoso.onmicrosoft.com
   ```

4. Después de ejecutar la herramienta, revise el resultado. Confirme que el estado es **correcto** para los requisitos de instalación. Una validación correcta tiene un aspecto similar al del siguiente ejemplo:

   ```powershell
   Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
   Starting Azure Identity Validation

   Checking Installation Requirements: OK

   Finished Azure Identity Validation

   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsAzureIdentityValidation Completed
   ```

## <a name="report-and-log-file"></a>Informe y archivo de registro

Cada vez que se ejecuta la validación, los resultados se registran en **AzsReadinessChecker.log** y **AzsReadinessCheckerReport.json**. La ubicación de estos archivos se muestra con los resultados de validación de PowerShell.

Estos archivos pueden ayudarle a compartir el estado de validación antes de implementar Azure Stack Hub o de investigar problemas de validación. Ambos archivos conservan los resultados de cada comprobación de validación posterior. El informe ofrece la confirmación del equipo de implementación de la configuración de identidad. El archivo de registro puede ayudar al equipo de implementación o de soporte técnico a investigar los problemas de validación.

De forma predeterminada, ambos archivos se escriben en `C:\Users\<username>\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json`.  

- Use el parámetro `-OutputPath <path>` al final de la línea de comandos de ejecución para especificar otra ubicación para el informe.
- Use el parámetro `-CleanReport` al final del comando de ejecución para borrar la información de ejecuciones anteriores de la herramienta desde **AzsReadinessCheckerReport.json**.

Para más información, consulte [Informe de validación para Azure Stack Hub](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Errores de validación

Si se producen errores en una comprobación de validación, los detalles sobre los errores se muestran en la ventana de PowerShell. La herramienta también registra información en el archivo AzsReadinessChecker.log.

Los ejemplos siguientes ofrecen instrucciones sobre errores de validación comunes.

### <a name="expired-or-temporary-password"></a>Contraseña temporal o expirada

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The password for account  has expired or is a temporary password that needs to be reset before continuing. Run Login-AzureRMAccount, login with  credentials and follow the prompts to reset.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Causa**: la cuenta no puede iniciar sesión porque la contraseña es temporal o ha expirado.

**Resolución**: en PowerShell, ejecute el siguiente comando y siga los avisos para restablecer la contraseña:

```powershell
Login-AzureRMAccount
```

O bien, inicie sesión en [Azure Portal](https://portal.azure.com) como propietario de la cuenta y el usuario se verá obligado a cambiar la contraseña.

### <a name="unknown-user-type"></a>Tipo de usuario desconocido 
 
```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
Unknown user type detected. Check the account  is valid for AzureChinaCloud
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Causa**: la cuenta no puede iniciar sesión en el entorno de Azure AD especificado (**AADDirectoryTenantName**). En este ejemplo, **AzureChinaCloud** se especifica como valor de **AzureEnvironment**.

**Resolución**: confirme que la cuenta es válida para el entorno de Azure especificado. En PowerShell, ejecute el siguiente comando para comprobar que la cuenta es válida para un entorno concreto:

```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

### <a name="account-is-not-an-administrator"></a>La cuenta no es de administrador

```powershell
Invoke-AzsAzureIdentityValidation v1.1809.1005.1 started.
Starting Azure Identity Validation

Checking Installation Requirements: Fail
Error Details for Service Administrator Account admin@contoso.onmicrosoft.com
The Service Admin account you entered 'admin@contoso.onmicrosoft.com' is not an administrator of the Azure Active Directory tenant 'contoso.onmicrosoft.com'.
Additional help URL https://aka.ms/AzsRemediateAzureIdentity

Finished Azure Identity Validation

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsAzureIdentityValidation Completed
```

**Causa**: aunque la cuenta puede iniciar sesión correctamente, la cuenta no es de un administrador de Azure AD (**AADDirectoryTenantName**).  

**Resolución**: inicie sesión en [Azure Portal](https://portal.azure.com) como propietario de la cuenta, vaya sucesivamente a **Azure Active Directory**, a **Usuarios** y a **Seleccione el usuario**. A continuación, seleccione **Rol de directorio** y asegúrese de que el usuario es un **Administrador global**. Si la cuenta es de **usuario**, vaya a **Azure Active Directory** > **Nombres de dominio personalizado** y confirme que el nombre que especificó para **AADDirectoryTenantName** está marcado como nombre de dominio principal del directorio. En este ejemplo, se trata de **contoso.onmicrosoft.com**.

Azure Stack Hub requiere que el nombre de dominio sea el nombre de dominio principal.

## <a name="next-steps"></a>Pasos siguientes

[Validación del registro de Azure](azure-stack-validate-registration.md)  
[Vista del informe de preparación](azure-stack-validation-report.md)  
[Consideraciones generales sobre la integración de Azure Stack Hub](azure-stack-datacenter-integration.md)  
