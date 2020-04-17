---
title: Generación de solicitudes de firma de certificado para Azure Stack Hub
description: Obtenga información acerca de cómo generar solicitudes de firma para los certificados PKI de Azure Stack Hub en los sistemas integrados de Azure Stack Hub.
author: IngridAtMicrosoft
ms.topic: article
ms.date: 09/10/2019
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: d197a8b4464af8f331a11af2ba642ad053273bf9
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "77699718"
---
# <a name="generate-certificate-signing-requests-for-azure-stack-hub"></a>Generación de solicitudes de firma de certificado para Azure Stack Hub

Puede usar la herramienta Azure Stack Hub Readiness Checker para crear solicitudes de firma de certificado (CSR) adecuadas para una implementación de Azure Stack Hub. Los certificados se deben solicitar, generar y validar con suficiente tiempo para probarlos antes de la implementación. Puede obtener la herramienta [en la Galería de PowerShell](https://aka.ms/AzsReadinessChecker).

Puede usar la herramienta Azure Stack Hub Readiness Checker (AzsReadinessChecker) para solicitar los siguientes certificados:

- **Solicitudes de certificado estándar** según se indica en [Generación de solicitudes de firma de certificado](azure-stack-get-pki-certs.md#generate-certificate-signing-requests).
- **Plataforma como servicio**: Puede solicitar nombres de Plataforma como servicio (PaaS) para los certificados como se especifica en [Requisitos de certificados de infraestructura de clave pública de Azure Stack Hub: Certificados PaaS opcionales](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Prerrequisitos

El sistema debe cumplir los siguientes requisitos previos antes de generar los CSR para los certificados PKI para una implementación de Azure Stack Hub:

- Microsoft Azure Stack Hub Readiness Checker
- Atributos de certificado:
  - Nombre de la región
  - Nombres de dominio completos (FQDN) externos
  - Asunto
- Windows 10 o Windows Server 2016

  > [!NOTE]  
  > Cuando reciba los certificados de vuelta de la entidad de certificación, deberá completar los pasos descritos en [Preparación de certificados PKI de Azure Stack Hub](azure-stack-prepare-pki-certs.md) en el mismo sistema.

## <a name="generate-certificate-signing-requests"></a>Generación de solicitudes de firma de certificado

Siga estos pasos para preparar y validar los certificados PKI de Azure Stack Hub:

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
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Para generar solicitudes de certificados de otros servicios de Azure Stack Hub, cambie el valor de `-CertificateType`. Por ejemplo:

    ```powershell  
    # App Services
    New-AzsCertificateSigningRequest -certificateType AppServices -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # DBAdapter
    New-AzsCertificateSigningRequest -certificateType DBAdapter -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # EventHubs
    New-AzsCertificateSigningRequest -certificateType EventHubs -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory

    # IoTHub
    New-AzsCertificateSigningRequest -certificateType IoTHub -RegionName $regionName -FQDN $externalFQDN -subject $subject -OutputRequestPath $OutputDirectory
    ```

7. Como alternativa, para entornos de desarrollo y pruebas, para generar una única solicitud de certificado con varios nombres alternativos del firmante, agregue el parámetro **-RequestType SingleCSR** y el valor (**no** se recomienda para entornos de producción):

    ```powershell  
    New-AzsCertificateSigningRequest -certificateType Deployment -RegionName $regionName -FQDN $externalFQDN -RequestType SingleCSR -subject $subject -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

8.  Revise la salida:

    ```powershell  
    New-AzsCertificateSigningRequest v1.1912.1082.37 started.
    Starting Certificate Request Process for Deployment
    CSR generating for following SAN(s): *.adminhosting.east.azurestack.contoso.com,*.adminvault.east.azurestack.contoso.com,*.blob.east.azurestack.contoso.com,*.hosting.east.azurestack.contoso.com,*.queue.east.azurestack.contoso.com,*.table.east.azurestack.contoso.com,*.vault.east.azurestack.contoso.com,adminmanagement.east.azurestack.contoso.com,adminportal.east.azurestack.contoso.com,management.east.azurestack.contoso.com,portal.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\checker\Documents\AzureStackCSR\wildcard_adminhosting_east_azurestack_contoso_com_CertRequest_20191219140359.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9.  Envíe el archivo **.REQ** generado a la entidad de certificación (puede ser interna o pública). El directorio de salida de **New-AzsCertificateSigningRequest** contiene las solicitudes de firma de certificados (CSR) necesarias que se enviarán a una entidad de certificación. Asimismo, también tiene un directorio secundario que contiene los archivos INF que se usan durante la generación de una solicitud de certificado, a modo de referencia. Asegúrese de que la entidad de certificación genera certificados mediante la solicitud generada que cumple los [Requisitos de PKI de Azure Stack Hub](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Pasos siguientes

[Preparación de certificados PKI de Azure Stack Hub](azure-stack-prepare-pki-certs.md)
