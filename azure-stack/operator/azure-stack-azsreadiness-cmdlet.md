---
title: Referencia del cmdlet Start-AzsReadinessChecker
description: Ayuda del cmdlet de PowerShell para el módulo Azure Stack Hub Readiness Checker.
author: ihenkel
ms.topic: conceptual
ms.date: 01/07/2020
ms.author: inhenkel
ms.reviewer: unknown
ms.lastreviewed: 05/09/2019
ms.openlocfilehash: e4794df9c66f1d060b6701d782c0486ddb914b02
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76877773"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Referencia del cmdlet Start-AzsReadinessChecker

Módulo: **Microsoft.AzureStack.ReadinessChecker**

Este módulo solo contiene un único cmdlet. El cmdlet realiza una o más funciones anteriores a la implementación o al mantenimiento en Azure Stack Hub.

## <a name="syntax"></a>Sintaxis

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>Descripción

El cmdlet **Start-AzsReadinessChecker** valida los certificados, las cuentas de Azure, las suscripciones de Azure y los directorios de Azure Active Directory (Azure AD). Ejecute la validación antes de implementar Azure Stack Hub o de realizar acciones de servicio de Azure Stack Hub como la rotación de secretos. El cmdlet también puede usarse para generar solicitudes de firma de certificado para certificados de infraestructura y, opcionalmente, para certificados de PaaS. Por último, el cmdlet puede volver a empaquetar los certificados PFX para solucionar problemas comunes de empaquetado.

## <a name="examples"></a>Ejemplos

### <a name="example-generate-certificate-signing-request"></a>Ejemplo: Generación de solicitudes de firma de certificado

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

En este ejemplo, `Start-AzsReadinessChecker` genera varias solicitudes de firma de certificados (CSR) para los certificados adecuados para la implementación de Azure Stack Hub en AD FS con un nombre de región **east** y un FQDN externo **azurestack.contoso.com**.

### <a name="example-validate-certificates"></a>Ejemplo: Validación de certificados

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

En este ejemplo, se solicita por seguridad la contraseña PFX y `Start-AzsReadinessChecker` busca en la carpeta relativa **Certificados** los certificados válidos para una implementación de Azure AD con un nombre de región de **east** y un FQDN externo de **azurestack.contoso.com**.

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Ejemplo: Validación de certificados con datos de implementación (implementación y soporte técnico)

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

En este ejemplo de implementación y soporte técnico, se solicita la contraseña PFX por seguridad y `Start-AzsReadinessChecker` busca en la carpeta relativa **Certificados** los certificados válidos para una implementación donde la identidad, la región y el FQDN externo se leen del archivo JSON de datos de implementación generado para la implementación.

