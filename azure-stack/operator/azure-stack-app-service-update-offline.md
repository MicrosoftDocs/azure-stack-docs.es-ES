---
title: Actualización de Azure App Service sin conexión | Microsoft Docs
description: Guía detallada para actualizar Azure App Service en Azure Stack Hub sin conexión
services: azure-stack
documentationcenter: ''
author: bryanla
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/13/2020
ms.author: anwestg
ms.reviewer: anwestg
ms.openlocfilehash: d0cfc906030355dac78da3561da2b746a9a0dc92
ms.sourcegitcommit: e47dc5fe9e59010ea3dbb9cb31abe15cfb821262
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/16/2020
ms.locfileid: "76124827"
---
# <a name="offline-update-of-azure-app-service-on-azure-stack-hub"></a>Actualización sin conexión de Azure App Service en Azure Stack Hub

> [!IMPORTANT]
> Aplique la actualización 1910 o posterior al sistema integrado de Azure Stack Hub o implemente el kit de desarrollo de Azure Stack Hub más reciente antes de implementar Azure App Service 1.8.

Siguiendo las instrucciones de este artículo, puede actualizar el [proveedor de recursos de Azure App Service](azure-stack-app-service-overview.md) implementado en un entorno de Azure Stack Hub que:

* No esté conectado a Internet
* Esté protegido por Servicios de federación de Active Directory (AD FS) 2.0.

> [!IMPORTANT]
> Antes de ejecutar la actualización, asegúrese de que se completó la [implementación de Azure App Service en el proveedor de recursos de Azure Stack Hub](azure-stack-app-service-deploy-offline.md) y de haber leído las [notas de la versión](azure-stack-app-service-release-notes-update-eight.md) que acompañan a la versión 1.8, para obtener información sobre nuevas funcionalidades, correcciones y problemas conocidos que podrían afectar a la implementación.

## <a name="run-the-app-service-resource-provider-installer"></a>Ejecución del instalador del proveedor de recursos de App Service

Para actualizar el proveedor de recursos de App Service en un entorno de Azure Stack Hub, debe completar estas tareas:

