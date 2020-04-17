---
title: Preparación del equipo host del Kit de desarrollo de Azure Stack
description: Obtenga información sobre cómo preparar el equipo host del Kit de desarrollo de Azure Stack (ASDK) para la instalación del ASDK.
author: justinha
ms.topic: article
ms.date: 08/28/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 08/28/2019
ms.openlocfilehash: ba64a3bfbef9601d9cc8d8a28176a84a9403326a
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "77696029"
---
# <a name="prepare-the-asdk-host-computer"></a>Preparación del equipo host del Kit de desarrollo de Azure Stack
Para poder instalar el Kit de desarrollo de Azure Stack (ASDK) en el equipo host, el host del ASDK se debe preparar para su instalación. Cuando el host esté preparado, se iniciará desde el disco duro de la máquina virtual (VM) CloudBuilder.vhdx para comenzar la implementación del ASDK.

## <a name="prepare-the-development-kit-host-computer"></a>Preparar el equipo host del kit de desarrollo
Para poder instalar el ASDK en el equipo host, se debe preparar el entorno de este. Para preparar ese entorno, siga estos pasos:

1. Inicie sesión como administrador local en el equipo host del ASDK.
2. Asegúrese de que el archivo CloudBuilder.vhdx se ha movido a la raíz de la unidad C:\ drive (`C:\CloudBuilder.vhdx`).
3. Ejecute el script siguiente para descargar el archivo instalador del ASDK (asdk-installer.ps1) del [repositorio de herramientas de GitHub para Azure Stack](https://github.com/Azure/AzureStack-Tools) en la carpeta **C:\AzureStack_Installer** del equipo host del ASDK:

   > [!IMPORTANT]
   > Asegúrese de descargar el archivo asdk-installer.ps1 cada vez que instale el ASDK. Se hacen cambios frecuentes en este script y se debe usar la versión más reciente para cada implementación del ASDK. Las versiones anteriores del script podrían no funcionar con la versión más reciente.

   ```powershell
   # Variables
   $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/asdk-installer.ps1'
   $LocalPath = 'C:\AzureStack_Installer'
   # Create folder
   New-Item $LocalPath -Type directory
   # Enforce usage of TLSv1.2 to download from GitHub
   [Net.ServicePointManager]::SecurityProtocol = [Net.SecurityProtocolType]::Tls12
   # Download file
   Invoke-WebRequest $uri -OutFile ($LocalPath + '\' + 'asdk-installer.ps1')
   ```

4. En una consola de PowerShell con privilegios elevados, inicie el script **C:\AzureStack_Installer\asdk-installer.ps1** y, a continuación, haga clic en **Preparar entorno**.

    ![Preparar el entorno en el ASDK](media/asdk-prepare-host/1.PNG) 

5. En la página **Select Cloudbuilder vhdx** (Seleccionar Cloudbuilder vhdx) del instalador, busque y seleccione el archivo **cloudbuilder.vhdx** que ha descargado y extraído [en los pasos anteriores](asdk-download.md). Si quiere, en esta página también puede activar la casilla **Agregar controladores** en caso de que necesite agregar más controladores al equipo host del kit del ASDK. Haga clic en **Next**.  

    ![Seleccionar cloudbuilder.vhdx y agregar controladores al ASDK](media/asdk-prepare-host/2.PNG)

6. En la página **Configuración opcional**, especifique la información de la cuenta de administrador local para el equipo host del ASDK y haga clic en **Siguiente**.

    Si no se especifican las credenciales de administrador local en este paso, se necesitará acceso directo o de KVM al host después de que el equipo se reinicie como parte de la configuración del ASDK.

   ![Configuración opcional en el ASDK: proporcionar información de la cuenta de administrador local](media/asdk-prepare-host/3.PNG)

    También puede especificar los valores de los siguientes ajustes opcionales:
    - **Nombre del equipo**: Esta opción permite establecer el nombre para el host del ASDK. El nombre debe cumplir los requisitos de FQDN y debe tener una longitud máxima de 15 caracteres. El valor predeterminado es un nombre de equipo aleatorio generado por Windows.

        - Seleccionar un adaptador de red. Asegúrese de que puede conectarse al adaptador antes de hacer clic en **Siguiente**.

            ![Captura de pantalla de la configuración del adaptador de red](media/asdk-prepare-host/step-four-network-adapter.png)

        - Asegúrese de que los valores de **Dirección IP**, **Puerta de enlace** y **DNS** que se muestran son correctos y proporcione una dirección **IP del servidor horario**. A continuación, haga clic en **Siguiente**.

            >[!TIP]
            >Para buscar una dirección IP de servidor horario, visite [pool.ntp.org](https://www.ntppool.org/) o haga ping a time.windows.com. 

            ![Captura de pantalla de las opciones de configuración de IP](media/asdk-prepare-host/step-five-host-ip-config.png)

7. Haga clic en **Siguiente** para iniciar el proceso de preparación.
8. Cuando la preparación indique **Completado**, haga clic en **Siguiente**.

    ![Preparar el entorno en el ASDK](media/asdk-prepare-host/4.PNG)

9. Haga clic en **Reiniciar ahora** para iniciar el equipo host del ASDK en cloudbuilder.vhdx y [continuar el proceso de implementación](asdk-install.md).

    ![Reiniciar el ASDK](media/asdk-prepare-host/5.PNG)


## <a name="next-steps"></a>Pasos siguientes
[Instalación del Kit de desarrollo de Azure Stack](asdk-install.md)
