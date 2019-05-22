---
title: Implementación de App Service en un entorno sin conexión en Azure Stack | Microsoft Docs
description: Guía detallada sobre cómo implementar App Service en un entorno de Azure Stack desconectado protegido por AD FS.
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
ms.date: 02/27/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 01/11/2019
ms.openlocfilehash: d65489d0c401158b83ac20f4314fe6bdae886107
ms.sourcegitcommit: 85c3acd316fd61b4e94c991a9cd68aa97702073b
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 05/01/2019
ms.locfileid: "64983807"
---
# <a name="add-an-app-service-resource-provider-to-a-disconnected-azure-stack-environment-secured-by-ad-fs"></a>Incorporación de un proveedor de recursos de App Service a un entorno de Azure Stack desconectado protegido por AD FS

*Se aplica a: Sistemas integrados de Azure Stack y Kit de desarrollo de Azure Stack*

> [!IMPORTANT]
> Aplique la actualización 1901 al sistema integrado de Azure Stack o implemente el kit de desarrollo de Azure Stack más reciente antes de implementar Azure App Service 1.5.

Siguiendo las instrucciones de este artículo, puede instalar el [proveedor de recursos de App Service](azure-stack-app-service-overview.md) en un entorno de Azure Stack que:

- No esté conectado a Internet
- Esté protegido por Servicios de federación de Active Directory (AD FS) 2.0.

> [!IMPORTANT]  
> Antes de ejecutar el instalador del proveedor de recursos, asegúrese de haber seguido las instrucciones de [Antes de empezar a](azure-stack-app-service-before-you-get-started.md) y haber leído las [notas de la versión](azure-stack-app-service-release-notes-update-five.md) que acompañan a la versión 1.5, para obtener información sobre las nuevas funciones, correcciones y problemas conocidos que podrían afectar a la implementación.

Para agregar el proveedor de recursos de App Service para la implementación de Azure Stack sin conexión, debe completar estas tareas de nivel superior:

1. Realice los [pasos previos necesarios](azure-stack-app-service-before-you-get-started.md) (como la compra de certificados, que pueden tardar unos días en recibirse).
2. [Descargue y extraiga los archivos de instalación y auxiliares](azure-stack-app-service-before-you-get-started.md) en una máquina conectada a Internet.
3. Cree un paquete de instalación sin conexión.
4. Ejecute el archivo del instalador appservice.exe.

## <a name="create-an-offline-installation-package"></a>Creación de un paquete de instalación sin conexión

Para implementar App Service en un entorno desconectado, primero debe crear un paquete de instalación sin conexión en una máquina que esté conectada a Internet.

1. Ejecute el instalador AppService.exe en una máquina que esté conectada a Internet.

2. Haga clic en **Opciones avanzadas** > **Crear paquete de instalación sin conexión**.

    ![Instalador de App Service][1]

3. El instalador de App Service crea un paquete de instalación sin conexión y muestra su ruta de acceso. Puede hacer clic en **Abrir carpeta** para abrir la carpeta en el Explorador de archivos.

    ![Instalador de App Service](media/azure-stack-app-service-deploy-offline/image02.png)

4. Copie el instalador (AppService.exe) y el paquete de instalación sin conexión en la máquina host de Azure Stack.

## <a name="complete-the-offline-installation-of-app-service-on-azure-stack"></a>Realización de la instalación sin conexión de App Service en Azure Stack

1. Ejecute appservice.exe como administrador desde un equipo que pueda comunicarse con el punto de conexión de administración de recursos de Azure del administrador de Azure Stack.

2. Haga clic en **Opciones avanzadas** > **Completar la instalación sin conexión**.

    ![Instalador de App Service][2]

3. Vaya a la ubicación del paquete de instalación sin conexión que creó previamente y haga clic en **Siguiente**.

    ![Instalador de App Service](media/azure-stack-app-service-deploy-offline/image04.png)

