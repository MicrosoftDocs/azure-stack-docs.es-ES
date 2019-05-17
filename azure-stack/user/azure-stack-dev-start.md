---
title: Configuración de un entorno de desarrollo en Azure Stack | Microsoft Docs
description: Empiece a desarrollar aplicaciones para Azure Stack.
services: azure-stack
author: mattbriggs
ms.service: azure-stack
ms.topic: overview
ms.date: 04/25/2019
ms.author: mabrigg
ms.reviewer: sijuman
ms.lastreviewed: 04/24/2019
ms.openlocfilehash: 9b3d58e67d7c6ca7016b3ecb51c09171aae7c06a
ms.sourcegitcommit: 0d8ccf2a32b08ab9bcbe13d54c7c3dce2379757f
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/25/2019
ms.locfileid: "64490031"
---
# <a name="set-up-a-development-environment-on-azure-stack"></a>Configuración de un entorno de desarrollo en Azure Stack 

Puede desarrollar aplicaciones para Azure Stack con una estación de trabajo de Windows 10, Linux o macOS. En este artículo, nos fijamos en lo siguiente: 

- Los distintos contextos en que la aplicación se ejecuta en Azure Stack. 
- Los pasos que debe seguir para establecer una configuración con una estación de trabajo de Windows 10, Linux o macOS. 
- Los pasos para crear recursos en Azure Stack e implementarlos en una aplicación. 

## <a name="azure-stack-context-and-your-code"></a>Contexto de Azure Stack y el código 

Puede escribir scripts y aplicaciones que pueden hacer cualquier cosa en Azure Stack. Sin embargo, puede ser útil limitar lo que está tratando de lograr en tres modos diferentes. 

1. En el primer modo, puede crear aplicaciones que aprovisionarán los recursos en Azure Stack mediante plantillas de Azure Resource Manager. Por ejemplo, puede crear un script que construirá una plantilla de Azure Resource Manager que creará una red virtual y las máquinas virtuales que hospedarán su aplicación. 

2. En el segundo modo, trabaja directamente con los puntos de conexión con la API REST y un cliente REST creado en el código. En este modo, crearía un script que a su vez crea una red virtual y máquinas virtuales mediante el envío de solicitudes a las API. 

3. En el tercer modo, puede usar el código para crear una aplicación que se hospeda en Azure Stack. Una vez que tenga la infraestructura de Azure Stack para hospedar la aplicación, implemente la aplicación en la infraestructura. Normalmente, prepará su entorno y, a continuación, implementará la aplicación en ese entorno. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Infraestructura como servicio y plataforma como servicio 

En su calidad de nube, Azure Stack es compatible con ambos sistemas: 

- Infraestructura como servicio (IaaS) 
- Plataforma como servicio (PaaS) 

Tanto IaaS como PaaS informan de cómo le gustaría configurar su máquina de desarrollo. 

IaaS es la virtualización de las partes del centro de datos, que engloba el engranaje de la red, la red y los servidores, entre otros elementos. Al implementar una aplicación en una máquina virtual que hospeda el servidor web, está trabajando en un paradigma de IaaS. En este paradigma, Azure Stack administra el engranaje virtual y la aplicación está en un servidor virtual. Los proveedores de recursos de Azure Stack admiten componentes de red y servidores virtuales. 

PaaS abstrae la capa de infraestructura para que implemente su aplicación en un punto de conexión que luego ejecuta su aplicación. En el paradigma de PaaS, puede usar contenedores para hospedar su aplicación y luego implementar su aplicación en contenedores en un servicio que ejecuta el contenedor, o puede insertar su aplicación directamente en un servicio que ejecute su aplicación. Azure Stack puede usarse para ejecutar Azure App Service y Kubernetes. 

### <a name="azure-stack-resource-manager"></a>Administrador de recursos de Azure Stack 

Estos tres modos, así como PaaS o IaaS, se habilitan por la versión de Azure Stack de Azure Resource Manager. El marco de administración le permite implementar, administrar y supervisar los recursos de Azure Stack. El administrador le permite trabajar con estos elementos como un grupo en una sola operación. Para más información sobre el trabajo con el administrador de recursos de Azure Stack, consulte [Administración de perfiles de versión de API en Azure Stack](azure-stack-version-profiles.md). 

### <a name="azure-stack-development-kits"></a>Kits de desarrollo de Azure Stack 

Azure Stack usa una versión de Azure Stack de Azure Resource Manager.  Con el fin de ayudarle a trabajar con el administrador de recursos de Azure Stack mediante el código que prefiera, ofrecemos diferentes kits de desarrollo de software. Entre ellas se incluyen las siguientes: 

- [.Net/C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)

## <a name="before-you-start"></a>Antes de comenzar 

Necesitará: 

- Acceder al portal de usuarios de Azure Stack. 
- El nombre del inquilino. 
- Saber si está utilizando Azure Active Directory (Azure AD) o Servicios de federación de Active Directory (AD FS) como almacén de identidades. 

Si tiene alguna pregunta sobre Azure Stack, póngase en contacto con su operador de nube. 

## <a name="windows-10"></a>Windows 10 

Una máquina con Windows 10 le permitirá trabajar con PowerShell 5.0 y Visual Studio; si está trabajando con un ASDK, conéctese a su entorno con una conexión VPN. 

