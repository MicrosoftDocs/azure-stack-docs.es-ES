---
title: Aspectos básicos de administración de Azure Stack | Microsoft Docs
description: Aprenda lo necesario para administrar Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 856738a7-1510-442a-88a8-d316c67c757c
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: mabrigg
ms.lastreviewed: 10/15/2018
ms.openlocfilehash: 83c4295c5f14cb89122aca096b7e90cfd44d8d4a
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618597"
---
# <a name="azure-stack-administration-basics"></a>Aspectos básicos de administración de Azure Stack
Hay varias cosas que debe saber si no está familiarizado con la administración de Azure Stack. En este artículo se proporciona información general acerca del rol de operador de Azure Stack, y de lo que debe decir a los usuarios para que aumenten su productividad rápidamente.

## <a name="understand-the-builds"></a>Descripción de las compilaciones

### <a name="integrated-systems"></a>Sistemas integrados

Si se usa un sistema integrado de Azure Stack, las versiones actualizadas de Azure Stack se distribuyen a través de paquetes de actualización. Para importar y aplicar estos paquetes, haga clic en el icono Actualizaciones del portal de administrador. No se admite para actualizar las instalaciones de ASDK. 
 
### <a name="development-kit"></a>Kit de desarrollo

Si usa el Kit de desarrollo de Azure Stack, consulte el artículo [What is Azure Stack?](../asdk/asdk-what-is.md) (¿Qué es Azure Stack?) para asegurarse de que comprende el propósito del Kit de desarrollo y sus limitaciones. Debe usar el kit de desarrollo como un “espacio aislado” donde puede evaluar Azure Stack, y desarrollar y probar sus aplicaciones en un entorno que no es de producción. (para obtener información acerca de la implementación, consulte el artículo [Implementación del Kit de desarrollo de Azure Stack](../asdk/asdk-install.md)).

Como Azure, innovamos con rapidez. Publicaremos nuevas compilaciones con asiduidad. Si ejecuta el kit de desarrollo y desea pasar a la compilación más reciente, debe [volver a implementar Azure Stack](../asdk/asdk-redeploy.md), no puede aplicar paquetes de actualización. Este proceso lleva tiempo, pero la ventaja es que puede probar las características más recientes. La documentación del kit de desarrollo que hay en nuestro sitio web refleja la versión de compilación más reciente.

## <a name="learn-about-available-services"></a>Información sobre los servicios disponibles

Deberá saber qué servicios puede poner a disposición de los usuarios. Azure Stack admite un subconjunto de servicios de Azure. La lista de servicios admitidos continuará evolucionando.

**Servicios fundamentales**

De forma predeterminada, Azure Stack incluye los siguientes “servicios fundamentales” cuando se implementa Azure Stack:

- Proceso
- Almacenamiento
- Redes
- Key Vault

Con estos servicios fundamentales, puede ofrecer la infraestructura como servicio (IaaS) a los usuarios con una configuración mínima.

**Servicios adicionales**

Actualmente, se admiten los siguientes servicios de plataforma como servicio (PaaS) adicionales:

- App Service
- Azure Functions
- Bases de datos SQL y MySQL

Estos servicios requieren una configuración adicional para que pueda ponerlos a disposición de los usuarios. Para más información, consulte las secciones "Tutoriales" y "Guías de procedimientos\Servicios de la oferta" de la documentación del operador de Azure Stack.

**Mapa de ruta de los servicios**

Azure Stack continuará agregando compatibilidad con los servicios de Azure. Para información sobre el mapa de ruta previsto, consulte las notas del producto [Azure Stack: una extensión de Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409). También puede supervisar las [publicaciones de blog de Azure Stack](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) para conocer los nuevos anuncios.

## <a name="what-account-should-i-use"></a>¿Qué cuenta debo usar?
Al administrar Azure Stack, debe considerar algunos aspectos con respecto a la cuenta, especialmente en las implementaciones que utilizan Servicios de federación de Active Directory (AD FS) de Windows Server como proveedor de identidades en lugar de Azure Active Directory (Azure AD). Las siguientes consideraciones sobre la cuenta se aplican a los sistemas integrados de Azure Stack y las implementaciones de ASDK:


