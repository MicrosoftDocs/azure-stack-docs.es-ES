---
title: Rotación de secretos y certificados de App Service en Azure Stack Hub
description: Aprenda a rotar secretos y certificados usados por Azure App Service en Azure Stack Hub.
author: apwestgarth
ms.topic: article
ms.date: 04/09/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/09/2020
ms.openlocfilehash: 3a39329ba8c99e919b2466705553e11b49424c45
ms.sourcegitcommit: ddcd083430ca905653d412dc2f7b813218d79509
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/13/2020
ms.locfileid: "83374727"
---
# <a name="rotate-app-service-on-azure-stack-hub-secrets-and-certificates"></a>Rotación de secretos y certificados de App Service en Azure Stack Hub

Estas instrucciones solo se aplican a Azure App Service en Azure Stack Hub. La rotación de los secretos de Azure App Service en Azure Stack Hub no se incluye en el procedimiento centralizado de rotación de secretos de Azure Stack Hub. Los operadores pueden supervisar la validez de los secretos dentro del sistema, la fecha en la que se actualizaron por última vez y el tiempo restante hasta que expiren.

> [!Important]
> Los operadores no recibirán alertas en el panel de Azure Stack Hub cuando expire un secreto, ya que Azure App Service en Azure Stack Hub no está integrado con el servicio de alertas de Azure Stack Hub. Los operadores deben supervisar regularmente sus secretos con la experiencia de administración de Azure App Service en Azure Stack Hub en el portal de administración de Azure Stack Hub.

Este documento contiene el procedimiento para rotar los siguientes secretos:

* Claves de cifrado usadas en Azure App Service en Azure Stack Hub.
* Credenciales de conexión de base de datos que se usan en Azure App Service en Azure Stack Hub para interactuar con las bases de datos de hospedaje y medición.
* Certificados usados por Azure App Service en Azure Stack Hub para proteger los puntos de conexión y la rotación del certificado de la aplicación de identidad en Azure Active Directory (Azure AD) o los Servicios de federación de Active Directory (AD FS).
* Credenciales del sistema de los roles de infraestructura de Azure App Service en Azure Stack Hub.

## <a name="rotate-encryption-keys"></a>Rotación de las claves de cifrado

Para rotar las claves de cifrado usadas en Azure App Service en Azure Stack Hub, realice los pasos siguientes:

1. Vaya a la experiencia de administración de App Service en el portal de administración de Azure Stack Hub.

1. Vaya a la opción de menú **Secrets** (Secretos).

1. Haga clic en el botón **Rotate** (Rotar) de la sección de claves de cifrado.

1. Seleccione **OK** (Aceptar) para iniciar el procedimiento de rotación.

1. Las claves de cifrado se rotan y se actualizan todas las instancias de rol. Los operadores pueden comprobar el estado del procedimiento mediante el botón **Status** (Estado).

## <a name="rotate-connection-strings"></a>Rotación de las cadenas de conexión

Para actualizar las credenciales de la cadena de conexión de las bases de datos de hospedaje y medición de App Service, realice los pasos siguientes:

1. Vaya a la experiencia de administración de App Service en el portal de administración de Azure Stack Hub.

1. Vaya a la opción de menú **Secrets** (Secretos).

1. Seleccione el botón **Rotate** (Rotar) de la sección de cadenas de conexión.

1. Proporcione el **nombre de usuario de SA de SQL** y la **contraseña** y seleccione **OK** (Aceptar) para iniciar el procedimiento de rotación.

1. Las credenciales se rotarán en todas las instancias de rol de Azure App Service. Los operadores pueden comprobar el estado del procedimiento mediante el botón **Status** (Estado).

## <a name="rotate-certificates"></a>Rotación de certificados

Para rotar los certificados usados en Azure App Service en Azure Stack Hub, realice los pasos siguientes:

1. Vaya a la experiencia de administración de App Service en el portal de administración de Azure Stack Hub.

1. Vaya a la opción de menú **Secrets** (Secretos).

1. Seleccione el botón **Rotate** (Rotar) de la sección de certificados.

1. Proporcione el **archivo de certificado** y la **contraseña** asociada de los certificados que quiere rotar y seleccione **OK** (Aceptar).

1. Los certificados se rotarán según sea necesario en las instancias de rol de Azure App Service en Azure Stack Hub. Los operadores pueden comprobar el estado del procedimiento mediante el botón **Status** (Estado).

Cuando se rota el certificado de la aplicación de identidad, la aplicación correspondiente de Azure AD o AD FS también se debe actualizar con el nuevo certificado.

> [!IMPORTANT]
> Si no se actualiza la aplicación de identidad con el nuevo certificado después de la rotación, se interrumpirá la experiencia del portal de usuarios para Azure Functions, impedirá que los usuarios puedan usar las herramientas de desarrollo de KUDU e impedirá que los administradores administren los conjuntos de escalado de los niveles de trabajo desde la experiencia de administración de App Service.

### <a name="rotate-credential-for-the-azure-ad-identity-application"></a>Rotación de credenciales de la aplicación de identidad de Azure AD

El operador crea la aplicación de identidad antes de la implementación de Azure App Service en Azure Stack Hub. Si se desconoce el identificador de aplicación, siga estos pasos para averiguarlo:

1. Abra el **portal de administración de Azure Stack Hub**.

