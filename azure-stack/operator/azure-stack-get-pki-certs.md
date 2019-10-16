---
title: Generación de solicitudes de firma de certificado para Azure Stack | Microsoft Docs
description: Obtenga información acerca de cómo generar solicitudes de firma para los certificados PKI de Azure Stack en los sistemas integrados de Azure Stack.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/10/2019
ms.author: justinha
ms.reviewer: ppacent
ms.lastreviewed: 09/10/2019
ms.openlocfilehash: 365f727f7e07c697dc2fd3cfe2a5c1bea5b68409
ms.sourcegitcommit: 451cfaa24b349393f36ae9d646d4d311a14dd1fd
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/07/2019
ms.locfileid: "72019247"
---
# <a name="generate-certificate-signing-requests-for-azure-stack"></a>Generación de solicitudes de firma de certificado para Azure Stack

Puede usar la herramienta Azure Stack Readiness Checker para crear solicitudes de firma de certificado (CSR) adecuadas para una implementación de Azure Stack. Los certificados se deben solicitar, generar y validar con suficiente tiempo para probarlos antes de la implementación. Puede obtener la herramienta [en la Galería de PowerShell](https://aka.ms/AzsReadinessChecker).

Puede usar la herramienta Azure Stack Readiness Checker (AzsReadinessChecker) para solicitar los siguientes certificados:

- **Solicitudes de certificado estándar** según se indica en [Generación de solicitudes de firma de certificado](azure-stack-get-pki-certs.md#generate-certificate-signing-requests).
- **Plataforma como servicio**: Puede solicitar nombres de Plataforma como servicio (PaaS) para los certificados como se especifica en [Requisitos de certificados de infraestructura de clave pública de Azure Stack: Certificados PaaS opcionales](azure-stack-pki-certs.md#optional-paas-certificates).

## <a name="prerequisites"></a>Requisitos previos

El sistema debe cumplir los siguientes requisitos previos antes de generar los CSR para los certificados PKI para una implementación de Azure Stack:

- Microsoft Azure Stack Readiness Checker
- Atributos de certificado:
  - Nombre de la región
  - Nombres de dominio completos (FQDN) externos
  - Asunto
- Windows 10 o Windows Server 2016

  > [!NOTE]  
  > Cuando reciba los certificados de vuelta de la entidad de certificación, deberá completar los pasos descritos en [Preparación de certificados PKI de Azure Stack](azure-stack-prepare-pki-certs.md) en el mismo sistema.

## <a name="generate-certificate-signing-requests"></a>Generación de solicitudes de firma de certificado

Siga estos pasos para preparar y validar los certificados PKI de Azure Stack:

1. Instale AzsReadinessChecker desde un símbolo del sistema de PowerShell (5.1 o superior) mediante la ejecución del siguiente cmdlet:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```

2. Declare el **asunto** como un diccionario ordenado. Por ejemplo:

    ```powershell  
    $subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
    ```

    > [!note]  
    > Si se proporciona un nombre común (CN), se sobrescribirá con el primer nombre DNS de la solicitud de certificado.

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

5. Declare un **nombre de región** y un **FQDN externo** pensado para la implementación de Azure Stack.

    ```powershell
    $regionName = 'east'
    $externalFQDN = 'azurestack.contoso.com'
    ```

    > [!note]  
    > `<regionName>.<externalFQDN>` constituye la base en la que se crean todos los nombres DNS externos de Azure Stack. En este ejemplo, el portal sería `portal.east.azurestack.contoso.com`.  

6. Para generar solicitudes de firma de certificados para cada nombre DNS:

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Para incluir los servicios PaaS, especifique el conmutador ```-IncludePaaS```.

7. Como alternativa, para entornos de desarrollo y pruebas, para generar una única solicitud de certificado con varios nombres alternativos del firmante, agregue el parámetro **-RequestType SingleCSR** y el valor (**no** se recomienda para entornos de producción):

    ```powershell  
    New-AzsCertificateSigningRequest -RegionName $regionName -FQDN $externalFQDN -subject $subjectHash -RequestType SingleCSR -OutputRequestPath $OutputDirectory -IdentitySystem $IdentitySystem
    ```

    Para incluir los servicios PaaS, especifique el conmutador ```-IncludePaaS```

8. Revise la salida:

    ```powershell  
    New-AzsCertificateSigningRequest v1.1809.1005.1 started.

    CSR generating for following SAN(s): dns=*.east.azurestack.contoso.com&dns=*.blob.east.azurestack.contoso.com&dns=*.queue.east.azurestack.contoso.com&dns=*.table.east.azurestack.cont
    oso.com&dns=*.vault.east.azurestack.contoso.com&dns=*.adminvault.east.azurestack.contoso.com&dns=portal.east.azurestack.contoso.com&dns=adminportal.east.azurestack.contoso.com&dns=ma
    nagement.east.azurestack.contoso.com&dns=adminmanagement.east.azurestack.contoso.com*dn2=*.adminhosting.east.azurestack.contoso.com@dns=*.hosting.east.azurestack.contoso.com
    Present this CSR to your Certificate Authority for Certificate Generation: C:\Users\username\Documents\AzureStackCSR\wildcard_east_azurestack_contoso_com_CertRequest_20180405233530.req
    Certreq.exe output: CertReq: Request Created

    Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    New-AzsCertificateSigningRequest Completed
    ```

9. Envíe el archivo **.REQ** generado a la entidad de certificación (puede ser interna o pública). El directorio de salida de **New-AzsCertificateSigningRequest** contiene las solicitudes de firma de certificados (CSR) necesarias que se enviarán a una entidad de certificación. Asimismo, también tiene un directorio secundario que contiene los archivos INF que se usan durante la generación de una solicitud de certificado, a modo de referencia. Asegúrese de que la entidad de certificación genera certificados mediante la solicitud generada que cumple los [Requisitos de PKI de Azure Stack](azure-stack-pki-certs.md).

## <a name="next-steps"></a>Pasos siguientes

[Preparación de certificados PKI de Azure Stack](azure-stack-prepare-pki-certs.md)
