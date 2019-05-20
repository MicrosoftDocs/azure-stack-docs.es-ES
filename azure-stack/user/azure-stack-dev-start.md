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
ms.openlocfilehash: 06c462ab46ce6bbae8d5c3bd6fcb757e14417edf
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65617623"
---
# <a name="set-up-a-development-environment-in-azure-stack"></a>Configurar un entorno de desarrollo en Azure Stack 

Puede desarrollar aplicaciones para Azure Stack con una estación de trabajo de Windows 10, Linux o macOS. En este artículo, nos centramos en los siguientes temas: 

- Los distintos contextos en que la aplicación se ejecuta en Azure Stack. 
- Los pasos que debe seguir para establecer una configuración con una estación de trabajo de Windows 10, Linux o macOS. 
- Los pasos para crear recursos en Azure Stack e implementarlos en una aplicación. 

## <a name="azure-stack-context-and-your-code"></a>Contexto de Azure Stack y el código 

Puede escribir scripts y aplicaciones para realizar muchas tareas en Azure Stack. Sin embargo, resulta útil limitar el ámbito a los tres modos siguientes: 

1. En el primer modo, puede crear aplicaciones que aprovisionen los recursos en Azure Stack mediante plantillas de Azure Resource Manager. Por ejemplo, puede escribir un script que construya una plantilla de Azure Resource Manager que cree una red virtual y las máquinas virtuales que hospedarán su aplicación. 

2. En el segundo modo, trabaja directamente con los puntos de conexión mediante la API de REST y un cliente REST que se crearon en el código. En este modo, escribiría un script que a su vez crearía una red virtual y máquinas virtuales mediante el envío de solicitudes a las API. 

3. En el tercer modo, puede usar el código para crear una aplicación que se hospede en Azure Stack. Después de crear la infraestructura de Azure Stack para hospedar la aplicación, debe implementar la aplicación en la infraestructura. Normalmente, prepara el entorno y, a continuación, implementa la aplicación en ese entorno. 

###  <a name="infrastructure-as-a-service-and-platform-as-a-service"></a>Infraestructura como servicio y plataforma como servicio 

En su calidad de producto de plataforma en la nube, Azure Stack es compatible con ambos sistemas: 

- Infraestructura como servicio (IaaS) 
- Plataforma como servicio (PaaS) 

Tanto IaaS como PaaS informan sobre cómo configurar la máquina de desarrollo. 

IaaS es la virtualización de las partes del centro de datos, que engloba el engranaje de la red, la red y los servidores. Al implementar una aplicación en una VM que hospeda el servidor web, trabaja en un modelo de IaaS. En este modelo, Azure Stack administra el engranaje virtual y la aplicación está en un servidor virtual. Los proveedores de recursos de Azure Stack admiten componentes de red y servidores virtuales. 

PaaS abstrae la capa de infraestructura para que implemente su aplicación en un punto de conexión que luego ejecuta la aplicación. En el modelo PaaS, puede usar contenedores para hospedar la aplicación y, a continuación, implementar la aplicación en contenedores en un servicio que ejecute el contenedor. O bien, puede insertar la aplicación directamente en un servicio que ejecute la aplicación. Azure Stack puede usarse para ejecutar Azure App Service y Kubernetes. 

### <a name="azure-stack-resource-manager"></a>Administrador de recursos de Azure Stack 

Estos tres modos, así como PaaS o IaaS, se habilitan mediante la versión de Azure Stack de Azure Resource Manager. El marco de administración le permite implementar, administrar y supervisar los recursos de Azure Stack. Le permite trabajar con estos elementos como un grupo en una sola operación. Para más información sobre el trabajo con Resource Manager de Azure Stack, consulte [Administración de perfiles de versión de API en Azure Stack](azure-stack-version-profiles.md). 

### <a name="azure-stack-sdks"></a>SDK de Azure Stack 

Azure Stack usa una versión de Azure Stack de Azure Resource Manager. Para ayudarle a trabajar con Resource Manager de Azure Stack mediante el código que elija, ofrecemos varios SDK, como los siguientes: 

