---
title: Creación de bases de datos MySQL en Azure Stack Hub
description: Aprenda a crear y administrar bases de datos MySQL aprovisionadas con el proveedor de recursos del adaptador de MySQL en Azure Stack Hub.
author: mattbriggs
ms.topic: article
ms.date: 1/22/2020
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 5955dcf6c211f9f431d84227548496af7782a0d7
ms.sourcegitcommit: fd5d217d3a8adeec2f04b74d4728e709a4a95790
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/29/2020
ms.locfileid: "76881849"
---
# <a name="create-mysql-databases-in-azure-stack-hub"></a>Creación de bases de datos MySQL en Azure Stack Hub
Un usuario de Azure Stack Hub que esté suscrito a una oferta que incluya el servicio de base de datos MySQL puede crear y administrar bases de datos MySQL de autoservicio en el portal de usuarios.

## <a name="create-a-mysql-database"></a>Creación de una base de datos MySQL

1. Inicie sesión en el portal de usuarios de Azure Stack Hub.
2. Seleccione **+ Crear un recurso** > **Datos y almacenamiento** > **Base de datos MySQL** > **Agregar**.
3. En **Create MySQL Database** (Crear base de datos MySQL), escriba el nombre de la base de datos y configure los valores necesarios para su entorno.

    ![Crear una base de datos MySQL de prueba](./media/azure-stack-mysql-rp-deploy/mysql-create-db.png)

4. En **Create Database** (Crear base de datos), seleccione **SKU**. En **Select a MySQL SKU** (Seleccionar una SKU de MySQL), seleccione la SKU de su base de datos.

    ![Selección de una SKU de MySQL](./media/azure-stack-mysql-rp-deploy/mysql-select-sku.png)

    >[!Note]
    >A medida que se agregan servidores de hospedaje a Azure Stack Hub, se les asigna una SKU. Las bases de datos se crean en el grupo de servidores de hospedaje de una SKU.

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
2. En **Configuración**, seleccione **Contraseña**.
3. En **Password** (Contraseña), escriba la nueva contraseña y seleccione **Save** (Guardar).

![Actualización de la contraseña de administrador](./media/azure-stack-mysql-rp-deploy/mysql-update-password.png)

## <a name="next-steps"></a>Pasos siguientes

Obtenga información sobre cómo [ofrecer bases de datos MySQL de alta disponibilidad](azure-stack-tutorial-mysql.md).