|Cuenta|Azure AD|AD FS|
|-----|-----|-----|
|Administrador local (.\Administrator)|Administrador del host de ASDK|Administrador del host de ASDK|
|AzureStack\AzureStackAdmin|Administrador del host de ASDK<br><br>Se puede usar para iniciar sesión en el portal de administración de Azure Stack<br><br>Acceso para ver y administrar anillos de Service Fabric|Administrador del host de ASDK<br><br>Sin acceso al portal de administración de Azure Stack<br><br>Acceso para ver y administrar anillos de Service Fabric<br><br>Ya no es propietario de la suscripción de proveedor predeterminada (DPS)|
|AzureStack\CloudAdmin|Puede acceder a comandos permitidos, y ejecutarlos, en el punto de conexión con privilegios|Puede acceder a comandos permitidos, y ejecutarlos, en el punto de conexión con privilegios<br><br>No puede iniciar sesión en el host de ASDK<br><br>Propietario de la suscripción de proveedor predeterminada (DPS)|
|Administrador global de Azure AD|Se usa durante la instalación<br><br>Propietario de la suscripción de proveedor predeterminada (DPS)|No aplicable|
|

## <a name="what-tools-do-i-use-to-manage"></a>¿Qué herramientas debo usar para la administración?
 
Puede usar el [portal de administración](azure-stack-manage-portals.md) o PowerShell para administrar Azure Stack. La manera más fácil de aprender los conceptos básicos es a través del portal. Si quiere usar PowerShell, existen algunos pasos preparatorios. Debe [instalar](azure-stack-powershell-install.md) PowerShell, [descargar](azure-stack-powershell-download.md) módulos adicionales y [configurar](azure-stack-powershell-configure-admin.md) PowerShell.

Azure Stack usa Azure Resource Manager como mecanismo subyacente de implementación, administración y organización. Si va a administrar Azure Stack y ayudar en el soporte técnico a los usuarios, debe obtener información sobre Resource Manager. Consulte las notas del producto [Getting Started with Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) (Introducción a Azure Resource Manager).

## <a name="your-typical-responsibilities"></a>Sus responsabilidades típicas

Los usuarios quieren usar los servicios. Desde la perspectiva de estos, su rol principal es poner estos servicios a su disposición. Debe decidir qué servicios se ofrecen y poner dichos servicios a disposición de los usuarios mediante la creación de planes, ofertas y cuotas. Para más información, consulte [Introducción a la oferta de servicios en Azure Stack](azure-stack-offer-services-overview.md). 

