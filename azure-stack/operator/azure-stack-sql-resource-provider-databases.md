---
title: Uso de bases de datos que proporciona el proveedor de recursos del adaptador de SQL en Azure Stack | Microsoft Docs
description: Cómo crear y administrar las bases de datos SQL aprovisionadas mediante el proveedor de recursos del adaptador de SQL
services: azure-stack
documentationCenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: quying
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: ad02e37faf9d4f22cb8fe8d58a203c46ae758b52
ms.sourcegitcommit: 0973dddb81db03cf07c8966ad66526d775ced8b9
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 04/23/2019
ms.locfileid: "64309219"
---
# <a name="create-sql-databases"></a>Creación de bases de datos SQL

Puede crear y administrar bases de datos de autoservicio en el portal de usuarios. Un usuario de Azure Stack necesita una suscripción con una oferta que incluya el servicio de SQL Database.

1. Inicie sesión en el portal de usuarios de [Azure Stack](azure-stack-overview.md).

2. Seleccione **+ New** (+ Nuevo) &gt;**Data + Storage** (Datos y almacenamiento) &gt; **SQL Server Database** &gt; (Base de datos SQL Server) **Add** (Agregar).

3. En **Create Database** (Crear base de datos), escriba la información necesaria, como **Database Name** (Nombre de la base de datos) y **Max Size in MB** (Tamaño máximo en MB).

   >[!NOTE]
   >El tamaño de la base de datos debe ser al menos de 64 MB. Este tamaño se puede aumentar después de implementar la base de datos.

   Configure las demás opciones para su entorno, según corresponda.

4. En **Create Database** (Crear base de datos), seleccione **SKU**. En **Select a SKU** (Seleccionar una SKU), seleccione la SKU correspondiente a su base de datos.

   ![Creación de una base de datos](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >A medida que se agregan servidores de hospedaje a Azure Stack, se les asigna una SKU. Las bases de datos se crean en el grupo de servidores de hospedaje de una SKU.

5. Seleccione **Login** (Iniciar sesión).
6. En **Select a Login** (Seleccionar un inicio de sesión), elija un inicio de sesión existente o seleccione **+ Create a new login** (+ Crear un nuevo inicio de sesión).
7. En **New Login** (Nuevo inicio de sesión), escriba un nombre para **Database login** (Inicio de sesión de base de datos) y un valor para **Password** (Contraseña).

   >[!NOTE]
   >Esta configuración es la credencial de autenticación de SQL que se crea solo para el acceso a esta base de datos. El nombre de usuario de inicio de sesión debe ser único globalmente. Puede volver a usar la configuración de inicio de sesión con otras bases de datos que usan la misma SKU.

   ![Creación de un nuevo inicio de sesión en la base de datos](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Seleccione **OK** (Aceptar) para terminar de implementar la base de datos.

En **Essentials** (Información esencial), que se muestra después de implementar la base de datos, anote la **cadena de conexión**. Puede usar esta cadena en cualquier aplicación que necesite tener acceso a la base de datos de SQL Server.

![Recuperación de la cadena de conexión](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>Bases de datos de SQL AlwaysOn

Por naturaleza, las bases de datos AlwaysOn se controlan de forma diferente que en un entorno de servidor independiente. Para más información, consulte [Introducción a los grupos de disponibilidad Always On de SQL Server en Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Comprobación de las bases de datos SQL AlwaysOn

En la captura de pantalla siguiente se muestra cómo puede usar SQL Server Management Studio para examinar el estado de una base de datos en SQL AlwaysOn.

![Estado de las bases de datos AlwaysOn](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Las bases de datos AlwaysOn se muestran ahora como sincronizadas y disponibles en todas las instancias de SQL y aparecen en grupos de disponibilidad. En la captura de pantalla anterior, el ejemplo de base de datos es newdb1 y su estado es **newdb1 (sincronizado)**.

### <a name="delete-an-alwayson-database"></a>Eliminación de una base de datos AlwaysOn

Cuando se elimina una base de datos de SQL AlwaysOn del proveedor de recursos, SQL elimina la base de datos de la réplica principal y del grupo de disponibilidad.

A continuación, SQL coloca la base de datos en estado de restauración en las demás réplicas y no la descarta a menos que se desencadene. Si la base de datos no se descarta, las réplicas secundarias pasan a un estado de no sincronización.

## <a name="next-steps"></a>Pasos siguientes

[Mantenimiento de un proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-maintain.md)
