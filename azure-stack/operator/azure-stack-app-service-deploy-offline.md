---
title: Implementación de App Service en un entorno sin conexión en Azure Stack | Microsoft Docs
description: Aprenda a implementar App Service en un entorno de Azure Stack sin conexión protegido por AD FS.
services: azure-stack
documentationcenter: ''
author: BryanLa
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
ms.openlocfilehash: 25cb846b8e3a2200636dff0f2acfc25244a5e3d4
ms.sourcegitcommit: e8aa26b078a9bab09c8fafd888a96785cc7abb4d
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/01/2019
ms.locfileid: "71709017"
---
# <a name="deploy-app-service-in-an-offline-environment-in-azure-stack"></a>Implementación de App Service en un entorno sin conexión en Azure Stack

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

> [!IMPORTANT]
> Aplique la actualización 1907 al sistema integrado de Azure Stack o implemente el Kit de desarrollo de Azure Stack (ASDK) más reciente antes de implementar Azure App Service 1.7.

Siguiendo las instrucciones de este artículo, puede instalar el [proveedor de recursos de App Service](azure-stack-app-service-overview.md) en un entorno de Azure Stack que:

- No esté conectado a Internet.
- Esté protegido por Servicios de federación de Active Directory (AD FS).

> [!IMPORTANT]
> Antes de ejecutar el instalador del proveedor de recursos, asegúrese de que ha completado los pasos de los [requisitos previos para implementar App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md). Debe leer también las [notas de la versión](azure-stack-app-service-release-notes-update-seven.md) que acompañan a la versión 1.7 para obtener información sobre las nuevas funciones, las correcciones y los problemas conocidos que podrían afectar a la implementación.

Para agregar el proveedor de recursos de App Service para la implementación de Azure Stack sin conexión, debe completar estas tareas de nivel superior:

1. Realice los [pasos previos necesarios](azure-stack-app-service-before-you-get-started.md) (como la compra de certificados, que pueden tardar unos días en recibirse).
2. [Descargue y extraiga los archivos auxiliares y de instalación](azure-stack-app-service-before-you-get-started.md) en una máquina conectada a Internet.
3. Cree un paquete de instalación sin conexión.
4. Ejecute el archivo del instalador appservice.exe.

## <a name="create-an-offline-installation-package"></a>Creación de un paquete de instalación sin conexión

Para implementar App Service en un entorno sin conexión, primero debe crear un paquete de instalación sin conexión en una máquina que esté conectada a Internet.

1. Ejecute el instalador AppService.exe en una máquina que esté conectada a Internet.

2. Seleccione **Opciones avanzadas** > **Crear paquete de instalación sin conexión**.

    ![Creación de un paquete sin conexión en el instalador de App Service][1]

3. El instalador de App Service crea un paquete de instalación sin conexión y muestra su ruta de acceso. Puede seleccionar **Abrir carpeta** para abrir la carpeta en el Explorador de archivos.

    ![El paquete de instalación sin conexión generado correctamente en el instalador de App Service](media/azure-stack-app-service-deploy-offline/image02.png)

4. Copie el instalador (AppService.exe) y el paquete de instalación sin conexión en la máquina host de Azure Stack.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Realización de la instalación sin conexión de App Service en Azure Stack

1. Ejecute appservice.exe como administrador desde un equipo que pueda acceder al punto de conexión de administración de recursos de Azure del administrador de Azure Stack.

1. Seleccione **Opciones avanzadas** > **Completar la instalación sin conexión**.

    ![Finalización de la instalación sin conexión en el instalador de App Service][2]

1. Vaya a la ubicación del paquete de instalación sin conexión que creó previamente y seleccione **Siguiente**.

    ![Especificación de la ruta de acceso del paquete de instalación sin conexión en el instalador de App Service](media/azure-stack-app-service-deploy-offline/image04.png)

1. Revise y acepte los términos de licencia del software de Microsoft y seleccione **Siguiente**.

1. Revise y acepte los términos de licencia de terceros y seleccione **Siguiente**.

