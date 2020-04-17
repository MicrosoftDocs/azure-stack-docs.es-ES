---
title: Notas de la versión de la actualización 6 de App Service en Azure Stack Hub
description: Conozca el contenido de la actualización 6 para App Service en Azure Stack Hub, los problemas conocidos y la ubicación donde debe descargarse.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 06/24/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: d41455823c6905a947a703412664fc52ff45e1a8
ms.sourcegitcommit: a630894e5a38666c24e7be350f4691ffce81ab81
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/16/2020
ms.locfileid: "77701129"
---
# <a name="app-service-on-azure-stack-hub-update-6-release-notes"></a>Notas de la versión de la actualización 6 de App Service en Azure Stack Hub

Estas notas de la versión describen las mejoras y correcciones de la actualización 6 de Azure App Service en Azure Stack Hub y los problemas conocidos. Los problemas conocidos se dividen en aquellos que están relacionados directamente con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Aplique la actualización 1904 al sistema integrado de Azure Stack Hub o implemente el kit de desarrollo de Azure Stack más reciente antes de implementar Azure App Service 1.6.


## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 6 de App Service en Azure Stack Hub es **82.0.1.50**.

### <a name="prerequisites"></a>Prerrequisitos

Remítase a la [documentación de introducción](azure-stack-app-service-before-you-get-started.md) antes de comenzar la implementación.

Antes de comenzar la actualización de Azure App Service en Azure Stack Hub a la versión 1.6:

- Asegúrese de que todos los roles estén en estado preparado en la administración de Azure App Service del portal de administración de Azure Stack Hub.

- Realice una copia de seguridad de App Service y de las bases de datos maestras:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Realice una copia de seguridad del recurso compartido de archivos del contenido de la aplicación inquilina.

- Distribuya la **extensión de script personalizado** de la versión **1.9.1** desde Marketplace.

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

La actualización 6 de Azure App Service en Azure Stack Hub incluye las siguientes correcciones y mejoras:

- Actualizaciones de las **herramientas de Kudu, los portales de Functions, Admin e inquilino de App Service**. Es coherente con la versión del SDK del portal de Azure Stack Hub.

- Actualiza el **entorno de ejecución de Azure Functions** a **v1.0.12299**.

- Actualizaciones en el servicio principal para mejorar la confiabilidad y los mensajes de error, lo cual permite un diagnóstico de problemas comunes más sencillo.

- **Actualizaciones de las herramientas y plataformas de aplicaciones siguientes**:
  - ASP.NET Core 2.2.4
  - NodeJS 10.15.2
  - Zulu OpenJDK 8.36.0.1
  - Tomcat 7.0.81
  - Tomcat 8.5.37
  - Tomcat 9.0.14
  - PHP 5.6.39
  - PHP 7.0.33
  - PHP 7.1.25
  - PHP 7.2.13
  - Se actualizó Kudu a la versión 81.10329.3844.

- **Actualizaciones al sistema operativo subyacente de todos los roles**:
  - [Actualización acumulativa 2019-04 para Windows Server 2016 en sistemas basados en x64 (KB4493473)](https://support.microsoft.com/help/4493473/windows-10-update-kb4493473)

### <a name="post-deployment-steps"></a>Pasos posteriores a la implementación

> [!IMPORTANT]
> Si proporcionó el proveedor de recursos de App Service con una instancia de SQL Always On, DEBE [agregar las bases de datos appservice_hosting y appservice_metering a un grupo de disponibilidad](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database), así como sincronizar dichas bases de datos para evitar la pérdida de servicio en caso de producirse una conmutación por error de base de datos.

### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

- Los trabajos no pueden llegar al servidor de archivos cuando App Service está implementada en una red virtual existente y el servidor de archivos solo está disponible en la red privada, tal y como se describe en la documentación de implementación de Azure App Service en Azure Stack Hub.

Si decide realizar una implementación en una red virtual existente y en una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. De ese modo, permite que exista tráfico SMB entre la subred del rol de trabajo y el servidor de archivos. Vaya a WorkersNsg en el Portal de administración y agregue una regla de seguridad de salida con las siguientes propiedades:
 * Origen: Any
 * Intervalo de puertos de origen: *
 * Destino: Direcciones IP
 * Intervalo de direcciones IP de destino: Intervalo de direcciones IP del servidor de archivos
 * Intervalo de puertos de destino: 445
 * Protocolo: TCP
 * Acción: Allow
 * Prioridad: 700
 * Nombre: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Problemas conocidos para los administradores de la nube que usan Azure App Service en Azure Stack Hub

Consulte la documentación en las [notas de la versión 1908 de Azure Stack Hub](/azure-stack/operator/release-notes?view=azs-1908).

### <a name="known-issues-for-tenants-deploying-applications-on-azure-app-service-on-azure-stack-hub"></a>Problemas conocidos de los inquilinos que implementan aplicaciones en Azure App Service en Azure Stack Hub

- El Centro de implementación está atenuado.

Los inquilinos todavía no pueden hacer uso del Centro de implementación, que es una característica que se publicó en la nube pública a finales de 2018.  Los inquilinos todavía pueden utilizar los métodos de implementación estándar (FTP, Web Deploy, Git, etc.) mediante el portal, la CLI y PowerShell.

- Las opciones de implementación (Clásica) y las opciones del portal de credenciales de implementación y de experiencia de usuario no están disponibles.

Para alcanzar las opciones de implementación y las credenciales de implementación de las experiencias de usuario en la implementación de Azure Stack Hub, los usuarios deben acceder al portal mediante este formato URL: https://portal.&lt ;*región*&gt;.&lt;*FQDN*&gt; /?websitesExtension_oldvsts=true que, para el ASDK, sería [https://portal.local.azurestack.external/?websitesExtension_oldvsts=true](https://portal.local.azurestack.external/?websitesExtension_oldvsts=true) y, después, desplazarse a sus aplicaciones normalmente.

- La supervisión de Azure Functions sigue mostrando "Cargando" en el portal

Al intentar supervisar instancias de Functions individuales, en el portal de usuarios no verá ningún registro de invocaciones, recuento de operaciones correctas ni recuento de errores.  Para volver a habilitar esta funcionalidad, diríjase a su instancia de **Function App**, vaya a **Características de la plataforma** y luego a **Configuración de la aplicación**.  Agregue una nueva configuración de la aplicación (por ejemplo, **AzureWebJobsDashboard**) y establezca el mismo valor que se estableció en AzureWebJobsStorage.  A continuación, vaya a la vista Monitor en la función y verá la información de supervisión.

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre Azure App Service, consulte [Introducción a Azure App Service en Azure Stack Hub](azure-stack-app-service-overview.md).
- Para más información sobre cómo prepararse para implementar App Service en Azure Stack Hub, consulte [Requisitos previos para implementar App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md).
