---
title: Instalación de Visual Studio y conexión a Azure Stack Hub
description: Aprenda a instalar Visual Studio y conectarse a Azure Stack Hub.
author: sethmanheim
ms.topic: article
ms.date: 06/11/2020
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 01/04/2020
ms.openlocfilehash: c9d8ad6293735db1a65d6f24e7a53a869535af6d
ms.sourcegitcommit: dd140b3a2ac8e558eae9f5f422711d2ba560da16
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/12/2020
ms.locfileid: "84744936"
---
# <a name="install-visual-studio-and-connect-to-azure-stack-hub"></a>Instalación de Visual Studio y conexión a Azure Stack Hub

Puede usar Visual Studio para escribir e implementar [plantillas](azure-stack-arm-templates.md) de Azure Resource Manager en Azure Stack Hub. Los pasos de este artículo describen cómo instalar Visual Studio en [Azure Stack Hub](../asdk/asdk-connect.md#connect-to-azure-stack-using-rdp) o en un equipo externo si planea usar Azure Stack Hub a través de [VPN](../asdk/asdk-connect.md#connect-to-azure-stack-using-vpn).

## <a name="install-visual-studio"></a>Instalación de Visual Studio

1. Descargue y ejecute el [Instalador de plataforma web](https://www.microsoft.com/web/downloads/platform.aspx).  

2. Abra el **Instalador de plataforma web de Microsoft**.

3. Busque **Visual Studio Community 2015 con el SDK de Microsoft Azure - 2.9.6**. Haga clic en **Agregar** y en **Instalar**.

4. Desinstale la versión de **Microsoft Azure PowerShell** que está instalada como parte de Azure SDK.

    ![Captura de pantalla de los pasos de la instalación de WebPI](./media/azure-stack-install-visual-studio/image1.png)

5. [Instale PowerShell para Azure Stack Hub](../operator/azure-stack-powershell-install.md).

6. Una vez finalizada la instalación, reinicie el equipo.

## <a name="connect-to-azure-stack-hub-with-azure-ad"></a>Conexión a Azure Stack Hub con Azure AD

1. Inicie Visual Studio.

2. En el menú **Ver**, seleccione **Cloud Explorer**.

3. Seleccione **Administrar cuentas** y, a continuación, en el nuevo panel, inicie sesión con sus credenciales de Azure Active Directory (Azure AD).  

    ![Captura de pantalla de Cloud Explorer después de iniciar sesión en Azure Stack Hub y conectarse](./media/azure-stack-install-visual-studio/image2.png)

Después de iniciar sesión, puede [implementar plantillas](azure-stack-deploy-template-visual-studio.md) o examinar los tipos y los grupos de recursos disponibles para crear sus propias plantillas.  

## <a name="connect-to-azure-stack-hub-with-ad-fs"></a>Conexión a Azure Stack Hub con AD FS

1. Inicie Visual Studio.

2. En **Herramientas**, seleccione **Opciones**.

3. Expanda **Entorno** en el panel de navegación y seleccione **Cuentas**.

4. Seleccione **Agregar** y especifique el punto de conexión de usuario de Azure Resource Manager. Para el Kit de desarrollo de Azure Stack (ASDK), la dirección URL es `https://management.local.azurestack/external`.  Para los sistemas integrados de Azure Stack Hub, la dirección URL es `https://management.[Region}.[External FQDN]`.

    ![Incorporación de nuevo punto de conexión de Azure Cloud Discovery](./media/azure-stack-install-visual-studio/image5.png)

5. Seleccione **Agregar**.  

    Visual Studio llama a Azure Resource Manager y descubre los puntos de conexión, incluido el punto de conexión de autenticación para Azure Directory Federated Services (AD FS).

    ![Captura de pantalla de Cloud Explorer después de iniciar sesión en Azure Stack Hub y conectarse](./media/azure-stack-install-visual-studio/image6.png)

6. Seleccione **Cloud Explorer** en el menú **Ver**.

7. Seleccione **Agregar cuenta** e inicie sesión con sus credenciales de AD FS.  

    ![Inicie sesión en Visual Studio en Cloud Explorer](./media/azure-stack-install-visual-studio/image7.png)

    Cloud Explorer consulta las suscripciones disponibles. Puede seleccionar una suscripción disponible para administrar.

    ![Seleccione las suscripciones que desea administrar en Cloud Explorer](./media/azure-stack-install-visual-studio/image8.png)

8. Examine los recursos existentes, grupos de recursos o implemente plantillas.

## <a name="next-steps"></a>Pasos siguientes

- Obtenga más información sobre la [coexistencia](/visualstudio/install/install-visual-studio-versions-side-by-side) de Visual Studio con otras versiones de Visual Studio.
- [Desarrollo de plantillas para Azure Stack Hub](azure-stack-develop-templates.md).
