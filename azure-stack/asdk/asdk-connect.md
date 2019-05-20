---
title: Conexión a Azure Stack | Microsoft Docs
description: Aprenda a conectarse al ASDK.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 3cebbfa6-819a-41e3-9f1b-14ca0a2aaba3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: mabrigg
ms.reviewer: knithinc
ms.lastreviewed: 10/25/2018
ms.openlocfilehash: 39814c48af0a362bd62f7d7276a32d886628317b
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617574"
---
# <a name="connect-to-the-asdk"></a>Conectarse al ASDK

Para administrar recursos, primero debe conectarse al Kit de desarrollo de Azure Stack (ASDK). En este artículo se describen los pasos que debe seguir para conectarse a ASDK mediante el uso de las siguientes opciones de conexión:

* [Conexión a Escritorio remoto (RDP)](#connect-with-rdp). Cuando se conecta mediante Conexión a Escritorio remoto, un usuario puede conectarse rápidamente al kit de desarrollo.
* [Red privada virtual (VPN)](#connect-with-vpn). Cuando se conecte mediante una red privada virtual, varios usuarios pueden conectarse a la vez a los portales de Azure Stack desde los clientes externos a la infraestructura de Azure Stack. Una conexión VPN requiere cierta configuración.

<a name="connect-with-rdp"></a>
## <a name="connect-to-azure-stack-using-rdp"></a>Conexión a Azure Stack mediante RDP

Un único usuario simultáneo puede administrar los recursos en el portal de administración de Azure Stack o en el portal de usuario mediante Conexión a Escritorio remoto directamente desde el equipo host de ASDK. 

> [!TIP]
> Esta opción también le permite volver a usar RDP mientras tiene iniciada sesión en el equipo host de ASDK para iniciar sesión en las máquinas virtuales creadas en el equipo host de ASDK. 

1. Abra una Conexión a Escritorio remoto (mstc.exe) y conéctese a la dirección IP del equipo host del kit de desarrollo con una cuenta autorizada para iniciar una sesión remota en el equipo host de ASDK. De forma predeterminada, **AzureStack\AzureStackAdmin** tiene permisos para iniciar sesión de forma remota en el equipo host de ASDK.  

2. En el equipo host del kit de desarrollo, abra el Administrador del servidor (ServerManager.exe). Seleccione **Servidor local**, desactive la opción **Configuración de seguridad mejorada de IE** y cierre el Administrador del servidor.

3. Inicie sesión en el portal de administración como **AzureStack\CloudAdmin** o use otras credenciales de operador de Azure Stack. La dirección del portal de administración del ASDK es [https://adminportal.local.azurestack.external](https://adminportal.local.azurestack.external).

4. Inicie sesión en el portal de usuario como **AzureStack\CloudAdmin** o use otras credenciales de usuario de Azure Stack. La dirección del portal de usuario del ASDK es [https://portal.local.azurestack.external](https://portal.local.azurestack.external).

> [!NOTE]
> Para obtener más información sobre cuándo usar cada tipo de cuenta, consulte [Conceptos básicos de administración del Kit de desarrollo de Azure Stack](asdk-admin-basics.md#what-account-should-i-use).

<a name="connect-with-vpn"></a>
## <a name="connect-to-azure-stack-using-vpn"></a>Conexión a Azure Stack mediante VPN

Puede establecer un conexión VPN de túnel dividido a un equipo host de ASDK para tener acceso a los portales de Azure Stack y a las herramientas instaladas localmente, como Visual Studio y PowerShell. Con las conexiones VPN, varios usuarios pueden conectarse al mismo tiempo a los recursos de Azure Stack hospedados por el ASDK.

La conectividad VPN se admite en Azure AD y en las implementaciones de Servicios de federación de Active Directory (AD FS).

> [!NOTE]
> Una conexión VPN *no* proporciona conectividad a las máquinas virtuales de Azure Stack. No podrá usar el protocolo RDP en las máquinas virtuales de Azure Stack mientras está conectado mediante VPN.

### <a name="prerequisites"></a>Requisitos previos
Antes de configurar una conexión VPN al ASDK, asegúrese de cumplir los siguientes requisitos previos.

- Instale [Azure Stack-compatible Azure PowerShell](asdk-post-deploy.md#install-azure-stack-powershell) en su equipo local.  
- Descargue las [herramientas necesarias para trabajar con Azure Stack](asdk-post-deploy.md#download-the-azure-stack-tools).

### <a name="set-up-vpn-connectivity"></a>Configuración de la conectividad VPN

Para crear una conexión VPN al ASDK, abra Windows PowerShell como administrador en el equipo local basado en Windows. A continuación, ejecute el siguiente script (actualice los valores de dirección IP y contraseña para su entorno):

```powershell
# Change directories to the default Azure Stack tools directory
cd C:\AzureStack-Tools-master

# Configure Windows Remote Management (WinRM), if it's not already configured.
winrm quickconfig  

Set-ExecutionPolicy RemoteSigned

# Import the Connect module.
Import-Module .\Connect\AzureStack.Connect.psm1

# Add the development kit host computer's IP address as the ASDK certificate authority (CA) to the list of trusted hosts. Make sure you update the IP address and password values for your environment.

$hostIP = "<Azure Stack host IP address>"

$Password = ConvertTo-SecureString `
  "<operator's password provided when deploying Azure Stack>" `
  -AsPlainText `
  -Force

Set-Item wsman:\localhost\Client\TrustedHosts `
  -Value $hostIP `
  -Concatenate

# Create a VPN connection entry for the local user.
Add-AzsVpnConnection `
  -ServerAddress $hostIP `
  -Password $Password

```

Si la instalación se realiza correctamente, **Azure Stack** aparece en la lista de conexiones VPN.

![Conexiones de red](media/asdk-connect/vpn.png)  

### <a name="connect-to-azure-stack"></a>Conexión a Azure Stack

  Conéctese a la instancia de Azure Stack mediante uno los métodos siguientes:  

  * Use el comando `Connect-AzsVpn`:
      
    ```powershell
    Connect-AzsVpn `
      -Password $Password
    ```

  * En el equipo local, seleccione **Configuración de red** > **VPN** > **Azure Stack** > **conectar**. En el símbolo del sistema de inicio de sesión, escriba el nombre de usuario (**AzureStack\AzureStackAdmin**) y la contraseña.

La primera vez que se conecte, se le pedirá que instale el certificado raíz de Azure Stack de **AzureStackCertificateAuthority** en el almacén de certificados del equipo local. Este paso agrega la autoridad de certificación (CA) de ASDK a la lista de hosts de confianza. Haga clic en **Sí** para instalar el certificado.

![Certificado raíz](media/asdk-connect/cert.png)  
  
  > [!IMPORTANT]
  > La petición puede quedar oculta por la ventana de PowerShell u otras aplicaciones.

### <a name="test-vpn-connectivity"></a>Probar la conectividad VPN

Para probar la conexión del portal, abra un explorador web y vaya al portal de usuarios (https://portal.local.azurestack.external/) o al portal de administración (https://adminportal.local.azurestack.external/). 

Inicie sesión con las credenciales de la suscripción adecuada para crear y administrar los recursos.  

## <a name="next-steps"></a>Pasos siguientes

[Solución de problemas](asdk-troubleshooting.md)
