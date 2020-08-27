---
title: Conexión a Azure Stack Hub mediante la extensión de la cuenta de Azure en Visual Studio Code
description: Conectarse a Azure Stack Hub como desarrollador mediante la extensión de la cuenta de Azure en Visual Studio Code
author: mattbriggs
ms.topic: conceptual
ms.date: 08/24/2020
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 11/11/2019
ms.openlocfilehash: a3cd09d7138029ba578648e1599916fd71b42292
ms.sourcegitcommit: a5d3cbe1a10c2a63de95b9e72391dd83473ee299
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 08/26/2020
ms.locfileid: "88920617"
---
# <a name="connect-to-azure-stack-hub-using-azure-account-extension-in-visual-studio-code"></a>Conexión a Azure Stack Hub mediante la extensión de la cuenta de Azure en Visual Studio Code

En este artículo, le orientaremos acerca de cómo conectarse a Azure Stack Hub mediante la extensión de la cuenta de Azure. Necesitará actualizar la configuración de Visual Studio Code (VS Code).

VS Code es un editor ligero para compilar y depurar aplicaciones web y en la nube. ASP.NET Core, Python, NodeJS, Go y otros desarrolladores usan VS Code. Con la extensión de la cuenta de Azure, puede usar un único inicio de sesión de Azure con filtrado de suscripción para extensiones adicionales de Azure. La extensión hace que Azure Cloud Shell esté disponible en el terminal integrado de VS Code. Mediante la extensión, puede conectarse a una suscripción de Azure Stack Hub mediante Azure AD (Azure AD) y Active Directory Federated Services (AD FS) para el administrador de identidades. Puede iniciar sesión en Azure Stack Hub, seleccionar la suscripción y abrir una nueva línea de comandos en un shell en la nube. 

> [!NOTE]  
> Puede usar los pasos de este artículo para un entorno de Active Directory Federated Services (AD FS). Use los puntos de conexión y las credenciales de AD FS.

## <a name="pre-requisites-for-the-azure-account-extension"></a>Requisitos previos para la extensión de la cuenta de Azure