- [.NET/C#](azure-stack-version-profiles-net.md)
- [Java](azure-stack-version-profiles-java.md)
- [Go](azure-stack-version-profiles-go.md)
- [Ruby](azure-stack-version-profiles-ruby.md)
- [Python](azure-stack-version-profiles-python.md)

## <a name="before-you-start"></a>Antes de comenzar 

Antes de comenzar a configurar el entorno, necesita lo siguiente: 

- Acceder al portal de usuarios de Azure Stack. 
- El nombre del inquilino. 
- Para determinar si está utilizando Azure Active Directory (Azure AD) o Servicios de federación de Active Directory (AD FS) como administrador de identidades. 

Si tiene alguna pregunta sobre Azure Stack, póngase en contacto con su operador de nube. 

## <a name="windows-10"></a>Windows 10 

Si usa un máquina Windows 10, puede trabajar con PowerShell 5.0 y Visual Studio. Si trabaja con un Kit de desarrollo de Azure Stack (ASDK), puede conectarse a su entorno con una conexión VPN. 

### <a name="set-up-your-tools"></a>Configuración de las herramientas 

1. Establezca la configuración con PowerShell. Para obtener instrucciones, consulte [Install Azure Stack Powershell](../operator/azure-stack-powershell-install.md) (Instalación de PowerShell de Azure Stack). 

2. Descargue las herramientas de Azure Stack. Para obtener instrucciones, consulte [Descarga de herramientas de Azure Stack desde GitHub](../operator/azure-stack-powershell-download.md). 

3. Si usa un ASDK, instale y configure una [conexión VPN a Azure Stack](azure-stack-connect-azure-stack.md#connect-to-azure-stack-with-vpn). 

4. Instale y configure la CLI de Azure. Para obtener más información, consulte [Uso de los perfiles de la versión de la API con la CLI de Azure en Azure Stack](azure-stack-version-profiles-azurecli2.md). 

5. Instale y configure el Explorador de Azure Storage. El Explorador de Storage es una aplicación independiente que le permite trabajar fácilmente con datos de almacenamiento de Azure Stack. Para obtener más información, consulte [Conexión del Explorador de Storage a una suscripción de Azure Stack o una cuenta de almacenamiento](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Instalación del entorno de desarrollo integrado 

1. Instale el entorno de desarrollo integrado (IDE), dependiendo de su base de código y sus preferencias. 

     - Visual Studio Code (Python, Go, NodeJS). Descargue Visual Studio Code para su máquina de [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Descargue la edición Visual Studio Community de [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Descargue Eclipse de [eclipse.org](https://www.eclipse.org/downloads/). 

2. Instale el SDK para el código: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="linux"></a>Linux 

Si utiliza una máquina Linux, puede trabajar con la CLI de Azure, Visual Studio Code o su propio entorno de desarrollo integrado preferido. 

> [!Note]   
> Si usa una máquina Linux con el ASDK, el equipo remoto deberá estar en la misma red que el ASDK. No podrá conectarse usando una conexión de red privada virtual. 

### <a name="set-up-your-tools"></a>Configuración de las herramientas 

1. Instale y configure la CLI de Azure. Para obtener más información, consulte [Uso de los perfiles de la versión de la API con la CLI de Azure en Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Instale y configure el Explorador de Azure Storage. El Explorador de Storage es una aplicación independiente que le permite trabajar fácilmente con datos de almacenamiento de Azure Stack. Para obtener más información, consulte [Conexión del Explorador de Storage a una suscripción de Azure Stack o una cuenta de almacenamiento](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Instalación del entorno de desarrollo integrado 

1. Instale el entorno de desarrollo integrado (IDE), dependiendo de su base de código y sus preferencias. 

     - Visual Studio Code (Python, Go, NodeJS). Descargue Visual Studio Code para su máquina de [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Descargue la edición Visual Studio Community de [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Descargue Eclipse de [eclipse.org](https://www.eclipse.org/downloads/). 

2. Instale el SDK para el código: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md) 
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="macos"></a>macOS 

Una máquina macOS le permitirá trabajar con la CLI de Azure y Visual Studio Code, o bien con su propio entorno de desarrollo integrado preferido. 

> [!Note]   
> Si usa una máquina macOS con el ASDK, la máquina remota deberá estar en la misma red que el ASDK. No podrá conectarse usando una conexión de red privada virtual. 

### <a name="set-up-your-tools"></a>Configuración de las herramientas 

1. Instale y configure la CLI de Azure. Para obtener más información, consulte [Uso de los perfiles de la versión de la API con la CLI de Azure en Azure Stack](azure-stack-version-profiles-azurecli2.md). 

2. Instale y configure el Explorador de Azure Storage. El Explorador de Storage es una aplicación independiente que le permite trabajar fácilmente con datos de almacenamiento de Azure Stack. Para obtener más información, consulte [Conexión del Explorador de Storage a una suscripción de Azure Stack o una cuenta de almacenamiento](azure-stack-storage-connect-se.md). 

### <a name="install-your-integrated-development-environment"></a>Instalación del entorno de desarrollo integrado 

1. Instale el entorno de desarrollo integrado (IDE), dependiendo de su base de código y sus preferencias. 

     - Visual Studio Code (Python, Go, NodeJS). Descargue Visual Studio Code para su máquina de [code.visualstudio.com](https://code.visualstudio.com/Download). 
     - Visual Studio (.NET/C#). Descargue la edición Visual Studio Community de [visualstudio.microsoft.com](https://visualstudio.microsoft.com/vs/community/). 
     - Eclipse (Java). Descargue Eclipse de [eclipse.org](https://www.eclipse.org/downloads/). 

2. Instale el SDK para el código: 

     - [.NET/C#](azure-stack-version-profiles-net.md) 
     - [Java](azure-stack-version-profiles-java.md) 
     - [Go](azure-stack-version-profiles-go.md)
     - [Ruby](azure-stack-version-profiles-python.md) 
     - [Python](azure-stack-version-profiles-python.md) 

## <a name="next-steps"></a>Pasos siguientes 

Para implementar una aplicación en los recursos de Azure Stack, consulte [Implementaciones comunes para Azure Stack](azure-stack-dev-start-deploy-app.md).
