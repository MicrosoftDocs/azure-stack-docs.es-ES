---
title: Aspectos básicos de la administración de Azure Stack Hub
titleSuffix: Azure Stack Hub
description: Aprenda los aspectos básicos de la administración de Azure Stack Hub.
author: justinha
ms.topic: article
ms.date: 04/03/2020
ms.author: justinha
ms.reviewer: thoroet
ms.lastreviewed: 04/03/2020
ms.openlocfilehash: 66fbcf3b94ae89f5f0946ee34582d69cea4ea97e
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "80979365"
---
# <a name="azure-stack-hub-administration-basics"></a>Aspectos básicos de la administración de Azure Stack Hub

Si no está familiarizado con la administración de Azure Stack Hub, hay varias cosas que debe saber. En este artículo se ofrece información general acerca de su rol como operador de Azure Stack Hub y lo que debe decir a los usuarios para ayudarles a ser productivos.

## <a name="understand-the-builds"></a>Descripción de las compilaciones

Si usa un sistema integrado de Azure Stack Hub, las versiones actualizadas de Azure Stack Hub se distribuyen mediante paquetes de actualizaciones. Para importar y aplicar estos paquetes, haga clic en el icono **Actualizaciones** del portal de administración.

## <a name="learn-about-available-services"></a>Información sobre los servicios disponibles

Tenga en cuenta los servicios que puede poner a disposición de los usuarios. Azure Stack Hub admite un subconjunto de servicios de Azure. La lista de servicios admitidos continuará evolucionando.

### <a name="foundational-services"></a>Servicios fundamentales

De forma predeterminada, Azure Stack Hub incluye los siguientes servicios fundamentales cuando se implementa:

- Proceso
- Storage
- Redes
- Key Vault

Con estos servicios fundamentales, puede ofrecer la infraestructura como servicio (IaaS) a los usuarios con una configuración mínima.

### <a name="additional-services"></a>Servicios adicionales

Se admiten los siguientes servicios de plataforma como servicio (PaaS) adicionales:

- App Service
- Azure Functions
- Bases de datos SQL y MySQL
- Kubernetes
- IoT Hub
- Centro de eventos

Estos servicios requieren una configuración adicional para que pueda ponerlos a disposición de los usuarios. Para más información, consulte **Tutoriales** y **Guías de procedimientos** > **Servicios de la oferta** de la [documentación del operador de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/).

### <a name="service-roadmap"></a>Mapa de ruta de los servicios