También tendrá que agregar elementos al [Marketplace](azure-stack-marketplace.md), como imágenes de máquinas virtuales. La manera más fácil es [descargar elementos de Marketplace de Azure a Azure Stack](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Si quiere probar los planes, las ofertas y los servicios, debe utilizar el [portal de usuarios](azure-stack-manage-portals.md), no el portal de administración.

Además de ofrecer servicios, debe realizar todas las tareas habituales de un operador para mantener Azure Stack en funcionamiento. Estas tareas incluyen las siguientes:

- Agregar cuentas de usuario (para la implementación de [Azure Active Directory](azure-stack-add-new-user-aad.md) o de [Servicios de federación de Active Directory](azure-stack-add-users-adfs.md))
- [Asignar roles de control de acceso basado en roles (RBAC)](azure-stack-manage-permissions.md) (esto no se restringe a los administradores).
- [Supervisar el estado de la infraestructura](azure-stack-monitor-health.md)
- Administrar los recursos de [red](azure-stack-viewing-public-ip-address-consumption.md) y [almacenamiento](azure-stack-manage-storage-accounts.md)
- Reemplazar el hardware defectuoso, por ejemplo [reemplazar un disco con errores](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Qué decirles a los usuarios

Deberá informar a los usuarios cómo trabajar con los servicios en Azure Stack, conectarse con el entorno y suscribirse a las ofertas. Además de toda la documentación personalizada que puede proporcionar a los usuarios, puede enviar a los usuarios al sitio de documentación de usuarios de Azure Stack.

**Información sobre cómo trabajar con servicios en Azure Stack**

Hay información que los usuarios deben comprender antes de usar los servicios y compilar aplicaciones en Azure Stack. Por ejemplo, existen requisitos específicos para las versiones de PowerShell y API. Además, existen algunas diferencias de características entre un servicio en Azure y el servicio equivalente en Azure Stack. Asegúrese de que los usuarios consulten los siguientes artículos:

- [Consideraciones clave: uso de servicios o compilación de aplicaciones para Azure Stack](../user/azure-stack-considerations.md)
- [Consideraciones sobre máquinas virtuales en Azure Stack](../user/azure-stack-vm-considerations.md)
- [Storage: differences and considerations](../user/azure-stack-acs-differences.md) (Storage: diferencias y consideraciones)

La información de estos artículos resume las diferencias entre un servicio en Azure y Azure Stack. Complementa la información disponible para un servicio de Azure en la documentación global de Azure.

**Conexión a Azure Stack como usuario**

En un entorno del kit de desarrollo, si un usuario no tiene acceso del Escritorio remoto al host del kit de desarrollo, debe configurar una conexión de red privada virtual (VPN) para poder obtener acceso a Azure Stack. Consulte [Conexión a Azure Stack](../asdk/asdk-connect.md). 

Los usuarios querrán saber cómo [acceder al portal de usuarios](../user/azure-stack-use-portal.md) o cómo conectarse a través de PowerShell. En un entorno de sistemas integrados, la dirección del portal de usuarios varía en función de la implementación. Tendrá que proporcionar a los usuarios la dirección URL correcta.

Si usa PowerShell, puede que los usuarios deban registrar proveedores de recursos para poder usar los servicios. (Un proveedor de recursos administra un servicio. Por ejemplo, el proveedor de recursos de red administra recursos, como redes virtuales, interfaces de red y equilibradores de carga). Debe [instalar](azure-stack-powershell-install.md) PowerShell, [descargar](azure-stack-powershell-download.md) módulos adicionales y [configurar](../user/azure-stack-powershell-configure-user.md) PowerShell (que incluye el registro de proveedores de recursos).

**Suscripción a una oferta**

Para que un usuario pueda acceder a los servicios, debe [suscribirse a una oferta](azure-stack-subscribe-plan-provision-vm.md) que haya creado un operador.

## <a name="where-to-get-support"></a>Dónde obtener soporte técnico

### <a name="integrated-systems"></a>Sistemas integrados

En el caso de un sistema integrado, hay un proceso de escalamiento y resolución coordinado entre Microsoft y nuestros partners de hardware de fabricante de equipos originales (OEM).

Si hay algún problema en los servicios en la nube, el soporte técnico se ofrece a través de los servicios de soporte al cliente de Microsoft (CSS). Si hace clic en el icono de Ayuda y soporte técnico (signo de interrogación), que encontrará en la esquina superior derecha del portal de administración, y después en **Nueva solicitud de soporte técnico**, se abrirá un sitio en el que se pueden abrir directamente solicitudes de soporte técnico.

Si surge algún problema en la implementación, aplicación de revisiones y actualizaciones, hardware (incluidas las unidades reemplazables en campo) y cualquier software específico de una marca de hardware, como el software que se ejecuta en el host del ciclo de vida del hardware, en primer lugar póngase en contacto con su proveedor de hardware OEM.

Para todo lo demás, póngase en contacto con el servicio de asistencia al cliente (CSS) de Microsoft.

### <a name="development-kit"></a>Kit de desarrollo

En el caso del kit de desarrollo, puede dirigir las preguntas relacionadas con el soporte técnico a los [foros de Microsoft](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Si hace clic el icono de ayuda y soporte técnico (signo de interrogación) en la esquina superior derecha del portal de administración y, a continuación, hace clic en **Nueva solicitud de soporte técnico**, se abrirá el sitio de foros directamente. Estos foros se supervisan periódicamente. Dado que el kit de desarrollo es un entorno de evaluación, el servicio de asistencia al cliente (CSS) de Microsoft no ofrece soporte técnico oficial.

## <a name="next-steps"></a>Pasos siguientes

[Administración de regiones en Azure Stack](azure-stack-region-management.md)