4. Revise y acepte los términos de licencia del software de Microsoft y haga clic en **Siguiente**.

5. Revise y acepte los términos de licencia de terceros y haga clic en **Siguiente**.

6. Asegúrese de que la información de configuración de nube de App Service es correcta. Si usó la configuración predeterminada durante la implementación del Kit de desarrollo de Azure Stack, puede aceptar los valores predeterminados aquí. Sin embargo, si personalizó las opciones cuando implementó Azure Stack o va a realizar la implementación en un sistema integrado, debe editar los valores de esta ventana para reflejar ese hecho. Por ejemplo, si usa el sufijo de dominio mycloud.com, el punto de conexión de Azure Resource Manager del inquilino de Azure Stack debe cambiarse por `management.<region>.mycloud.com`. Después de confirmar su información, haga clic en **Siguiente**.

    ![Instalador de App Service][3]

7. En la página siguiente:
   1. Haga clic en el botón **Conectar** situado junto al cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack).
      - Proporcione la cuenta de administrador. Por ejemplo, cloudadmin@azurestack.local. Escriba la contraseña y haga clic en **Iniciar sesión**.
   2. En el cuadro **Azure Stack Subscriptions** (Suscripciones de Azure Stack), seleccione **Default Provider Subscription** (Suscripción de proveedor predeterminada).
    
      > [!NOTE]
      > App Service solo puede implementarse en la **suscripción del proveedor predeterminado**.
      >
    
   3. En el cuadro **Azure Stack Locations** (Ubicaciones de Azure Stack), seleccione la ubicación que corresponda a la región en la que se va a implementar. Por ejemplo, seleccione **local** si va a implementar con el Kit de desarrollo de Azure Stack.
   4. Haga clic en **Next**.

      ![Instalador de App Service][4]

