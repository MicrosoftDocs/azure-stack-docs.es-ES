---
title: Generación de solicitudes de firma de certificado para Azure Stack Hub
description: Obtenga información acerca de cómo generar solicitudes de firma para los certificados PKI de Azure Stack Hub en los sistemas integrados de Azure Stack Hub.
author: PatAltimore
ms.topic: article
ms.date: 10/19/2020
ms.author: patricka
ms.reviewer: ppacent
ms.lastreviewed: 10/19/2020
ms.openlocfilehash: b03766efe531683310b81dbf2d03de8e990deec0
ms.sourcegitcommit: 733a22985570df1ad466a73cd26397e7aa726719
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/05/2021
ms.locfileid: "97870450"
---
# <a name="generate-certificate-signing-requests-for-azure-stack-hub"></a>Generación de solicitudes de firma de certificado para Azure Stack Hub

Puede usar la herramienta Azure Stack Hub Readiness Checker para crear solicitudes de firma de certificado (CSR) adecuadas para una implementación de Azure Stack Hub. Los certificados se deben solicitar, generar y validar con suficiente tiempo para probarlos antes de la implementación. Puede obtener la herramienta [en la Galería de PowerShell](https://aka.ms/AzsReadinessChecker).

Puede usar la herramienta Azure Stack Hub Readiness Checker (AzsReadinessChecker) para solicitar los siguientes certificados:

- **Solicitudes de certificado estándar** según se indica en [Generación de solicitudes de firma de certificado para nuevas implementaciones](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-new-deployments).
- **Renovación de solicitudes de certificado estándar** según se indica en [Generación de solicitudes de firma de certificado para nuevas implementaciones](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-certificate-renewal).
- **Plataforma como servicio**: Puede solicitar nombres de Plataforma como servicio (PaaS) para los certificados como se especifica en [Requisitos de certificados de infraestructura de clave pública de Azure Stack Hub: Certificados PaaS opcionales](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Requisitos previos

El sistema debe cumplir los siguientes requisitos previos antes de generar los CSR para los certificados PKI para una implementación de Azure Stack Hub:

- Microsoft Azure Stack Hub Readiness Checker
- Atributos de certificado:
  - Nombre de la región
  - Nombres de dominio completos (FQDN) externos
  - Asunto
- Windows 10 o Windows Server 2016

  > [!NOTE]  
  > Cuando reciba los certificados de vuelta de la entidad de certificación, deberá completar los pasos descritos en [Preparación de certificados PKI de Azure Stack Hub](azure-stack-prepare-pki-certs.md) en el mismo sistema.

## <a name="generate-certificate-signing-requests-for-new-deployments"></a>Generación de solicitudes de firma de certificados para nuevas implementaciones

Siga estos pasos para preparar solicitudes de firma de certificados para los nuevos certificados PKI de Azure Stack Hub:

1. Instale AzsReadinessChecker desde un símbolo del sistema de PowerShell (5.1 o superior) mediante la ejecución del siguiente cmdlet:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Declare el **asunto**. Por ejemplo:

    ```powershell  
    $subject = "C=US,ST=Washington,L=Redmond,O=Microsoft,OU=Azure Stack Hub"
    ```

    > [!NOTE]  
    > Si se proporciona un nombre común, este se configurará en cada solicitud de certificado. Si se omite este nombre, el primer nombre DNS del servicio Azure Stack Hub se configurará en la solicitud del certificado.

3. Declare un directorio de salida que ya exista. Por ejemplo:

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Declare el sistema de identidad.

    Azure Active Directory (Azure AD):

    ```powershell
    $IdentitySystem = "AAD"
    ```

    Servicios de federación de Active Directory (AD FS):

    ```powershell
    $IdentitySystem = "ADFS"
    ```
    > [!NOTE]  
    > El parámetro solo se necesita para la implementación de CertificateType.

5. Declare un **nombre de región** y un **FQDN externo** pensado para la implementación de Azure Stack Hub.

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!NOTE]  
    > `<regionName>.<externalFQDN>` constituye la base en la que se crean todos los nombres DNS externos de Azure Stack Hub. En este ejemplo, el portal sería `portal.east.azurestack.contoso.com`.  

6. Para generar solicitudes de firma de certificados para la implementación:

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Para generar solicitudes de certificados de otros servicios de Azure Stack Hub, cambie el valor de `-CertificateType`. Por ejemplo:

    ```powershell  
    # App Services
    New-AzsHubAppServicesCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsHubDbAdapterCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsHubEventHubsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsHubIoTHubCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory
    ```

7. Como alternativa, para entornos de desarrollo y pruebas, para generar una única solicitud de certificado con varios nombres alternativos del firmante, agregue el parámetro **-RequestType SingleCSR** y el valor (**no** se recomienda para entornos de producción):

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -RequestType SingleCSR -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

8.  Revise la salida:

    ```powershell  
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\[*redacted*]\Documents\AzureStackCSR\Deployment_east_azurestack_contoso_com_SingleCSR_CertRequest_20200710165538.req
    Certreq.exe output: CertReq: Request Created
    ```

9.  Envíe el archivo **.REQ** generado a la entidad de certificación (puede ser interna o pública). El directorio de salida de **New-AzsCertificateSigningRequest** contiene las solicitudes de firma de certificados (CSR) necesarias que se enviarán a una entidad de certificación. Asimismo, también tiene un directorio secundario que contiene los archivos INF que se usan durante la generación de una solicitud de certificado, a modo de referencia. Asegúrese de que la entidad de certificación genera certificados mediante la solicitud generada que cumple los [Requisitos de PKI de Azure Stack Hub](azure-stack-pki-certs.md).

## <a name="generate-certificate-signing-requests-for-certificate-renewal"></a>Generación de solicitudes de firma de certificados para la renovación de certificados

Siga estos pasos para preparar solicitudes de firma de certificados para la renovación de los certificados PKI de Azure Stack Hub existentes:

1. Instale AzsReadinessChecker desde un símbolo del sistema de PowerShell (5.1 o superior) mediante la ejecución del siguiente cmdlet:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker -Force -AllowPrerelease
    ```

2. Declare el valor de **stampEndpoint** con el formato regionname.domain.com del sistema de Azure Stack Hub. Por ejemplo, si la dirección del portal del inquilino de Azure Stack Hub es <code> https://</code><code>portal.east.azurestack.contoso.com</code>):

    ```powershell  
    $stampEndpoint = 'east.azurestack.contoso.com'
    ```

    > [!NOTE]  
    > Se requiere conectividad HTTPS para el sistema de Azure Stack Hub anterior.
    > Readiness Checker utilizará el valor de stampendpoint (región y dominio) para crear un puntero a los certificados existentes necesarios por tipo de certificado. Por ejemplo, para los certificados de implementación, la herramienta antepone "portal", de modo que, para la clonación de certificados, se utiliza portal.east.azurestack.contoso.com, para AppServices se utiliza sso.appservices.east.azurestack.contoso.com, etc. El certificado enlazado al punto de conexión procesado se usará para clonar atributos como los de asunto, longitud de la clave y algoritmo de firma.  Si desea cambiar cualquiera de estos atributos, debe seguir los pasos de [Generación de solicitudes de firma de certificado para nuevas implementaciones](azure-stack-get-pki-certs.md#generate-certificate-signing-requests-for-new-deployments) en su lugar.

3. Declare un directorio de salida que ya exista. Por ejemplo:

    ```powershell  
    $outputDirectory = "$ENV:USERPROFILE\Documents\AzureStackCSR"
    ```

4. Para generar solicitudes de firma de certificados para la implementación:

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory
    ```

    Para generar solicitudes de certificados de otros servicios de Azure Stack Hub use:

    ```powershell  
    # App Services
    New-AzsHubAppServicesCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsHubDBAdapterCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsHubEventHubsCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsHubIotHubCertificateSigningRequest -StampEndpoint $stampEndpoint -OutputRequestPath $OutputDirectory
    ```

5. Como alternativa, para entornos de desarrollo y pruebas, para generar una única solicitud de certificado con varios nombres alternativos del firmante, agregue el parámetro **-RequestType SingleCSR** y el valor (**no** se recomienda para entornos de producción):

    ```powershell  
    New-AzsHubDeploymentCertificateSigningRequest -StampEndpoint $stampendpoint -OutputRequestPath $OutputDirectory -RequestType SingleCSR
    ```

6.  Revise la salida:

    ```powershell  
    Querying StampEndpoint portal.east.azurestack.contoso.com for existing certificate
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\[*redacted*]\Documents\AzureStackCSR\Deployment_east_azurestack_contoso_com_SingleCSR_CertRequest_20200710122723.req
    Certreq.exe output: CertReq: Request Created
    ```

7.  Envíe el archivo **.REQ** generado a la entidad de certificación (puede ser interna o pública). El directorio de salida de **New-AzsCertificateSigningRequest** contiene las solicitudes de firma de certificados (CSR) necesarias que se enviarán a una entidad de certificación. Asimismo, también tiene un directorio secundario que contiene los archivos INF que se usan durante la generación de una solicitud de certificado, a modo de referencia. Asegúrese de que la entidad de certificación genera certificados mediante la solicitud generada que cumple los [Requisitos de PKI de Azure Stack Hub](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Pasos siguientes

[Preparación de certificados PKI de Azure Stack Hub](azure-stack-prepare-pki-certs.md)
