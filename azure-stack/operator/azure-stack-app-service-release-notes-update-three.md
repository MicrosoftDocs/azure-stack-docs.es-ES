---
title: Notas de la versión de la actualización 3 de App Service en Azure Stack | Microsoft Docs
description: Conozca el contenido de la actualización 3 de App Service en Azure Stack, los problemas conocidos y dónde descargar la actualización.
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
ms.lastreviewed: 08/20/2018
ms.openlocfilehash: 4397fbbc570972fb7a041f583d81115e28fe70ce
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2019
ms.locfileid: "66269105"
---
# <a name="app-service-on-azure-stack-update-3-release-notes"></a>Notas de la versión de la actualización 3 de App Service en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

Estas notas de la versión describen las mejoras y correcciones de la actualización 3 de Azure App Service en Azure Stack y los problemas conocidos. Los problemas conocidos se dividen en aquellos que están relacionados directamente con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

> [!IMPORTANT]
> Aplique la actualización 1807 al sistema integrado de Azure Stack o implemente el Kit de desarrollo de Azure Stack más reciente antes de implementar Azure App Service 1.3.
>
>

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de la actualización 3 de App Service en Azure Stack es **74.0.13698.31**

### <a name="prerequisites"></a>Requisitos previos

Remítase a la [documentación de introducción](azure-stack-app-service-before-you-get-started.md) antes de comenzar la implementación.

Antes de comenzar la actualización de Azure App Service en Azure Stack a la versión 1.3, asegúrese de que todos los roles están listos en la administración de Azure App Service en el Portal de administración de Azure Stack

![Estado de los roles de App Service](media/azure-stack-app-service-release-notes-update-three/image01.png)

### <a name="new-features-and-fixes"></a>Nuevas características y correcciones

La actualización 3 de Azure App Service en Azure Stack incluye las siguientes correcciones y mejoras:

- Compatibilidad con el uso de SQL Server Always On para las bases de datos del proveedor de recursos de Azure App Service.

- Se ha agregado un nuevo parámetro de entorno al script auxiliar Create-AADIdentityApp para ayudar a establecer como destino distintas regiones de AAD.

- Actualizaciones de las **herramientas de Kudu, los portales de Functions, Admin e inquilino de App Service**. Es coherente con la versión del SDK de Azure Stack Portal.

- Actualiza el **entorno de ejecución de Azure Functions** a **v1.0.11820**.

- Actualizaciones en el servicio principal para mejorar la confiabilidad y los mensajes de error, lo cual permite un diagnóstico de problemas comunes más sencillo.

- **Actualizaciones de las herramientas y plataformas de aplicaciones siguientes**:
  - Se ha agregado ASP.Net Core 2.1.2
  - Se ha agregado NodeJS 10.0.0
  - Se ha agregado Zulu OpenJDK 8.30.0.1
  - Se han agregado Tomcat 8.5.31 y 9.0.8
  - Se han agregado las versiones PHP:
    - 5.6.36
    - 7.0.30
    - 7.1.17
    - 7.2.5
  - Se ha agregado Wincache 2.0.0.8
  - Se ha actualizado Git para Windows a la versión 2.17.1.2
  - Se ha actualizado Kudu a la versión 74.10611.3437
  
- **Actualizaciones al sistema operativo subyacente de todos los roles**:
  - [Actualización de la pila de servicio para Windows Server 2016 en sistemas basados en x64 (KB4132216)](https://support.microsoft.com/help/4132216/servicing-stack-update-for-windows-10-1607-may-17-2018)
  - [Actualización acumulativa 2018-07 para Windows Server 2016 en sistemas basados en x64 (KB4338822)](https://support.microsoft.com/help/4338822/windows-10-update-kb4338822)

### <a name="post-update-steps-optional"></a>Pasos posteriores a la actualización (opcional)

Para los clientes que deseen migrar a una base de datos independiente para las implementaciones de Azure App Service en Azure Stack existentes, ejecute estos pasos una vez completada la actualización 1.3 de Azure App Service en Azure Stack:

> [!IMPORTANT]
> Este procedimiento tarda aproximadamente entre 5 y 10 minutos.  Este procedimiento implica la eliminación de las sesiones de inicio de sesión de base de datos existentes.  Planee el tiempo de inactividad para migrar y validar Azure App Service en Azure Stack después de la migración.
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

1. Convierta una base de datos a parcialmente independiente.  Este paso incurrirá en un tiempo de inactividad, ya que se deben eliminar todas las sesiones activas.

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
    '''

1. Migrate Logins to Contained Database Users

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

Validación

1. Compruebe si SQL Server tiene la independencia habilitada.

    ```sql
        sp_configure  @configname='contained database authentication'
    ```

1. Compruebe el comportamiento de independencia existente.
    ```sql
        SELECT containment FROM sys.databases WHERE NAME LIKE (SELECT DB_NAME())
    ```

### <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

- Los trabajos no pueden llegar al servidor de archivos cuando App Service está implementada en una red virtual existente y el servidor de archivos solo está disponible en la red privada.  Esto también se indica en la documentación de implementación de Azure App Service en Azure Stack.

Si decide realizar una implementación en una red virtual existente y en una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. De ese modo, permite que exista tráfico SMB entre la subred del rol de trabajo y el servidor de archivos. Para ello, vaya a WorkersNsg en el Portal de administración y agregue una regla de seguridad de salida con las siguientes propiedades:
 * Origen: Cualquiera
 * Intervalo de puertos de origen: *
 * Destino: Direcciones IP
 * Intervalo de direcciones IP de destino: Intervalo de direcciones IP del servidor de archivos
 * Intervalo de puertos de destino: 445
 * Protocolo: TCP
 * Acción: PERMITIR
 * Prioridad: 700
 * Nombre: Outbound_Allow_SMB445

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conocidos para los administradores en la nube que trabajan con Azure App Service en Azure Stack

Consulte la documentación de las notas de la versión de Azure Stack 1807.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de Azure App Service, consulte la [Información general de Azure App Service en Azure Stack](azure-stack-app-service-overview.md).
- Para obtener más información acerca de cómo prepararse para implementar App Service en Azure Stack, consulte [Antes de empezar a trabajar con App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md).
