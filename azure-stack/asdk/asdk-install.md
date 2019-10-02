---
title: Instalar el ASDK | Microsoft Docs
description: Obtenga información sobre cómo instalar el Kit de desarrollo de Azure Stack (ASDK).
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
ms.openlocfilehash: deac15b2f1e3ebd86fdd2e171f664bd4b5917e37
ms.sourcegitcommit: 3af71025e85fc53ce529de2f6a5c396b806121ed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/20/2019
ms.locfileid: "71159435"
---
# <a name="install-the-asdk"></a>Instalación del Kit de desarrollo de Azure Stack
Después de [preparar el equipo host del ASDK](asdk-prepare-host.md), el Kit de desarrollo de Azure Stack (ASDK) se puede implementar en la imagen CloudBuilder.vhdx mediante los siguientes pasos de este artículo.

## <a name="install-the-asdk"></a>Instalación del Kit de desarrollo de Azure Stack
Los pasos de este artículo muestran cómo implementar ASDK mediante una interfaz gráfica de usuario (GUI) que se proporciona al descargar y ejecutar el script de PowerShell **asdk-installer.ps1**.

> [!NOTE]
> La interfaz de usuario del instalador del ASDK es un script de código abierto basado en WCF y PowerShell.


1. Una vez que el equipo host se inicie correctamente en la imagen de CloudBuilder.vhdx, inicie sesión con las credenciales de administrador que especificó cuando [preparó el equipo host del ASDK](asdk-prepare-host.md) para la instalación del ASDK. Estas deben ser las mismas que las credenciales de administrador local de host del ASDK.
2. Abra una consola de PowerShell con privilegios elevados y ejecute el script de PowerShell **letra de unidad>\AzureStack_Installer\asdk-installer.ps1&lt;** . Tenga en cuenta que el script ahora podría estar en una unidad diferente a C:\ en la imagen CloudBuilder.vhdx. Haga clic en **Instalar**.

    ![Instalar el ASDK](media/asdk-install/1.PNG) 

3. En el cuadro desplegable **Tipo** del Proveedor de identidades, seleccione **Azure China Cloud**, **Azure US Government Cloud**, **AD FS** o **Nube de Azure**. En **Contraseña de administrador local**, escriba la contraseña de administrador local (que debe coincidir con la contraseña de administrador local configurada actualmente) en el cuadro **Contraseña** y, luego, haga clic en **Siguiente**.

    ![Lista desplegable del tipo de proveedor de identidades en el ASDK](media/asdk-install/2.PNG) 
  
    Si elige un proveedor de identidades de la suscripción a Azure, necesita una conexión a Internet, el nombre completo de un inquilino del directorio de Azure AD en el formato *nombreDeDominio*.onmicrosoft.com, o un nombre de dominio personalizado verificado por Azure AD. También necesita credenciales de administrador global para el directorio especificado.

    Después de la implementación, no se necesita el permiso de administrador global de Azure Active Directory (Azure AD). Sin embargo, algunas operaciones pueden requerir la credencial de administrador global. Por ejemplo, un script del instalador del proveedor de recursos o una nueva característica que necesita la concesión de un permiso. Puede restablecer temporalmente los permisos de administrador global de la cuenta o usar una cuenta de administrador global independiente que sea propietaria de la *suscripción del proveedor predeterminada*.

    Al usar AD FS como proveedor de identidades, se usa el servicio de directorio con la marca predeterminado. La cuenta predeterminada con la que se inicia sesión es azurestackadmin@azurestack.local y la contraseña que se usa es la que especificó en la configuración.

   > [!NOTE]
   > Para obtener unos resultados óptimos, aunque quiera un entorno de Azure Stack desconectado que use AD FS como proveedor de identidades, es aconsejable que instale el ASDK mientras está conectado a Internet. De este modo, se puede activar la versión de evaluación de Windows Server 2016, incluida en la instalación del ASDK, durante la implementación.

4. Seleccione un adaptador de red para usarlo para el ASDK y, a continuación, haga clic en **Siguiente**.

    ![Seleccionar el adaptador de red para el ASDK](media/asdk-install/3.PNG)

5. En la página **Configuración de red**, proporcione la **dirección IP válida del servidor de hora** válida. Este campo obligatorio establece el servidor de tiempo que va a usar el ASDK. Este parámetro se debe proporcionar como una dirección IP válida del servidor horario. No se admiten los nombres de servidor.

      > [!TIP]
      > Para buscar una dirección IP de servidor horario, visite [pool.ntp.org](https://www.ntppool.org/) o haga ping a time.windows.com. 

    **Opcionalmente,** , puede proporcionar una dirección IP de **reenviador DNS**. Se crea un servidor DNS como parte de la implementación de Azure Stack. Para permitir que los equipos dentro de la solución resuelvan nombres fuera de la marca, proporcione el servidor DNS de infraestructura existente. El servidor DNS en la marca reenvía solicitudes de resolución de nombres desconocidos a este servidor.

    ![Reenviador de DNS y configuración de red en el ASDK](media/asdk-install/4.PNG)

6. En la página **Verifying network interface card properties** (Comprobación de las propiedades de la tarjeta de interfaz de red), verá una barra de progreso. Cuando la comprobación se haya completado, haga clic en **Siguiente**.

    ![Verificación de las propiedades de la tarjeta de interfaz de red en el ASDK](media/asdk-install/5.PNG)

7. En la página **Resumen**, haga clic en **Implementar** para iniciar la instalación del ASDK en el equipo host del ASDK.

    ![Resumen del script antes de implementarlo en ASDK](media/asdk-install/6.PNG)

    > [!TIP]
    > Aquí también puede copiar los comandos de instalación de PowerShell que se usarán para instalar el ASDK. Esto resulta útil si alguna vez necesita [volver a implementar el ASDK en el equipo host mediante PowerShell](asdk-deploy-powershell.md).

8. Si está realizando una implementación de Azure AD, se le pedirá que escriba sus credenciales de la cuenta de administrador global de Azure AD unos minutos después de que comience la instalación.

9. El proceso de implementación tardará unas horas, durante las que el equipo host se reiniciará automáticamente una vez. Si quiere supervisar el progreso de la implementación, inicie sesión como azurestack\AzureStackAdmin después de que se reinicie el host del ASDK. Si la implementación se realiza correctamente, en la consola de PowerShell aparece el mensaje: **COMPLETE: Action ‘Deployment’** (COMPLETADA: Acción "Implementación"). 
    > [!IMPORTANT]
    > Si inicia sesión como administrador local después de unir la máquina al dominio azurestack, no podrá ver el progreso de la implementación. No vuelva a ejecutar la implementación. En su lugar, inicie sesión como azurestack\AzureStackAdmin para validar que se está ejecutando.

    ![Implementación correcta del ASDK](media/asdk-install/7.PNG)

Enhorabuena, ha instalado correctamente el ASDK.

Si se produce un error en la implementación, puede [volver a realizar la implementación](asdk-redeploy.md) desde el principio o usar los siguientes comandos de PowerShell para reiniciar la implementación desde el último paso correcto. Los comandos se pueden usar desde la misma ventana con privilegios elevados de Powershell:

  ```powershell
  cd C:\CloudDeployment\Setup
  .\InstallAzureStackPOC.ps1 -Rerun
  ```

## <a name="next-steps"></a>Pasos siguientes
[Configuración después de la implementación](asdk-post-deploy.md)
