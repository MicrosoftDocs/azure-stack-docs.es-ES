---
title: Bases de datos SQL disponibles para los usuarios de Azure Stack | Microsoft Docs
description: Tutorial para instalar al proveedor de recursos de SQL Server y crear ofertas que permiten a los usuarios de Azure Stack crear bases de datos SQL.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: mabrigg
ms.reviewer: quying
ms.lastreviewed: 11/05/2018
ms.custom: mvc
ms.openlocfilehash: e65bebf5b6126455ef1294fabeb1450f91689d5d
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618369"
---
# <a name="tutorial-make-sql-databases-available-to-your-azure-stack-users"></a>Tutorial: poner bases de datos SQL a disposición de los usuarios de Azure Stack

Como administrador de la nube de Azure Stack, puede crear ofertas que permitan a los usuarios (inquilinos) crear bases de datos SQL que se puedan usar con sus aplicaciones en la nube, sitios web y cargas de trabajo. Si proporciona a los usuarios estas bases de datos personalizadas, en la nube y a petición, puede ahorrarles tiempo y recursos. Para configurar esta opción, tendrá que:

> [!div class="checklist"]
> * Implementar un proveedor de recursos de SQL Server
> * Creación de una oferta
> * Probar la oferta

## <a name="deploy-the-sql-server-resource-provider"></a>Implementar un proveedor de recursos de SQL Server

El proceso de implementación se describe con todo detalle en el artículo [Uso de bases de datos SQL en Microsoft Azure Stack](azure-stack-sql-resource-provider-deploy.md) y consta de los siguientes pasos principales:

1. [Implemente el proveedor de recursos SQL](azure-stack-sql-resource-provider-deploy.md).
2. [Compruebe la implementación](azure-stack-sql-resource-provider-deploy.md#verify-the-deployment-using-the-azure-stack-portal).
3. Aprovisionamiento de capacidad conectando con un servidor SQL de hospedaje Para más información, consulte [Incorporación de servidores de hospedaje](azure-stack-sql-resource-provider-hosting-servers.md)

## <a name="create-an-offer"></a>Creación de una oferta

1.  [Establezca una cuota](azure-stack-plan-offer-quota-overview.md ) y asígnele el nombre *CuotaDeSQLServer*. Seleccione **Microsoft.SQLAdapter** para el campo **Espacio de nombres**.
2.  [Cree un plan](azure-stack-create-plan.md). Asígnele el nombre *PlanDePruebaDeSQLServer*, seleccione el servicio **Microsoft.SQLAdapter** y la cuota **CuotaDeSQLServer**.

    > [!NOTE]
    > Para permitir que los usuarios creen otras aplicaciones, podrían ser necesarios otros servicios en el plan. Por ejemplo, Azure Functions requiere el servicio **Microsoft.Storage** en el plan, mientras que Wordpress requiere **Microsoft.MySQLAdapter**.

3.  [Cree una oferta](azure-stack-create-offer.md), asígnele el nombre **OfertaDePruebaDeSQLServer** y seleccione el plan **PlanDePruebaDeSQLServer**.

## <a name="test-the-offer"></a>Probar la oferta

Ahora que ha implementado el proveedor de recursos de SQL Server y ha creado una oferta, puede iniciar sesión como un usuario, suscribirse a la oferta y crear una base de datos.

### <a name="subscribe-to-the-offer"></a>Suscripción a la oferta

1. Inicie sesión en el portal de Azure Stack (https://portal.local.azurestack.external)) como inquilino.
2. Seleccione **Obtener una suscripción** y, después, escriba **TestSQLServerSubscription** en **Nombre para mostrar**.
3. Seleccione **Seleccionar una oferta** > **TestSQLServerOffer** > **Crear**.
4. Seleccione **Todos los servicios** > **Suscripciones** > **TestSQLServerSubscription** > **Proveedores de recursos**.
5. Seleccione **Registrar** junto al proveedor **Microsoft.SQLAdapter**.

### <a name="create-a-sql-database"></a>Creación de una base de datos SQL

1. Seleccione **+** > **Datos y almacenamiento** > **SQL Database**.
2. Mantenga los valores predeterminados o use estos ejemplos en los campos siguientes:
    - **Nombre de base de datos**: SQLdb
    - **Tamaño máximo en MB**: 100
    - **Suscripción**: TestSQLOffer
    - **Grupo de recursos**: SQL-RG
3. Seleccione **Login Settings** (Configuración de inicio de sesión), escriba las credenciales de la base de datos y, a continuación, seleccione **Aceptar**.
4. Seleccione **SKU** > seleccione la SKU de SQL que ha creado para el servidor de hospedaje SQL > y, después, seleccione **Aceptar**.
5. Seleccione **Crear**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Implementar un proveedor de recursos de SQL Server
> * Creación de una oferta
> * Probar la oferta

Prosiga con el siguiente tutorial para aprender a:

> [!div class="nextstepaction"]
> [Aplicaciones web, móviles y de API disponibles para los usuarios]( azure-stack-tutorial-app-service.md)