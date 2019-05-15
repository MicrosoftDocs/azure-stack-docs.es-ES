---
title: Actualización de Azure App Service en Azure Stack | Microsoft Docs
description: Guía detallada para actualizar Azure App Service en Azure Stack
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/18/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/18/2019
ms.openlocfilehash: bc379bfa93983c2e1579ebf657ce86558abc7d20
ms.sourcegitcommit: 2a4321a9cf7bef2955610230f7e057e0163de779
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/14/2019
ms.locfileid: "65618209"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Actualización de Azure App Service en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

> [!IMPORTANT]  
> Aplique la actualización 1901 al sistema integrado de Azure Stack o implemente el kit de desarrollo de Azure Stack más reciente antes de implementar Azure App Service 1.5.

Siguiendo las instrucciones de este artículo, puede actualizar el [proveedor de recursos de App Service](azure-stack-app-service-overview.md) implementado en un entorno de Azure Stack que esté conectado a Internet.

> [!IMPORTANT]  
> Antes de ejecutar la actualización, asegúrese de que se completó la [implementación de Azure App Service en el proveedor de recursos de Azure Stack](azure-stack-app-service-deploy.md) y de haber leído las [notas de la versión](azure-stack-app-service-release-notes-update-five.md), que acompañan a la versión 1.5, para obtener información sobre nuevas funciones, correcciones y problemas conocidos que podrían afectar a la implementación.

## <a name="run-the-app-service-resource-provider-installer"></a>Ejecución del instalador del proveedor de recursos de App Service

Durante este proceso, la actualización hará lo siguiente:

* Detectar la implementación anterior de App Service
* Preparar todos los paquetes de actualización y las nuevas versiones de todas las bibliotecas de OSS que se van a implementar
* Cargar en el almacenamiento
* Actualizar todos los roles de App Service (roles de controlador, administración, front-end, publicador y trabajo)
* Actualizar las definiciones de conjuntos de escalado de App Service
* Actualizar el manifiesto del proveedor de recursos de App Service

> [!IMPORTANT]
> El instalador de App Service se debe ejecutar en un equipo que pueda alcanzar el punto de conexión del administrador de Azure Resource Manager de Azure Stack.
>
>

Para actualizar la implementación de App Service en Azure Stack, siga estos pasos:

1. Descargue el [instalador de App Service](https://aka.ms/appsvcupdate5installer)

2. Ejecute appservice.exe como administrador

    ![Instalador de App Service][1]

3. Haga clic en **Implementar App Service o actualizar a la última versión.**

4. Revise y acepte los términos de licencia del software de Microsoft y haga clic en **Siguiente**.

5. Revise y acepte los términos de licencia de terceros y haga clic en **Siguiente**.

6. Asegúrese de que la información del punto de conexión de Azure Resource Manager de Azure Stack y el inquilino de Active Directory son correctos. Si usó la configuración predeterminada durante la implementación del Kit de desarrollo de Azure Stack, puede aceptar los valores predeterminados aquí. Sin embargo, si personalizó las opciones al implementar Azure Stack, debe editar los valores en esta ventana. Por ejemplo, si se usa el sufijo de dominio *mycloud.com*, el punto de conexión de Azure Resource Manager de Azure Stack debe cambiarse a *management.region.mycloud.com*. Después de confirmar su información, haga clic en **Siguiente**.

    ![Información de la nube de Azure Stack][2]

7. En la página siguiente:

   1. Haga clic en el botón **Conectar** situado junto al cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack).
        * Si se usa Azure Active Directory (Azure AD), escriba la cuenta de administrador y la contraseña de Azure AD que proporcionó al implementar Azure Stack. Haga clic en **Iniciar sesión**.
        * Si se usan los Servicios de federación de Active Directory (AD FS), debe proporcionar la cuenta de administrador. Por ejemplo, *cloudadmin\@azurestack.local*. Escriba la contraseña y haga clic en **Iniciar sesión**.
   2. En el cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack), seleccione **Default Provider Subscription** (Suscripción de proveedor predeterminada).
   3. En el cuadro **Azure Stack Locations** (Ubicaciones de Azure Stack), seleccione la ubicación que corresponda a la región en la que se va a implementar. Por ejemplo, seleccione **local** si va a implementar con el Kit de desarrollo de Azure Stack.
   4. Si se detecta una implementación existente de App Service, la cuenta de almacenamiento y el grupo de recursos se rellenan y quedan atenuados.
   5. Haga clic en **Siguiente** para revisar el resumen de la actualización.

      ![Instalación de App Service detectada][3]

8. En la página de resumen:
   1. Compruebe las selecciones realizadas. Para realizar cambios, use los botones **Anterior** para visitar las páginas anteriores.
   2. Si las configuraciones son correctas, active la casilla.
   3. Para iniciar la actualización, haga clic en **Siguiente**.

       ![Resumen de la actualización de App Service][4]

9. Página de progreso de la actualización:
    1. Realice un seguimiento del progreso de la actualización. La duración de la actualización de App Service en Azure Stack varía según el número de instancias de rol implementadas.
    2. Después de que la actualización finalice correctamente, haga clic en **Salir**.

        ![Progreso de la actualización de App Service][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Pasos siguientes

También puede probar otros [servicios de Plataforma como servicio (PaaS)](azure-stack-offer-services-overview.md).

* [Proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md)
* [Proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md)