1. Asegúrese de que la información de configuración en la nube de App Service es correcta. Si ha usado la configuración predeterminada durante la implementación del ASDK, puede aceptar los valores predeterminados. Sin embargo, si personalizó las opciones cuando implementó Azure Stack o va a realizar la implementación en un sistema integrado, debe editar los valores de esta ventana para reflejar esos cambios. Por ejemplo, si usa el sufijo de dominio mycloud.com, el punto de conexión de Azure Resource Manager del inquilino de Azure Stack debe cambiarse por `management.<region>.mycloud.com`. Después de confirmar la información, seleccione **Siguiente**.

    ![Configuración de la nube de Azure App Service en el instalador de App Service][3]

1. En la página siguiente:

   a. Seleccione el botón **Conectar** situado junto al cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack). 

   b. Proporcione la cuenta de administrador. Por ejemplo: cloudadmin@azurestack.local. Escriba la contraseña y seleccione **Iniciar sesión**.

   c. En el cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack), seleccione **Default Provider Subscription** (Suscripción de proveedor predeterminada).
   > [!NOTE]
   > App Service solo puede implementarse en la **suscripción de proveedor predeterminada**.

   d. En el cuadro **Azure Stack Locations** (Ubicaciones de Azure Stack), seleccione la ubicación que corresponda a la región en la que se va a implementar. Por ejemplo, seleccione **local** si va a realizar la implementación en el ASDK.

   e. Seleccione **Next** (Siguiente).

      ![Suscripciones y ubicaciones de Azure Stack en el instalador de App Service][4]

