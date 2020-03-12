---
title: Notas de la versión de la actualización 8 de App Service en Azure Stack Hub
description: Conozca el contenido de la actualización 8 de App Service en Azure Stack Hub, los problemas conocidos y la ubicación dónde debe descargarse.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 03/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/25/2019
ms.openlocfilehash: 82f43028253638f92866bb679a5ccb5478a5a56e
ms.sourcegitcommit: 8198753ebafd69d0dbfc6b3548defbd70f4c79c1
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 03/09/2020
ms.locfileid: "78935077"
---
# <a name="app-service-on-azure-stack-hub-update-8-release-notes"></a>Notas de la versión de la actualización 8 de App Service en Azure Stack Hub

En estas notas de la versión se describen las mejoras y correcciones de la actualización 8 de Azure App Service en Azure Stack Hub y los problemas conocidos. Los problemas conocidos se dividen en aquellos que están relacionados directamente con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Aplique la actualización 1910 al sistema integrado de Azure Stack o implemente el kit de desarrollo de Azure Stack más reciente antes de implementar Azure App Service 1.8.

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 8 de App Service en Azure Stack Hub es **86.0.2.13**.

### <a name="prerequisites"></a>Prerrequisitos

Remítase a la [documentación de introducción](azure-stack-app-service-before-you-get-started.md) antes de comenzar la implementación.

Antes de comenzar la actualización de Azure App Service en Azure Stack a la versión 1.8:

- Asegúrese de que todos los roles están listos en la administración de Azure App Service desde el portal de administración de Azure Stack.

- Realice una copia de seguridad de App Service y de las bases de datos maestras:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Realice una copia de seguridad del recurso compartido de archivos del contenido de la aplicación inquilina.

- Distribuya la **extensión de script personalizado** de la versión **1.9.3** desde Marketplace.

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

La actualización 8 de Azure App Service en Azure Stack incluye las siguientes correcciones y mejoras:

- Actualizaciones de las **herramientas de Kudu, los portales de Functions, Admin e inquilino de App Service**. Es coherente con la versión del SDK de Azure Stack Portal.

- Actualiza el **entorno de ejecución de Azure Functions** a **v1.0.12615**.

- Actualizaciones en el servicio principal para mejorar la confiabilidad y los mensajes de error, lo cual permite un diagnóstico de problemas comunes más sencillo.

- **Actualizaciones de las herramientas y plataformas de aplicaciones siguientes**:
  - ASP.NET Core 3.1.0
  - ASP.NET Core 3.0.1
  - ASP.NET Core 2.2.8
  - Módulo ASP.NET Core v2 13.1.19331.0
  - Azul OpenJDK 8.38.0.13
  - Tomcat 7.0.94
  - Tomcat 8.5.42
  - Tomcat 9.0.21
  - PHP 7.1.32
  - PHP 7.2.22
  - PHP 7.3.9
  - Kudu actualizado a la versión 85.11024.4154
  - MSDeploy 3.5.80916.15
  - NodeJS 10.16.3
  - NPM 6.9.0
  - Git para Windows 2.19.1.0

