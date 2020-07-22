---
title: Preparación de certificados PKI de Azure Stack Hub para implementación o rotación
titleSuffix: Azure Stack Hub
description: Aprenda a preparar certificados PKI para la implementación de sistemas integrados de Azure Stack Hub o para rotar secretos de un entorno de Azure Stack Hub existente.
author: IngridAtMicrosoft
ms.topic: how-to
ms.date: 03/04/2020
ms.author: inhenkel
ms.reviewer: ppacent
ms.lastreviewed: 09/16/2019
ms.openlocfilehash: a371723250e52fb98e5c1ab7dc3151d1d4b9cbf3
ms.sourcegitcommit: c1f48c19c8a9c438fd22298bc570c12a9b19bb45
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/16/2020
ms.locfileid: "86410647"
---
# <a name="prepare-azure-stack-hub-pki-certificates-for-deployment-or-rotation"></a>Preparación de certificados PKI de Azure Stack Hub para implementación o rotación

Los archivos de certificado [obtenidos de la entidad de certificación (CA) de su elección](azure-stack-get-pki-certs.md) deben importarse y exportarse de forma que las propiedades coincidan con los requisitos de certificado de Azure Stack Hub.

## <a name="prepare-certificates-for-deployment-with-azure-stack-readiness-checker"></a>Preparación de certificados para la implementación (con Azure Stack Readiness Checker)

Use la herramienta Azure Stack Hub Readiness Checker para importar, empaquetar y validar certificados listos para la implementación o la rotación.

## <a name="prerequisites"></a>Requisitos previos

El sistema debe cumplir los siguientes requisitos previos para empaquetar los certificados PKI de una implementación de Azure Stack Hub:

- Microsoft Azure Stack Hub Readiness Checker
- Certificados devueltos por la entidad de certificación en un único directorio en formato .cer (otros formatos configurables .cert, .SST o .pfx).
- Windows 10 o Windows Server 2016
- Use el mismo sistema que generó la solicitud de firma de certificado. (A menos que el destino del certificado anterior se haya empaquetado previamente en PFXs)

## <a name="generate-certificate-signing-requests-for-new-deployments"></a>Generación de solicitudes de firma de certificados para nuevas implementaciones

Siga estos pasos para empaquetar los certificados de los nuevos certificados PKI de Azure Stack Hub:

1. Instale AzsReadinessChecker desde un símbolo del sistema de PowerShell (5.1 o superior) mediante la ejecución del siguiente cmdlet:

    ```powershell  
        Install-Module Microsoft.AzureStack.ReadinessChecker
    ```
2. Declare la **ruta de acceso** en la que los certificados residen en el disco. Por ejemplo:

    ```powershell  
        $Path = "$env:USERPROFILE\Documents\AzureStack"
    ```

3. Declare la contraseña **pfxPassword**. Por ejemplo:

    ```powershell  
        $pfxPassword = Read-Host -AsSecureString -Prompt "PFX Password"
    ```
4. Declare la ruta de **ExportPath** a la que se exportarán los PFX resultantes. Por ejemplo:

    ```powershell  
        $ExportPath = "$env:USERPROFILE\Documents\AzureStack"
    ```

5. Convierta los certificados en certificados de Azure Stack Hub. Por ejemplo:

    ```powershell  
        ConvertTo-AzsPFX -Path $Path -pfxPassword $pfxPassword -ExportPath $ExportPath
    ```
