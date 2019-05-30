---
title: Instalación del Kit de desarrollo de Azure Stack (ASDK) | Microsoft Docs
description: Describe cómo instalar el Kit de desarrollo de Azure Stack (ASDK).
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 277b2e8c86cda65167a734d0e5775ec1748ef219
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2019
ms.locfileid: "66267640"
---
# <a name="install-the-azure-stack-development-kit-asdk"></a>Instalación del Kit de desarrollo de Azure Stack (ASDK)
Después de [preparar el equipo host del Kit de desarrollo de Azure Stack](asdk-prepare-host.md), este se puede implementar en la imagen de CloudBuilder.vhdx mediante los siguientes pasos de este artículo.

## <a name="install-the-asdk"></a>Instalación del Kit de desarrollo de Azure Stack
Los pasos de este artículo muestran cómo implementar ASDK mediante una interfaz gráfica de usuario (GUI) que se proporciona al descargar y ejecutar el script de PowerShell **asdk-installer.ps1**.

> [!NOTE]
> La interfaz de usuario del instalador del Kit de desarrollo de Azure Stack es un script de código abierto basado en WCF y PowerShell.


1. Una vez que el equipo host arranque correctamente en la imagen de CloudBuilder.vhdx, inicie sesión con las credenciales de administrador que especificó cuando [preparó el equipo host del kit de desarrollo](asdk-prepare-host.md) para la instalación del ASDK. Dichas credenciales deben coincidir con las de administrador local del host del kit de desarrollo.
2. Abra una consola de PowerShell con privilegios elevados y ejecute el script de PowerShell **letra de unidad>\AzureStack_Installer\asdk-installer.ps1&lt;** . Tenga en cuenta que el script puede estar en una unidad distinta de C:\ en la imagen de CloudBuilder.vhdx. Haga clic en **Instalar**.

    ![](media/asdk-install/1.PNG) 

3. En el cuadro desplegable **Tipo** del Proveedor de identidades, seleccione **Azure China Cloud**, **Azure US Government Cloud**, **AD FS** o **Nube de Azure**. En **Local Administrator Password** (Contraseña del administrador local), en el cuadro **Password** (Contraseña), escriba la contraseña del administrador local (que debe coincidir con la configurada en la actualidad) y haga clic en **Next** (Siguiente).

    ![](media/asdk-install/2.PNG) 
  
   Si elige un proveedor de identidades con suscripción a Azure, se necesitan una conexión a Internet, el nombre completo de un inquilino del directorio de Azure AD con la forma *nombreDeDominio*.onmicrosoft.com, o un nombre de dominio personalizado comprobado, y las credenciales de administrador global del directorio especificado.<br><br>Después de la implementación, no se necesita el permiso de administrador global de Azure Active Directory. Pero algunas operaciones pueden requerir la credencial de administrador global. Por ejemplo, un script del instalador del proveedor de recursos o una nueva característica que necesita la concesión de un permiso. Puede restablecer temporalmente los permisos de administrador global de la cuenta o usar una cuenta de administrador global independiente que sea propietaria de la *suscripción del proveedor predeterminada*.<br><br>Al usar AD FS como proveedor de identidades, se usa el servicio de directorio con la marca predeterminado. La cuenta predeterminada con la que se inicia sesión es azurestackadmin@azurestack.local y la contraseña que se usa es la que especificó en la configuración.

   > [!NOTE]
   > Para que los resultados sean inmejorables, aunque desee un entorno de Azure Stack desconectado que use AD FS como proveedor de identidades, es aconsejable que instale el ASDK mientras está conectado a Internet. De este modo, se puede activar la versión de evaluación de Windows Server 2016, incluida en la instalación del kit de desarrollo, durante la implementación.

4. Seleccione un adaptador de red para usarlo para el kit de desarrollo y, a continuación, haga clic en **Siguiente**.

    ![](media/asdk-install/3.PNG)

5. En la página **Configuración de red**, proporcione la **dirección IP válida del servidor de hora** válida. Este campo obligatorio establece el servidor horario que va a usar el kit de desarrollo. Este parámetro se debe proporcionar como una dirección IP válida del servidor horario. No se admiten nombres del servidor.

      > [!TIP]
      > Para buscar una dirección IP de servidor horario, visite [pool.ntp.org](https://www.ntppool.org/) o haga ping a time.windows.com. 

    **Opcionalmente,** , puede proporcionar una dirección IP de **reenviador DNS**. Se crea un servidor DNS como parte de la implementación de Azure Stack. Para permitir que los equipos dentro de la solución resuelvan nombres fuera de la marca, proporcione el servidor DNS de infraestructura existente. El servidor DNS en la marca reenvía solicitudes de resolución de nombres desconocidos a este servidor.

    ![](media/asdk-install/4.PNG)

6. En la página **Verifying network interface card properties** (Comprobación de las propiedades de la tarjeta de interfaz de red), verá una barra de progreso. Cuando la comprobación se haya completado, haga clic en **Siguiente**.

    ![](media/asdk-install/5.PNG)

7. En la página **Resumen**, haga clic en **Implementar** para iniciar la instalación de ASDK en el equipo host del kit de desarrollo.

    ![](media/asdk-install/6.PNG)

    > [!TIP]
    > Aquí también puede copiar los comandos de instalación de PowerShell que se usarán para instalar el kit de desarrollo. Esto resulta útil si alguna vez necesita [volver a implementar el ASDK en el equipo host mediante PowerShell](asdk-deploy-powershell.md).

8. Si va a realizar una implementación de Azure AD, se le solicitará que escriba sus credenciales de la cuenta de administrador global de Azure AD unos minutos después de iniciar la instalación.

9. El proceso de implementación tardará unas horas, durante las que el equipo host se reiniciará automáticamente una vez. Si desea supervisar el progreso de la implementación, inicie sesión como azurestack\AzureStackAdmin después de que se reinicie el host del kit de desarrollo. Si la implementación se realiza correctamente, en la consola de PowerShell aparece el mensaje: **COMPLETE: Action ‘Deployment’** (COMPLETADA: Acción "Implementación"). 
    > [!IMPORTANT]
    > Si inicia sesión como administrador local después de unir la máquina al dominio azurestack, no podrá ver el progreso de la implementación. No vuelva a ejecutar la implementación, en su lugar, inicie sesión como azurestack\AzureStackAdmin para validar que se está ejecutando.

    ![](media/asdk-install/7.PNG)

Enhorabuena, ha instalado correctamente el ASDK.

Si se produce un error en la implementación, puede [volver a realizarla](asdk-redeploy.md) desde el principio o usar los siguientes comandos de PowerShell, desde la misma ventana de PowerShell con privilegios elevados, para reiniciar la implementación desde el último paso correcto:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Pasos siguientes
[Configuración después de la implementación](asdk-post-deploy.md)
