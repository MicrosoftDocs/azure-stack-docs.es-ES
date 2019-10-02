---
title: Implementación de ASDK desde la línea de comandos mediante PowerShell | Microsoft Docs
description: En este artículo aprenderá a implementar ASDK desde la línea de comandos mediante PowerShell.
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
ms.custom: ''
ms.date: 05/06/2019
ms.author: justinha
ms.reviewer: misainat
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 0ba8723b9d0f03006b52ecd016c3713280b63dea
ms.sourcegitcommit: 3af71025e85fc53ce529de2f6a5c396b806121ed
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/20/2019
ms.locfileid: "71159296"
---
# <a name="deploy-asdk-from-the-command-line-using-powershell"></a>Implementación de ASDK desde la línea de comandos mediante PowerShell

El Kit de desarrollo de Azure Stack (ASDK) es un entorno de desarrollo y pruebas que se puede implementar para evaluar y demostrar las características y servicios de Azure Stack. Para que funcione correctamente, debe preparar el hardware del entorno y ejecutar algunos scripts. Tenga en cuenta que los scripts tardan varias horas en ejecutarse. Una vez finalizado, puede iniciar sesión en los portales del administrador y de usuarios para comenzar a usar Azure Stack.

## <a name="prerequisites"></a>Requisitos previos

Prepare el equipo host de ASDK. Planee el hardware, software y red. El equipo que hospeda ASDK debe cumplir los requisitos de hardware, software y red. También debe elegir entre el uso de Azure Active Directory (Azure AD) o los Servicios de federación de Active Directory (AD FS). Asegúrese de cumplir estos requisitos previos antes de comenzar la implementación para que el proceso de instalación se ejecute sin problemas.

Antes de implementar ASDK, asegúrese de que las configuraciones de hardware, sistema operativo, cuenta y red del equipo host de ASDK planeado cumplen los requisitos mínimos para su instalación.

**[Revisión de las consideraciones y requisitos de implementación de ASDK](asdk-deploy-considerations.md)** .

> [!TIP]
> Puede usar la [herramienta de comprobación de requisitos de implementación de Azure Stack](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) después de instalar el sistema operativo para confirmar que el hardware cumple todos los requisitos.

## <a name="download-and-extract-the-deployment-package"></a>Descargar y extraer el paquete de implementación
Después de asegurarse de que el equipo host de ASDK cumple los requisitos básicos para instalar ASDK, el siguiente paso es descargar y extraer el paquete de implementación de ASDK. El paquete de implementación incluye el archivo Cloudbuilder.vhdx, que es una unidad de disco duro virtual que incluye un sistema operativo de arranque y los archivos de instalación de Azure Stack.

Puede descargar el paquete de implementación en el host de ASDK o en otro equipo. Los archivos de implementación extraídos necesitan hasta 60 GB de espacio libre en disco, por lo que usar otro equipo puede ayudarle a reducir los requisitos de hardware para el host de ASDK.

**[Descarga y extracción de Kit de desarrollo de Azure Stack](asdk-download.md)**

## <a name="prepare-the-asdk-host-computer"></a>Preparación del equipo host del Kit de desarrollo de Azure Stack
Antes de poder instalar el Kit de desarrollo de Azure Stack en el equipo host, se debe preparar el entorno y el sistema configurado para arrancar desde VHD. Después de este paso, el host de ASDK arrancará en Cloudbuilder.vhdx (una unidad de disco duro virtual que incluye un sistema operativo de arranque y los archivos de instalación de Azure Stack).

Utilice PowerShell para configurar el equipo host del Kit de desarrollo de Azure Stack para arrancar desde CloudBuilder.vhdx. Estos comandos configuran el equipo host del Kit de desarrollo de Azure Stack para que arranque desde el disco duro virtual que ha descargado y extraído (CloudBuilder.vhdx). Después de completar estos pasos, reinicie el equipo host de ASDK.

Para configurar el equipo host del Kit de desarrollo de Azure Stack para arrancar desde CloudBuilder.vhdx:

  1. Inicie un símbolo del sistema como administrador.
  2. Ejecute `bcdedit /copy {current} /d "Azure Stack"`.
  3. Copie (CTRL + C) el valor de CLSID devuelto, incluyendo los corchetes necesarios (`{}`). Este valor se conoce como `{CLSID}` y deberá pegarlo en los pasos restantes (pulsando CTRL+V o haciendo clic con el botón derecho).
  4. Ejecute `bcdedit /set {CLSID} device vhd=[C:]\CloudBuilder.vhdx`.
  5. Ejecute `bcdedit /set {CLSID} osdevice vhd=[C:]\CloudBuilder.vhdx`.
  6. Ejecute `bcdedit /set {CLSID} detecthal on`.
  7. Ejecute `bcdedit /default {CLSID}`.
  8. Para comprobar la configuración de arranque, ejecute `bcdedit`.
  9. Asegúrese de que el archivo CloudBuilder.vhdx se ha movido a la raíz de la unidad C:\ (`C:\CloudBuilder.vhdx`) y reinicie el equipo host de ASDK. Cuando se reinicie el equipo host de ASDK, se debe arrancar desde el disco duro de la máquina virtual (VM) CloudBuilder.vhdx para iniciar la implementación de ASDK.