8. Ahora tiene la opción de realizar la implementación en una instancia existente de Virtual Network, configurada mediante los pasos [aquí](azure-stack-app-service-before-you-get-started.md#virtual-network) descritos, o permitir que el instalador de App Service cree una red virtual y las subredes asociadas.
   1. Seleccione **Create VNet with default settings** (Crear red virtual con la configuración predeterminada), acepte los valores predeterminados y haga clic en **Next** (Siguiente).
   2. O, seleccione **Use existing VNet and Subnets** (Usar red virtual y subredes existentes).
       1. Seleccione el **grupo de recursos** que contiene la instancia de Virtual Network.
       2. Elija el nombre correcto de la instancia de **Virtual Network** en la que quiere realizar la implementación.
       3. Seleccione los valores correctos de **subred** para cada una de las subredes de rol necesarias.
       4. Haga clic en **Siguiente**

      ![Instalador de App Service][5]

9. Escriba la información para el recurso compartido de archivos y, a continuación, haga clic en **Siguiente**. La dirección del recurso compartido de archivos debe usar el nombre de dominio completo o la dirección IP del servidor de archivos. Por ejemplo, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, o \\\10.0.0.1\websites.  Si usa un servidor de archivos que está unido a un dominio, debe proporcionar el nombre de usuario completo, incluido el dominio (por ejemplo, miDominioDeServidorDeArchivo\FileShareOwner).

    > [!NOTE]
    > El instalador intenta comprobar la conectividad con el recurso compartido de archivos antes de continuar.  Sin embargo, si eligió realizar la implementación en una red virtual existente, puede que el instalador no pueda conectarse al recurso compartido de archivos y muestre una advertencia, que le pregunta si desea continuar.  Compruebe la información del recurso compartido de archivos y continúe si es correcta.
    >
    >

   ![Instalador de App Service][8]

10. En la página siguiente:
    1. En el cuadro **Identificador de la aplicación de identidad**, escriba el GUID de la aplicación que va a usar para la identidad (de Azure AD).
    2. En el cuadro **Identity Application certificate file** (Archivo del certificado de la aplicación de identidad) escriba (o vaya a) la ubicación del archivo del certificado.
    3. En el cuadro **Identity Application certificate password** (Contraseña del certificado de la aplicación de identidad), escriba la contraseña para el certificado. Esta contraseña es la que anotó cuando usó el script para crear los certificados.
    4. En el cuadro **Azure Resource Manager root certificate file** (Archivo del certificado raíz de Azure Resource Manager) escriba (vaya a) la ubicación del archivo del certificado.
    5. Haga clic en **Next**.

    ![Instalador de App Service][10]

11. Para cada uno de los tres cuadros de archivo de certificado, haga clic en **Examinar** y navegue hasta el archivo de certificado adecuado. Debe proporcionar la contraseña de cada certificado. Estos certificados son los que creó en el [paso para crear los certificados necesarios](azure-stack-app-service-before-you-get-started.md#get-certificates). Haga clic en **Siguiente** después de escribir toda la información.

    | Box | Ejemplo de nombre de archivo de certificado |
    | --- | --- |
    | **Archivo de certificado SSL predeterminado de App Service**  | \_.appservice.local.AzureStack.external.pfx |
    | **Archivo de certificado SSL de API de App Service** | api.appservice.local.AzureStack.external.pfx |
    | **Archivo de certificado SSL del publicador de App Service** | ftp.appservice.local.AzureStack.external.pfx |

    Si usó un sufijo de dominio diferente cuando creó los certificados, los nombres de archivo de certificado no usan *local.AzureStack.external*. En su lugar, use la información del dominio personalizado.

    ![Instalador de App Service][11]

12. Escriba los detalles de SQL Server para la instancia de servidor que se usa para hospedar las bases de datos del proveedor de recursos de App Service y, después, haga clic en **Siguiente**. El instalador valida las propiedades de conexión de SQL. **Debe** escribir la dirección IP interna o el nombre de dominio completo para el nombre de SQL Server.

    > [!NOTE]
    > El instalador intenta comprobar la conectividad con la instancia de SQL Server antes de continuar.  Sin embargo, si eligió realizar la implementación en una red virtual existente, puede que el instalador no pueda conectarse a la instancia de SQL Server y muestre una advertencia, que le pregunta si desea continuar.  Compruebe la información de SQL Server y continúe si es correcta.
    >
    > Desde Azure App Service en Azure Stack 1.3 y versiones posteriores, el instalador comprobará que el servidor SQL Server tiene la contención de base de datos habilitada en el nivel de SQL Server.  Si no es así, se le indicará la siguiente excepción:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;  
    >    GO  
    >    RECONFIGURE;  
    >    GO
    >    ***********************************************************
    > ```
    > Remítase a las [notas de la versión de Azure App Service en Azure Stack 1.3](azure-stack-app-service-release-notes-update-three.md) para más detalles.
   
    ![Instalador de App Service][12]

13. Revise las opciones de SKU y la instancia de rol. Los valores predeterminados se rellenan con el número mínimo de instancias y la SKU mínima de cada rol en una implementación de ASDK. Se proporciona un resumen de los requisitos de memoria y vCPU para ayudar a planear la implementación. Después de realizar las selecciones, haga clic en **Siguiente**.

     > [!NOTE]
     > En las implementaciones de producción, siga las instrucciones que se indican en [Planeamiento de la capacidad de los roles de servidor de Azure App Service en Azure Stack](azure-stack-app-service-capacity-planning.md).
     >
     >

    | Rol | Número mínimo de instancias | SKU mínima | Notas |
    | --- | --- | --- | --- |
    | Controller | 1 | Standard_A2: (2 vCPU, 3584 MB) | Administra y mantiene el estado de la nube de App Service. |
    | Administración | 1 | Standard_A2: (2 vCPU, 3584 MB) | Administra los puntos de conexión de API y Azure Resource Manager de App Service, las extensiones de portales (portal de Functions, administración e inquilino) y el servicio de datos. Para admitir la conmutación por error, se incrementan las instancias recomendadas a 2. |
    | Publicador | 1 | Standard_A1: (1 vCPU, 1792 MB) | Publica contenido a través de la implementación de web y FTP. |
    | FrontEnd | 1 | Standard_A1: (1 vCPU, 1792 MB) | Enruta las solicitudes a las aplicaciones de App Service. |
    | Trabajo compartido | 1 | Standard_A1: (1 vCPU, 1792 MB) | Hospeda aplicaciones de API o web y aplicaciones de Azure Functions. Puede ser conveniente agregar más instancias. Como operador, puede definir su oferta y elegir cualquier nivel de SKU. Los niveles deben tener una vCPU como mínimo. |

    ![Instalador de App Service][14]

    > [!NOTE]
    > **Windows Server 2016 Core no es una imagen de plataforma compatible para su uso con Azure App Service en Azure Stack.  No use imágenes de evaluación para las implementaciones de producción.  Azure App Service en Azure Stack requiere que Microsoft.Net 3.5.1 SP1 se active en la imagen utilizada para la implementación.   Las imágenes de Windows Server 2016 distribuidas por Marketplace no tienen habilitada esta característica; por lo tanto, debe crear y utilizar una imagen de Windows Server 2016 con la característica habilitada previamente.**

14. En el cuadro **Select Platform Image** (Seleccionar imagen de plataforma), elija su imagen de máquina virtual Windows Server 2016 de implementación entre las que están disponibles en el proveedor de recursos de procesos para la nube de App Service. Haga clic en **Next**.

15. En la página siguiente:
     1. Escriba el nombre de usuario y la contraseña del administrador de la máquina virtual con el rol de trabajo.
     2. Escriba el nombre de usuario y la contraseña del administrador de la máquina virtual con otros roles.
     3. Haga clic en **Next**.

    ![Instalador de App Service][16]

16. En la página de resumen:
    1. Compruebe las selecciones realizadas. Para realizar cambios, use los botones **Anterior** para visitar las páginas anteriores.
    2. Si las configuraciones son correctas, active la casilla.
    3. Para iniciar la implementación, haga clic en **Siguiente**.

    ![Instalador de App Service][17]

17. En la página siguiente:
    1. Realice un seguimiento del progreso de la instalación. App Service en Azure Stack tarda aproximadamente 60 minutos en realizar la implementación según las selecciones predeterminadas.
    2. Después de que el instalador finalice correctamente, haga clic en **Salir**.

    ![Instalador de App Service][18]

## <a name="post-deployment-steps"></a>Pasos posteriores a la implementación

> [!IMPORTANT]  
> Si proporcionó el proveedor de recursos de App Service con una instancia de SQL Always On, DEBE [agregar las bases de datos appservice_hosting y appservice_metering a un grupo de disponibilidad](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database), así como sincronizar dichas bases de datos para evitar la pérdida de servicio en caso de producirse una conmutación por error de base de datos.

## <a name="validate-the-app-service-on-azure-stack-installation"></a>Validación de la instalación de App Service en Azure Stack

1. En el Portal de administración de Azure Stack, vaya a **Administración - App Service**.

2. En la información general, debajo del estado, compruebe que en **Estado** se muestra el mensaje **Todos los roles están listos**.

    ![Administración de App Service](media/azure-stack-app-service-deploy/image12.png)

> [!NOTE]
> Si decide realizar una implementación en una red virtual existente y en una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. De ese modo, permite que exista tráfico SMB entre la subred del rol de trabajo y el servidor de archivos.  Para ello, vaya a WorkersNsg en el Portal de administración y agregue una regla de seguridad de salida con las siguientes propiedades:
> * Origen: Cualquiera
> * Intervalo de puertos de origen: *
> * Destino: Direcciones IP
> * Intervalo de direcciones IP de destino: Intervalo de direcciones IP del servidor de archivos
> * Intervalo de puertos de destino: 445
> * Protocolo: TCP
> * Acción: PERMITIR
> * Prioridad: 700
> * Nombre: Outbound_Allow_SMB445
>

## <a name="test-drive-app-service-on-azure-stack"></a>Prueba de App Service en Azure Stack

Después de implementar y registrar el proveedor de recursos de App Service, pruébelo para asegurarse de que los usuarios pueden implementar aplicaciones web y de API.

> [!NOTE]
> Debe crear una oferta que tenga el espacio de nombres Microsoft.Web dentro del plan. A continuación, debe tener una suscripción de inquilino que suscriba esta oferta. Para más información, consulte [Create offer](azure-stack-create-offer.md) (Creación de una oferta) y [Create plan](azure-stack-create-plan.md) (Creación de un plan).
>
> Se *debe* tener una suscripción de inquilino para crear aplicaciones que usen App Service en Azure Stack. Las únicas funcionalidades que un administrador del servicio puede completar en el portal de administración están relacionadas con la administración del proveedor de recursos de App Service. Entre estas funcionalidades se incluyen la adición de capacidad, la configuración de orígenes de implementación y la adición de niveles de trabajo y SKU.
>
> A partir de la tercera versión preliminar técnica, para crear aplicaciones web, de API y Azure Functions, se debe usar el portal de inquilino y tener una suscripción de inquilino.

1. En el portal de inquilinos de Azure Stack, haga clic en **+ Crear un recurso** > **Web y móvil** > **Aplicación web**.

2. En la hoja **Aplicación web**, escriba un nombre en el cuadro **Aplicación web**.

3. En **Grupo de recursos**, haga clic en **Nuevo**. Escriba un nombre en el cuadro **Grupo de recursos**.

4. Haga clic en **Plan de App Service/Ubicación** > **Create New** (Crear nuevo).

5. En la hoja **Plan de App Service**, escriba un nombre en el cuadro **Plan de App Service**.

6. Haga clic en **Plan de tarifa** > **Free-Shared** (Gratis - Compartido) o **Shared-Shared** (Compartido - Compartido) > **Seleccionar** > **Aceptar** > **Crear**.

7. En menos de un minuto, aparecerá un icono para la nueva aplicación web en el panel. Haga clic en el icono.

8. En la hoja **Aplicación web**, haga clic en **Examinar** para ver el sitio web predeterminado para esta aplicación.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implementación de un sitio web de WordPress, DNN o Django (opcional)

1. En el portal de inquilino de Azure Stack, haga clic en **+**, vaya a Azure Marketplace, implemente un sitio web de Django y espere a que se complete correctamente. La plataforma web de Django usa una base de datos basada en el sistema de archivos. No se necesita ningún proveedor de recursos adicional, como SQL o MySQL.

2. Si también implementó un proveedor de recursos de MySQL, puede implementar un sitio web de WordPress en Marketplace. Cuando se le pidan los parámetros de la base de datos, escriba el nombre de usuario, como *Usario1\@Servidor1*, con el nombre de usuario y el nombre del servidor de su elección.

3. Si también implementó un proveedor de recursos de SQL Server, puede implementar un sitio web de DNN en Marketplace. Cuando se le pidan los parámetros de la base de datos, elija una base de datos del equipo que ejecute la instancia de SQL Server que está conectado a su proveedor de recursos.

## <a name="next-steps"></a>Pasos siguientes

También puede probar otros [servicios de Plataforma como servicio (PaaS)](azure-stack-offer-services-overview.md).

- [Proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md)
- [Proveedor de recursos de MySQL](azure-stack-mysql-resource-provider-deploy.md)

<!--Links-->
[Azure_Stack_App_Service_preview_installer]: https://go.microsoft.com/fwlink/?LinkID=717531
[App_Service_Deployment]: https://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: https://go.microsoft.com/fwlink/?LinkId=733525

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
