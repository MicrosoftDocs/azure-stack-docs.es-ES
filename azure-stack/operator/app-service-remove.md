---
title: Eliminación de Azure App Service de Azure Stack Hub
description: Aprenda a eliminar Azure App Service de Azure Stack Hub
author: apwestgarth
ms.topic: article
ms.date: 04/17/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 04/17/20207
ms.openlocfilehash: b371b319ae87bb112633b64d77d66f8916a13124
ms.sourcegitcommit: 355e21dd9b8c3f44e14abaae0b4f176443cf7495
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/17/2020
ms.locfileid: "81636494"
---
# <a name="remove-azure-app-service-from-azure-stack-hub"></a>Eliminación de Azure App Service de Azure Stack Hub

En este artículo se muestra cómo quitar el proveedor de recursos de Azure App Service y los componentes relacionados de Azure Stack Hub.

## <a name="remove-resource-provider"></a>Eliminación de un proveedor de recursos

> [!Important]
> Esta operación quitará todos los recursos de los inquilinos, quitará el servicio y las cuotas de todos los planes, así como el proveedor de recursos de Azure App Service en su totalidad.  Si ha implementado el servidor de archivos de alta disponibilidad de App Service y la plantilla de inicio rápido de SQL Server, estos recursos también se quitarán cuando se implementen en el mismo grupo de recursos que Azure App Service en Azure Stack Hub.

Para eliminar Azure App Service de Azure Stack Hub, siga este paso:

1. Elimine el grupo de recursos que contiene los recursos de Azure App Service en Azure Stack Hub, por ejemplo, AppService.local.

## <a name="remove-databases-and-file-share-content"></a>Eliminación de bases de datos y contenido del recurso compartido de archivos

Esta sección solo debe seguirse si SQL Server o el servidor de archivos se implementan fuera del stamp o en otro grupo de recursos; de lo contrario, continúe con la sección siguiente.

### <a name="remove-databases-and-logins"></a>Eliminación de bases de datos e inicios de sesión

1. Si usa **SQL Server Always On**, quite las bases de datos **AppService_Hosting** y **AppService_Metering** del grupo de disponibilidad:

1. Ejecute el siguiente script de SQL para quitar las bases de datos y los inicios de sesión

   ```sql
   --******************************************************************
   /*
   Script to clean up App Service objects (databases and logins).
   */
   USE [master]
   GO

   DROP DATABASE [appservice_hosting]
   GO

   DROP DATABASE [appservice_metering]
   GO

   DECLARE @sql NVARCHAR(MAX) = N'';    
 
   SELECT @sql += '
   DROP LOGIN [' + name + '];' 
   from master.sys.sql_logins
   WHERE name LIKE  '%_hosting_%' OR 
   name LIKE  '%_metering_%' OR
   name LIKE  '%WebWorker_%';

   PRINT @sql;
   EXEC sp_executesql @sql;
   PRINT 'Completed';

   --******************************************************************
   ```

### <a name="remove-the-application-file-content-from-the-file-server"></a>Eliminación del contenido del archivo de aplicación del servidor de archivos

1. Quite el recurso compartido de contenido del servidor de archivos.

## <a name="next-steps"></a>Pasos siguientes

Para realizar la reinstalación, vuelva al artículo [Requisitos previos para implementar App Service en Azure Stack Hub](azure-stack-app-service-before-you-get-started.md).
