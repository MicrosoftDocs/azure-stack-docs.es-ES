---
title: Validación de certificados de infraestructura de clave pública de Azure Stack Hub para la implementación de sistemas integrados de Azure Stack Hub | Microsoft Docs
description: Describe cómo validar certificados PKI de Azure Stack Hub para sistemas integrados de Azure Stack Hub. Incluye el uso de la herramienta Azure Stack Hub Certificate Checker.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2019
ms.author: mabrigg
ms.reviewer: ppacent
ms.lastreviewed: 01/08/2019
ms.openlocfilehash: 23225b21d1dc3074c69cefa2af23a99b634a7a73
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2020
ms.locfileid: "75812865"
---
# <a name="validate-azure-stack-hub-pki-certificates"></a>Preparación de certificados PKI de Azure Stack Hub

La herramienta Azure Stack Hub Readiness Checker que se describe en este artículo está disponible [en la Galería de PowerShell](https://aka.ms/AzsReadinessChecker). Puede usar esta herramienta para comprobar si los [certificados PKI generados](azure-stack-get-pki-certs.md) son válidos para la implementación previa. Cuando valide los certificados, deje tiempo suficiente para que se prueben los certificados y, en caso necesario, vuelvan a emitirse.

La herramienta Readiness Checker realiza las siguientes validaciones de certificados:

- **Análisis de PFX**  
    Comprueba si el archivo PFX es válido, la contraseña es correcta y si la información pública está protegida con contraseña. 
- **Fecha de expiración**  
    Comprueba que tenga una validez mínima de 7 días. 
- **Algoritmo de firma**  
    Comprueba que el algoritmo de firma no sea SHA1.
- **Clave privada**  
    Comprueba si la clave privada está presente y se exporta con el atributo de máquina local. 
- **Cadena de certificados**  
    Comprueba si la cadena de certificados está intacta junto con los certificados autofirmados.
- **Nombres DNS**  
    Comprueba que el SAN contiene nombres DNS apropiados para cada punto de conexión o si hay un comodín compatible.
- **Uso de la clave**  
    Comprueba si el uso de la clave contiene una firma digital y el cifrado de la clave, y si el uso mejorado de clave contiene la autenticación del servidor y del cliente.
- **Tamaño de la clave**  
    Comprueba si tamaño de la clave es 2048 o más.
- **Orden de la cadena**  
    Comprueba que el orden de los demás certificados que conforman la cadena es correcto.
- **Otros certificados**  
    Garantiza que ningún otro certificado se haya empaquetado en PFX, solo el certificado de hoja pertinente y su cadena.

> [!IMPORTANT]  
> El certificado PKI es un archivo PFX y una contraseña que deben tratarse como información confidencial.

## <a name="prerequisites"></a>Prerequisites

El sistema debe cumplir los siguientes requisitos previos para poder validar los certificados PKI de una implementación de Azure Stack Hub:

- Microsoft Azure Stack Hub Readiness Checker
- Certificados SSL exportados siguiendo las [instrucciones de preparación](azure-stack-prepare-pki-certs.md)
- DeploymentData.json
- Windows 10 o Windows Server 2016

## <a name="perform-core-services-certificate-validation"></a>Ejecución de la validación de certificados de servicios principales

Siga estos pasos para preparar y validar los certificados PKI de Azure Stack Hub para implementación y rotación de secretos:

1. Instale **AzsReadinessChecker** desde un símbolo del sistema de PowerShell (5.1 o superior) mediante la ejecución del siguiente cmdlet:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -force 
    ```

2. Cree la estructura de directorios del paquete. En el ejemplo siguiente, puede cambiar `<C:\Certificates\Deployment>` por la ruta de un nuevo directorio de su elección.
    ```powershell  
    New-Item C:\Certificates\Deployment -ItemType Directory
    
    $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    
    $destination = 'C:\Certificates\Deployment'
    
    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```
    
    > [!Note]  
    > Se requieren AD FS y Graph si utiliza AD FS como sistema de identidad. Por ejemplo:
    >
    > ```powershell  
    > $directories = 'ACSBlob', 'ACSQueue', 'ACSTable', 'ADFS', 'Admin Extension Host', 'Admin Portal', 'ARM Admin', 'ARM Public', 'Graph', 'KeyVault', 'KeyVaultInternal', 'Public Extension Host', 'Public Portal'
    > ```
    
     - Sitúe los certificados en los directorios apropiados que creó en el paso anterior. Por ejemplo:  
        - `C:\Certificates\Deployment\ACSBlob\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\Admin Portal\CustomerCertificate.pfx`
        - `C:\Certificates\Deployment\ARM Admin\CustomerCertificate.pfx`

3. En la ventana de PowerShell, cambie los valores de **RegionName** y **FQDN** apropiados para el entorno de Azure Stack Hub y ejecute lo siguiente:

    ```powershell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 
    Invoke-AzsCertificateValidation -CertificateType Deployment -CertificatePath C:\Certificates\Deployment -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD  
    ```

4. Compruebe que la salida y todos los certificados pasen todas las pruebas. Por ejemplo:

    ```powershell
    Invoke-AzsCertificateValidation v1.1912.1082.37 started.
    Testing: KeyVaultInternal\adminvault.pfx
    Thumbprint: B1CB76****************************565B99
            Expiry Date: OK
            Signature Algorithm: OK
            DNS Names: OK
            Key Usage: OK
            Key Length: OK
            Parse PFX: OK
            Private Key: OK
            Cert Chain: OK
            Chain Order: OK
            Other Certificates: OK
    Testing: ARM Public\management.pfx
    Thumbprint: 44A35E****************************36052A
            Expiry Date: OK
            Signature Algorithm: OK
            DNS Names: OK
            Key Usage: OK
            Key Length: OK
            Parse PFX: OK
            Private Key: OK
            Cert Chain: OK
            Chain Order: OK
            Other Certificates: OK
    Testing: Admin Portal\adminportal.pfx
    Thumbprint: 3F5E81****************************9EBF9A
            Expiry Date: OK
            Signature Algorithm: OK
            DNS Names: OK
            Key Usage: OK
            Key Length: OK
            Parse PFX: OK
            Private Key: OK
            Cert Chain: OK
            Chain Order: OK
            Other Certificates: OK
    Testing: Public Portal\portal.pfx

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

    Para validar certificados de otros servicios de Azure Stack Hub cambie el valor de ```-CertificateType```. Por ejemplo:

    ```powershell  
    # App Services
    Invoke-AzsCertificateValidation -CertificateType AppServices -CertificatePath C:\Certificates\AppServices -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # DBAdapter
    Invoke-AzsCertificateValidation -CertificateType DBAdapter -CertificatePath C:\Certificates\DBAdapter -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # EventHub
    Invoke-AzsCertificateValidation -CertificateType EventHubs -CertificatePath C:\Certificates\EventHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com

    # IoTHub
    Invoke-AzsCertificateValidation -CertificateType IoTHub -CertificatePath C:\Certificates\IoTHub -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com
    ```
Cada carpeta debe contener un único archivo PFX para el tipo de certificado. Si un tipo de certificado requiere varios certificados, es previsible la existencia de carpetas anidadas y con un nombre que distingue mayúsculas de minúsculas para cada certificado individual.  El siguiente código muestra una estructura de carpetas y certificados de ejemplo para todos los tipos de certificados, y el valor adecuado para ```-CertificateType``` y ```-CertificatePath```.
    
    ```powershell  
    C:\>tree c:\SecretStore /A /F
        Folder PATH listing
        Volume serial number is 85AE-DF2E
        C:\SECRETSTORE
        \---AzureStack
            +---CertificateRequests
            \---Certificates
                +---AppServices         # Invoke-AzsCertificateValidation `
                |   +---API             #     -CertificateType AppServices `
                |   |       api.pfx     #     -CertificatePath C:\Certificates\AppServices
                |   |
                |   +---DefaultDomain
                |   |       wappsvc.pfx
                |   |
                |   +---Identity
                |   |       sso.pfx
                |   |
                |   \---Publishing
                |           ftp.pfx
                |
                +---DBAdapter           # Invoke-AzsCertificateValidation `
                |       dbadapter.pfx   #   -CertificateType DBAdapter `
                |                       #   -CertificatePath C:\Certificates\DBAdapter
                |
                +---Deployment          # Invoke-AzsCertificateValidation `
                |   +---ACSBlob         #   -CertificateType Deployment `
                |   |       acsblob.pfx #   -CertificatePath C:\Certificates\Deployment
                |   |
                |   +---ACSQueue
                |   |       acsqueue.pfx
               ./. ./. ./. ./. ./. ./. ./.    <- Deployment certificate tree trimmed.
                |   \---Public Portal
                |           portal.pfx
                |
                +---EventHub            # Invoke-AzsCertificateValidation `
                |       eventhub.pfx    #   -CertificateType EventHub `
                |                       #   -CertificatePath C:\Certificates\EventHub
                |
                \---IoTHub              # Invoke-AzsCertificateValidation `
                        iothub.pfx      #   -CertificateType IoTHub `
                                        #   -CertificatePath C:\Certificates\IoTHub
    ```
### <a name="known-issues"></a>Problemas conocidos

**Síntoma**: se omiten las pruebas

**Causa**: AzsReadinessChecker omite varias pruebas si no se cumple una dependencia:

 - Otros certificados se omiten si se produce un error en la cadena de certificados.

    ```powershell  
    Testing: ACSBlob\singlewildcard.pfx
        Read PFX: OK
        Signature Algorithm: OK
        Private Key: OK
        Cert Chain: OK
        DNS Names: Fail
        Key Usage: OK
        Key Size: OK
        Chain Order: OK
        Other Certificates: Skipped
    Details:
    The certificate records '*.east.azurestack.contoso.com' do not contain a record that is valid for '*.blob.east.azurestack.contoso.com'. Please refer to the documentation for how to create the required certificate file.
    The Other Certificates check was skipped because Cert Chain and/or DNS Names failed. Follow the guidance to remediate those issues and recheck. 
    Detailed log can be found C:\AzsReadinessChecker\CertificateValidation\CertChecker.log

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
    Invoke-AzsCertificateValidation Completed
    ```

**Solución:** siga las instrucciones de la herramienta que se describen en la sección de detalles en cada conjunto de pruebas de cada certificado.

## <a name="certificates"></a>Certificados

| Directorio | Certificado |
| ---    | ----        |
| acsBlob | wildcard_blob_\<region>_\<externalFQDN> |
| ACSQueue  |  wildcard_queue_\<region>_\<externalFQDN> |
| ACSTable  |  wildcard_table_\<region>_\<externalFQDN> |
| Host de extensiones de administración  |  wildcard_adminhosting_\<region>_\<externalFQDN> |
| Admin Portal  |  adminportal_\<region>_\<externalFQDN> |
| Administración de ARM  |  adminmanagement_\<region>_\<externalFQDN> |
| Público de ARM  |  management_\<region>_\<externalFQDN> |
| KeyVault  |  wildcard_vault_\<region>_\<externalFQDN> |
| KeyVaultInternal  |  wildcard_adminvault_\<region>_\<externalFQDN> |
| Host de extensiones públicas  |  wildcard_hosting_\<region>_\<externalFQDN> |
| Public Portal  |  portal_\<region>_\<externalFQDN> |

## <a name="using-validated-certificates"></a>Uso de certificados validados

Una vez que AzsReadinessChecker valide los certificados, podrá utilizarlos en la implementación de Azure Stack Hub o en la rotación de secretos de Azure Stack Hub. 

 - En el caso de la implementación, transfiera de forma segura los certificados al ingeniero de implementación para que pueda copiarlos en el host de la implementación tal y como se especifica en la [documentación de los requisitos de infraestructura de clave pública (PKI) de Azure Stack Hub](azure-stack-pki-certs.md).
 - En el caso de la rotación de secretos, puede usar los certificados para actualizar los certificados antiguos de los puntos de conexión de la infraestructura pública del entorno de Azure Stack Hub, tal y como se indica en la [documentación de rotación de secretos de Azure Stack Hub](azure-stack-rotate-secrets.md).
 - En el caso de los servicios de PaaS, puede utilizar los certificados para instalar proveedores de recursos de SQL, MySQL y App Services en Azure Stack Hub siguiendo la [documentación de introducción a la oferta de servicios en Azure Stack Hub](service-plan-offer-subscription-overview.md).

## <a name="next-steps"></a>Pasos siguientes

[Integración de identidades de centros de datos](azure-stack-integrate-identity.md)