1. Compilación 1904 o posterior del entorno de Azure Stack Hub
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Extensión de la cuenta de Azure](https://github.com/Microsoft/vscode-azure-account)
4. [Una suscripción a Azure Stack Hub](https://azure.microsoft.com/overview/azure-stack/)

## <a name="steps-to-connect-to-azure-stack-hub"></a>Pasos para establecer una conexión con Azure Stack Hub

1. Ejecute el script **Identity** desde las herramientas de Azure Stack Hub en GitHub.

    - Antes de ejecutar el script, deberá tener PowerShell instalado y configurado para su entorno. Para obtener instrucciones, consulte [Instalación de PowerShell para Azure Stack Hub](../operator/azure-stack-powershell-install.md).

    - Para el script **Identity** y sus instrucciones, consulte [AzureStack-Tools/Identity](https://aka.ms/aa6z611).

    - En la misma sesión, ejecute:

    ```powershell  
    Update-AzsHomeDirectoryTenant -AdminResourceManagerEndpoint $adminResourceManagerEndpoint `
    -DirectoryTenantName $homeDirectoryTenantName -Verbose
    Register-AzsWithMyDirectoryTenant -TenantResourceManagerEndpoint $tenantARMEndpoint `
    -DirectoryTenantName $guestDirectoryTenantName
    ```

2. Abra VS Code.

3. Seleccione **Extensiones** en la esquina izquierda.

4. En el cuadro de búsqueda, escriba `Azure Account`.

5. Seleccione **Cuenta de Azure** y, a continuación, **Instalar**.

      ![Visual Studio Code en Azure Stack Hub](media/azure-stack-dev-start-vscode-azure/image1.png)

6. Reinicie VS Code para cargar la extensión.

7. Recupere los metadatos para conectarse a Azure Resource Manager en su instancia de Azure Stack Hub. 
    
    Microsoft Azure Resource Manager es un marco de administración que le permite implementar, administrar y supervisar recursos de Azure.
    - La dirección URL de Resource Manager para el Kit de desarrollo de Azure Stack (ASDK) es: `https://management.local.azurestack.external/` 
    - La dirección URL de Resource Manager para un sistema integrado es: `https://management.region.<fqdn>/`, donde `<fqdn>` es el nombre de dominio completo.
    - Agregue el texto siguiente a la URL para acceder a los metadatos: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

    Por ejemplo, la dirección URL para recuperar los metadatos para el punto de conexión de Azure Resource Manager puede tener un aspecto similar a este: `https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`

    Anote el código JSON devuelto. Necesitará los valores para la propiedad `loginEndpoint` y `audiences`.

8. Presione **Ctrl + Mayús + P** y seleccione **Preferencias: Abrir configuración de usuario (JSON)** .

9. En el editor de código, actualice el siguiente fragmento de código JSON con los valores para su entorno y, a continuación, péguelo en el bloque de configuración.

    - Valores:

        | Parámetro | Descripción |
        | --- | --- |
        | `tenant-ID` | El valor de su [identificador de inquilino](../operator/azure-stack-identity-overview.md) de Azure Stack Hub. |
        | `activeDirectoryEndpointUrl` | Esta es la dirección URL de la propiedad loginEndpoint. |
        | `activeDirectoryResourceId` | Se trata de la dirección URL de la propiedad Audiences.
        | `resourceManagerEndpointUrl` | Se trata de la dirección URL raíz de Azure Resource Manager para Azure Stack Hub. | 

    - Fragmento de código JSON:

      ```JSON  
      "azure.tenant": "tenant-ID",
      "azure.ppe": {
          "activeDirectoryEndpointUrl": "Login endpoint",
          "activeDirectoryResourceId": "This is the URL from the audiences property.",
          "resourceManagerEndpointUrl": "Aure Resource Management Endpoint",
      },
      "azure.cloud": "AzurePPE"
      ```

10. Guarde la configuración de usuario y use **Ctrl + Mayús + P** una vez más. Seleccione **Azure: Sign in to Azure Cloud**. Se mostrará la nueva opción, **AzurePPE**, en la lista de destinos.

11. Seleccione **AzurePPE**. La página de autenticación se cargará en el explorador. Inicie sesión en el punto de conexión.

12. Para comprobar que ha iniciado sesión correctamente en su suscripción de Azure Stack Hub, use **Ctrl + Mayús + P** y seleccione **Azure: Seleccionar suscripción**. A continuación, compruebe si la suscripción está disponible.

## <a name="commands"></a>Comandos:

| Azure: Sign In | Inicie sesión en la suscripción de Azure |
| --- | --- |
| Azure: Sign In with Device Code | Inicie sesión en su suscripción de Azure con un código de dispositivo. Use un código de dispositivo en configuraciones donde el comando Sign In no funcione. |
| Azure: Sign In to Azure Cloud | Inicie sesión en su suscripción de Azure en una de las nubes soberanas. |
| Azure: cierre de sesión del | Cierre la sesión de su suscripción a Azure. |
| Azure: Selección de suscripciones | Elija el conjunto de suscripciones con el que quiere trabajar. La extensión solo muestra los recursos dentro de las suscripciones filtradas. |
| Azure: Creación de una cuenta | Si no tiene ninguna cuenta de Azure, puede [registrarse](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account) para conseguir una hoy mismo y recibir 200 USD en créditos gratis. |
| Azure: Open Bash in Cloud Shell | Abra un terminal nuevo ejecutando Bash en Cloud Shell. |
| Azure: Open PowerShell in Cloud Shell | Abra un terminal nuevo ejecutando PowerShell en Cloud Shell. |
| Azure: Upload to Cloud Shell | Cargue un archivo en su cuenta de almacenamiento de Cloud Shell. |

## <a name="next-steps"></a>Pasos siguientes

[Configuración de un entorno de desarrollo en Azure Stack Hub](azure-stack-dev-start.md)
