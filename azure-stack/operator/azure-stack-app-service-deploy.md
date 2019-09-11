---
title: Implementación de App Service en Azure Stack | Microsoft Docs
description: Aprenda a implementar App Service en Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/29/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: 7088a001fab8e3e618da4be8107f5a5f5a22373f
ms.sourcegitcommit: e2f6205e6469b39c2395ee09424bb7632cb94c40
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 09/04/2019
ms.locfileid: "70271771"
---
# <a name="deploy-app-service-in-azure-stack"></a>Implementación de App Service en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

En este artículo se describe cómo implementar App Service en Azure Stack.

> [!IMPORTANT]
> Aplique la actualización 1907 al sistema integrado de Azure Stack o implemente el Kit de desarrollo de Azure Stack (ASDK) más reciente antes de implementar Azure App Service 1.7.

Puede ofrecer a los usuarios la capacidad de crear aplicaciones web y API. Para permitir que los usuarios creen estas aplicaciones, deberá:

- Agregue el [proveedor de recursos de App Service](azure-stack-app-service-overview.md) a la implementación de Azure Stack mediante los pasos descritos en este artículo.
- Después de instalar el proveedor de recursos de App Service, puede incluirlos en sus planes y ofertas. A continuación, los usuarios pueden suscribirse para obtener el servicio y empezar a crear aplicaciones.

> [!IMPORTANT]
> Antes de ejecutar el instalador del proveedor de recursos, asegúrese de haber seguido las instrucciones de [Antes de empezar](azure-stack-app-service-before-you-get-started.md) y haber leído las [notas de la versión](azure-stack-app-service-release-notes-update-seven.md) que acompañan a la versión 1.7. Este contenido le ayudará a descubrir las nuevas funciones, correcciones y problemas conocidos que podrían afectar a la implementación.

## <a name="run-the-app-service-resource-provider-installer"></a>Ejecución del instalador del proveedor de recursos de App Service

La instalación del proveedor de recursos de App Service tarda al menos una hora. El período de tiempo necesario depende del número de instancias de rol que vaya a implementar. Durante la implementación, el instalador ejecuta las siguientes tareas:

- Creerá un contenedor de blobs en la cuenta de almacenamiento de Azure Stack especificada.
- Creará una zona DNS y las entradas para App Service.
- Registrará el proveedor de recursos de App Service.
- Registrará los elementos de la galería de App Service.

Para implementar el proveedor de recursos de App Service, siga estos pasos:

1. Ejecute appservice.exe como administrador desde un equipo que pueda acceder al punto de conexión de administración de recursos de Azure del administrador de Azure Stack.

2. Seleccione **Implementar App Service o actualizar a la última versión**.

    ![Instalador de App Service][1]

3. Revise y acepte los términos de licencia del software de Microsoft y seleccione **Siguiente**.

4. Revise y acepte los términos de licencia de terceros y seleccione **Siguiente**.

5. Asegúrese de que la información de configuración de nube de App Service es correcta. Si usó la configuración predeterminada durante la implementación del ASDK, puede aceptar los valores predeterminados. Pero, si personalizó las opciones cuando implementó el ASDK o va a realizar la implementación en un sistema integrado de Azure Stack, debe editar los valores de esta ventana para reflejar las diferencias.

   Por ejemplo, si se usa el sufijo de dominio mycloud.com, el punto de conexión de Azure Resource Manager del inquilino de Azure Stack debe cambiarse a management.&lt;región&gt;.mycloud.com. Revise estos valores y, a continuación, seleccione **Siguiente** para guardar la configuración.

   ![Instalador de App Service][2]

6. En la página siguiente del instalador de App Service, siga estos pasos:

    a. Seleccione **Conectar** situado junto al cuadro de **suscripciones de Azure Stack**.

   - Si se usa Azure Active Directory (Azure AD), escriba la cuenta de administrador y la contraseña de Azure AD que proporcionó al implementar Azure Stack. Seleccione **Iniciar sesión**.
   - Si se usan los Servicios de federación de Active Directory (AD FS), debe proporcionar la cuenta de administrador. Por ejemplo, cloudadmin@azurestack.local. Escriba la contraseña y seleccione **Iniciar sesión**.

   b. En el cuadro de **suscripciones de Azure Stack**, seleccione **Default Provider Subscription** (Suscripción de proveedor predeterminada).

     > [!IMPORTANT]
     > App Service **debe** implementarse en la **suscripción de proveedor predeterminada**.

   c. En **Azure Stack Locations** (Ubicaciones de Azure Stack), seleccione la ubicación que corresponda a la región en la que se va a implementar. Por ejemplo, seleccione **local** si va a realizar la implementación en el ASDK.

    ![Instalador de App Service][3]