8.  Revise la salida:

    ```powershell  
    ConvertTo-AzsPFX v1.2005.1286.272 started.

    Stage 1: Scanning Certificates
        Path: C:\Users\[*redacted*]\Documents\AzureStack Filter: CER Certificate count: 11
        adminmanagement_east_azurestack_contoso_com_CertRequest_20200710235648.cer
        adminportal_east_azurestack_contoso_com_CertRequest_20200710235645.cer
        management_east_azurestack_contoso_com_CertRequest_20200710235644.cer
        portal_east_azurestack_contoso_com_CertRequest_20200710235646.cer
        wildcard_adminhosting_east_azurestack_contoso_com_CertRequest_20200710235649.cer
        wildcard_adminvault_east_azurestack_contoso_com_CertRequest_20200710235642.cer
        wildcard_blob_east_azurestack_contoso_com_CertRequest_20200710235653.cer
        wildcard_hosting_east_azurestack_contoso_com_CertRequest_20200710235652.cer
        wildcard_queue_east_azurestack_contoso_com_CertRequest_20200710235654.cer
        wildcard_table_east_azurestack_contoso_com_CertRequest_20200710235650.cer
        wildcard_vault_east_azurestack_contoso_com_CertRequest_20200710235647.cer

    Detected ExternalFQDN: east.azurestack.contoso.com

    Stage 2: Exporting Certificates
        east.azurestack.contoso.com\Deployment\ARM Admin\ARMAdmin.pfx
        east.azurestack.contoso.com\Deployment\Admin Portal\AdminPortal.pfx
        east.azurestack.contoso.com\Deployment\ARM Public\ARMPublic.pfx
        east.azurestack.contoso.com\Deployment\Public Portal\PublicPortal.pfx
        east.azurestack.contoso.com\Deployment\Admin Extension Host\AdminExtensionHost.pfx
        east.azurestack.contoso.com\Deployment\KeyVaultInternal\KeyVaultInternal.pfx
        east.azurestack.contoso.com\Deployment\ACSBlob\ACSBlob.pfx
        east.azurestack.contoso.com\Deployment\Public Extension Host\PublicExtensionHost.pfx
        east.azurestack.contoso.com\Deployment\ACSQueue\ACSQueue.pfx
        east.azurestack.contoso.com\Deployment\ACSTable\ACSTable.pfx
        east.azurestack.contoso.com\Deployment\KeyVault\KeyVault.pfx

    Stage 3: Validating Certificates.

    Validating east.azurestack.contoso.com-Deployment-AAD certificates in C:\Users\[*redacted*]\Documents\AzureStack\east.azurestack.contoso.com\Deployment 

    Testing: KeyVaultInternal\KeyVaultInternal.pfx
    Thumbprint: E86699****************************4617D6
        PFX Encryption: OK
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
    Testing: ARM Public\ARMPublic.pfx
        ...
    Log location (contains PII): C:\Users\[*redacted*]\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
    ConvertTo-AzsPFX Completed
    ```
    > [!NOTE]
    > Para un uso adicional, use Get-help ConvertTo-AzsPFX -Full para un uso avanzado como, por ejemplo, para deshabilitar la validación o el filtrado de los diferentes formatos de certificado.

    Después de una validación correcta, se pueden presentar los certificados para su implementación o rotación sin necesidad de realizar ningún paso adicional.

## <a name="prepare-certificates-for-deployment-manual-steps"></a>Preparación de los certificados para la implementación (pasos manuales)

Siga estos pasos para preparar y validar los certificados PKI de Azure Stack Hub que se usarán para implementar un nuevo entorno de Azure Stack Hub o para rotar los secretos de un entorno existente de Azure Stack Hub.

### <a name="import-the-certificate"></a>Importación del certificado

1. Copie las versiones de los certificados originales [obtenidas de la entidad de certificación de elección](azure-stack-get-pki-certs.md) en un directorio del host de implementación. 
   > [!WARNING]
   > No copie los archivos que ya se han importado, exportado o modificado de alguna forma de los archivos proporcionados directamente por la entidad de certificación.

1. Haga clic con el botón derecho en el certificado y seleccione **Instalar certificado** o **Instalar PFX**, dependiendo de cómo se entrega el certificado desde la entidad de certificación.

1. En el **Asistente para importar certificados**, seleccione **Máquina local** como la ubicación de importación. Seleccione **Next** (Siguiente). En la siguiente pantalla, vuelva a seleccionar Siguiente.

    ![Ubicación de importación de la máquina local para el certificado](./media/prepare-pki-certs/1.png)