### <a name="set-up-your-tools"></a>Configuración de las herramientas 

1. Establezca la configuración con PowerShell. Para obtener instrucciones, siga los pasos de [Install Azure Stack Powershell](../operator/azure-stack-powershell-install.md) (Instalación de PowerShell para Azure Stack). 

2. Descargue las herramientas de Azure Stack. Para obtener instrucciones, siga los pasos de [Descarga de herramientas de Azure Stack desde GitHub](../operator/azure-stack-powershell-download.md). 

3. Si usa un ASDK, instale y configure una [conexión VPN a Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

4. Instale y configure la CLI de Azure. Para obtener instrucciones, siga los pasos de [Uso de los perfiles de la versión de la API con la CLI de Azure en Azure Stack](azure-stack-version-profiles-azurecli2.md). 

5. Instale y configure el explorador de almacenamiento de Azure. El explorador de almacenamiento de Azure es una aplicación independiente que le permite trabajar fácilmente con datos de almacenamiento de Azure Stack.  Para obtener instrucciones, siga los pasos de [Connect storage explorer to an Azure Stack subscription or a storage account](azure-stack-storage-connect-se.md) (Conexión del explorador de almacenamiento a una suscripción de Azure Stack o una cuenta de almacenamiento). 

### <a name="install-your-integrated-development-environment"></a>Instalación del entorno de desarrollo integrado 

1. Instale el entorno de desarrollo integrado (IDE), dependiendo de su base de código y sus preferencias. 

     - Visual Studio Code (Python, Go, NodeJS). Descargue Visual Studio Code para su máquina de [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.Net/C#). Descargue la edición Visual Studio Community de [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Descargue Eclipse de [eclipse.org](https://www.eclipse.org/downloads/). 

2. Instale el kit de desarrollo de software (SDK) para el código. 

     - [.Net/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Una máquina Linux le permitirá trabajar con la CLI de Azure y Visual Studio Code o su propio entorno de desarrollo integrado preferido. 

> [!Note]   
> Si usa una máquina Linux con el ASDK, el equipo remoto deberá estar en la misma red que el ASDK. No podrá conectarse usando una conexión de red privada virtual. 

### <a name="set-up-your-tools"></a>Configuración de las herramientas 

1. Instale y configure la CLI de Azure. Para obtener instrucciones, siga los pasos de [Uso de los perfiles de la versión de la API con la CLI de Azure en Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Instale y configure el explorador de almacenamiento de Azure. El explorador de almacenamiento de Azure es una aplicación independiente que le permite trabajar fácilmente con datos de almacenamiento de Azure Stack.  Para obtener instrucciones, siga los pasos de [Connect storage explorer to an Azure Stack subscription or a storage account](azure-stack-storage-connect-se.md) (Conexión del explorador de almacenamiento a una suscripción de Azure Stack o una cuenta de almacenamiento). 

### <a name="install-your-integrated-development-environment"></a>Instalación del entorno de desarrollo integrado 

1. Instale el entorno de desarrollo integrado (IDE), dependiendo de su base de código y sus preferencias. 

     - Visual Studio Code (Python, Go, NodeJS). Descargue Visual Studio Code para su máquina de [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.Net/C#). Descargue la edición Visual Studio Community de [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Descargue Eclipse de [eclipse.org](https://www.eclipse.org/downloads/). 

2. Instale el kit de desarrollo de software (SDK) para el código. 

     - [.Net/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOs 

Una máquina macOS le permitirá trabajar con la CLI de Azure y Visual Studio Code o su propio entorno de desarrollo integrado preferido. 

> [!Note]   
> Si usa una máquina macOS con el ASDK, el equipo remoto deberá estar en la misma red que el ASDK. No podrá conectarse usando una conexión de red privada virtual. 

### <a name="set-up-your-tools"></a>Configuración de las herramientas 

1. Instale y configure la CLI de Azure. Para obtener instrucciones, siga los pasos de [Uso de los perfiles de la versión de la API con la CLI de Azure en Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Instale y configure el explorador de almacenamiento de Azure. El explorador de almacenamiento de Azure es una aplicación independiente que le permite trabajar fácilmente con datos de almacenamiento de Azure Stack. Para obtener instrucciones, siga los pasos de [Connect storage explorer to an Azure Stack subscription or a storage account](azure-stack-storage-connect-se.md) (Conexión del explorador de almacenamiento a una suscripción de Azure Stack o una cuenta de almacenamiento). 

### <a name="install-your-integrated-development-environment"></a>Instalación del entorno de desarrollo integrado 

1. Instale el entorno de desarrollo integrado (IDE), dependiendo de su base de código y sus preferencias. 

     - Visual Studio Code (Python, Go, NodeJS). Descargue Visual Studio Code para su máquina de [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.Net/C#). Descargue la edición Visual Studio Community de [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Descargue Eclipse de [eclipse.org](https://www.eclipse.org/downloads/). 

2. Instale el kit de desarrollo de software (SDK) para el código. 

     - [.Net/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Pasos siguientes 

Implemente una aplicación en los recursos de Azure Stack. Puede encontrar los pasos descritos en [Common deployments for Azure Stack](azure-stack-dev-start-deploy-app.md) (Implementaciones comunes para Azure Stack).