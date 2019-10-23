---
title: Notas de la versión de la actualización 7 de App Service en Azure Stack | Microsoft Docs
description: Conozca el contenido de la actualización 7 para App Service en Azure Stack, los problemas conocidos y la ubicación dónde debe descargarse.
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2019
ms.author: anwestg
ms.reviewer: ''
ms.openlocfilehash: 77fb9772f027d25b0d8c0d8355e3a868c2142d6c
ms.sourcegitcommit: d159652f50de7875eb4be34c14866a601a045547
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/11/2019
ms.locfileid: "72282604"
---
# <a name="app-service-on-azure-stack-update-7-release-notes"></a>Notas de la versión de App Service en la actualización 7 de Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Estas notas de la versión describen las mejoras y correcciones de la actualización 7 de Azure App Service en Azure Stack y los problemas conocidos. Los problemas conocidos se dividen en aquellos que están relacionados directamente con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Aplique la actualización 1907 al sistema integrado de Azure Stack o implemente el kit de desarrollo de Azure Stack más reciente antes de implementar Azure App Service 1.7.


## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 7 de App Service en Azure Stack es **84.0.2.10**.

### <a name="prerequisites"></a>Requisitos previos

Remítase a la [documentación de introducción](azure-stack-app-service-before-you-get-started.md) antes de comenzar la implementación.

Antes de comenzar la actualización de Azure App Service en Azure Stack a la versión 1.7:

- Asegúrese de que todos los roles están listos en la administración de Azure App Service desde el portal de administración de Azure Stack.

- Realice una copia de seguridad de App Service y de las bases de datos maestras:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Realice una copia de seguridad del recurso compartido de archivos del contenido de la aplicación inquilina.

- Distribuya la **extensión de script personalizado** de la versión **1.9.3** desde Marketplace.

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

La actualización 7 de Azure App Service en Azure Stack incluye las siguientes correcciones y mejoras:

- Resolución de la vulnerabilidad [CVE-2019-1372 ](https://portal.msrc.microsoft.com/en-US/security-guidance/advisory/CVE-2019-1372) de ejecución remota de código

- Actualizaciones de las **herramientas de Kudu, los portales de Functions, Admin e inquilino de App Service**. Es coherente con la versión del SDK de Azure Stack Portal.

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

- **Restricciones de acceso habilitadas ahora en el Portal de usuarios**:
  - A partir de esta versión, los usuarios pueden configurar restricciones de acceso para sus aplicaciones web/API/Functions según la documentación publicada: [Restricciones de acceso de Azure App Service](https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions). **NOTA**: Azure App Service en Azure Stack no admite puntos de conexión de servicio.

- **Funcionalidad de opciones de implementación (clásico) restaurada**:
  - Los usuarios pueden volver a usar las opciones de implementación (clásico) para configurar la implementación de sus aplicaciones desde GitHub, Bitbucket, Dropbox, OneDrive, repositorios locales y externos, y para establecer las credenciales de implementación para sus aplicaciones.

- **Supervisión de Azure Functions** configurada correctamente.

- **Comportamiento de Windows Update**: A partir de los comentarios de los clientes, hemos cambiado el modo en que Windows Update se configura en los roles de App Service a partir de la actualización 7:
  - Tres modos:
    - **Deshabilitado**: el servicio de Windows Update está deshabilitado, Windows se actualiza con los KB que se incluyen con Azure App Service en las versiones de Azure Stack;
    - **Automático**: el servicio de Windows Update está habilitado y determina cómo y cuándo se realiza la actualización;
    - **Administrado**: el servicio de Windows Update está deshabilitado, Azure App Service realizará un ciclo de Windows Update durante el método OnStart del rol individual.

  **Nuevas** implementaciones: el servicio de Windows Update está deshabilitado de forma predeterminada.

  Implementaciones **existentes**: si ha modificado la configuración en el controlador, ahora el valor cambiará de **False** a **Deshabilitado** y el valor anterior **True** se convertirá en **Automático**.

### <a name="post-deployment-steps"></a>Pasos posteriores a la implementación

> [!IMPORTANT]
> Si proporcionó el proveedor de recursos de App Service con una instancia de SQL Always On, DEBE [agregar las bases de datos appservice_hosting y appservice_metering a un grupo de disponibilidad](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database), así como sincronizar dichas bases de datos para evitar la pérdida de servicio en caso de producirse una conmutación por error de base de datos.

### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

- Los trabajos no pueden llegar al servidor de archivos cuando App Service está implementada en una red virtual existente y el servidor de archivos solo está disponible en la red privada, tal y como se describe en la documentación de implementación de Azure App Service en Azure Stack.

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conocidos para los administradores en la nube que trabajan con Azure App Service en Azure Stack

Consulte la documentación de las [notas de la versión de Azure Stack 1907](azure-stack-release-notes-1907.md).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de Azure App Service, consulte la [Información general de Azure App Service en Azure Stack](azure-stack-app-service-overview.md).
- Para obtener más información acerca de cómo prepararse para implementar App Service en Azure Stack, consulte [Antes de empezar a trabajar con App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md).