> [!IMPORTANT]
> Asegúrese de que tiene acceso a KVM o acceso físico directo al equipo host de ASDK antes de reiniciarlo. Cuando la máquina virtual se inicia por primera vez, le pedirá que complete el programa de instalación de Windows Server. Especifique las mismas credenciales de administrador que usó para iniciar sesión en el equipo host de ASDK.

### <a name="prepare-the-asdk-host-using-powershell"></a>Preparación del host de ASDK con PowerShell 
Después de que el equipo host de ASDK se arranque correctamente en la imagen de CloudBuilder.vhdx, inicie sesión con las mismas credenciales de administrador local que usó para iniciar sesión en el equipo host de ASDK. Estas son las mismas credenciales que proporcionó como parte del proceso de finalización del programa de instalación de Windows Server cuando el equipo host se arrancó desde VHD.

> [!NOTE]
> Si lo desea, también puede configurar [los valores de telemetría de Azure Stack](asdk-telemetry.md#set-telemetry-level-in-the-windows-registry) *antes* de instalar el Kit de desarrollo de Azure Stack.

Abra una consola de PowerShell con privilegios elevados y ejecute los comandos de esta sección para implementar ASDK en el host de ASDK.

> [!IMPORTANT]
> La instalación de ASDK admite exactamente una tarjeta de interfaz de red (NIC) para la conexión con redes. Si tiene varias NIC, asegúrese de que solo una está habilitada (y todas las demás deshabilitadas) antes de ejecutar el script de implementación.

Puede implementar Azure Stack con Azure AD o Windows Server AD FS como proveedor de identidades. Azure Stack, los proveedores de recursos y otras aplicaciones funcionan igual con ambos.

> [!TIP]
> Si no proporciona ningún parámetro de instalación (consulte los parámetros opcionales y ejemplos de InstallAzureStackPOC.ps1 que aparecen a continuación), se le piden los parámetros obligatorios.

### <a name="deploy-azure-stack-using-azure-ad"></a>Implementación de Azure Stack con Azure AD 
Para implementar Azure Stack **empleando Azure AD como proveedor de identidades**, debe tener conectividad a Internet directamente o a través de un proxy transparente. 

Ejecute los siguientes comandos de PowerShell para implementar ASDK con Azure AD:

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator     
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password
  ```

Unos minutos después de empezar la instalación de ASDK se le pedirán las credenciales de Azure AD. Debe proporcionar credenciales de administrador global para el inquilino de Azure AD.

Después de la implementación, no se necesita el permiso de administrador global de Azure Active Directory. Sin embargo, algunas operaciones pueden requerir la credencial de administrador global. Los ejemplos de tales operaciones incluyen un script de instalación del proveedor de recursos o una nueva característica que necesite la concesión de un permiso. Puede restablecer temporalmente los permisos de administrador global de la cuenta o usar una cuenta de administrador global independiente que sea propietaria de la *suscripción del proveedor predeterminada*.

### <a name="deploy-azure-stack-using-ad-fs"></a>Implementación de Azure Stack con AD FS 
Para implementar ASDK **mediante AD FS como proveedor de identidades**, ejecute los siguientes comandos de PowerShell (solo necesita agregar el parámetro -UseADFS):

  ```powershell
  cd C:\CloudDeployment\Setup     
  $adminpass = Get-Credential Administrator 
  .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -UseADFS
  ```

En las implementaciones con AD FS el servicio de directorio con la marca predeterminada se usará como proveedor de identidades. La cuenta predeterminada con la que iniciar sesión es azurestackadmin@azurestack.local y la contraseña que debe utilizar es la que proporcionó como parte de los comandos de configuración de PowerShell.

El proceso de implementación puede tardar unas horas, durante las cuales el sistema se reiniciará automáticamente una vez. Si la implementación se realiza correctamente, en la consola de PowerShell aparece el mensaje: **COMPLETE: Action ‘Deployment’** (COMPLETADA: Acción "Implementación"). Si se produce un error en la implementación, puede intentar ejecutar el script de nuevo mediante el parámetro -rerun. O bien, puede [volver a implementar ASDK](asdk-redeploy.md) desde el principio.

> [!IMPORTANT]
> Si desea supervisar el progreso de la implementación una vez que se reinicie el host de ASDK, deberá iniciar sesión como AzureStack\AzureStackAdmin. Si inicia sesión como administrador local después de que el equipo host se reinicie (y se una al dominio azurestack.local), no podrá ver el progreso de la implementación. No vuelva a ejecutar la implementación. En su lugar, inicie sesión como AzureStack\AzureStackAdmin con la misma contraseña que el administrador local para validar que el programa de instalación se esté ejecutando.


#### <a name="azure-ad-deployment-script-examples"></a>Ejemplos de scripts de implementación de Azure AD
Puede crear scripts para toda la implementación de Azure AD. Estos son unos pocos ejemplos comentados que incluyen algunos parámetros opcionales.

Si la identidad de Azure AD está asociada solo con **un** directorio de Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 52.168.138.145 #Example time server IP address.
```

Si la identidad de Azure AD está asociada con **más de un** directorio de Azure AD:
```powershell
cd C:\CloudDeployment\Setup 
$adminpass = Get-Credential Administrator 
$aadcred = Get-Credential "<Azure AD global administrator account name>" #Example: user@AADDirName.onmicrosoft.com 
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -InfraAzureDirectoryTenantName "<Azure AD directory in the form of domainname.onmicrosoft.com or an Azure AD verified custom domain name>" -TimeServer 52.168.138.145 #Example time server IP address.
```

Si su entorno no tiene DHCP habilitado, debe incluir los siguientes parámetros adicionales en una de las opciones anteriores (se proporciona un ejemplo de uso): 

```powershell
.\InstallAzureStackPOC.ps1 -AdminPassword $adminpass.Password -InfraAzureDirectoryTenantAdminCredential $aadcred -TimeServer 10.222.112.26
```

### <a name="asdk-installazurestackpocps1-optional-parameters"></a>Parámetros opcionales InstallAzureStackPOC.ps1 de ASDK

|Parámetro|Obligatorio/opcional|DESCRIPCIÓN|
|-----|-----|-----|
|AdminPassword|Obligatorio|Establece la cuenta de administrador local y el resto de cuentas de usuario en todas las VM que se crean como parte de la implementación de ASDK. Esta contraseña debe coincidir con la contraseña de administrador local actual del host.|
|InfraAzureDirectoryTenantName|Obligatorio|Establece el directorio del inquilino. Utilice este parámetro para especificar un directorio concreto en el que la cuenta de Azure AD tenga permisos para administrar varios directorios. Es el nombre completo de un inquilino de Azure AD con el formato .onmicrosoft.com, o bien un nombre de dominio personalizado de Azure AD verificado.|
|TimeServer|Obligatorio|Utilice este parámetro para especificar un servidor horario específico. Este parámetro se debe proporcionar como una dirección IP válida del servidor horario. No se admiten los nombres de servidor.|
|InfraAzureDirectoryTenantAdminCredential|Opcional|Permite establecer el nombre de usuario y la contraseña de Azure Active Directory. Estas credenciales de Azure deben ser un identificador de organización.|
|InfraAzureEnvironment|Opcional|Seleccione el entorno de Azure con el que desea registrar esta implementación de Azure Stack. Las opciones incluyen Azure global, Azure China, Azure: US Government.|
|DNSForwarder|Opcional|Se crea un servidor DNS como parte de la implementación de Azure Stack. Para permitir que los equipos dentro de la solución resuelvan nombres fuera de la marca, proporcione el servidor DNS de infraestructura existente. El servidor DNS en la marca reenvía solicitudes de resolución de nombres desconocidos a este servidor.|
|Rerun|Opcional|Use esta marca para volver a ejecutar la implementación. Se utilizarán todas las entradas anteriores. No se admite la reescritura de datos proporcionados anteriormente, ya que se generan varios valores únicos que se emplean en la implementación.|


## <a name="perform-post-deployment-configurations"></a>Realización de configuraciones después de la implementación
Después de instalar el Kit de desarrollo de Azure Stack, se recomiendan algunas comprobaciones posteriores a la instalación y cambios de configuración que deben realizarse. Para validar que la instalación se realizó correctamente, use el cmdlet test-AzureStack e instale las herramientas PowerShell y GitHub de Azure Stack.

Es recomendable restablecer la directiva de expiración de contraseñas para asegurarse de que la contraseña del host de ASDK no expire antes de que termine el período de evaluación.

> [!NOTE]
> Si lo desea, también puede configurar los [valores de telemetría de Azure Stack](asdk-telemetry.md#enable-or-disable-telemetry-after-deployment) *después* de instalar el Kit de desarrollo de Azure Stack.

**[Tareas de implementación posteriores al Kit de desarrollo de Azure Stack](asdk-post-deploy.md)**

## <a name="register-with-azure"></a>Registrarse en Azure
Debe registrar Azure Stack en Azure para que pueda [descargar elementos de Azure Marketplace](../operator/azure-stack-create-and-publish-marketplace-item.md) a Azure Stack.

**[Registro de Azure Stack en Azure](asdk-register.md)**

## <a name="next-steps"></a>Pasos siguientes
Felicidades. Después de completar estos pasos, tendrá un entorno de ASDK con los portales de [administración](https://adminportal.local.azurestack.external) y de [usuarios](https://portal.local.azurestack.external). 

[Tareas de configuración posteriores a la instalación de ASDK](asdk-post-deploy.md)