7. Ahora puede realizar la implementación en una red virtual existente que haya configurado [siguiendo estos pasos](azure-stack-app-service-before-you-get-started.md#virtual-network), o dejar que el instalador de App Service cree una nueva red virtual y subredes. Para crear una red virtual, siga estos pasos:

   a. Seleccione **Crear una red virtual con la configuración predeterminada**, acepte los valores predeterminados y haga clic en **Siguiente**.

   b. O, seleccione **Usar la red virtual y las subredes existentes**. Complete las siguientes acciones:

     - Seleccione el **grupo de recursos** que contiene la red virtual.
     - Elija el nombre de la instancia de **Virtual Network** en la que desea realizar la implementación.
     - Seleccione los valores correctos de **subred** para cada una de las subredes de rol necesarias.
     - Seleccione **Next** (Siguiente).

   ![Instalador de App Service][4]

8. Escriba la información para el recurso compartido de archivos y, a continuación, seleccione **Siguiente**. La dirección del recurso compartido debe usar el nombre de dominio completo (FQDN) o la dirección IP del servidor de archivos. Por ejemplo, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, o \\\10.0.0.1\websites.  Si usa un servidor de archivos que está unido a un dominio, debe proporcionar el nombre de usuario completo, incluido el dominio. Por ejemplo, miDominioDeServidorDeArchivos\propietarioDeRecursoCompartido.

   >[!NOTE]
   >El instalador intenta comprobar la conectividad con el recurso compartido de archivos antes de continuar. Sin embargo, si implementa en una red virtual existente, puede que se produzca un error en esta prueba de conectividad. Se le envía una advertencia y un aviso para continuar. Si la información del recurso compartido de archivos es correcta, continúe con la implementación.

   ![Instalador de App Service][7]

9. En la página siguiente del instalador de App Service, siga estos pasos:

   a. En el cuadro **Identificador de la aplicación de identidad**, escriba el GUID de la aplicación que va a usar para la identidad (de Azure AD).

   b. En el cuadro **Identity Application certificate file** (Archivo del certificado de la aplicación de identidad) escriba (o vaya a) la ubicación del archivo del certificado.

   c. En el cuadro **Identity Application certificate password** (Contraseña del certificado de la aplicación de identidad), escriba la contraseña para el certificado. Esta contraseña es la que anotó cuando usó el script para crear los certificados.

   d. En el cuadro **Azure Resource Manager root certificate file** (Archivo del certificado raíz de Azure Resource Manager) escriba (vaya a) la ubicación del archivo del certificado.

   e. Seleccione **Next** (Siguiente).

   ![Instalador de App Service][9]

10. Para cada uno de los tres cuadros de archivo de certificado, seleccione **Examinar** y navegue hasta el archivo de certificado adecuado. Debe proporcionar la contraseña de cada certificado. Estos certificados son los que creó en el [paso para crear los certificados necesarios](azure-stack-app-service-before-you-get-started.md#get-certificates). Seleccione **Siguiente** después de escribir toda la información.

    | Box | Ejemplo de nombre de archivo de certificado |
    | --- | --- |
    | **Archivo de certificado SSL predeterminado de App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **Archivo de certificado SSL de API de App Service** | api.appservice.local.AzureStack.external.pfx |
    | **Archivo de certificado SSL del publicador de App Service** | ftp.appservice.local.AzureStack.external.pfx |

    Si usó un sufijo de dominio diferente cuando creó los certificados, los nombres de archivo de certificado no usan *local.AzureStack.external*. En su lugar, use la información del dominio personalizado.

    ![Instalador de App Service][10]

11. Escriba los detalles de SQL Server para la instancia de servidor que se usa para hospedar la base de datos del proveedor de recursos de App Service y, después, seleccione **Siguiente**. El instalador valida las propiedades de conexión de SQL.<br><br>El instalador de App Service intenta probar la conectividad con la instancia de SQL Server antes de continuar. Si realiza la implementación en una red virtual existente, puede que se produzca un error en esta prueba de conectividad. Se le envía una advertencia y un aviso para continuar. Si la información de SQL Server es correcta, continúe con la implementación.

    ![Instalador de App Service][11]

12. Revise las opciones de SKU y la instancia de rol. Los valores predeterminados se rellenan con el número mínimo de instancias y la SKU mínima de cada rol en una implementación de ASDK. Se proporciona un resumen de los requisitos de memoria y vCPU para ayudar a planear la implementación. Después de realizar las selecciones, elija **Siguiente**.

    >[!NOTE]
    >Para las implementaciones de producción, siga las instrucciones que encontrará en [Planeamiento de la capacidad de los roles de servidor de Azure App Service en Azure Stack](azure-stack-app-service-capacity-planning.md).

    | Rol | Número mínimo de instancias | SKU mínima | Notas |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A2: (2 vCPU, 3584 MB) | Administra y mantiene el estado de la nube de App Service. |
    | Administración | 1 | Standard_A2: (2 vCPU, 3584 MB) | Administra los puntos de conexión de API y Azure Resource Manager de App Service, las extensiones de portales (portal de Functions, administración e inquilino) y el servicio de datos. Para admitir la conmutación por error, se incrementan las instancias recomendadas a 2. |
    | Publicador | 1 | Standard_A1: (1 vCPU, 1792 MB) | Publica contenido a través de la implementación de web y FTP. |
    | FrontEnd | 1 | Standard_A1: (1 vCPU, 1792 MB) | Enruta las solicitudes a las aplicaciones de App Service. |
    | Trabajo compartido | 1 | Standard_A1: (1 vCPU, 1792 MB) | Hospeda aplicaciones de API o web y aplicaciones de Azure Functions. Puede ser conveniente agregar más instancias. Como operador, puede definir su oferta y elegir cualquier nivel de SKU. Los niveles deben tener una vCPU como mínimo. |

    ![Instalador de App Service][13]

    >[!NOTE]
    >**Windows Server 2016 Core no es una imagen de plataforma compatible para su uso con Azure App Service en Azure Stack.  No use imágenes de evaluación para las implementaciones de producción.**

13. En el cuadro **Seleccionar imagen de plataforma**, elija su imagen de máquina virtual (VM) Windows Server 2016 de implementación entre las que están disponibles en el proveedor de recursos de procesos para la nube de App Service. Seleccione **Next** (Siguiente).

14. En la página siguiente del instalador de App Service, siga estos pasos:

     a. Escriba el nombre de usuario y la contraseña del administrador de máquinas virtuales con rol de trabajo.

     b. Escriba el nombre de usuario y la contraseña del administrador de máquinas virtuales con otros roles.

     c. Seleccione **Next** (Siguiente).

    ![Instalador de App Service][15]

15. En la página de resumen del instalador de App Service, siga estos pasos:

    a. Compruebe las selecciones realizadas. Para realizar cambios, use los botones **Anterior** para visitar las páginas anteriores.

    b. Si las configuraciones son correctas, active la casilla.

    c. Para iniciar la implementación, seleccione **Siguiente**.

    ![Instalador de App Service][16]

16. En la página siguiente del instalador de App Service, siga estos pasos:

    a. Realice un seguimiento del progreso de la instalación. App Service en Azure Stack tarda aproximadamente 60 minutos en realizar la implementación según las selecciones predeterminadas.

    b. Después de que el instalador finalice correctamente, seleccione **Salir**.

    ![Instalador de App Service][17]

## <a name="post-deployment-steps"></a>Pasos posteriores a la implementación

> [!IMPORTANT]
> Si ha proporcionado el proveedor de recursos de App Service con una instancia de SQL Always On, **debe** [agregar las bases de datos appservice_hosting y appservice_metering a un grupo de disponibilidad](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database), así como sincronizar dichas bases de datos para evitar la pérdida de servicio en caso de producirse una conmutación por error de base de datos.

Si va a realizar la implementación en una red virtual existente y a usar una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. Esta regla habilita el tráfico SMB entre la subred de trabajo y el servidor de archivos. En el portal de administración vaya al grupo de seguridad de red WorkersNsg y agregue una regla de seguridad de salida con las siguientes propiedades:

- Origen: Any
- Intervalo de puertos de origen: *
- Destino: Direcciones IP
- Intervalo de direcciones IP de destino: Intervalo de direcciones IP del servidor de archivos
- Intervalo de puertos de destino: 445
- Protocolo: TCP
- Acción: Allow
- Prioridad: 700
- Nombre: Outbound_Allow_SMB445

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validación de la instalación de App Service en Azure Stack

1. En el Portal de administración de Azure Stack, vaya a **Administración - App Service**.

2. En la información general, debajo del estado, compruebe que en **Estado** se muestra el mensaje **Todos los roles están listos**.

    ![Administración de App Service](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack"></a>Prueba de App Service en Azure Stack

Después de implementar y registrar el proveedor de recursos de App Service, pruébelo para asegurarse de que los usuarios pueden implementar aplicaciones web y de API.

>[!NOTE]
>Debe crear una oferta que tenga el espacio de nombres Microsoft.Web en el plan. También necesita una suscripción de inquilino que se suscriba a la oferta. Para más información, consulte [Creación de una oferta](azure-stack-create-offer.md) y [Creación de un plan](azure-stack-create-plan.md).
>
>*Debe* tener una suscripción de inquilino para crear aplicaciones que usen App Service en Azure Stack. Las únicas tareas que un administrador del servicio puede completar en el portal de administración están relacionadas con la administración del proveedor de recursos de App Service. Entre estas tareas se incluyen la adición de capacidad, la configuración de orígenes de implementación y la adición de niveles de trabajo y SKU.
>
>Para crear aplicaciones web, de API y Azure Functions, se debe usar el portal de inquilino y tener una suscripción de inquilino.
>

Para crear una aplicación web de prueba, siga estos pasos:

1. En el portal de usuario de Azure Stack, seleccione **+ Crear un recurso** > **Web y móvil** > **Aplicación web**.

2. En **Aplicación web**, escriba un nombre en **Aplicación web**.

3. En **Grupo de recursos**, seleccione **Nuevo**. Escriba un nombre para el **grupo de recursos**.

4. Seleccione **Plan de App Service/Ubicación** > **Crear nuevo**.

5. En **plan de App Service**, escriba un nombre para el **plan de App Service**.

6. Seleccione **Plan de tarifa** > **Free-Shared** (Gratis - Compartido) o **Shared-Shared** (Compartido - Compartido) > **Seleccionar** > **Aceptar** > **Crear**.

7. Aparecerá un icono para la nueva aplicación web en el panel. Seleccione el icono.

8. En la hoja **Aplicación web**, seleccione **Examinar** para ver el sitio web predeterminado para esta aplicación.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implementación de un sitio web de WordPress, DNN o Django (opcional)

1. En el portal del inquilino de Azure Stack, seleccione **+** , vaya a Azure Marketplace, implemente un sitio web de Django y, a continuación, espere a que finalice la implementación. La plataforma web de Django usa una base de datos basada en el sistema de archivos. No se necesita ningún proveedor de recursos adicional, como SQL o MySQL.

2. Si también implementó un proveedor de recursos de MySQL, puede implementar un sitio web de WordPress en Marketplace. Cuando se le pidan los parámetros de la base de datos, escriba el nombre de usuario, como *Usario1\@Servidor1*, con el nombre de usuario y el nombre del servidor de su elección.

3. Si también implementó un proveedor de recursos de SQL Server, puede implementar un sitio web de DNN en Marketplace. Cuando se le pidan los parámetros de la base de datos, elija una base de datos del equipo que ejecute la instancia de SQL Server que está conectado a su proveedor de recursos.

## <a name="next-steps"></a>Pasos siguientes

Prepárese para realizar operaciones de administración adicionales para App Service en Azure Stack:

- [Planeamiento de capacidad](azure-stack-app-service-capacity-planning.md)
- [Configuración de orígenes de implementación](azure-stack-app-service-configure-deployment-sources.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy/app-service-custom-VNET-config-with-values.png
[7]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[8]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration-error.png
[9]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[10]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[11]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[12]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration-error.png
[13]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[14]: ./media/azure-stack-app-service-deploy/app-service-windows-image-selection.png
[15]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[16]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[17]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png
