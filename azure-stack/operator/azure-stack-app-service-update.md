---
title: Actualización de Azure App Service en Azure Stack Hub
description: Obtenga información sobre cómo actualizar Azure App Service en Azure Stack Hub.
author: BryanLa
ms.topic: article
ms.date: 01/13/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/13/2019
ms.openlocfilehash: 07cf0762a87fbf0cea490d246e6e31e003fd7275
ms.sourcegitcommit: a7db4594de43c31fe0c51e60e84fdaf4d41ef1bb
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 02/24/2020
ms.locfileid: "77568612"
---
# <a name="update-azure-app-service-on-azure-stack-hub"></a>Actualización de Azure App Service en Azure Stack Hub

> [!IMPORTANT]
> Aplique la actualización 1910 al sistema integrado de Azure Stack Hub o implemente el Kit de desarrollo de Azure Stack (ASDK) más reciente antes de implementar Azure App Service 1.8.

En este artículo, se le mostrará cómo actualizar el [proveedor de recursos de Azure App Service](azure-stack-app-service-overview.md) implementado en un entorno de Azure Stack Hub conectado a Internet.

> [!IMPORTANT]
> Antes de ejecutar la actualización, asegúrese de que ya ha completado la [implementación de Azure App Service en Azure Stack Hub](azure-stack-app-service-deploy.md). Debe leer también las [notas de la versión](azure-stack-app-service-release-notes-update-eight.md) que acompañan a la versión 1.8 para obtener información sobre las nuevas funcionalidades, las correcciones y los problemas conocidos que podrían afectar a la implementación.

## <a name="run-the-azure-app-service-resource-provider-installer"></a>Ejecución del instalador del proveedor de recursos de Azure App Service

Durante este proceso, la actualización hará lo siguiente:

* Detectar la implementación anterior de Azure App Service.
* Preparar todos los paquetes de actualización y las nuevas versiones de todas las bibliotecas de OSS que se van a implementar
* Cargar en el almacenamiento
* Actualizar todos los roles de Azure App Service (roles de controlador, administración, front-end, publicador y trabajo).
* Actualizar las definiciones de conjuntos de escalado de Azure App Service.
* Actualizar el manifiesto del proveedor de recursos de Azure App Service.

> [!IMPORTANT]
> El instalador de Azure App Service se debe ejecutar en un equipo que pueda alcanzar el punto de conexión del administrador de Azure Resource Manager de Azure Stack Hub.

Para actualizar la implementación de Azure App Service en Azure Stack Hub, siga estos pasos:

1. Descargue el [instalador de Azure App Service](https://aka.ms/appsvcupdate8installer).

2. Ejecute appservice.exe como administrador.

    ![Instalador de Azure App Service][1]

3. Haga clic en **Implementar Azure App Service o actualizar a la última versión.**

4. Revise y acepte los términos de licencia del software de Microsoft y haga clic en **Siguiente**.

5. Revise y acepte los términos de licencia de terceros y haga clic en **Siguiente**.

6. Asegúrese de que la información del punto de conexión de Azure Resource Manager de Azure Stack Hub y del inquilino de Active Directory es correcta. Si ha usado la configuración predeterminada durante la implementación del ASDK, puede aceptar los valores predeterminados. Sin embargo, si personalizó las opciones al implementar Azure Stack Hub, debe editar los valores en esta ventana. Por ejemplo, si se usa el sufijo de dominio *mycloud.com*, el punto de conexión de Azure Resource Manager de Azure Stack Hub se debe cambiar a *management.region.mycloud.com*. Después de confirmar la información, haga clic en **Siguiente**.

    ![Información de la nube de Azure Stack Hub][2]

7. En la página siguiente:

    1. Seleccione el método de conexión que quiera usar: **Credencial** o **Entidad de servicio**
        - **Credential:**
            - Si usa Azure Active Directory (Azure AD), escriba la cuenta de administrador y la contraseña de Azure AD que proporcionó al implementar Azure Stack Hub. Seleccione **Conectar**.
            - Si se usan los Servicios de federación de Active Directory (AD FS), debe proporcionar la cuenta de administrador. Por ejemplo, cloudadmin@azurestack.local. Escriba la contraseña y, a continuación, seleccione **Conectar**.
        - **Entidad de seguridad de servicio**
            - La entidad de servicio que utilice **debe** tener derechos de **Propietario** en la **suscripción de proveedor predeterminada**.
            - Proporcione el **identificador de entidad de servicio**, el **archivo de certificado** y la **contraseña** y seleccione **Conectar**.

    1. En **Suscripciones de Azure Stack Hub**, seleccione la **suscripción de proveedor predeterminada**.    Azure App Service en Azure Stack Hub **debe** implementarse en la **suscripción de proveedor predeterminada**.

    1. En **Ubicaciones de Azure Stack Hub**, seleccione la ubicación que corresponda a la región en la que se va a implementar. Por ejemplo, seleccione **local** si va a realizar la implementación en el ASDK.

    1. Si se detecta una implementación existente de Azure App Service, la cuenta de almacenamiento y el grupo de recursos se rellenan y quedan no disponibles.

      ![Instalación de Azure App Service detectada][3]

8. En la página de resumen:
   1. Compruebe las selecciones realizadas. Para realizar cambios, use los botones **Anterior** para visitar las páginas anteriores.
   2. Si las configuraciones son correctas, active la casilla.
   3. Para iniciar la actualización, haga clic en **Siguiente**.

       ![Resumen de la actualización de Azure App Service][4]

9. Página de progreso de la actualización:
    1. Realice un seguimiento del progreso de la actualización. La duración de la actualización de Azure App Service en Azure Stack Hub varía según el número de instancias de rol implementadas.
    2. Después de que la actualización finalice correctamente, haga clic en **Salir**.

        ![Progreso de la actualización de Azure App Service][5]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update/app-service-exe.png
[2]: ./media/azure-stack-app-service-update/app-service-azure-resource-manager-endpoints.png
[3]: ./media/azure-stack-app-service-update/app-service-installation-detected.png
[4]: ./media/azure-stack-app-service-update/app-service-upgrade-summary.png
[5]: ./media/azure-stack-app-service-update/app-service-upgrade-complete.png

## <a name="next-steps"></a>Pasos siguientes

Prepárese para realizar operaciones de administración adicionales para App Service en Azure Stack Hub:

* [Planear la capacidad adicional](azure-stack-app-service-capacity-planning.md)
* [Agregar capacidad adicional](azure-stack-app-service-add-worker-roles.md)