Azure Stack Hub continuará agregando compatibilidad con los servicios de Azure. Para información sobre el mapa de ruta previsto, consulte las notas del producto [Azure Stack Hub: una extensión de Azure](https://go.microsoft.com/fwlink/?LinkId=842846&clcid=0x409). También puede supervisar las [publicaciones del blog de Azure Stack Hub](https://azure.microsoft.com/blog/tag/azure-stack-technical-preview) para conocer los nuevos anuncios.

## <a name="what-account-should-i-use"></a>¿Qué cuenta debo usar?

Al administrar Azure Stack Hub, es preciso tener en cuenta varios aspectos con respecto a las cuentas. especialmente en las implementaciones que usan los Servicios de federación de Active Directory (AD FS) de Windows Server como proveedor de identidades en lugar de Azure Active Directory (Azure AD).

| **Cuenta** | **Azure** | **AD FS** |
|---|---|---|
| Administrador local (.\Administrator) |   |
| Administrador global de Azure AD | Se usa durante la instalación. <br> Propietario del proveedor predeterminado | No aplicable. |
| Cuenta para el almacenamiento extendido|   |   |
||

## <a name="what-tools-do-i-use-to-manage"></a>¿Qué herramientas debo usar para la administración?

Puede usar el [portal del administrador](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-portals) o PowerShell para administrar Azure Stack Hub. La manera más fácil de aprender los conceptos básicos es a través del portal. Si quiere usar PowerShell, existen algunos pasos preparatorios. Antes de comenzar, es posible que desee familiarizarse con el uso de PowerShell en Azure Stack Hub. Para más información, consulte [Introducción a PowerShell en Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-powershell-overview).

Azure Stack Hub usa Azure Resource Manager como mecanismo subyacente de implementación, administración y organización. Si va a administrar Azure Stack Hub y ayudar en el soporte técnico a los usuarios, debe obtener información sobre Resource Manager. Consulte las notas del producto [Getting Started with Azure Resource Manager](https://download.microsoft.com/download/E/A/4/EA4017B5-F2ED-449A-897E-BD92E42479CE/Getting_Started_With_Azure_Resource_Manager_white_paper_EN_US.pdf) (Introducción a Azure Resource Manager).

## <a name="your-typical-responsibilities"></a>Sus responsabilidades típicas

Los usuarios quieren usar los servicios. Desde la perspectiva de estos, su rol principal es poner estos servicios a su disposición. Decida qué servicios se ofrecen y póngalos a disposición de los usuarios mediante la creación de planes, ofertas y cuotas. Para más información, consulte [Introducción a la oferta de servicios de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/service-plan-offer-subscription-overview).

También necesitará agregar elementos a [Marketplace de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-marketplace). La forma más fácil de hacerlo es [descargar elementos de Marketplace de Azure a Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-download-azure-marketplace-item).

Si desea probar sus planes, ofertas y servicios, puede utilizar el [portal de usuario](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-portals), no el portal de administración.

Además de ofrecer servicios, debe realizar las tareas habituales de un operador para mantener Azure Stack Hub en funcionamiento. Estas tareas incluyen lo siguiente:

- Adición de cuentas de usuario para la implementación de [Azure AD](https://docs.microsoft.com/azure-stack/operator/azure-stack-add-new-user-aad).
- [Establecimiento de permisos de acceso mediante el control de acceso basado en rol](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-permissions). (Esta tarea no está restringida a los administradores).
- [Supervisar el mantenimiento de la infraestructura](https://docs.microsoft.com/azure-stack/operator/azure-stack-monitor-health).
- Administrar los recursos de [red](https://docs.microsoft.com/azure-stack/operator/azure-stack-viewing-public-ip-address-consumption) y [almacenamiento](https://docs.microsoft.com/azure-stack/operator/azure-stack-manage-storage-accounts).
- [Inicio y detención de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-start-and-stop&branch=release-tzl).
- [Funcionamiento del almacenamiento extendido](https://docs.microsoft.com/azure-stack/tdc/extended-storage-operator-guide&branch=release-tzl).
- [Administración de IoT Hub](https://docs.microsoft.com/azure-stack/operator/iot-hub-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&.bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- [Administración de Event Hubs](https://docs.microsoft.com/azure-stack/operator/event-hubs-rp-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- [Administración de App Service](https://docs.microsoft.com/azure-stack/operator/azure-stack-app-service-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).
- Reemplazar el hardware defectuoso. Esta es la lista de [elementos reemplazables](https://docs.microsoft.com/azure-stack/tdc/cru-replaceable-parts&branch=release-tzl).
- [Obtención de soporte técnico](https://docs.microsoft.com/azure-stack/operator/azure-stack-help-and-support-overview?toc=%2Fazure-stack%2Ftdc%2Ftoc.json&bc=%2Fazure-stack%2Fbreadcrumb%2Ftoc.json&view=azs-2002&branch=release-tzl).

## <a name="operator-tasks"></a>Tareas del operador

Esta es la lista de tareas diarias, semanales y mensuales de un operador:

# <a name="daily"></a>[Diariamente](#tab/daily)

1. Comprobación de alertas.
2. Comprobación del estado de la copia de seguridad.
3. Actualización de la firma de Defender (sistemas desconectados).
4. Comprobación del estado y los eventos del sistema Isilon en OneFS.
5. Comprobación de la capacidad de Isilon.

# <a name="weekly"></a>[Semanal](#tab/weekly)

1. Comprobación de la capacidad.
2. Ejecución de `isi status –verbose` en la conexión de Avocent.

# <a name="monthly"></a>[Mensualmente](#tab/monthly)

1. Aplicación de los paquetes de actualización mensuales (Microsoft y OEM).
2. Validación de la copia de seguridad mediante ASDK.
3. Administración del Marketplace de Azure Stack Hub (mantener actualizado).
4. Actualización del firmware del conmutador y de Avocent.
5. Reclamación de la capacidad de almacenamiento.

# <a name="ondemand"></a>[OnDemand](#tab/ondemand)

1. Rotación de secretos.
2. Creación y actualización de ofertas, planes y cuotas.
3. Aplicación de paquetes de revisión.
4. Aplicación de paquetes de revisión.
5. Expansión de la capacidad (nodos e IPSpace).
6. Ejecución de `isi status –verbose` en la conexión de Avocent.
7. Restauración de cuentas de almacenamiento.
8. Detención del sistema.
9. Recopilación de registros de diagnóstico.

---

## <a name="what-to-tell-your-users"></a>Qué decirles a los usuarios

Deberá informar a los usuarios cómo trabajar con los servicios en Azure Stack Hub, conectarse con el entorno y suscribirse a las ofertas. Además de toda la documentación personalizada que puede proporcionar a los usuarios, puede enviar a los usuarios a la [Documentación del usuario de Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/).

### <a name="understand-how-to-work-with-services-in-azure-stack-hub"></a>Información sobre cómo trabajar con los servicios de Azure Stack Hub

Hay información que los usuarios deben comprender antes de usar los servicios y crear aplicaciones en Azure Stack Hub. Por ejemplo, existen requisitos específicos para las versiones de PowerShell y API. Además, existen algunas diferencias de características entre un servicio en Azure y el servicio equivalente en Azure Stack Hub. Asegúrese de que los usuarios consulten los siguientes artículos:

- [Diferencias entre Azure Stack Hub y Azure al usar servicios y compilar aplicaciones](https://docs.microsoft.com/azure-stack/user/azure-stack-considerations)
- [Características de las máquinas virtuales de Azure Stack Hub](https://docs.microsoft.com/azure-stack/user/azure-stack-vm-considerations)
- [Azure Stack Hub Storage: diferencias y consideraciones](https://docs.microsoft.com/azure-stack/user/azure-stack-acs-differences)

La información de estos artículos resume las diferencias entre un servicio en Azure y Azure Stack Hub. Complementa la información disponible para un servicio de Azure en la documentación global de Azure.

### <a name="connect-to-azure-stack-hub-as-a-user"></a>Conexión a Azure Stack Hub como usuario

Los usuarios querrán saber cómo [acceder al portal de usuarios](https://docs.microsoft.com/azure-stack/user/azure-stack-use-portal) o cómo conectarse a través de PowerShell. En un entorno de sistemas integrados, la dirección del portal de usuarios varía en función de la implementación. Tendrá que proporcionar a los usuarios la dirección URL correcta.

Si usa PowerShell, puede que los usuarios deban registrar proveedores de recursos para poder usar los servicios. Un proveedor de recursos administra un servicio. Por ejemplo, el proveedor de recursos de red administra los recursos, como redes virtuales, interfaces de red y equilibradores de carga. Debe [instalar](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-install) PowerShell, [descargar](https://docs.microsoft.com/azure-stack/operator/azure-stack-powershell-download) módulos adicionales y [configurar](https://docs.microsoft.com/azure-stack/user/azure-stack-powershell-configure-user) PowerShell (que incluye el registro de proveedores de recursos).

### <a name="subscribe-to-an-offer"></a>Suscripción a una oferta

Para que un usuario pueda usar los servicios, debe [suscribirse a una oferta](https://docs.microsoft.com/azure-stack/operator/azure-stack-subscribe-plan-provision-vm) que haya creado un operador.

## <a name="where-to-get-support"></a>Dónde obtener soporte técnico

Para obtener información de soporte técnico de las versiones anteriores de Azure Stack Hub (antes de la 1905), consulte [Directiva de servicio de Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-servicing-policy).

En el caso de un sistema integrado, hay un proceso de escalación y resolución coordinado entre Microsoft y nuestros asociados de hardware de fabricante de equipos originales (OEM).

Si hay algún problema en los servicios en la nube, el soporte técnico se ofrece a través de los servicios de soporte técnico al cliente de Microsoft (CSS). Para abrir una solicitud de soporte técnico, seleccione el icono de Ayuda y soporte técnico (signo de interrogación) de la esquina superior derecha del portal de administración. Después, seleccione**Ayuda y soporte técnico** y, a continuación, **Nueva solicitud de soporte técnico** en la sección **Soporte técnico**.

Si surge algún problema de implementación, aplicación de revisiones y actualizaciones, hardware (incluidas las unidades reemplazables en campo) o cualquier software específico de una marca de hardware, como el software que se ejecuta en el host del ciclo de vida del hardware, en primer lugar póngase en contacto con el proveedor de hardware OEM.

Para todo lo demás, póngase en contacto con el servicio de asistencia al cliente (CSS) de Microsoft.

## <a name="next-steps"></a>Pasos siguientes

- [Administración de regiones en Azure Stack Hub](https://docs.microsoft.com/azure-stack/operator/azure-stack-region-management)