1. Descargue el [instalador de Azure App Service](https://aka.ms/appsvcupdate8installer).
2. Cree un paquete de actualización sin conexión.
3. Ejecute el instalador de App Service (appservice.exe) y realice la actualización.

Durante este proceso, la actualización hará lo siguiente:

* Detectar la implementación anterior de App Service
* Cargar en el almacenamiento
* Actualizar todos los roles de App Service (roles de controlador, administración, front-end, publicador y trabajo)
* Actualizar las definiciones de conjuntos de escalado de App Service
* Actualizar el manifiesto del proveedor de recursos de App Service

## <a name="create-an-offline-upgrade-package"></a>Crear un paquete de actualización sin conexión

Para actualizar App Service en un entorno desconectado, primero debe crear un paquete de actualización sin conexión en una máquina que esté conectada a Internet.

1. Ejecute appservice.exe como administrador

    ![Instalador de Azure App Service][1]

2. Haga clic en **Avanzado** > **Crear paquete sin conexión**

    ![Instalador avanzado de Azure App Service][2]

3. El instalador de Azure App Service crea un paquete de actualización sin conexión y muestra su ruta de acceso.  Puede hacer clic en **Abrir carpeta** para abrir la carpeta en el Explorador de archivos.

4. Copie el instalador (AppService.exe) y el paquete de actualización sin conexión en la máquina host de Azure Stack Hub.

## <a name="complete-the-upgrade-of-app-service-on-azure-stack-hub"></a>Realización de la actualización de App Service en Azure Stack Hub

> [!IMPORTANT]
> El instalador de Azure App Service se debe ejecutar en un equipo que pueda alcanzar el punto de conexión del administrador de Azure Resource Manager de Azure Stack Hub.

1. Ejecute appservice.exe como administrador.

    ![Instalador de Azure App Service][1]

2. Haga clic en **Avanzado** > **Completar la instalación o actualización sin conexión**.

    ![Instalador avanzado de Azure App Service][2]

3. Vaya a la ubicación del paquete de actualización sin conexión que creó previamente y haga clic en **Siguiente**.

4. Revise y acepte los términos de licencia del software de Microsoft y haga clic en **Siguiente**.

5. Revise y acepte los términos de licencia de terceros y haga clic en **Siguiente**.

6. Asegúrese de que la información del punto de conexión de Azure Resource Manager de Azure Stack Hub y el inquilino de Active Directory son correctos. Si usó la configuración predeterminada durante la implementación del Kit de desarrollo de Azure Stack Hub, puede aceptar los valores predeterminados aquí. Sin embargo, si personalizó las opciones al implementar Azure Stack Hub, debe editar los valores en esta ventana. Por ejemplo, si se usa el sufijo de dominio *mycloud.com*, el punto de conexión de Azure Resource Manager de Azure Stack Hub se debe cambiar a *management.region.mycloud.com*. Después de confirmar su información, haga clic en **Siguiente**.

    ![Información de la nube de Azure Stack Hub][3]

7. En la página siguiente:

   1. Seleccione el método de conexión que quiera usar: **Credencial** o **Entidad de servicio**
        - **Credential:**
            - Si usa Azure Active Directory (Azure AD), escriba la cuenta de administrador y la contraseña de Azure AD que proporcionó al implementar Azure Stack Hub. Seleccione **Conectar**.
            - Si se usan los Servicios de federación de Active Directory (AD FS), debe proporcionar la cuenta de administrador. Por ejemplo, cloudadmin@azurestack.local. Escriba la contraseña y, a continuación, seleccione **Conectar**.
        - **Entidad de seguridad de servicio**
            - La entidad de servicio que utilice **debe** tener derechos de **Propietario** en la **suscripción de proveedor predeterminada**.
            - Proporcione el **identificador de entidad de servicio**, el **archivo de certificado** y la **contraseña** y seleccione **Conectar**.

   1. En **Suscripciones de Azure Stack Hub**, seleccione la **suscripción de proveedor predeterminada**.  Azure App Service en Azure Stack Hub **debe** implementarse en la **suscripción de proveedor predeterminada**.

   1. En **Ubicaciones de Azure Stack Hub**, seleccione la ubicación que corresponda a la región en la que se va a implementar. Por ejemplo, seleccione **local** si va a realizar la implementación en el ASDK.
   
   1. Si se detecta una implementación existente de App Service, la cuenta de almacenamiento y el grupo de recursos se rellenan y quedan atenuados.

      ![Instalación de Azure App Service detectada][4]
8. En la página de resumen:
   1. Compruebe las selecciones realizadas. Para realizar cambios, use los botones **Anterior** para visitar las páginas anteriores.
   2. Si las configuraciones son correctas, active la casilla.
   3. Para iniciar la actualización, haga clic en **Siguiente**.

       ![Resumen de la actualización de Azure App Service][5]

9. Página de progreso de la actualización:
    1. Realice un seguimiento del progreso de la actualización. La duración de la actualización de App Service en Azure Stack Hub varía según el número de instancias de rol implementadas.
    2. Después de que la actualización finalice correctamente, haga clic en **Salir**.

        ![Progreso de la actualización de Azure App Service][6]

<!--Image references-->
[1]: ./media/azure-stack-app-service-update-offline/app-service-exe.png
[2]: ./media/azure-stack-app-service-update-offline/app-service-exe-advanced.png
[3]: ./media/azure-stack-app-service-update-offline/app-service-azure-resource-manager-endpoints.png
[4]: ./media/azure-stack-app-service-update-offline/app-service-installation-detected.png
[5]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-summary.png
[6]: ./media/azure-stack-app-service-update-offline/app-service-upgrade-complete.png

## <a name="next-steps"></a>Pasos siguientes

Preparación de las operaciones adicionales del administrador para Azure App Service en Azure Stack Hub

* [Planear la capacidad adicional](azure-stack-app-service-capacity-planning.md)
* [Agregar capacidad adicional](azure-stack-app-service-add-worker-roles.md)