### <a name="example-validate-paas-certificates"></a>Ejemplo: Validación de certificados de PaaS

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -RegionName east -FQDN azurestack.contoso.com
```

En este ejemplo, se crea una tabla hash con las rutas de acceso y las contraseñas de cada certificado de PaaS. Los certificados se pueden omitir. `Start-AzsReadinessChecker` comprueba que cada ruta de acceso de PFX existe y la valida con la región **east** y el FQDN externo **azurestack.contoso.com**.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Ejemplo: Validación de certificados de PaaS con datos de implementación

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

En este ejemplo, se crea una tabla hash con las rutas de acceso y las contraseñas de cada certificado de PaaS. Los certificados se pueden omitir. `Start-AzsReadinessChecker` comprueba que cada ruta de acceso de PFX existe y la valida con la región y el FQDN externo que se leen del archivo JSON de datos de implementación generado para la implementación.

### <a name="example-validate-azure-identity"></a>Ejemplo: Validación de la identidad de Azure

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

En este ejemplo, se solicitan las credenciales de la cuenta de administrador del servicio por seguridad y `Start-AzsReadinessChecker` comprueba que la cuenta de Azure y Azure AD son válidos para una implementación de Azure AD con un nombre de directorio de inquilino de **azurestack.contoso.com**.

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Ejemplo: Validación de la identidad de Azure con datos de implementación (compatibilidad de implementación)

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

En este ejemplo, se solicitan las credenciales de la cuenta de administrador del servicio por seguridad y `Start-AzsReadinessChecker` comprueba que Azure AD y la cuenta de Azure son válidos para una implementación de Azure AD donde **AzureCloud** y **TenantName** se leen del archivo JSON de datos de implementación generado para la implementación.

### <a name="example-validate-azure-registration"></a>Ejemplo: Validación del registro de Azure

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

En este ejemplo, las credenciales del propietario de la suscripción se solicitan por seguridad y `Start-AzsReadinessChecker` realiza la validación en la cuenta y suscripción indicadas para garantizar que se puede utilizar para el registro de Azure Stack Hub.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Ejemplo: Validación del registro de Azure con datos de implementación (equipo de implementación)

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

En este ejemplo, las credenciales del propietario de la suscripción se solicitan por seguridad y `Start-AzsReadinessChecker` realiza la validación en la cuenta y suscripción indicadas para garantizar que puede usarse para el registro de Azure Stack Hub donde los detalles adicionales se leen del archivo JSON de datos de implementación generado para la implementación.

### <a name="example-importexport-pfx-package"></a>Ejemplo: Paquete PFX de importación y exportación

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

En este ejemplo, se solicita la contraseña PFX por seguridad. El archivo Ssl.pfx se importa en el almacén de certificados de la máquina local, se vuelve a exportar con la misma contraseña y se guarda como Ssl_new.pfx. Este procedimiento se utiliza cuando la validación del certificado marca que una clave privada no tiene establecido el atributo de **máquina local**, la cadena del certificado está rota, hay certificados irrelevantes en el PFX o la cadena del certificado está en un orden incorrecto.

### <a name="example-view-validation-report-deployment-and-support"></a>Ejemplo: Vista del informe de validación (implementación y soporte técnico)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

En este ejemplo, el equipo de implementación o de soporte técnico reciben el informe de preparación del cliente (Contoso) y usa `Start-AzsReadinessChecker` para ver el estado de las ejecuciones de validación que realiza Contoso.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Ejemplo: Vista del resumen del informe de validación solo de validación de certificados (implementación y soporte técnico)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

En este ejemplo, el equipo de implementación o de soporte técnico reciben el informe de preparación del cliente (Contoso) y usa `Start-AzsReadinessChecker` para ver un estado resumido de las ejecuciones de validación del certificado que realiza Contoso.

## <a name="required-parameters"></a>Parámetros obligatorios

### <a name="-regionname"></a>-RegionName

Especifica el nombre de la región de la implementación de Azure Stack Hub.

|  |  |
|----------------------------|--------------|
|Escriba:                       |String        |
|Posición:                   |con nombre         |
|Valor predeterminado:              |None          |
|Aceptar la entrada de la canalización:      |False         |
|Aceptar caracteres comodín: |False         |

### <a name="-fqdn"></a>-FQDN

Especifica el FQDN externo de la implementación de Azure Stack Hub, con los alias **ExternalFQDN** y **ExternalDomainName**.

|  |  |
|----------------------------|--------------|
|Escriba:                       |String        |
|Posición:                   |con nombre         |
|Valor predeterminado:              |ExternalFQDN, ExternalDomainName |
|Aceptar la entrada de la canalización:      |False         |
|Aceptar caracteres comodín: |False         |

### <a name="-identitysystem"></a>-IdentitySystem

Especifica los valores válidos del sistema de identidades de la implementación de Azure Stack Hub, AAD o ADFS, para Azure Active Directory y Servicios de federación de Active Directory (AD FS), respectivamente.

|  |  |
|----------------------------|--------------|
|Escriba:                       |String        |
|Posición:                   |con nombre         |
|Valor predeterminado:              |None          |
|Valores válidos:               |"AAD", "ADFS"  |
|Aceptar la entrada de la canalización:      |False         |
|Aceptar caracteres comodín: |False         |

### <a name="-pfxpassword"></a>-PfxPassword

Especifica la contraseña asociada a los archivos de certificado PFX.

|  |  |
|----------------------------|---------|
|Escriba:                       |SecureString |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-paascertificates"></a>-PaaSCertificates

Especifica la tabla hash que contiene las rutas de acceso y las contraseñas de los certificados de PaaS.

|  |  |
|----------------------------|---------|
|Escriba:                       |Hashtable |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Especifica el archivo de configuración JSON de datos de implementación de Azure Stack Hub. Este archivo se genera para la implementación.

|  |  |
|----------------------------|---------|
|Escriba:                       |String   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-pfxpath"></a>-PfxPath

Especifica la ruta de acceso a un certificado problemático que requiere que se corrija la rutina de importación y exportación, tal y como se indica en la validación de certificados de esta herramienta.

|  |  |
|----------------------------|---------|
|Escriba:                       |String   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

Especifica la ruta de acceso de destino para el archivo PFX resultante de la rutina de importación y exportación.  

|  |  |
|----------------------------|---------|
|Escriba:                       |String   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-subject"></a>-Subject

Especifica un diccionario ordenado del firmante para la generación de solicitudes de certificado.

|  |  |
|----------------------------|---------|
|Escriba:                       |OrderedDictionary   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-requesttype"></a>-RequestType

Especifica el tipo de SAN de la solicitud de certificado. Los valores válidos son **MultipleCSR**, **SingleCSR**.

- **MultipleCSR** genera varias solicitudes de certificado, una por servicio.
- **SingleCSR** genera una solicitud de certificado para todos los servicios.

|  |  |
|----------------------------|---------|
|Escriba:                       |String   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Valores válidos:               |"MultipleCSR","SingleCSR" |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

Especifica la ruta de acceso de destino para los archivos de solicitud de certificado. El directorio debe existir ya.

|  |  |
|----------------------------|---------|
|Escriba:                       |String   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Especifica el administrador del servicio de Azure AD que se va a usar para la implementación de Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Escriba:                       |PSCredential   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Especifica el nombre de Azure AD que se va a usar para la implementación de Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Escriba:                       |String   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-azureenvironment"></a>-AzureEnvironment

Especifica la instancia de servicios de Azure que contiene las cuentas, los directorios y las suscripciones que se van a usar para la implementación y el registro de Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Escriba:                       |String   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Valores válidos:               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Especifica la cuenta de registro que se va a usar para el registro de Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Escriba:                       |String   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

Especifica el identificador de suscripción del registro que se va a usar para el registro de Azure Stack Hub.

|  |  |
|----------------------------|---------|
|Escriba:                       |Guid     |
|Posición:                   |con nombre    |
|Valor predeterminado:              |None     |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-reportpath"></a>-ReportPath

Especifica la ruta de acceso para el informe de preparación, tiene como valor predeterminado el nombre del informe predeterminado y el directorio actual.

|  |  |
|----------------------------|---------|
|Escriba:                       |String   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |All      |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

## <a name="optional-parameters"></a>Parámetros opcionales

### <a name="-certificatepath"></a>-CertificatePath

Especifica la ruta de acceso en la que solo se encuentran las carpetas de certificados necesarias para el certificado.

Las carpetas necesarias para la implementación de Azure Stack Hub con el sistema de identidades de Azure AD son las siguientes:

- ACSBlob, ACSQueue, ACSTable, Admin Portal, ARM Admin, ARM Public, KeyVault, KeyVaultInternal, Public Portal

Las carpetas necesarias para la implementación de Azure Stack Hub con el sistema de identidades de Servicios de federación de Active Directory (AD FS) son las siguientes:

- ACSBlob, ACSQueue, ACSTable, ADFS, Admin Portal, ARM Admin, ARM Public, Graph, KeyVault, KeyVaultInternal, Public Portal

|  |  |
|----------------------------|---------|
|Escriba:                       |String   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |.\Certificates |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-includepaas"></a>-IncludePaaS  

Especifica si se deben agregar servicios y nombres de host de PaaS a las solicitudes de certificado.

|  |  |
|----------------------------|------------------|
|Escriba:                       |SwitchParameter   |
|Posición:                   |con nombre             |
|Valor predeterminado:              |False             |
|Aceptar la entrada de la canalización:      |False             |
|Aceptar caracteres comodín: |False             |

### <a name="-reportsections"></a>-ReportSections

Especifica si solo se mostrará el resumen del informe; omite los detalles.

|  |  |
|----------------------------|---------|
|Escriba:                       |String   |
|Posición:                   |con nombre    |
|Valor predeterminado:              |All      |
|Valores válidos:               |"Certificate","AzureRegistration","AzureIdentity","Jobs","All" |
|Aceptar la entrada de la canalización:      |False    |
|Aceptar caracteres comodín: |False    |

### <a name="-summary"></a>-Summary

Especifica si solo se mostrará el resumen del informe; omite los detalles.

|  |  |
|----------------------------|------------------|
|Escriba:                       |SwitchParameter   |
|Posición:                   |con nombre             |
|Valor predeterminado:              |False             |
|Aceptar la entrada de la canalización:      |False             |
|Aceptar caracteres comodín: |False             |

### <a name="-cleanreport"></a>-CleanReport

Quita el historial de ejecuciones y validaciones anteriores y escribe las validaciones en un nuevo informe.

|  |  |
|----------------------------|------------------|
|Escriba:                       |SwitchParameter   |
|Alias:                    |cf                |
|Posición:                   |con nombre             |
|Valor predeterminado:              |False             |
|Aceptar la entrada de la canalización:      |False             |
|Aceptar caracteres comodín: |False             |

### <a name="-outputpath"></a>-OutputPath

Especifica una ruta de acceso personalizada donde se guarda el informe JSON de preparación y el archivo de registro detallado. Si la ruta de acceso no existe, el comando intenta crear el directorio.

|  |  |
|----------------------------|------------------|
|Escriba:                       |String            |
|Posición:                   |con nombre             |
|Valor predeterminado:              |$ENV:TEMP\AzsReadinessChecker  |
|Aceptar la entrada de la canalización:      |False             |
|Aceptar caracteres comodín: |False             |

### <a name="-confirm"></a>-Confirm

Solicita confirmación antes de ejecutar el cmdlet.

|  |  |
|----------------------------|------------------|
|Escriba:                       |SwitchParameter   |
|Alias:                    |cf                |
|Posición:                   |con nombre             |
|Valor predeterminado:              |False             |
|Aceptar la entrada de la canalización:      |False             |
|Aceptar caracteres comodín: |False             |

### <a name="-whatif"></a>-WhatIf

Muestra lo que sucedería si se ejecutara el cmdlet. El cmdlet no se ejecuta.

|  |  |
|----------------------------|------------------|
|Escriba:                       |SwitchParameter   |
|Alias:                    |wi                |
|Posición:                   |con nombre             |
|Valor predeterminado:              |False             |
|Aceptar la entrada de la canalización:      |False             |
|Aceptar caracteres comodín: |False             |
