---
title: Conectarse a Azure Stack con la extensión de la cuenta de Azure de Visual Studio Code | Microsoft Docs
description: Conectarse a Azure Stack como desarrollador mediante la extensión de la cuenta de Azure en Visual Studio Code
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: Howto
ms.date: 05/31/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 05/31/2019
ms.openlocfilehash: 8e64a570ab45e57e3cf58639bc2ec23d9b9bd81b
ms.sourcegitcommit: 07cc716d97bf484c7260eb165ae205ae25e09589
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/31/2019
ms.locfileid: "66453582"
---
# <a name="connect-to-azure-stack-using-azure-account-extension-in-visual-studio-code"></a>Conectarse a Azure Stack mediante la extensión de la cuenta de Azure en Visual Studio Code

En este artículo, le orientaremos acerca de cómo conectarse a Azure Stack con la extensión de la cuenta de Azure. Necesitará actualizar la configuración de Visual Studio Code (VS Code).

VS Code es un editor ligero para compilar y depurar aplicaciones web y en la nube. Lo usan ASP.NET Core, Python, NodeJS, Go y otros desarrolladores. Con la extensión de la cuenta de Azure, puede usar un único inicio de sesión de Azure con filtrado de suscripción para extensiones adicionales de Azure. La extensión hace que Azure Cloud Shell esté disponible en el terminal integrado de VS Code. Con la extensión, puede conectarse a la suscripción de Azure Stack con Azure AD (Azure AD) y Active Directory Federated Services (AD FS) para el administrador de identidades. Esto le permite iniciar sesión en Azure Stack, seleccionar la suscripción y abrir una nueva línea de comandos en un shell en la nube. 

> [!Note]  
> Puede usar los pasos de este artículo para un entorno de Active Directory Federated Services (AD FS). Use los puntos de conexión y las credenciales de AD FS.

## <a name="pre-requisites-for-the-azure-account-extension"></a>Requisitos previos para la extensión de la cuenta de Azure

1. Compilación 1904 o posterior del entorno de Azure Stack
2. [Visual Studio Code](https://code.visualstudio.com/)
3. [Extensión de la cuenta de Azure](https://github.com/Microsoft/vscode-azure-account)
4. [Una suscripción a Azure Stack](https://azure.microsoft.com/overview/azure-stack/)

## <a name="steps-to-connect-to-azure-stack"></a>Pasos para conectar con Azure Stack

1. Abra VS Code.

2. Seleccione **Extensiones** en la esquina izquierda.

3. En el cuadro de búsqueda, escriba `Azure Account`.

4. Seleccione **Cuenta de Azure** y, a continuación, **Instalar**.

      ![Visual Studio Code en Azure Stack](media/azure-stack-dev-start-vscode-azure/image1.png)

5. Reinicie VS Code para cargar la extensión.

6. Recupere los metadatos para conectarse a Azure Resource Manager desde Azure Stack. 
    
    Microsoft Azure Resource Manager es un marco de administración que le permite implementar, administrar y supervisar recursos de Azure.
    - La dirección URL de Resource Manager para el Kit de desarrollo de Azure Stack (ASDK) es: `https://management.local.azurestack.external/` 
    - La dirección URL de Resource Manager para un sistema integrado es: `https://management.<location>.ext-<machine-name>.masd.stbtest.microsoft.com/`
    - Agregue el texto siguiente a la URL para acceder a los metadatos: `<ResourceManagerUrl>/metadata/endpoints?api-version=1.0`

    Por ejemplo, la dirección URL para recuperar los metadatos para el punto de conexión de Azure Resource Manager puede tener un aspecto similar a este: `https://management.local.azurestack.external/metadata/endpoints?api-version=1.0`

    Anote el código JSON devuelto. Necesitará los valores para la propiedad `loginEndpoint` y `loginEndgraphEndpointpoint`.

7. Presione **Ctrl + Mayús + P** y seleccione **Preferencias: Abrir configuración de usuario (JSON)** .

8. En el editor de código, actualice el siguiente fragmento de código JSON con los valores para su entorno y, a continuación, péguelo en el bloque de configuración.

    - Valores:

        | Parámetro | DESCRIPCIÓN |
        | --- | --- |
        | `tenant-ID` | El valor de su [identificador de inquilino](../operator/azure-stack-identity-overview.md) de Azure Stack. |
        | `activeDirectoryEndpointUrl` | Esta es la dirección URL de la propiedad loginEndpoint. |
        | `activeDirectoryResourceId` | Esta es la dirección URL de la propiedad loginEndgraphEndpointpoint.
        | `resourceManagerEndpointUrl` | Se trata de la dirección URL raíz de Azure Resource Manager para Azure Stack. | 

    - Fragmento de código JSON:

      ```JSON  
      "azure.tenant": "tenant-ID",
      "azure.ppe": {
          "activeDirectoryEndpointUrl": "Login endpoint",
          "activeDirectoryResourceId": "graph audience",
          "resourceManagerEndpointUrl": "Management Endpoint",
      },
      "azure.cloud": "AzurePPE"
      ```

8. Guarde la configuración de usuario y use **Ctrl + Mayús + P** una vez más. Seleccione **Azure: Sign in to Azure Cloud**. Se mostrará la nueva opción, **AzurePPE**, en la lista de destinos.

9. Seleccione **AzurePPE**. La página de autenticación se cargará en el explorador. Inicie sesión en el punto de conexión.

11. Para comprobar que ha iniciado sesión correctamente en su suscripción de Azure Stack, use **Ctrl + Mayús + P** y seleccione **Azure: Seleccionar suscripción**. A continuación, compruebe si la suscripción está disponible.

## <a name="commands"></a>Comandos:

| Azure: Sign In | Inicie sesión en la suscripción de Azure |
| --- | --- |
| Azure: Sign In with Device Code | Inicie sesión en su suscripción de Azure con un código de dispositivo. Úselo en configuraciones en que el comando Sign In no funcione. |
| Azure: Sign In to Azure Cloud | Inicie sesión en su suscripción de Azure en una de las nubes soberanas. |
| Azure: cierre de sesión del | Cierre la sesión de su suscripción a Azure. |
| Azure: Selección de suscripciones | Elija el conjunto de suscripciones con el que quiere trabajar. La extensión solo muestra los recursos dentro de las suscripciones filtradas. |
| Azure: Creación de una cuenta | Si no tiene ninguna cuenta de Azure, puede [registrarse](https://azure.microsoft.com/free/?utm_source=campaign&utm_campaign=vscode-azure-account&mktingSource=vscode-azure-account) para conseguir una hoy mismo y recibir 200 USD en créditos gratis. |
| Azure: Open Bash in Cloud Shell | Abra un terminal nuevo ejecutando Bash en Cloud Shell. |
| Azure: Open PowerShell in Cloud Shell | Abra un terminal nuevo ejecutando PowerShell en Cloud Shell. |
| Azure: Upload to Cloud Shell | Cargue un archivo en su cuenta de almacenamiento de Cloud Shell. |

## <a name="next-steps"></a>Pasos siguientes

[Configurar un entorno de desarrollo en Azure Stack ](azure-stack-dev-start.md)