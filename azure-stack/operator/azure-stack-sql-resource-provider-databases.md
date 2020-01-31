---
title: Creación de bases de datos SQL
titleSuffix: Azure Stack Hub
description: Aprenda a crear y administrar las bases de datos SQL aprovisionadas con el adaptador del proveedor de recursos de SQL.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 6a0135d643ebb87fb99ba12bec4741c67faa1111
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76882827"
---
# <a name="create-sql-databases"></a>Creación de bases de datos SQL

Puede crear y administrar bases de datos de autoservicio en el portal de usuarios. Un usuario de Azure Stack Hub necesita una suscripción con una oferta que incluya el servicio SQL Database.

1. Inicie sesión en el portal de usuarios de [Azure Stack Hub](azure-stack-overview.md).

2. Select **+ Nuevo** &gt; **Datos y almacenamiento** &gt; **Base de datos de SQL Server** &gt; **Agregar**.

3. En **Create Database** (Crear base de datos), escriba la información necesaria, como **Database Name** (Nombre de la base de datos) y **Max Size in MB** (Tamaño máximo en MB).

   >[!NOTE]
   >El tamaño de la base de datos debe ser al menos de 64 MB. Este tamaño se puede aumentar después de implementar la base de datos.

   Configure las demás opciones para su entorno, según corresponda.

4. En **Create Database** (Crear base de datos), seleccione **SKU**. En **Select a SKU** (Seleccionar una SKU), seleccione la SKU correspondiente a su base de datos.

   ![Cree una base de datos en el portal de usuarios de Azure Stack Hub.](./media/azure-stack-sql-rp-deploy/newsqldb.png)

   >[!NOTE]
   >A medida que se agregan servidores de hospedaje a Azure Stack Hub, se les asigna una SKU. Las bases de datos se crean en el grupo de servidores de hospedaje de una SKU.

5. Seleccione **Login** (Iniciar sesión).

6. En **Select a Login** (Seleccionar un inicio de sesión), elija un inicio de sesión existente o seleccione **+ Create a new login** (+ Crear un nuevo inicio de sesión).

7. En **New Login** (Nuevo inicio de sesión), escriba un nombre para **Database login** (Inicio de sesión de base de datos) y un valor para **Password** (Contraseña).

   >[!NOTE]
   >Esta configuración es la credencial de autenticación de SQL que se crea solo para el acceso a esta base de datos. El nombre de usuario de inicio de sesión debe ser único globalmente. Puede volver a usar la configuración de inicio de sesión con otras bases de datos que usan la misma SKU.

   ![Creación de un inicio de sesión de base de datos en el portal de usuarios de Azure Stack Hub](./media/azure-stack-sql-rp-deploy/create-new-login.png)

8. Seleccione **OK** (Aceptar) para terminar de implementar la base de datos.

En **Essentials** (Información esencial), que se muestra después de implementar la base de datos, anote la **cadena de conexión**. Puede usar esta cadena en cualquier aplicación que necesite acceder a la base de datos de SQL Server.

![Recuperar la cadena de conexión de la base de datos de SQL Server](./media/azure-stack-sql-rp-deploy/sql-db-settings.png)

## <a name="sql-always-on-databases"></a>Bases de datos de SQL AlwaysOn

Por naturaleza, las bases de datos AlwaysOn se controlan de forma diferente que en un entorno de servidor independiente. Para más información, consulte [Introducción a los grupos de disponibilidad Always On de SQL Server en Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview).

### <a name="verify-sql-always-on-databases"></a>Comprobación de las bases de datos SQL AlwaysOn

En la captura de pantalla siguiente se muestra cómo puede usar SQL Server Management Studio para examinar el estado de una base de datos en SQL AlwaysOn.

![Estado de la base de datos AlwaysOn en SQL Server Management Studio](./media/azure-stack-sql-rp-deploy/verifyalwayson.png)

Las bases de datos AlwaysOn se muestran ahora como **sincronizadas** y disponibles en todas las instancias de SQL y aparecen en **grupos de disponibilidad**. En la captura de pantalla anterior, el ejemplo de base de datos es newdb1 y su estado es **newdb1 (sincronizado)** .

### <a name="delete-an-alwayson-database"></a>Eliminación de una base de datos AlwaysOn

Cuando se elimina una base de datos de SQL AlwaysOn del proveedor de recursos, SQL elimina la base de datos de la réplica **principal** y del grupo de disponibilidad.

A continuación, SQL coloca la base de datos en estado de **restauración** en las demás réplicas y no la descarta a menos que se desencadene. Si la base de datos no se descarta, las réplicas secundarias pasan a un estado de **no sincronización**.

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [ofrecer bases de datos SQL de alta disponibilidad](azure-stack-tutorial-sql.md).