1. Vaya a **Subscriptions** (Suscripciones) y seleccione **Default Provider Subscription** (Suscripción de proveedor predeterminada).

1. Seleccione **Access Control (IAM)** y seleccione la aplicación **App Service**.

1. Tome nota del valor de **APP ID** (identificador de aplicación), este valor es el identificador de la aplicación de identidad que se debe actualizar en Azure AD.

Para rotar el certificado para la aplicación en Azure AD, siga estos pasos:

1. Vaya a **Azure Portal** e inicie sesión con el administrador global que se usó para implementar Azure Stack Hub.

1. Vaya a **Azure Active Directory** y, luego, a **Registros de aplicaciones**.

1. Busque el campo **Identificador de aplicación** y especifique el identificador de la aplicación de identidad.

1. Seleccione la aplicación y, después, vaya a **Certificados y secretos**.

1. Seleccione **Cargar certificado** y cargue el nuevo certificado para la aplicación de identidad con uno de los siguientes tipos de archivo: .cer, .pem y .crt.

1. Confirme que la **huella digital** coincide con la que aparece en la experiencia de administración de App Service en el portal de administración de Azure Stack Hub.

1. Elimine el antiguo certificado.

### <a name="rotate-certificate-for-ad-fs-identity-application"></a>Rotación del certificado para la aplicación de identidad de ADFS

El operador crea la aplicación de identidad antes de la implementación de Azure App Service en Azure Stack Hub. Si se desconoce el identificador de objeto de la aplicación, siga estos pasos para averiguarlo:

1. Abra el **portal de administración de Azure Stack Hub**.

1. Vaya a **Subscriptions** (Suscripciones) y seleccione **Default Provider Subscription** (Suscripción de proveedor predeterminada).

1. Seleccione **Access Control (IAM)** y seleccione la aplicación **AzureStack-AppService<guid>** .

1. Tome nota del campo **Object ID** (Identificador de objeto), este valor es el identificador de la entidad de servicio que se debe actualizar en AD FS.

Para rotar el certificado de la aplicación en AD FS, debe tener acceso al punto de conexión con privilegios (PEP). A continuación, actualice la credencial de certificado con PowerShell y reemplace sus propios valores por los marcadores de posición siguientes:

| Marcador de posición | Descripción | Ejemplo |
| ----------- | ----------- | ------- |
| `<PepVM>` | Nombre de la VM del extremo de conexión con privilegios en la instancia de Azure Stack Hub. | "AzS-ERCS01" |
| `<CertificateFileLocation>` | Ubicación en el disco del certificado X509. | "d:\certs\sso.cer" |
| `<ApplicationObjectId>` | Identificador asignado a la aplicación de identidad. | "S-1-5-21-401916501-2345862468-1451220656-1451" |

1. Abra una sesión de Windows PowerShell con privilegios elevados y ejecute el script siguiente:

    ```powershell
    # Sign in to PowerShell interactively, using credentials that have access to the VM running the Privileged Endpoint
    $Creds = Get-Credential

    # Create a new Certificate object from the identity application certificate exported as .cer file
    $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2("<CertificateFileLocation>")

    # Create a new PSSession to the PrivelegedEndpoint VM
    $Session = New-PSSession -ComputerName "<PepVm>" -ConfigurationName PrivilegedEndpoint -Credential $Creds

    # Use the privileged endpoint to update the certificate thumbprint, used by the service principal associated with the App Service identity application
    $SpObject = Invoke-Command -Session $Session -ScriptBlock {Set-GraphApplication -ApplicationIdentifier "<ApplicationObjectId>" -ClientCertificates $using:Cert}
    $Session | Remove-PSSession

    # Output the updated service principal details
    $SpObject

    ```

1. Cuando el script finaliza, muestra la información de registro de la aplicación actualizada, incluido el valor de la huella digital del certificado.

    ```shell
    ApplicationIdentifier : S-1-5-21-401916501-2345862468-1451220656-1451
    ClientId              : 
    Thumbprint            : FDAA679BF9EDDD0CBB581F978457A37BFD73CA3B
    ApplicationName       : Azurestack-AppService-d93601c2-1ec0-4cac-8d1c-8ccde63ef308
    ClientSecret          : 
    PSComputerName        : AzS-ERCS01
    RunspaceId            : cb471c79-a0d3-40ec-90ba-89087d104510
    ```

## <a name="rotate-system-credentials"></a>Rotación de las credenciales del sistema

Para rotar las credenciales del sistema usadas en Azure App Service en Azure Stack Hub, realice los pasos siguientes:

1. Vaya a la experiencia de administración de App Service en el portal de administración de Azure Stack Hub.

1. Vaya a la opción de menú **Secrets** (Secretos).

1. Seleccione el botón **Rotate** (Rotar) de la sección de credenciales del sistema.

1. Seleccione el **ámbito** de la credencial del sistema que va a rotar. Los operadores pueden optar por rotar las credenciales del sistema de todos los roles o solo de algunos.

1. Especifique un **nuevo nombre de usuario administrador local** y una nueva **contraseña**. A continuación, confirme la **contraseña** y seleccione **OK** (Aceptar).

1. Las credenciales se rotarán según sea necesario en la instancia de rol correspondiente de Azure App Service en Azure Stack Hub. Los operadores pueden comprobar el estado del procedimiento mediante el botón **Status** (Estado).