1. Puede permitir que el instalador de App Service cree una red virtual y las subredes asociadas. O bien, puede realizar la implementación en una red virtual existente, tal y como se configura mediante [estos pasos](azure-stack-app-service-before-you-get-started.md#virtual-network).
   - Para usar el método del instalador de App Service, seleccione **Crear una red virtual con la configuración predeterminada**, acepte los valores predeterminados y seleccione **Siguiente**.
   - Para realizar la implementación en una red existente, seleccione **Usar la red virtual y las subredes existentes**, y después:
       1. Seleccione la opción **Grupo de recursos** que contiene la red virtual.
       2. Elija el nombre de la instancia de **Virtual Network** en la que desea realizar la implementación.
       3. Seleccione los valores correctos de **subred** para cada una de las subredes de rol necesarias.
       4. Seleccione **Next** (Siguiente).

      ![Información de la red virtual y subred en el instalador de App Service][5]

1. Escriba la información para el recurso compartido de archivos y, a continuación, seleccione **Siguiente**. La dirección del recurso compartido de archivos debe usar el nombre de dominio completo (FQDN) o la dirección IP del servidor de archivos. Por ejemplo, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, o \\\10.0.0.1\websites.  Si usa un servidor de archivos que está unido a un dominio, debe proporcionar el nombre de usuario completo, incluido el dominio. Por ejemplo: `<myfileserverdomain>\<FileShareOwner>`.

    > [!NOTE]
    > El instalador intenta comprobar la conectividad con el recurso compartido de archivos antes de continuar. Sin embargo, si ha elegido realizar la implementación en una red virtual existente, puede que el instalador no pueda conectarse al recurso compartido de archivos y muestre una advertencia, que le pregunta si desea continuar. Compruebe la información del recurso compartido de archivos y continúe si es correcta.

   ![Información del recurso compartido de archivos en el instalador de App Service][8]

1. En la página siguiente:
    1. En el cuadro **Identificador de la aplicación de identidad**, escriba el GUID de la aplicación que va a usar para la identidad (de Azure AD).
    1. En el cuadro **Identity Application certificate file** (Archivo del certificado de la aplicación de identidad) escriba (o vaya a) la ubicación del archivo del certificado.
    1. En el cuadro **Identity Application certificate password** (Contraseña del certificado de la aplicación de identidad), escriba la contraseña para el certificado. Esta contraseña es la que anotó cuando usó el script para crear los certificados.
    1. En el cuadro **Azure Resource Manager root certificate file** (Archivo del certificado raíz de Azure Resource Manager) escriba (vaya a) la ubicación del archivo del certificado.
    1. Seleccione **Next** (Siguiente).

    ![Especificación del identificador de la aplicación y la información del certificado en el instalador de App Service][10]

1. Para cada uno de los tres cuadros de archivo de certificado, seleccione **Examinar** y vaya hasta el archivo de certificado adecuado. Debe proporcionar la contraseña de cada certificado. Estos certificados son los que creó en el [paso para crear los certificados necesarios](azure-stack-app-service-before-you-get-started.md#get-certificates). Seleccione **Siguiente** después de escribir toda la información.

    | Box | Ejemplo de nombre de archivo de certificado |
    | --- | --- |
    | **Archivo de certificado SSL predeterminado de App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **Archivo de certificado SSL de API de App Service** | api.appservice.local.AzureStack.external.pfx |
    | **Archivo de certificado SSL del publicador de App Service** | ftp.appservice.local.AzureStack.external.pfx |

    Si usó un sufijo de dominio diferente cuando creó los certificados, los nombres de archivo de certificado no usan *local.AzureStack.external*. En su lugar, use la información del dominio personalizado.

    ![Especificación de la información del certificado SSL en el instalador de App Service][11]

1. Escriba los detalles de SQL Server para la instancia de servidor que se usa para hospedar las bases de datos del proveedor de recursos de App Service y, después, seleccione **Siguiente**. El instalador valida las propiedades de conexión de SQL. **Debe** escribir la dirección IP interna o el nombre de dominio completo para el nombre de SQL Server.

    > [!NOTE]
    > El instalador intenta comprobar la conectividad con el equipo que ejecuta la instancia de SQL Server antes de continuar. Sin embargo, si ha elegido realizar la implementación en una red virtual existente, puede que el instalador no pueda conectarse al equipo que ejecuta la instancia de SQL Server y muestre una advertencia, que le pregunta si desea continuar. Compruebe la información de SQL Server y continúe si es correcta.
    >
    > Desde Azure App Service en Azure Stack 1.3 y versiones posteriores, el instalador comprueba que el equipo que ejecuta la instancia de SQL Server tiene la contención de base de datos habilitada en el nivel de SQL Server. Si no es así, se le indicará la siguiente excepción:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;
    >    GO
    >    RECONFIGURE;
    >    GO
    >    ***********************************************************
    > ```
    > Para más información, consulte las [notas de la versión de Azure App Service en Azure Stack 1.3](azure-stack-app-service-release-notes-update-three.md).

    ![Especificación de la información de SQL Server en el instalador de App Service][12]

1. Revise las opciones de SKU y la instancia de rol. Los valores predeterminados se rellenan con el número mínimo de instancias y la SKU mínima de cada rol en una implementación de ASDK. Se proporciona un resumen de los requisitos de memoria y vCPU para ayudar a planear la implementación. Después de realizar las selecciones, elija **Siguiente**.

     > [!NOTE]
     > En las implementaciones de producción, siga las instrucciones que se indican en [Planeamiento de la capacidad de los roles de servidor de Azure App Service en Azure Stack](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Role | Número mínimo de instancias | SKU mínima | Notas |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A2: (2 vCPU, 3584 MB) | Administra y mantiene el estado de la nube de App Service. |
    | Administración | 1 | Standard_A2: (2 vCPU, 3584 MB) | Administra los puntos de conexión de API y Azure Resource Manager de App Service, las extensiones de portales (portal de Functions, administración e inquilino) y el servicio de datos. Para admitir la conmutación por error, se incrementan las instancias recomendadas a 2. |
    | Publicador | 1 | Standard_A1: (1 vCPU, 1792 MB) | Publica contenido a través de la implementación de web y FTP. |
    | FrontEnd | 1 | Standard_A1: (1 vCPU, 1792 MB) | Enruta las solicitudes a las aplicaciones de App Service. |
    | Trabajo compartido | 1 | Standard_A1: (1 vCPU, 1792 MB) | Hospeda aplicaciones de API o web y aplicaciones de Azure Functions. Puede ser conveniente agregar más instancias. Como operador, puede definir su oferta y elegir cualquier nivel de SKU. Los niveles deben tener una vCPU como mínimo. |

    ![Establecimiento de los niveles de rol y las opciones de SKU en el instalador de App Service][14]

    > [!NOTE]
    > Windows Server 2016 Core *no* es una imagen de plataforma compatible para su uso con Azure App Service en Azure Stack.  No use imágenes de evaluación para las implementaciones de producción. Azure App Service en Azure Stack requiere que Microsoft.Net 3.5.1 SP1 se active en la imagen utilizada para la implementación. Las imágenes de Windows Server 2016 distribuidas en Marketplace no tienen habilitada esta característica. Por lo tanto, debe crear y usar una imagen de Windows Server 2016 con esta característica habilitada previamente.

1. En el cuadro **Seleccionar imagen de plataforma**, elija su imagen de máquina virtual (VM) Windows Server 2016 de implementación entre las que están disponibles en el proveedor de recursos de proceso para la nube de App Service. Seleccione **Next** (Siguiente).

1. En la página siguiente:
     1. Escriba el nombre de usuario y la contraseña del administrador de máquinas virtuales con rol de trabajo.
     2. Escriba el nombre de usuario y la contraseña del administrador de máquinas virtuales con otros roles.
     3. Seleccione **Next** (Siguiente).

    ![Especificación de administradores de máquina virtual de rol en el instalador de App Service][16]

1. En la página de resumen:
    1. Compruebe las selecciones realizadas. Para realizar cambios, use los botones **Anterior** para visitar las páginas anteriores.
    2. Si las configuraciones son correctas, active la casilla.
    3. Para iniciar la implementación, seleccione **Siguiente**.

    ![Resumen de las selecciones realizadas en el instalador de App Service][17]

1. En la página siguiente:
    1. Realice un seguimiento del progreso de la instalación. App Service en Azure Stack tarda aproximadamente 60 minutos en realizar la implementación según las selecciones predeterminadas.
    2. Cuando el instalador termine de ejecutarse, seleccione **Salir**.

    ![Realización del seguimiento del proceso en el instalador de App Service][18]

## <a name="post-deployment-steps"></a>Pasos posteriores a la implementación

> [!IMPORTANT]
> *Debe* [agregar las bases de datos appservice_hosting y appservice_metering a un grupo de disponibilidad](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) si ha proporcionado al proveedor de recursos de App Service una instancia de SQL Always On. También debe sincronizar las bases de datos para evitar cualquier pérdida de servicio en caso de una conmutación por error de la base de datos.

Si decide realizar una implementación en una red virtual existente y en una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. De ese modo, permite que exista tráfico SMB entre la subred del rol de trabajo y el servidor de archivos. En el portal del administrador vaya al grupo de seguridad de red WorkersNsg y agregue una regla de seguridad de salida con las siguientes propiedades:

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

1. En el portal de administración de Azure Stack, vaya a **Administración: App Service**.

1. En la información general, debajo del estado, compruebe que en **Estado** se muestra el mensaje **Todos los roles están listos**.

    ![Introducción a la administración de App Service](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack"></a>Prueba de App Service en Azure Stack

Después de implementar y registrar el proveedor de recursos de App Service, pruébelo para asegurarse de que los usuarios pueden implementar aplicaciones web y de API.

> [!NOTE]
> Debe crear una oferta que tenga el espacio de nombres Microsoft.Web dentro del plan. A continuación, debe tener una suscripción de inquilino que suscriba esta oferta. Para más información, consulte [Creación de una oferta](azure-stack-create-offer.md) y [Creación de un plan](azure-stack-create-plan.md).
>
> *Debe* tener una suscripción de inquilino para crear aplicaciones que usen App Service en Azure Stack. Las únicas funcionalidades que un administrador del servicio puede completar en el portal del administrador están relacionadas con la administración del proveedor de recursos de App Service. Entre estas funcionalidades se incluyen la adición de capacidad, la configuración de orígenes de implementación y la adición de niveles de trabajo y SKU.
>
> A partir de la tercera versión preliminar técnica, para crear aplicaciones web, de API y Azure Functions, se debe usar el portal de inquilino y tener una suscripción de inquilino.

1. En el portal de inquilino de Azure Stack, seleccione **+ Crear un recurso** > **Web y móvil** > **Aplicación web**.

1. En la hoja **Aplicación web**, escriba un nombre en el cuadro **Aplicación web**.

1. En **Grupo de recursos**, seleccione **Nuevo**. Escriba un nombre en el cuadro **Grupo de recursos**.

1. Seleccione **Plan de App Service/Ubicación** > **Crear nuevo**.

1. En la hoja **Plan de App Service**, escriba un nombre en el cuadro **Plan de App Service**.

1. Seleccione **Plan de tarifa** > **Free-Shared** (Gratis - Compartido) o **Shared-Shared** (Compartido - Compartido) > **Seleccionar** > **Aceptar** > **Crear**.

1. En menos de un minuto, aparecerá un icono para la nueva aplicación web en el panel. Seleccione el icono.

1. En la hoja **Aplicación web**, seleccione **Examinar** para ver el sitio web predeterminado para esta aplicación.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implementación de un sitio web de WordPress, DNN o Django (opcional)

1. En el portal de inquilino de Azure Stack, seleccione **+** , vaya a Azure Marketplace, implemente un sitio web de Django y espere a que se complete correctamente. La plataforma web de Django usa una base de datos basada en el sistema de archivos. No se necesita ningún proveedor de recursos adicional, como SQL o MySQL.

1. Si también implementó un proveedor de recursos de MySQL, puede implementar un sitio web de WordPress en Azure Marketplace. Cuando se le pidan los parámetros de la base de datos, escriba el nombre de usuario, como *Usario1\@Servidor1*, con el nombre de usuario y el nombre del servidor de su elección.

1. Si también implementó un proveedor de recursos de SQL Server, puede implementar un sitio web de DNN en Azure Marketplace. Cuando se le pidan los parámetros de la base de datos, elija una base de datos del equipo que ejecute la instancia de SQL Server que esté conectada a su proveedor de recursos.

## <a name="next-steps"></a>Pasos siguientes

Prepárese para realizar operaciones de administración adicionales para App Service en Azure Stack:

- [Planificación de capacidad](azure-stack-app-service-capacity-planning.md)
- [Configuración de orígenes de implementación](azure-stack-app-service-configure-deployment-sources.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-create-package.png
[2]: ./media/azure-stack-app-service-deploy-offline/app-service-exe-advanced-complete-offline.png
[3]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-arm-endpoints.png
[4]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-subscription-information.png
[5]: ./media/azure-stack-app-service-deploy-offline/app-service-default-VNET-config.png
[6]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config.png
[7]: ./media/azure-stack-app-service-deploy-offline/app-service-custom-VNET-config-with-values.png
[8]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration.png
[9]: ./media/azure-stack-app-service-deploy-offline/app-service-fileshare-configuration-error.png
[10]: ./media/azure-stack-app-service-deploy-offline/app-service-identity-app.png
[11]: ./media/azure-stack-app-service-deploy-offline/app-service-certificates.png
[12]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration.png
[13]: ./media/azure-stack-app-service-deploy-offline/app-service-sql-configuration-error.png
[14]: ./media/azure-stack-app-service-deploy-offline/app-service-cloud-quantities.png
[15]: ./media/azure-stack-app-service-deploy-offline/app-service-windows-image-selection.png
[16]: ./media/azure-stack-app-service-deploy-offline/app-service-role-credentials.png
[17]: ./media/azure-stack-app-service-deploy-offline/app-service-azure-stack-deployment-summary.png
[18]: ./media/azure-stack-app-service-deploy-offline/app-service-deployment-progress.png
