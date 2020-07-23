---
title: Notas de la versión de la actualización 7 de App Service en Azure Stack Hub
description: Notas de la versión de la actualización 7 de App Service en Azure Stack Hub, incluidas las nuevas características, correcciones y problemas conocidos.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 10/11/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 08/20/2019
ms.openlocfilehash: 449c1dac3e983aacefe4d69581ee854239d435ae
ms.sourcegitcommit: e9a1dfa871e525f1d6d2b355b4bbc9bae11720d2
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 07/20/2020
ms.locfileid: "86489613"
---
# <a name="app-service-on-azure-stack-hub-update-7-release-notes"></a>Notas de la versión de la actualización 7 de App Service en Azure Stack Hub

En estas notas de la versión se describen nuevas características, correcciones y problemas conocidos de la actualización 7 de Azure App Service en Azure Stack Hub. Los problemas conocidos se dividen en dos secciones: aquellos que están relacionados con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Aplique la actualización 1910 al sistema integrado de Azure Stack o implemente el Kit de desarrollo de Azure Stack (ASDK) más reciente antes de implementar Azure App Service 1.7.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 7 de App Service en Azure Stack Hub es **84.0.2.10**.

## <a name="prerequisites"></a>Prerrequisitos

Consulte [Requisitos previos para implementar App Service en Azure Stack Hub](azure-stack-app-service-before-you-get-started.md) antes de comenzar la implementación.

Antes de comenzar la actualización de Azure App Service en Azure Stack Hub a la versión 1.7:

- Asegúrese de que todos los roles están listos en la administración de Azure App Service del portal de administración de Azure Stack Hub.

- Realice una copia de seguridad de los secretos de App Service mediante la administración de App Service en el portal de administración de Azure Stack Hub.

- Realice una copia de seguridad de App Service y de las bases de datos maestras:
  - AppService_Hosting;
  - AppService_Metering;
  - maestro

- Realice una copia de seguridad del recurso compartido de archivos del contenido de la aplicación inquilina.

  > [!Important]
  > Los operadores de la nube son responsables del mantenimiento y funcionamiento del servidor de archivos y el servidor de SQL Server.  El proveedor de recursos no administra estos recursos.  El operador de la nube es responsable de realizar copias de seguridad de las bases de datos y los recursos compartidos de archivos del contenido del inquilino de App Service.

- Distribuya la **extensión de script personalizado** de la versión **1.9.3** desde Marketplace de Azure Stack Hub.

## <a name="new-features-and-fixes"></a>Nuevas características y correcciones

La actualización 7 de Azure App Service en Azure Stack Hub incluye las siguientes correcciones y mejoras:

- Resolución de la vulnerabilidad [CVE-2019-1372 ](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1372) de ejecución remota de código.

- Actualizaciones de las **herramientas de Kudu, los portales de Functions, administración e inquilino de App Service**. Es coherente con la versión del SDK del portal de Azure Stack Hub.

- Actualiza el **entorno de ejecución de Azure Functions** a **v1.0.12582**.

- Actualizaciones en el servicio principal para mejorar la confiabilidad y los mensajes de error, lo cual permite un diagnóstico de problemas comunes más sencillo.

- **Actualizaciones de las herramientas y plataformas de aplicaciones siguientes**:

  - ASP.NET Core 2.2.46
  - Zul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 5.6.40
  - PHP 7.3.6
  - Se actualizó Kudu a la versión 82.10503.3890.

- **Actualizaciones al sistema operativo subyacente de todos los roles**:
  - [Actualización acumulativa 2019-08 para Windows Server 2016 en sistemas basados en x64 (KB4512495)](https://support.microsoft.com/help/4512495)

- **Restricciones de acceso habilitadas ahora en el portal de usuarios**:
  - Los usuarios pueden ya configurar restricciones de acceso para sus aplicaciones web, API o de funciones según la documentación publicada: [Restricciones de acceso de Azure App Service](/azure/app-service/app-service-ip-restrictions).
  
  > [!NOTE]
  > Azure App Service en Azure Stack Hub no admite puntos de conexión de servicio.

- **Funcionalidad de opciones de implementación (clásico) restaurada**:
  - Los usuarios pueden volver a usar las opciones de implementación (clásico) para configurar la implementación de sus aplicaciones desde GitHub, Bitbucket, Dropbox, OneDrive, repositorios locales y externos, y para establecer las credenciales de implementación para sus aplicaciones.

- **Supervisión de Azure Functions** configurada correctamente.

- **Comportamiento de Windows Update**: A partir de los comentarios de los clientes, hemos cambiado el modo en que Windows Update se configura en los roles de App Service a partir de la actualización 7:
  - Tres modos:
    - **Deshabilitado**: el servicio Windows Update está deshabilitado; Windows se actualizará con los KB que se incluyen con Azure App Service en las versiones de Azure Stack Hub.
    - **Automático**: el servicio de Windows Update está habilitado y determina cómo y cuándo se realiza la actualización.
    - **Administrado**: el servicio de Windows Update está deshabilitado, Azure App Service realizará un ciclo de Windows Update durante el método OnStart del rol individual.

  **Nuevas** implementaciones: el servicio de Windows Update está deshabilitado de forma predeterminada.

  Implementaciones **existentes**: si ha modificado la configuración en el controlador, el valor cambiará de **False** a **Deshabilitado** y el valor anterior **True** se convertirá en **Automático**.

## <a name="post-deployment-steps"></a>Pasos posteriores a la implementación

> [!IMPORTANT]
> Si ha proporcionado al proveedor de recursos de App Service una instancia de SQL Always On, DEBE [agregar las bases de datos appservice_hosting y appservice_metering a un grupo de disponibilidad](/sql/database-engine/availability-groups/windows/availability-group-add-a-database) y sincronizarlas para evitar la pérdida de servicio en caso de producirse una conmutación por error de la base de datos.

## <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

- Los trabajos no pueden llegar al servidor de archivos cuando App Service está implementada en una red virtual existente y el servidor de archivos solo está disponible en la red privada, tal y como se describe en la documentación de implementación de Azure App Service en Azure Stack Hub.

Si decide realizar una implementación en una red virtual existente y en una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. De ese modo, permite que exista tráfico SMB entre la subred del rol de trabajo y el servidor de archivos. Vaya a WorkersNsg en el portal del administrador y agregue una regla de seguridad de salida con las siguientes propiedades:

* Origen: Any
* Intervalo de puertos de origen: *
* Destino: Direcciones IP
* Intervalo de direcciones IP de destino: Intervalo de direcciones IP del servidor de archivos
* Intervalo de puertos de destino: 445
* Protocolo: TCP
* Acción: Allow
* Prioridad: 700
* Nombre: Outbound_Allow_SMB445

## <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Problemas conocidos para los administradores de la nube que usan Azure App Service en Azure Stack Hub

Consulte la documentación en las [notas de la versión 1907 de Azure Stack Hub](./release-notes.md?view=azs-2002).

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre Azure App Service, consulte [Introducción a Azure App Service y Azure Functions en Azure Stack Hub](azure-stack-app-service-overview.md).
- Para más información sobre cómo prepararse para implementar App Service en Azure Stack Hub, consulte [Requisitos previos para implementar App Service en Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
