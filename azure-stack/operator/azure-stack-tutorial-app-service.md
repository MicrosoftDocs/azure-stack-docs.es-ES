---
title: Aplicaciones web y API disponibles para los usuarios de Azure Stack | Microsoft Docs
description: Tutorial para instalar el proveedor de recursos de App Service y crear ofertas que permitan a los usuarios de Azure Stack crear aplicaciones web y de API.
services: azure-stack
documentationcenter: ''
author: justinha
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/11/2019
ms.author: justinha
ms.reviewer: anwestg
ms.custom: mvc
ms.lastreviewed: 11/05/2018
ms.openlocfilehash: fbf6993447f22a354c61102b9e670c8f1188f48d
ms.sourcegitcommit: 797dbacd1c6b8479d8c9189a939a13709228d816
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/28/2019
ms.locfileid: "66268090"
---
# <a name="tutorial-make-web-and-api-apps-available-to-your-azure-stack-users"></a>Tutorial: Aplicaciones web y aplicaciones de API disponibles para los usuarios de Azure Stack

Como administrador en la nube de Azure Stack, puede crear ofertas que permitan a los usuarios (inquilinos) crear aplicaciones web, móviles y de API, así como de Azure Functions. Si proporciona a los usuarios acceso a estas aplicaciones en la nube y a petición, puede ahorrarles tiempo y recursos.

Para configurar esta opción, tendrá que:

> [!div class="checklist"]
> * Implementar el proveedor de recursos de App Service
> * Creación de una oferta
> * Probar la oferta

## <a name="deploy-the-app-service-resource-provider"></a>Implementar el proveedor de recursos de App Service

1. [Prepare el host del Kit de desarrollo de Azure Stack](azure-stack-app-service-before-you-get-started.md). Esto incluye implementar el proveedor de recursos de SQL Server, que se requiere para crear algunas aplicaciones.
2. [Descargue los scripts de aplicación auxiliar y del instalador](azure-stack-app-service-deploy.md).
3. [Ejecute el script de aplicación auxiliar para crear los certificados necesarios](azure-stack-app-service-deploy.md).
4. [Instale el proveedor de recursos de App Service](azure-stack-app-service-deploy.md) (tardará un par de horas en instalarse y que todos los roles de trabajo aparezcan).
5. [Valide la instalación](azure-stack-app-service-deploy.md#validate-the-app-service-on-azure-stack-installation).

## <a name="create-an-offer"></a>Creación de una oferta

Por ejemplo, puede crear una oferta que permita a los usuarios crear sistemas de administración de contenido web DNN. Requiere el servicio de SQL Server que ya habilitó al instalar al proveedor de recursos de SQL Server.

1.  [Establezca una cuota](azure-stack-plan-offer-quota-overview.md ) y asígnele el nombre *CuotaDeAppService*. Seleccione **Microsoft.Web** para el campo **Espacio de nombres**.
2.  [Cree un plan](azure-stack-create-plan.md). Asígnele el nombre *PlanDePruebaDeAppService*, seleccione el servicio **Microsoft.SQL** y la cuota **AppService Quota** (Cuota de AppService).

    > [!NOTE]
    > Para permitir que los usuarios creen otras aplicaciones, podrían ser necesarios otros servicios en el plan. Por ejemplo, Azure Functions requiere que el plan incluya el servicio **Microsoft.Storage**, mientras que Wordpress requiere **Microsoft.MySQL**.

3.  [Cree una oferta](azure-stack-create-offer.md), asígnele el nombre **OfertaDePruebaDeAppService** y seleccione el plan **PlanDePruebaDeAppService**.

## <a name="test-the-offer"></a>Probar la oferta

Ahora que ha implementado el proveedor de recursos de App Service y ha creado una oferta, puede iniciar sesión como un usuario, suscribirse a la oferta y crear una aplicación.

En este ejemplo, vamos a crear un sistema de administración de contenido de la plataforma DNN. Primero, cree una base de datos SQL y, a continuación, la aplicación web DNN.

### <a name="subscribe-to-the-offer"></a>Suscripción a la oferta

1. Inicie sesión en el portal de Azure Stack (https://portal.local.azurestack.external)) como inquilino.
2. Seleccione **Obtener una suscripción** >, escriba **SuscripciónDePruebaDeAppService** en **Nombre para mostrar** > **Seleccionar una oferta** > **OfertaDePruebaDeAppService** > **Crear**.

### <a name="create-a-sql-database"></a>Creación de una base de datos SQL

1. Seleccione **+**  > **Datos y almacenamiento** > **SQL Database**.
2. Mantenga los valores predeterminados, excepto para los campos siguientes:

    - **Nombre de base de datos**: DNNdb
    - **Tamaño máximo en MB**: 100
    - **Suscripción**: TestAppServiceOffer
    - **Grupo de recursos**: DNN-RG

3. Seleccione **Login Settings** (Configuración de inicio de sesión), escriba las credenciales de la base de datos y, a continuación, seleccione **Aceptar**. Usará estas credenciales más adelante en este tutorial.
4. En **SKU** > seleccione la SKU de SQL que ha creado para el servidor de hospedaje SQL > y, después, seleccione **Aceptar**.
5. Seleccione **Crear**.

### <a name="create-a-dnn-app"></a>Creación de una aplicación DNN

1. Seleccione **+**  > **Ver todo** > **DNN Platform preview** (Versión preliminar de la plataforma DNN) > **Crear**.
2. Escriba *AppDNN* en **Nombre de la aplicación** y seleccione **OfertaDePruebaDeAppService** en **Suscripción**.
3. Seleccione **Configurar los valores obligatorios** > **Crear nuevo** > para escribir un nombre de **Plan de App Service**.
4. Seleccione **Plan de tarifa** > **F1 gratuito** > **Seleccionar** > **Aceptar**.
5. Seleccione **Base de datos** y escriba las credenciales de la base de datos SQL que creó anteriormente.
6. Seleccione **Crear**.

## <a name="next-steps"></a>Pasos siguientes

En este tutorial aprendió lo siguiente:

> [!div class="checklist"]
> * Implementar el proveedor de recursos de App Service
> * Creación de una oferta
> * Probar la oferta

Prosiga con el siguiente tutorial para aprender a:

> [!div class="nextstepaction"]
> [Implementar aplicaciones en Azure y Azure Stack](../user/azure-stack-solution-pipeline.md)
