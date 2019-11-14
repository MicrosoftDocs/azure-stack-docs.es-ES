---
title: Notas de la versión de la actualización 5 de App Service en Azure Stack | Microsoft Docs
description: Obtenga información sobre las mejoras, las correcciones y los problemas conocidos de la actualización 5 para App Service en Azure Stack.
services: azure-stack
documentationcenter: ''
author: bryanla
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: f44bfcaf91e06979d1a9eb745bf681c0d9f69371
ms.sourcegitcommit: cb9548e5a2ca27d9c44f349eeb08d94c9c6334da
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/11/2019
ms.locfileid: "73916396"
---
# <a name="app-service-on-azure-stack-update-5-release-notes"></a>Notas de la versión de la actualización 5 de App Service en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Estas notas de la versión describen las mejoras, las correcciones y los problemas conocidos de la actualización 5 de Azure App Service en Azure Stack. Los problemas conocidos se dividen tres secciones: los problemas directamente relacionados con la implementación, los problemas relacionados con el proceso de actualización y los problemas relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Aplique la actualización 1901 al sistema integrado de Azure Stack o implemente el Kit de desarrollo de Azure Stack más reciente antes de implementar Azure App Service 1.5.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 5 de App Service en Azure Stack es **80.0.2.15**.

### <a name="prerequisites"></a>Requisitos previos

Consulte [Requisitos previos para implementar App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md) antes de comenzar la implementación.

Antes de comenzar la actualización de Azure App Service en Azure Stack a la versión 1.5:

- Asegúrese de que todos los roles están listos en la administración de Azure App Service desde el portal de administración de Azure Stack.

- Realice una copia de seguridad de App Service y de las bases de datos maestras:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Realice una copia de seguridad del recurso compartido de archivos del contenido de la aplicación inquilina.

- Distribuya la **extensión de script personalizado** de la versión **1.9.1** desde Marketplace.

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

La actualización 5 de Azure App Service en Azure Stack incluye las siguientes correcciones y mejoras:

- Actualizaciones de las **herramientas de Kudu, los portales de Functions, Admin e inquilino de App Service**. Es coherente con la versión del SDK de Azure Stack Portal.

- Actualiza el **entorno de ejecución de Azure Functions** a **v1.0.12205**.

- Actualizaciones de las **herramientas de Kudu** para resolver problemas con la funcionalidad y la aplicación de estilos para los clientes que utilizan Azure Stack **sin conexión**. 

- Actualizaciones en el servicio principal para mejorar la confiabilidad y los mensajes de error, lo cual permite un diagnóstico de problemas comunes más sencillo.

- **Actualizaciones de las herramientas y plataformas de aplicaciones siguientes**:
  - Se ha agregado ASP.Net Core 2.1.6 y 2.2.0
  - Se agregó NodeJS 10.14.1.
  - Se agregó NPM 6.4.1.
  - Se actualizó Kudu a la versión 79.20129.3767.
  
- **Actualizaciones al sistema operativo subyacente de todos los roles**:
  - [Actualización acumulativa 2019-02 para Windows Server 2016 en sistemas basados en x64 (KB4487006)](https://support.microsoft.com/help/4487006/windows-10-update-kb4487006)

### <a name="post-deployment-steps"></a>Pasos posteriores a la implementación

> [!IMPORTANT]  
> Si ha proporcionado el proveedor de recursos de App Service con una instancia de SQL Always On, *debe* [agregar las bases de datos appservice_hosting y appservice_metering a un grupo de disponibilidad](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database), así como sincronizar dichas bases de datos para evitar la pérdida de servicio en caso de producirse una conmutación por error de base de datos.

### <a name="post-update-steps"></a>Pasos posteriores a la actualización

Para los clientes que quieran migrar a una base de datos independiente para las implementaciones de Azure App Service en Azure Stack existentes, ejecute estos pasos una vez completada la actualización 1.5 de Azure App Service en Azure Stack:

> [!IMPORTANT]
> Este procedimiento de migración tarda aproximadamente entre 5 y 10 minutos. El procedimiento implica la eliminación de las sesiones de inicio de sesión de base de datos existentes. Planee el tiempo de inactividad para migrar y validar Azure App Service en Azure Stack después de la migración. Si ha completado estos pasos después de actualizar a Azure App Service en Azure Stack 1.3, no es necesario que los realice.

1. Agregue las [bases de datos de AppService (appservice_hosting y appservice_metering) al grupo de disponibilidad](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. Habilite la base de datos independiente.
    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Durante la conversión de una base de datos a parcialmente independiente, la conversión provocará un tiempo de inactividad, ya que todas las sesiones activas deben terminarse.

    ```sql
        /******** [appservice_metering] Migration Start********/
            USE [master];

            -- kill all active sessions
            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_metering')

            EXEC(@kill);

            USE [master]  
            GO  
            ALTER DATABASE [appservice_metering] SET CONTAINMENT = PARTIAL  
            GO  

        /********[appservice_metering] Migration End********/

        /********[appservice_hosting] Migration Start********/

            -- kill all active sessions
            USE [master];

            DECLARE @kill varchar(8000) = '';  
            SELECT @kill = @kill + 'kill ' + CONVERT(varchar(5), session_id) + ';'  
            FROM sys.dm_exec_sessions
            WHERE database_id  = db_id('appservice_hosting')

            EXEC(@kill);

            -- Convert database to contained
            USE [master]  
            GO  
            ALTER DATABASE [appservice_hosting] SET CONTAINMENT = PARTIAL  
            GO  

            /********[appservice_hosting] Migration End********/
    ```

1. Migre los inicios de sesión a usuarios de la base de datos independiente.

    ```sql
        IF EXISTS(SELECT * FROM sys.databases WHERE Name=DB_NAME() AND containment = 1)
        BEGIN
        DECLARE @username sysname ;  
        DECLARE user_cursor CURSOR  
        FOR
            SELECT dp.name
            FROM sys.database_principals AS dp  
            JOIN sys.server_principals AS sp
                ON dp.sid = sp.sid  
                WHERE dp.authentication_type = 1 AND dp.name NOT IN ('dbo','sys','guest','INFORMATION_SCHEMA');
            OPEN user_cursor  
            FETCH NEXT FROM user_cursor INTO @username  
                WHILE @@FETCH_STATUS = 0  
                BEGIN  
                    EXECUTE sp_migrate_user_to_contained
                    @username = @username,  
                    @rename = N'copy_login_name',  
                    @disablelogin = N'do_not_disable_login';  
                FETCH NEXT FROM user_cursor INTO @username  
            END  
            CLOSE user_cursor ;  
            DEALLOCATE user_cursor ;
            END
        GO
    ```

**Validación**

1. Compruebe si SQL Server tiene la independencia habilitada.

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Compruebe el comportamiento de independencia existente.
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

- Los trabajos no pueden llegar al servidor de archivos cuando App Service está implementada en una red virtual existente y el servidor de archivos solo está disponible en la red privada. Esto también se indica en la documentación de implementación de Azure App Service en Azure Stack.

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conocidos para los administradores en la nube que trabajan con Azure App Service en Azure Stack

Consulte la documentación de las [notas de la versión de Azure Stack 1809](azure-stack-update-1903.md).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de Azure App Service, consulte la [Información general de Azure App Service en Azure Stack](azure-stack-app-service-overview.md).
- Para más información acerca de cómo prepararse para implementar App Service en Azure Stack, consulte [Requisitos previos para implementar App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md).