- **Actualizaciones al sistema operativo subyacente de todos los roles**:
  - [Actualización acumulativa 2019-12 para Windows Server 2016 en sistemas basados en x64 (KB4530689)](https://support.microsoft.com/help/4530689)

- **Compatibilidad de disco administrado para nuevas implementaciones**

Todas las nuevas implementaciones de Azure App Service en Azure Stack Hub utilizarán los discos administrados para todas las máquinas virtuales y conjuntos de escalado de máquinas virtuales.  Todas las implementaciones existentes seguirán usando discos no administrados.

- **TLS 1.2 aplicado por equilibradores de carga front-end**

A partir de esta actualización, **TLS 1.2** se aplicará para todas las aplicaciones.

### <a name="known-issues-upgrade"></a>Problemas conocidos (actualización)

- Se producirá un error en la actualización si un clúster de Grupos de disponibilidad AlwaysOn de SQL Server se ha conmutado por error a un nodo secundario

Durante la actualización, hay una llamada para comprobar la existencia de la base de datos mediante la cadena de conexión maestra que producirá un error porque el inicio de sesión se encontraba en el nodo maestro anterior.

Realice una de las siguientes acciones y haga clic en Retry within the installer (Reintentar en el instalador).

- Copie el inicio de sesión de appservice_hostingAdmin desde el nuevo nodo secundario de SQL,

**OR**

- Conmute por error el clúster de SQL al nodo activo anterior.

### <a name="post-deployment-steps"></a>Pasos posteriores a la implementación

> [!IMPORTANT]
> Si proporcionó el proveedor de recursos de App Service con una instancia de SQL Always On, DEBE [agregar las bases de datos appservice_hosting y appservice_metering a un grupo de disponibilidad](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database), así como sincronizar dichas bases de datos para evitar la pérdida de servicio en caso de producirse una conmutación por error de base de datos.

### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

- Los trabajos no pueden llegar al servidor de archivos cuando App Service está implementada en una red virtual existente y el servidor de archivos solo está disponible en la red privada, tal y como se describe en la documentación de implementación de Azure App Service en Azure Stack.

  Si decide realizar una implementación en una red virtual existente y en una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. De ese modo, permite que exista tráfico SMB entre la subred del rol de trabajo y el servidor de archivos. Vaya a WorkersNsg en el Portal de administración y agregue una regla de seguridad de salida con las siguientes propiedades:
  - Origen: Any
  - Intervalo de puertos de origen: *
  - Destino: Direcciones IP
  - Intervalo de direcciones IP de destino: Intervalo de direcciones IP del servidor de archivos
  - Intervalo de puertos de destino: 445
  - Protocolo: TCP
  - Acción: Allow
  - Prioridad: 700
  - Nombre: Outbound_Allow_SMB445

- Las nuevas implementaciones de Azure App Service en Azure Stack Hub 1.8 requieren que las bases de datos se conviertan en bases de datos independientes.

Debido a una regresión en esta versión, las bases de datos de App Service (appservice_hosting y appservice_metering) deben convertirse en bases de datos independientes cuando se implementen por **primera vez**.  Esto **NO** afecta a las implementaciones **actualizadas**.

> [!IMPORTANT]
> Este procedimiento tarda aproximadamente entre 5 y 10 minutos. Este procedimiento implica la eliminación de las sesiones de inicio de sesión de base de datos existentes. Planee el tiempo de inactividad para migrar y validar Azure App Service en Azure Stack Hub después de la migración
>
>

1. Agregue las [bases de datos de AppService (appservice_hosting y appservice_metering) al grupo de disponibilidad](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database).

1. Habilite la base de datos independiente.

    ```sql

        sp_configure 'contained database authentication', 1;
        GO
        RECONFIGURE;
            GO
    ```

1. Convierta una base de datos a parcialmente independiente. Este paso incurrirá en un tiempo de inactividad, ya que se deben eliminar todas las sesiones activas.

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

- No se pueden escalar horizontalmente los trabajos

  Los nuevos trabajos no pueden adquirir la cadena de conexión de la base de datos necesaria.  Para solucionar este problema, conéctese a una de las instancias del controlador, por ejemplo, CN0-VM, y ejecute el siguiente script de PowerShell:

    ```powershell
    
    [System.Reflection.Assembly]::LoadWithPartialName("Microsoft.Web.Hosting")
    $siteManager = New-Object Microsoft.Web.Hosting.SiteManager

    $builder = New-Object System.Data.SqlClient.SqlConnectionStringBuilder -ArgumentList (Get-AppServiceConnectionString -Type Hosting)
    $conn = New-Object System.Data.SqlClient.SqlConnection -ArgumentList $builder.ToString()

    $siteManager.RoleServers | Where-Object {$_.IsWorker} | ForEach-Object {
        $worker = $_
        $dbUserName = "WebWorker_" + $worker.Name

        if (!$siteManager.ConnectionContexts[$dbUserName]) {
            $dbUserPassword = [Microsoft.Web.Hosting.Common.Security.PasswordHelper]::GenerateDatabasePassword()

            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "CREATE USER [$dbUserName] WITH PASSWORD = '$dbUserPassword'"
            $command.ExecuteNonQuery()
            $conn.Close()
            
            $conn.Open()
            $command = $conn.CreateCommand()
            $command.CommandText = "ALTER ROLE [WebWorkerRole] ADD MEMBER [$dbUserName]"
            $command.ExecuteNonQuery()
            $conn.Close()
            
            $builder.Password = $dbUserPassword
            $builder["User ID"] = $dbUserName
            
            $siteManager.ConnectionContexts.Add($dbUserName, $builder.ToString())
        }
    }

    $siteManager.CommitChanges()
        
    ```

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conocidos para los administradores en la nube que trabajan con Azure App Service en Azure Stack

Consulte la documentación de las [notas de la versión de Azure Stack 1907](azure-stack-release-notes-1907.md).

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de Azure App Service, consulte la [Información general de Azure App Service en Azure Stack](azure-stack-app-service-overview.md).
- Para obtener más información acerca de cómo prepararse para implementar App Service en Azure Stack, consulte [Antes de empezar a trabajar con App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md).
