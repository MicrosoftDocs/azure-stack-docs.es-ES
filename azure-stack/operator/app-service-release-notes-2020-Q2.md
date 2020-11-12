---
title: Notas de la versión de App Service en Azure Stack Hub 2002 Q2
description: Conozca el contenido de la versión 2002 Q2 de App Service en Azure Stack Hub, los problemas conocidos y la ubicación dónde debe descargarse.
author: apwestgarth
manager: stefsch
ms.topic: article
ms.date: 05/05/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/30/2020
ms.openlocfilehash: c5e6ac0a2a500cf43cf94cbc40b2a95c58784d28
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94544724"
---
# <a name="app-service-on-azure-stack-hub-2020-q2-release-notes"></a>Notas de la versión de App Service en Azure Stack Hub 2020 Q2

Estas notas de la versión describen las mejoras y correcciones de Azure App Service en Azure Stack Hub 2020 Q2 y los problemas conocidos. Los problemas conocidos se dividen en aquellos que están relacionados directamente con el proceso de actualización y aquellos que están relacionados con la compilación (posteriores a la instalación).

[!INCLUDE [Azure Stack Hub update reminder](../includes/app-service-hub-update-banner.md)]

## <a name="build-reference"></a>Referencia de compilación

El número de compilación de App Service en Azure Stack Hub 2020 Q2 es **87.0.2.10**.

## <a name="prerequisites"></a>Prerrequisitos

Remítase a la [documentación de introducción](azure-stack-app-service-before-you-get-started.md) antes de comenzar la implementación.

Antes de comenzar la actualización de Azure App Service en Azure Stack a la versión 2020 Q2:

- Asegúrese de que todos los roles estén en estado preparado en la administración de Azure App Service del portal de administración de Azure Stack Hub.

- Realice una copia de seguridad de los secretos de App Service mediante la administración de App Service en el portal de administración de Azure Stack Hub.

- Realice una copia de seguridad de App Service y de las bases de datos maestras:
  - AppService_Hosting;
  - AppService_Metering;
  - Master

- Realice una copia de seguridad del recurso compartido de archivos del contenido de la aplicación inquilina.

  > [!Important]
  > Los operadores de la nube son responsables del mantenimiento y funcionamiento del servidor de archivos y el servidor de SQL Server.  El proveedor de recursos no administra estos recursos.  El operador de la nube es responsable de realizar copias de seguridad de las bases de datos y los recursos compartidos de archivos del contenido del inquilino de App Service.

- Distribuya la **extensión de script personalizado** de la versión  **1.9.3** desde Marketplace.



## <a name="updates"></a>Actualizaciones

La actualización Q2 de Azure App Service en Azure Stack incluye las siguientes correcciones y mejoras:

- Actualizaciones de las **herramientas de Kudu, los portales de Functions, Admin e inquilino de App Service**. Es coherente con la versión del SDK de Azure Stack Portal.

- Actualiza el **entorno de ejecución de Azure Functions** a **v1.0.13021**.

- Actualizaciones en el servicio principal para mejorar la confiabilidad y los mensajes de error, lo cual permite un diagnóstico de problemas comunes más sencillo.

- **Actualizaciones de las herramientas y plataformas de aplicaciones siguientes** :
  - ASP.NET Framework 4.7.2
  - ASP.NET Core 3.1.3
  - Módulo ASP.NET Core v2 13.1.19331.0
  - PHP 7.4.2
  - Kudu actualizado a la versión 86.20224.4450
  - NodeJS 
    - 8.17.0
    - 10.19.0
    - 12.13.0
    - 12.15.0
  - NPM
    - 5.6.0
    - 6.1.0
    - 6.12.0
    - 6.13.4
  
