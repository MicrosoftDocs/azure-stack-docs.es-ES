---
title: Uso de las bases de datos proporcionadas por el RP del adaptador de MySQL en Azure Stack | Microsoft Docs
description: Cómo crear y administrar bases de datos MySQL aprovisionadas con el proveedor de recursos del adaptador de MySQL
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 6a933f812edeede1802c35e3d25915ce09b6f01b
ms.sourcegitcommit: a7207f4a4c40d4917b63e729fd6872b3dba72968
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/03/2019
ms.locfileid: "71909210"
---
# <a name="create-mysql-databases"></a>Creación de bases de datos MySQL
Un usuario de Azure Stack, suscrito a una oferta que incluye el servicio de base de datos de MySQL, puede crear y administrar el autoservicio de bases de datos de MySQL en el portal de usuarios.

## <a name="create-a-mysql-database"></a>Creación de una base de datos MySQL

1. Inicie sesión en el portal de usuarios de Azure Stack.
2. Seleccione **+ Crear un recurso** > **Datos y almacenamiento** > **Base de datos MySQL** > **Agregar**.
3. En **Create MySQL Database** (Crear base de datos MySQL), escriba el nombre de la base de datos y configure los valores necesarios para su entorno.

    ![Crear una base de datos MySQL de prueba](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. En **Create Database** (Crear base de datos), seleccione **SKU**. En **Select a MySQL SKU** (Seleccionar una SKU de MySQL), seleccione la SKU de su base de datos.

    ![Selección de una SKU de MySQL](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >A medida que se agregan servidores de hospedaje a Azure Stack, se les asigna una SKU. Las bases de datos se crean en el grupo de servidores de hospedaje de una SKU.

5. En **Login** (inicio de sesión), seleccione ***Configure required settings*** (Configurar valores necesarios).
6. En **Select a Login** (Seleccionar un inicio de sesión), puede elegir uno ya existente o seleccionar **+ Create a new login** (+ Crear un inicio de sesión) para configurar uno nuevo.  Escriba un nombre de **inicio de sesión de base de datos** y una **contraseña** y, luego, seleccione **OK** (Aceptar).

    ![Creación de un nuevo inicio de sesión en la base de datos](./media/azure-stack-mysql-rp-deploy/create-new-login.png)

    >[!NOTE]
    >La longitud del nombre de inicio de sesión de base de datos no puede superar los 32 caracteres en MySQL 5.7. En versiones anteriores, no puede superar los 16 caracteres.

7. Seleccione **Create** (Crear) para terminar de configurar la base de datos.

Una vez implementada la base de datos, anote la **cadena de conexión** que aparece debajo de **Essentials** (Información esencial). Puede usar esta cadena en cualquier aplicación que necesite acceder a la base de datos MySQL.

![Obtener la cadena de conexión para la base de datos MySQL](./media/azure-stack-mysql-rp-deploy/mysql-db-created.png)

## <a name="update-the-administrative-password"></a>Actualizar la contraseña administrativa

Puede modificar la contraseña cambiándola la primera vez en la instancia del servidor MySQL.

1. Seleccione **RECURSOS ADMINISTRATIVOS** > **MySQL Hosting Servers** (Servidores de hospedaje MySQL). Seleccione el servidor de hospedaje.
2. En **Settings** (Configuración), seleccione **Password** (Contraseña).
3. En **Password** (Contraseña), escriba la nueva contraseña y seleccione **Save** (Guardar).

![Actualización de la contraseña de administrador](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Pasos siguientes

[Actualización del proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-update.md)
