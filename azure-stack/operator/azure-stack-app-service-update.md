---
title: Actualización de Azure App Service en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo actualizar Azure App Service en Azure Stack.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 05/28/2019
ms.openlocfilehash: 768a6270021d6a87be3d2d28508288836ffadd29
ms.sourcegitcommit: 7d7a4c8c46613b6104caf23763bfd2275f6a826b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/09/2019
ms.locfileid: "70808259"
---
# <a name="update-azure-app-service-on-azure-stack"></a>Actualización de Azure App Service en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

> [!IMPORTANT]
> Aplique la actualización 1904 al sistema integrado de Azure Stack o implemente el Kit de desarrollo de Azure Stack más reciente antes de implementar Azure App Service 1.7.

En este artículo, se le mostrará cómo actualizar el [proveedor de recursos de App Service](azure-stack-app-service-overview.md) implementado en un entorno de Azure Stack conectado a Internet.

> [!IMPORTANT]
> Antes de ejecutar la actualización, asegúrese de que ya ha completado la [implementación de Azure App Service en Azure Stack](azure-stack-app-service-deploy.md). Debe leer también las [notas de la versión](azure-stack-app-service-release-notes-update-seven.md) que acompañan a la versión 1.7 para obtener información sobre las nuevas funciones, las correcciones y los problemas conocidos que podrían afectar a la implementación.

## <a name="run-the-app-service-resource-provider-installer"></a>Ejecución del instalador del proveedor de recursos de App Service

Durante este proceso, la actualización hará lo siguiente:

* Detectar la implementación anterior de App Service
* Preparar todos los paquetes de actualización y las nuevas versiones de todas las bibliotecas de OSS que se van a implementar
* Cargar en el almacenamiento
* Actualizar todos los roles de App Service (roles de controlador, administración, front-end, publicador y trabajo)
* Actualizar las definiciones de conjuntos de escalado de App Service
* Actualizar el manifiesto del proveedor de recursos de App Service

> [!IMPORTANT]
> El instalador de App Service se debe ejecutar en un equipo que pueda alcanzar el punto de conexión de Azure Resource Manager del administrador de Azure Stack.

Para actualizar la implementación de App Service en Azure Stack, siga estos pasos:

1. Descargue el [instalador de App Service](https://aka.ms/appsvcupdate7installer).

2. Ejecute appservice.exe como administrador.

    ![Instalador de App Service][1]

3. Haga clic en **Implementar App Service o actualizar a la última versión.**

4. Revise y acepte los términos de licencia del software de Microsoft y haga clic en **Siguiente**.

5. Revise y acepte los términos de licencia de terceros y haga clic en **Siguiente**.

6. Asegúrese de que la información del punto de conexión de Azure Resource Manager de Azure Stack y del inquilino de Active Directory es correcta. Si ha usado la configuración predeterminada durante la implementación del ASDK, puede aceptar los valores predeterminados. Sin embargo, si personalizó las opciones al implementar Azure Stack, debe editar los valores en esta ventana. Por ejemplo, si se usa el sufijo de dominio *mycloud.com*, el punto de conexión de Azure Resource Manager de Azure Stack debe cambiarse a *management.region.mycloud.com*. Después de confirmar la información, haga clic en **Siguiente**.

    ![Información de la nube de Azure Stack][2]

7. En la página siguiente:

   1. Haga clic en el botón **Conectar** situado junto al cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack).
        * Si se usa Azure Active Directory (Azure AD), escriba la cuenta de administrador y la contraseña de Azure AD que proporcionó al implementar Azure Stack. Haga clic en **Iniciar sesión**.
        * Si se usan los Servicios de federación de Active Directory (AD FS), debe proporcionar la cuenta de administrador. Por ejemplo, *cloudadmin\@azurestack.local*. Escriba la contraseña y haga clic en **Iniciar sesión**.
   2. En el cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack), seleccione **Default Provider Subscription** (Suscripción de proveedor predeterminada).
   3. En el cuadro **Azure Stack Locations** (Ubicaciones de Azure Stack), seleccione la ubicación que corresponda a la región en la que se va a implementar. Por ejemplo, seleccione **local** si va a realizar la implementación en el ASDK.
   4. Si se detecta una implementación existente de App Service, la cuenta de almacenamiento y el grupo de recursos se rellenan y quedan no disponibles.
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

Prepárese para realizar operaciones de administración adicionales para App Service en Azure Stack:

* [Planear la capacidad adicional](azure-stack-app-service-capacity-planning.md)
* [Agregar capacidad adicional](azure-stack-app-service-add-worker-roles.md)