- **Actualizaciones al sistema operativo subyacente de todos los roles** :
  - [Actualización acumulativa 2020-04 para Windows Server 2016 en sistemas basados en x64 (KB4550929)](https://support.microsoft.com/help/4550929)
  - [Actualización 2020-04 de la pila de servicio para Windows Server 2016 en sistemas basados en x64 (KB4550994)](https://support.microsoft.com/help/4550994)

- **Las actualizaciones acumulativas de Windows Server ahora se aplican a los roles de Controlador como parte de la implementación y la actualización**

- **Se han actualizado las SKU de las máquinas virtuales y conjuntos de escalado predeterminados de las nuevas implementaciones** : Para mantener la coherencia con nuestro servicio en la nube pública, las nuevas implementaciones de Azure App Service en Azure Stack Hub usarán las siguientes SKU para las máquinas y conjuntos de escalado subyacentes empleados para operar el proveedor de recursos.
  
  | Role | SKU mínima |
  | --- | --- |
  | Controller | Standard_A4_v2 - (4 núcleos, 8192 MB) |
  | Administración | Standard_D3_v2 - (4 cores, 14 336 MB) |
  | Publicador | Standard_A2_v2 - (2 núcleos, 4096 MB) |
  | FrontEnd | Standard_A4_v2 - (4 núcleos, 8192 MB) |
  | Trabajo compartido | Standard_A4_v2 - (4 núcleos, 8192 MB) |
  | Trabajo dedicado pequeño | Standard_A1_v2 - (1 núcleo, 2048 MB) |
  | Trabajo dedicado medio | Standard_A2_v2 - (2 núcleos, 4096 MB) |
  | Trabajo dedicado grande | Standard_A4_v2 - (4 núcleos, 8192 MB) |

En el caso de las implementaciones de ASDK, puede reducir verticalmente las instancias para las SKU inferiores para reducir el uso de los núcleos y la memoria pero experimentará una degradación del rendimiento.

## <a name="issues-fixed-in-this-release"></a>Problemas corregidos en esta versión

- Las actualizaciones se completarán ahora si un clúster de Grupos de disponibilidad AlwaysOn de SQL se ha conmutado por error a un nodo secundario.
- Las nuevas implementaciones de Azure App Service en Azure Stack Hub ya no requieren que las bases de datos se conviertan manualmente en bases de datos independientes.
- La adición de instancias de rol de infraestructura o trabajos adicionales se completará correctamente sin intervención manual.
- La adición de niveles de trabajo personalizados se completará correctamente sin intervención manual.
- La eliminación de los niveles de trabajo personalizados ahora se completa sin errores de portal.
- Los trabajos ya no se marcan como listos si el disco local no tiene espacio
- Aumento del tiempo de espera para recuperar el certificado de Azure Resource Manager
- El número de mensajes recuperados, de los registros del servidor y los que se muestran en el portal de administración, está limitado para que permanezca por debajo del tamaño máximo de solicitudes de Azure Resource Manager.
- El problema de agotamiento del tiempo de espera que provocaba problemas en el inicio del uso del servicio
- Se ha resuelto el problema de implementación que se producía al crear sitios de Orchard CMS.
- Los controladores ahora se actualizan con las actualizaciones acumulativas de Windows como parte de los procesos de implementación y actualización.
- App Service ya no bloquea las operaciones cuando se produce un error en la comprobación del dominio personalizado.

## <a name="pre-update-steps"></a>Pasos previos a la actualización

Revise los [problemas conocidos de la actualización](#known-issues-update) y realice las acciones que se recomiendan.

## <a name="post-deployment-steps"></a>Pasos posteriores a la implementación

> [!IMPORTANT]
> Si proporcionó el proveedor de recursos de App Service con una instancia de SQL Always On, DEBE [agregar las bases de datos appservice_hosting y appservice_metering a un grupo de disponibilidad](/sql/database-engine/availability-groups/windows/availability-group-add-a-database), así como sincronizar dichas bases de datos para evitar la pérdida de servicio en caso de producirse una conmutación por error de base de datos.

## <a name="known-issues-update"></a>Problemas conocidos (actualización)

- En situaciones en las que un cliente ha convertido las bases de datos appservice_hosting y appservice_metering en una base de datos independiente, la actualización puede producir un error si los inicios de sesión no se han migrado correctamente para los usuarios de dicha base de datos.

Los clientes que han convertido las bases de datos appservice_hosting y appservice_metering en una base de datos independiente después de la implementación, y que no han migrado correctamente los inicios de sesión de la base de datos para los usuarios de la base de datos independiente, pueden experimentar errores de actualización.  

Los clientes deben ejecutar el siguiente script en el servidor de SQL Server que hospeda appservice_hosting y appservice_metering antes de actualizar Azure App Service en Azure Stack Hub a la versión 2020 Q2.  **Este script no es destructivo y no provocará tiempo de inactividad**.

Este script se debe ejecutar en las siguientes condiciones:

- Lo debe ejecutar un usuario que tenga privilegios de administrador del sistema, por ejemplo, la cuenta SA de SQL;
- Si usa grupos de disponibilidad Always On de SQL, asegúrese de que el script se ejecuta desde la instancia de SQL que contiene todos los inicios de sesión de App Service de la forma siguiente:

    - appservice_hosting_FileServer
    - appservice_hosting_HostingAdmin
    - appservice_hosting_LoadBalancer
    - appservice_hosting_Operations
    - appservice_hosting_Publisher
    - appservice_hosting_SecurePublisher
    - appservice_hosting_WebWorkerManager
    - appservice_metering_Common
    - appservice_metering_Operations
    - Todos los inicios de sesión de WebWorker, que tienen el formato WebWorker_<instance ip address>

```sql
        USE appservice_hosting
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

        USE appservice_metering
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

## <a name="known-issues-post-installation"></a>Problemas conocidos (posteriores a la instalación)

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

### <a name="known-issues-for-cloud-admins-operating-azure-app-service-on-azure-stack"></a>Problemas conocidos para los administradores en la nube que trabajan con Azure App Service en Azure Stack

- Los inquilinos no pueden crear un plan de App Service con la nueva vista Plan de App Service en el portal de inquilinos

Cuando se crea una nueva aplicación, los inquilinos pueden crear planes de App Service durante el flujo de trabajo de creación de la aplicación, al cambiar el plan de App Service de una aplicación actual o mediante el elemento de Marketplace del plan de App Service.

- Los dominios personalizados no se admiten en entornos sin conexión.

App Service realiza la comprobación de la propiedad del dominio con los puntos de conexión DNS públicos. Por este motivo, los dominios personalizados no se admiten en escenarios desconectados.

## <a name="next-steps"></a>Pasos siguientes

- Para obtener información general de Azure App Service, consulte la [Información general de Azure App Service en Azure Stack](azure-stack-app-service-overview.md).
- Para obtener más información acerca de cómo prepararse para implementar App Service en Azure Stack, consulte [Antes de empezar a trabajar con App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md).