1. Elija **Colocar todos los certificados en el siguiente almacén** y, luego, seleccione **Confianza empresarial** como ubicación. Seleccione **Aceptar** para cerrar el cuadro de diálogo de selección de almacén de certificados y, luego, seleccione **Siguiente**.

   ![Configuración del almacén de certificados para la importación de certificados](./media/prepare-pki-certs/3.png)

   a. Si va a importar un PFX, aparecerá un cuadro de diálogo adicional. En la página **Protección de clave privada**, escriba la contraseña de los archivos de certificado y, luego, habilite **Marcar esta clave como exportable. Esto permite realizar copias de seguridad o transportar las claves en un momento posterior**. Seleccione **Next** (Siguiente).

   ![Marcar clave como exportable](./media/prepare-pki-certs/2.png)

1. Seleccione **Finalizar** para completar la importación.

> [!NOTE]
> Después de importar un certificado para Azure Stack Hub, la clave privada del certificado se almacena como un archivo PKCS 12 (PFX) en el almacenamiento en clúster.

### <a name="export-the-certificate"></a>Exportación de certificados

Abra la consola MMC del administrador de certificados y conéctese al almacén de certificados de la máquina local.

1. Abra Microsoft Management Console. Para abrir la consola en Windows 10, haga clic con el botón derecho en el **menú Inicio**, seleccione **Ejecutar**, escriba **MMC** y presione Entrar.

2. Haga clic en **Archivo** > **Agregar o quitar complemento** y, después, seleccione **Certificados** y haga clic en **Agregar**.

    ![Incorporación del complemento Certificados en Microsoft Management Console](./media/prepare-pki-certs/mmc-2.png)

3. Seleccione la **cuenta de equipo** y seleccione **Siguiente**. Seleccione **Equipo local** y, después, seleccione **Finalizar**. Seleccione **Aceptar** para cerrar la página Agregar o quitar complemento.

    ![Seleccionar la cuenta para agregar complemento de certificados en Microsoft Management Console](./media/prepare-pki-certs/mmc-3.png)

4. Vaya a **Certificados** > **Confianza empresarial** > **Ubicación de certificado**. Compruebe que ve el certificado a la derecha.

5. En la barra de tareas de la consola del administrador de certificados, seleccione **Acciones** > **Todas las tareas** > **Exportar**. Seleccione **Next** (Siguiente).

   > [!NOTE]
   > Dependiendo del número de certificados de Azure Stack Hub que tenga, puede que necesite completar este proceso más de una vez.

6. Seleccione **Exportar la clave privada** y, después, haga clic en **Siguiente**.

7. En la sección Formato de archivo de exportación:
    
   - Seleccione **Incluir todos los certificados en la ruta de certificación (si es posible)** .  
   - Seleccione **Exportar todas las propiedades extendidas**.  
   - Seleccione **Habilitar privacidad de certificado**.  
   - Haga clic en **Next**.  
    
     ![Asistente para exportar certificados con las opciones seleccionadas](./media/prepare-pki-certs/azure-stack-save-cert.png)

8. Seleccione **Contraseña** y proporcione una contraseña para los certificados. Cree una contraseña que cumpla los siguientes requisitos de complejidad:

    * Una longitud mínima de ocho caracteres.
    * Un mínimo de tres de los siguientes caracteres: letras en mayúsculas, letras en minúsculas, números del 0 al 9, caracteres especiales, caracteres alfabéticos que no estén en mayúsculas ni en minúsculas.

    Anote esta contraseña. Se usará como parámetro de implementación.

9. Seleccione **Next** (Siguiente).

10. Elija un nombre de archivo y la ubicación del archivo PFX que se va a exportar. Seleccione **Next** (Siguiente).

11. Seleccione **Finalizar**.

## <a name="next-steps"></a>Pasos siguientes

[Validar los certificados PKI](azure-stack-validate-pki-certs.md)
