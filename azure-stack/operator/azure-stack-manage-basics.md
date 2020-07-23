---
title: Aspectos básicos de la administración de Azure Stack Hub
description: Aprenda los aspectos básicos de la administración de Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 06/16/2020
ms.author: justinha
ms.lastreviewed: 06/16/2020
ms.openlocfilehash: bacaccd664996af7dafa7fd6b7b3518b7dcaa851
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2020
ms.locfileid: "86488559"
---
# <a name="azure-stack-hub-administration-basics"></a>Aspectos básicos de la administración de Azure Stack Hub

Si no está familiarizado con la administración de Azure Stack Hub, hay varias cosas que debe saber. En este artículo se ofrece información general acerca de su rol como operador de Azure Stack Hub y lo que debe decir a los usuarios para ayudarles a ser productivos.

## <a name="understand-the-builds"></a>Descripción de las compilaciones

### <a name="integrated-systems"></a>Sistemas integrados

Si usa un sistema integrado de Azure Stack Hub, las actualizaciones distribuyen versiones actualizadas de Azure Stack Hub. Para importar y aplicar estos paquetes, haga clic en el icono **Actualizaciones** del portal de administración.
 
### <a name="development-kit"></a>Kit de desarrollo

Si usa el Kit de desarrollo de Azure Stack (ASDK), consulte el artículo [¿Qué es Azure Stack Hub?](../asdk/asdk-what-is.md) para conocer el propósito del ASDK y sus limitaciones. El Kit de desarrollo de Azure Stack se puede usar como un *espacio aislado* donde se puede evaluar Azure Stack Hub, así como desarrollar y probar sus aplicaciones en un entorno que no sea de producción. Para obtener información acerca de la implementación, consulte [Implementación del Kit de desarrollo de Azure Stack](../asdk/asdk-install.md).

Como Azure, innovamos con rapidez. Publicaremos nuevas compilaciones con asiduidad. Si ejecuta ASDK y desea pasar a la compilación más reciente, debe [volver a implementar Azure Stack Hub](../asdk/asdk-redeploy.md). No puede aplicar paquetes de actualización. Este proceso lleva tiempo, pero la ventaja es que puede probar las características más recientes. La documentación de ADSK que se encuentra en nuestro sitio web hace referencia a la versión de compilación más reciente.

## <a name="learn-about-available-services"></a>Información sobre los servicios disponibles

Deberá saber qué servicios puede poner a disposición de los usuarios. Azure Stack Hub admite un subconjunto de servicios de Azure. La lista de servicios admitidos continuará evolucionando.

**Servicios fundamentales**

De forma predeterminada, Azure Stack Hub incluye los siguientes “servicios fundamentales” cuando se implementa Azure Stack Hub:

- Proceso
- Storage
- Redes
- Key Vault

Con estos servicios fundamentales, puede ofrecer la infraestructura como servicio (IaaS) a los usuarios con una configuración mínima.

**Servicios adicionales**

Actualmente, se admiten los siguientes servicios de plataforma como servicio (PaaS) adicionales:

- App Service
- Azure Functions
- Bases de datos SQL y MySQL
- Kubernetes (en versión preliminar)

Estos servicios requieren una configuración adicional para que pueda ponerlos a disposición de los usuarios. Para más información, consulte las secciones "Tutoriales" y "Guías de procedimientos\Servicios de la oferta" de la documentación del operador de Azure Stack Hub.

**Mapa de ruta de los servicios**

Azure Stack Hub continuará agregando compatibilidad con los servicios de Azure. Para información sobre el mapa de ruta previsto, consulte las notas del producto [Azure Stack Hub: una extensión de Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409). También puede supervisar las [publicaciones del blog de Azure Stack Hub](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) para conocer los nuevos anuncios.

## <a name="what-account-should-i-use"></a>¿Qué cuenta debo usar?

Al administrar Azure Stack Hub, es preciso tener en cuenta varios aspectos con respecto a las cuentas. especialmente en las implementaciones que utilizan Servicios de federación de Active Directory (AD FS) de Windows Server como proveedor de identidades en lugar de Azure Active Directory (Azure AD). Las siguientes consideraciones sobre las cuentas se aplican a los sistemas integrados de Azure Stack Hub y las implementaciones de ASDK:

|Cuenta|Azure AD|AD FS|
|-----|-----|-----|
|Administrador local (.\Administrator)|Administrador del host de ASDK.|Administrador del host de ASDK.|
|AzureStack\AzureStackAdmin|Administrador del host de ASDK.<br><br>Se puede usar para iniciar sesión en el portal del administrador de Azure Stack Hub.<br><br>Acceso para ver y administrar anillos de Service Fabric.|Administrador del host de ASDK.<br><br>Sin acceso al portal del administrador de Azure Stack Hub.<br><br>Acceso para ver y administrar anillos de Service Fabric.<br><br>Ya no es propietario de la suscripción de proveedor predeterminada (DPS).|
|AzureStack\CloudAdmin|Puede acceder a comandos permitidos, y ejecutarlos, en el punto de conexión con privilegios.|Puede acceder a comandos permitidos, y ejecutarlos, en el punto de conexión con privilegios.<br><br>No puede iniciar sesión en el host de ASDK.<br><br>Propietario de la suscripción de proveedor predeterminada (DPS).|
|Administrador global de Azure AD|Se usa durante la instalación.<br><br>Propietario de la suscripción de proveedor predeterminada (DPS).|No aplicable.|
|

## <a name="what-tools-do-i-use-to-manage"></a>¿Qué herramientas debo usar para la administración?
 
Puede usar el [portal del administrador](azure-stack-manage-portals.md) o PowerShell para administrar Azure Stack Hub. La manera más fácil de aprender los conceptos básicos es a través del portal. Si quiere usar PowerShell, existen algunos pasos preparatorios. Antes de comenzar, es posible que desee familiarizarse con el uso de PowerShell en Azure Stack Hub. Para más información, consulte [Introducción a PowerShell en Azure Stack Hub](../user/azure-stack-powershell-overview.md).

Azure Stack Hub usa Azure Resource Manager como mecanismo subyacente de implementación, administración y organización. Si va a administrar Azure Stack Hub y ayudar en el soporte técnico a los usuarios, puede obtener información acerca de Resource Manager. Consulte las notas del producto [Getting Started with Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) (Introducción a Azure Resource Manager).

## <a name="your-typical-responsibilities"></a>Sus responsabilidades típicas

Los usuarios quieren usar los servicios. Desde la perspectiva de estos, su rol principal es poner estos servicios a su disposición. Decida qué servicios se ofrecen y póngalos a disposición de los usuarios mediante la creación de planes, ofertas y cuotas. Para más información, consulte [Introducción a la oferta de servicios de Azure Stack Hub](service-plan-offer-subscription-overview.md). 

También necesitará agregar elementos a [Marketplace de Azure Stack Hub](azure-stack-marketplace.md). La forma más fácil de hacerlo es [descargar elementos de Marketplace de Azure a Azure Stack Hub](azure-stack-download-azure-marketplace-item.md).

> [!NOTE]
> Si desea probar sus planes, ofertas y servicios, puede utilizar el [portal de usuario](azure-stack-manage-portals.md), no el portal de administración.

Además de ofrecer servicios, debe realizar las tareas habituales de un operador para mantener Azure Stack Hub en funcionamiento. Estas tareas incluyen lo siguiente:

- Agregar cuentas de usuario (para implementaciones de [Azure AD](azure-stack-add-new-user-aad.md) o de [AD FS](azure-stack-add-users-adfs.md)).
- [Asignar roles de control de acceso basado en rol (RBAC)](azure-stack-manage-permissions.md) (esta tarea no se limita solo a los administradores).
- [Supervisar el mantenimiento de la infraestructura](azure-stack-monitor-health.md).
- Administrar los recursos de [red](azure-stack-viewing-public-ip-address-consumption.md) y [almacenamiento](azure-stack-manage-storage-accounts.md).
- Reemplazar el hardware defectuoso. Por ejemplo, [reemplazar un dispositivo con errores](azure-stack-replace-disk.md).

## <a name="what-to-tell-your-users"></a>Qué decirles a los usuarios

Deberá informar a los usuarios cómo trabajar con los servicios en Azure Stack Hub, conectarse con el entorno y suscribirse a las ofertas. Además de toda la documentación personalizada que puede proporcionar a los usuarios, puede enviar a los usuarios a la [Documentación del usuario de Azure Stack Hub](../user/index.yml).

**Información sobre cómo trabajar con los servicios de Azure Stack Hub**

