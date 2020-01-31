---
title: Notas de la versión de la actualización 4 de App Service en Azure Stack Hub
description: Conozca las mejoras, las correcciones y los problemas conocidos de la actualización 4 para App Service en Azure Stack Hub.
author: bryanla
manager: stefsch
ms.topic: article
ms.date: 03/25/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 54eae566f2171c471f91e3aaeaf242b90998e962
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76874423"
---
# <a name="app-service-on-azure-stack-hub-update-4-release-notes"></a>Notas de la versión de la actualización 4 de App Service en Azure Stack Hub

En estas notas de la versión se describen las mejoras, las correcciones y los problemas conocidos de la actualización 4 de Azure App Service en Azure Stack Hub. Los problemas conocidos se dividen tres secciones: los problemas directamente relacionados con la implementación, los problemas relacionados con el proceso de actualización y los problemas relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Aplique la actualización 1809 al sistema integrado de Azure Stack Hub o implemente el Kit de desarrollo de Azure Stack (ASDK) más reciente antes de implementar Azure App Service 1.4.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 4 de App Service en Azure Stack Hub es **78.0.13698.5**.

### <a name="prerequisites"></a>Prerequisites

Consulte [Requisitos previos para implementar App Service en Azure Stack Hub](azure-stack-app-service-before-you-get-started.md) antes de comenzar la implementación.

Antes de comenzar la actualización de Azure App Service en Azure Stack Hub a la versión 1.4:

- Asegúrese de que todos los roles están listos en la administración de Azure App Service del portal del administrador de Azure Stack Hub.

- Realice una copia de seguridad de App Service y de las bases de datos maestras:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Realice una copia de seguridad del recurso compartido de archivos del contenido de la aplicación inquilina.

- Distribuya la **extensión de script personalizado** de la versión **1.9** desde Azure Marketplace.

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

La actualización 4 de Azure App Service en Azure Stack Hub incluye las siguientes correcciones y mejoras:

- Resolución de la vulnerabilidad de scripts entre sitios (XSS) [CVE 2018 8600](https://aka.ms/CVE20188600).

- Se ha agregado compatibilidad para la versión de API 2018-02-01 de App Service.

- Actualizaciones de las **herramientas de Kudu, los portales de Functions, Admin e inquilino de App Service**. Es coherente con la versión del SDK del portal de Azure Stack Hub.

- Actualiza el **entorno de ejecución de Azure Functions** a **v1.0.11959**.

- Actualizaciones en el servicio principal para mejorar la confiabilidad y los mensajes de error, lo cual permite un diagnóstico de problemas comunes más sencillo.

- **Actualizaciones de las herramientas y plataformas de aplicaciones siguientes**:
  - Se agregó NodeJS 10.6.0.
  - Se agregó NPM 6.1.0.
  - Se agregó Zulu OpenJDK 8.31.0.2.
  - Se agregó Tomcat 8.5.34 y 9.0.12.
  - Se han agregado las versiones PHP:
    - 5.6.37
    - 7.0.31
    - 7.1.20
    - 7.2.8
  - Se ha actualizado a las versiones de Python:
    - 2.7.15
    - 3.6.6
  - Se ha actualizado Git para Windows a la versión 2.17.1.2
  - Se actualizó Kudu a la versión 78.11022.3613.
  
- **Actualizaciones al sistema operativo subyacente de todos los roles**:
  - [Actualización acumulativa 2018-10 para Windows Server 2016 en sistemas basados en x64 (KB4462928)](https://support.microsoft.com/help/4462928/windows-10-update-kb4462928)

- Se ha resuelto el problema de validación de plantilla durante la implementación de elementos de la galería en los CMS de Wordpress, DNN y Orchard.

- Se ha resuelto el problema de configuración cuando Azure Stack Hub rota el certificado de cliente de Azure Resource Manager.

- Se ha restaurado la funcionalidad en la configuración de uso compartido de recursos entre orígenes del portal de usuarios de App Service.

- Se muestra ahora un mensaje de error en el portal del administrador de App Service cuando el panel de control del proveedor de recursos no puede conectarse a la instancia de SQL Server configurada.

- Se garantiza que el punto de conexión se agregue a la cadena de conexión de almacenamiento personalizada cuando se especifica en la nueva aplicación de función.

### <a name="post-deployment-steps"></a>Pasos posteriores a la implementación

> [!IMPORTANT]  
> Si ha proporcionado al proveedor de recursos de App Service una instancia de SQL Always On, *debe* [agregar las bases de datos appservice_hosting y appservice_metering a un grupo de disponibilidad](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) y sincronizarlas para evitar la pérdida de servicio en caso de producirse una conmutación por error de la base de datos.

### <a name="post-update-steps-optional"></a>Pasos posteriores a la actualización (opcional)

En el caso de clientes que quieran migrar a una base de datos independiente en las implementaciones de Azure App Service en Azure Stack Hub existentes, ejecute estos pasos una vez completada la actualización 1.4 de Azure App Service en Azure Stack Hub:

> [!IMPORTANT]
> Este procedimiento de migración tarda aproximadamente entre 5 y 10 minutos. El procedimiento implica la eliminación de las sesiones de inicio de sesión de base de datos existentes. Planee el tiempo de inactividad para migrar y validar Azure App Service en Azure Stack Hub después de la migración. Si ha completado estos pasos después de actualizar a Azure App Service en Azure Stack Hub 1.3, no es necesario que los realice.

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

- Los trabajos no pueden llegar al servidor de archivos cuando App Service está implementada en una red virtual existente y el servidor de archivos solo está disponible en la red privada. Este problema también se indica en la documentación de implementación de Azure App Service en Azure Stack Hub.

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack-hub"></a>Problemas conocidos para los administradores de la nube que usan Azure App Service en Azure Stack Hub

Consulte la documentación de las [notas de la versión 1809 de Azure Stack Hub](azure-stack-update-1903.md).

## <a name="next-steps"></a>Pasos siguientes

- Para información general sobre Azure App Service, consulte [Introducción a Azure App Service en Azure Stack Hub](azure-stack-app-service-overview.md).
- Para más información sobre cómo prepararse para implementar App Service en Azure Stack Hub, consulte [Requisitos previos para implementar App Service en Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