Hay información que los usuarios deben comprender antes de usar los servicios y crear aplicaciones en Azure Stack Hub. Por ejemplo, existen requisitos específicos para las versiones de PowerShell y API. Además, existen algunas diferencias de características entre un servicio en Azure y el servicio equivalente en Azure Stack Hub. Asegúrese de que los usuarios consulten los siguientes artículos:

- [Consideraciones clave: uso de los servicios o creación de aplicaciones para Azure Stack Hub](../user/azure-stack-considerations.md)
- [Consideraciones sobre máquinas virtuales en Azure Stack Hub](../user/azure-stack-vm-considerations.md)
- [Storage: differences and considerations](../user/azure-stack-acs-differences.md) (Storage: diferencias y consideraciones)

La información de estos artículos resume las diferencias entre un servicio en Azure y Azure Stack Hub. Complementa la información disponible para un servicio de Azure en la documentación global de Azure.

**Conexión a Azure Stack Hub como usuario**

En un entorno de ASDK, si un usuario no usa Escritorio remoto para conectarse al host de ASDK, puede configurar una conexión de red privada virtual (VPN) para conectarse a Azure Stack Hub. Consulte [Conexión a Azure Stack Hub](../asdk/asdk-connect.md).

Los usuarios querrán saber cómo [acceder al portal de usuarios](../user/azure-stack-use-portal.md) o cómo conectarse a través de PowerShell. En un entorno de sistemas integrados, la dirección del portal de usuarios varía en función de la implementación. Tendrá que proporcionar a los usuarios la dirección URL correcta.

Si usa PowerShell, puede que los usuarios deban registrar proveedores de recursos para poder usar los servicios. Un proveedor de recursos administra un servicio. Por ejemplo, el proveedor de recursos de red administra los recursos, como redes virtuales, interfaces de red y equilibradores de carga. Debe [instalar](azure-stack-powershell-install.md) PowerShell, [descargar](azure-stack-powershell-download.md) módulos adicionales y [configurar](../user/azure-stack-powershell-configure-user.md) PowerShell (que incluye el registro de proveedores de recursos).

**Suscripción a una oferta**

Para que un usuario pueda usar los servicios, debe [suscribirse a una oferta](azure-stack-subscribe-plan-provision-vm.md) que haya creado un operador.

## <a name="where-to-get-support"></a>Dónde obtener soporte técnico

> [!Note]  
> Para obtener información de soporte técnico de las versiones anteriores de Azure Stack Hub (antes de la 1905), consulte [Ayuda y soporte técnico para las versiones anteriores de Azure Stack Hub (antes de la 1905)](azure-stack-servicing-policy.md).

### <a name="integrated-systems"></a>Sistemas integrados

En el caso de un sistema integrado, hay un proceso de escalación y resolución coordinado entre Microsoft y nuestros asociados de hardware de fabricante de equipos originales (OEM).

Si hay algún problema en los servicios en la nube, el soporte técnico se ofrece a través de Soporte técnico de Microsoft. Para abrir una solicitud de soporte técnico, seleccione el icono de Ayuda y soporte técnico (signo de interrogación) de la esquina superior derecha del portal de administración. Después, seleccione**Ayuda y soporte técnico** y, a continuación, **Nueva solicitud de soporte técnico** en la sección **Soporte técnico**.

Si surge algún problema de implementación, aplicación de revisiones y actualizaciones, hardware (incluidas las unidades reemplazables en campo) o cualquier software específico de una marca de hardware, como el software que se ejecuta en el host del ciclo de vida del hardware, en primer lugar póngase en contacto con el proveedor de hardware OEM.

Para todo lo demás, póngase en contacto con Soporte técnico de Microsoft.

### <a name="azure-stack-development-kit-asdk"></a>Kit de desarrollo de Azure Stack (ASDK)

En el caso de ASDK, puede dirigir las preguntas relacionadas con el soporte técnico a los [foros de Microsoft](https://social.msdn.microsoft.com/Forums/azure/home?forum=azurestack). Para acceder a los foros, seleccione el icono de ayuda y soporte técnico (signo de interrogación) en la esquina superior derecha del portal de administración, luego seleccione **Ayuda y soporte técnico** y, finalmente, **Foros de MSDN** en la sección **Soporte técnico**. Estos foros se supervisan periódicamente. Dado que ASDK es un entorno de evaluación, el servicio de asistencia al cliente (CSS) de Microsoft no ofrece soporte técnico oficial.

## <a name="next-steps"></a>Pasos siguientes

[Administración de regiones en Azure Stack Hub](azure-stack-region-management.md)
