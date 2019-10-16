---
title: Implementación de App Service en Azure Stack mediante una configuración de alta disponibilidad | Microsoft Docs
description: Obtenga información sobre cómo implementar App Service en Azure Stack mediante una configuración de alta disponibilidad.
services: azure-stack
documentationcenter: ''
author: BryanLa
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/07/2019
ms.author: anwestg
ms.reviewer: anwestg
ms.lastreviewed: 03/23/2019
ms.openlocfilehash: 0bf89b0f80557f99c83fb5ad6afd0c4a5dcd3849
ms.sourcegitcommit: dfaf0126bc9975ca1643d55f06c71df9e32ea976
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 10/09/2019
ms.locfileid: "72165021"
---
# <a name="deploy-app-service-in-a-highly-available-configuration"></a>Implementación de App Service en una configuración de alta disponibilidad

En este artículo se explica cómo utilizar elementos de Marketplace de Azure Stack para implementar App Service en Azure Stack mediante una configuración de alta disponibilidad. Además de los elementos de marketplace disponibles, esta solución también utiliza la plantilla de inicio rápido de Azure Stack [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha). Esta plantilla automatiza la creación de una infraestructura de alta disponibilidad para hospedar el proveedor de recursos de App Service. App Service se instala después en esta infraestructura de máquina virtual de alta disponibilidad. 

## <a name="deploy-the-highly-available-app-service-infrastructure-vms"></a>Implementación de máquinas virtuales en la infraestructura de App Service de alta disponibilidad
La plantilla de inicio rápido de Azure Stack [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) simplifica la implementación de App Service en una configuración de alta disponibilidad. Debe implementarse en la suscripción de proveedor predeterminada. 

Cuando se usa para crear un recurso personalizado en Azure Stack, la plantilla crea:
- Una red virtual y las subredes necesarias
- Grupos de seguridad de red para el servidor de archivos, SQL Server y las subredes de Active Directory Domain Services (AD DS)
- Cuentas de almacenamiento para discos de máquina virtual y el testigo en la nube del clúster
- Un equilibrador de carga interno para las VM de SQL con una dirección IP privada enlazada al agente de escucha AlwaysOn de SQL
- Tres conjuntos de disponibilidad para AD DS, el clúster de servidores de archivos y el clúster de SQL
- Clúster de SQL de dos nodos
- Clúster de servidores de archivos de dos nodos
- Dos controladores de dominio

### <a name="required-azure-stack-marketplace-items"></a>Elementos de marketplace requeridos para Azure Stack
Antes de usar esta plantilla, asegúrese de que los siguientes [elementos de marketplace de Azure Stack](azure-stack-marketplace-azure-items.md) están disponibles en la instancia de Azure Stack:

- Imagen de Windows Server 2016 Datacenter Core (para máquinas virtuales de servidor de archivos y AD DS)
- SQL Server 2016 SP2 en Windows Server 2016 (Enterprise)
- Última extensión IaaS de SQL 
- Última extensión de Desired State Configuration de PowerShell 

> [!TIP]
> Revise [el archivo Léame de la plantilla](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha) en GitHub para obtener más detalles sobre los requisitos de la plantilla y los valores predeterminados. 

### <a name="deploy-the-app-service-infrastructure"></a>Implementación de la infraestructura de App Service
Use los pasos de esta sección para crear una implementación personalizada con la plantilla de inicio rápido de Azure Stack **appservice-fileshare-sqlserver-ha**.

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Seleccione **\+** **Crear un recurso** > **Personalizar** y, a continuación, **Implementación de plantilla**.

   ![Implementación de plantilla personalizada](media/app-service-deploy-ha/1.png)


3. En la hoja **Implementación personalizada**, seleccione **Editar plantilla** > **Plantilla de inicio rápido** y, a continuación, use la lista desplegable de plantillas personalizadas disponibles para seleccionar la plantilla **appservice-fileshare-sqlserver-ha**. Haga clic en **Aceptar** y, a continuación, en **Guardar**.

   ![Selección de la plantilla de inicio rápido appservice-fileshare-sqlserver-ha](media/app-service-deploy-ha/2.png)

4. En la hoja **Implementación personalizada**, seleccione **Editar parámetros** y desplácese hacia abajo para revisar los valores predeterminados de la plantilla. Modifique estos valores según sea necesario para proporcionar toda la información de los parámetros necesaria y, a continuación, haga clic en **Aceptar**.<br><br> Como mínimo, proporcione contraseñas complejas para los parámetros `ADMINPASSWORD`, `FILESHAREOWNERPASSWORD`, `FILESHAREUSERPASSWORD`, `SQLSERVERSERVICEACCOUNTPASSWORD`y `SQLLOGINPASSWORD`.
    
   ![Edición de los parámetros de implementación personalizada](media/app-service-deploy-ha/3.png)

5. En la hoja **Implementación personalizada**, asegúrese de que la opción **Suscripción de proveedor predeterminada** está seleccionada como la suscripción que se usará y, después, cree un grupo de recursos o seleccione uno existente para la implementación personalizada.<br><br> A continuación, seleccione la ubicación del grupo de recursos (**local** para las instalaciones de ASDK) y, a continuación, haga clic en **Crear**. La configuración de implementación personalizada se validará antes de que se inicie la implementación de la plantilla.

    ![Creación de una implementación personalizada](media/app-service-deploy-ha/4.png)

6. En el portal del administrador, seleccione **Grupos de recursos** y, a continuación, el nombre del grupo de recursos que creó para la implementación personalizada (**app-service-ha** en este ejemplo). Vea el estado de la implementación para asegurarse de que todas las implementaciones se han completado correctamente.

   > [!NOTE]
   > La implementación de la plantilla tarda una hora en completarse aproximadamente.

   [![](media/app-service-deploy-ha/5-sm.png "Revisión del estado de implementación de la plantilla")](media/app-service-deploy-ha/5-lg.png#lightbox)


### <a name="record-template-outputs"></a>Registro de los resultados de la plantilla
Una vez completada correctamente la implementación de la plantilla, registre los resultados de dicha operación. Necesita esta información al ejecutar el instalador de App Service.

Asegúrese de registrar todos estos valores para los resultados:
- FileSharePath
- FileShareOwner
- FileShareUser
- SQLserver
- SQLuser

Siga estos pasos para detectar los valores de los resultados de la plantilla:

1. [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. En el portal del administrador, seleccione **Grupos de recursos** y, a continuación, el nombre del grupo de recursos que creó para la implementación personalizada (**app-service-ha** en este ejemplo). 

3. Haga clic en **Implementaciones** y seleccione **Microsoft.Template**.

    ![Implementación de Microsoft.Template](media/app-service-deploy-ha/6.png)

4. Después de seleccionar la implementación de **Microsoft.Template**, seleccione **Resultados** y registre el resultado del parámetro de la plantilla. Esta información se necesita cuando se implementa App Service.

    ![Resultado de los parámetros](media/app-service-deploy-ha/7.png)


## <a name="deploy-app-service-in-a-highly-available-configuration"></a>Implementación de App Service en una configuración de alta disponibilidad
Siga los pasos descritos en esta sección para implementar App Service en Azure Stack mediante una configuración de alta disponibilidad basada en la plantilla de inicio rápido de Azure Stack [appservice-fileshare-sqlserver-ha](https://github.com/Azure/azurestack-quickstart-templates/tree/master/appservice-fileserver-sqlserver-ha). 

Después de instalar el proveedor de recursos de App Service, puede incluirlos en sus planes y ofertas. A continuación, los usuarios pueden suscribirse para obtener el servicio y empezar a crear aplicaciones.

> [!IMPORTANT]
> Antes de ejecutar el instalador del proveedor de recursos, asegúrese de haber leído las notas de la versión que acompañan a cada versión de App Service, para obtener información sobre las nuevas funcionalidades, correcciones y problemas conocidos que podrían afectar a su implementación.

### <a name="prerequisites"></a>Requisitos previos
Para poder ejecutar el instalador de App Service, primero es necesario seguir los pasos descritos en el artículo [Antes de empezar a trabajar con App Service en Azure Stack](azure-stack-app-service-before-you-get-started.md):

> [!TIP]
> No todos los pasos descritos en dicho [artículo](azure-stack-app-service-before-you-get-started.md) son necesarios, ya que la implementación de la plantilla configura automáticamente las máquinas virtuales de la infraestructura.

- [Descargue los scripts auxiliares y el instalador de App Service](azure-stack-app-service-before-you-get-started.md#download-the-installer-and-helper-scripts).
- [Descargue los elementos de Marketplace de Azure Stack](azure-stack-app-service-before-you-get-started.md#download-items-from-the-azure-marketplace).
- [Genere los certificados necesarios](azure-stack-app-service-before-you-get-started.md#get-certificates).
- Cree la aplicación de identificador según el proveedor de identidades elegido para Azure Stack. Una aplicación de identificador puede crearse para [Azure AD](azure-stack-app-service-before-you-get-started.md#create-an-azure-active-directory-app) o [Servicios de federación de Active Directory (AD FS)](azure-stack-app-service-before-you-get-started.md#create-an-active-directory-federation-services-app) y para registrar el identificador de la aplicación.
- Asegúrese de haber agregado la imagen de Windows Server 2016 Datacenter al Marketplace de Azure Stack. Esta imagen es necesaria para la instalación de App Service.

### <a name="steps-for-app-service-deployment"></a>Pasos para la implementación de App Service
La instalación del proveedor de recursos de App Service tarda al menos una hora. El período de tiempo necesario depende del número de instancias de rol que vaya a implementar. Durante la implementación, el instalador ejecuta las siguientes tareas:

- Creerá un contenedor de blobs en la cuenta de almacenamiento de Azure Stack especificada.
- Creará una zona DNS y las entradas para App Service.
- Registrará el proveedor de recursos de App Service.
- Registrará los elementos de la galería de App Service.

Para implementar el proveedor de recursos de App Service, siga estos pasos:

1. Ejecute el instalador de App Service descargado anteriormente (**appservice.exe**) como administrador desde un equipo que pueda acceder al punto de conexión de administración de recursos de Azure del administrador de Azure Stack.

2. Seleccione **Implementar App Service o actualizar a la última versión**.

    ![Implementación o actualización de App Service](media/app-service-deploy-ha/01.png)

3. Acepte los términos de licencia de Microsoft y haga clic en **Siguiente**.

    ![Términos de licencia de Microsoft en App Service](media/app-service-deploy-ha/02.png)

4. Acepte los términos de licencia que no son de Microsoft y haga clic en **Siguiente**.

    ![Términos de licencia que no son de Microsoft en App Service](media/app-service-deploy-ha/03.png)

5. Proporcione la configuración del punto de conexión en la nube de App Service para el entorno de Azure Stack.

    ![Configuración del punto de conexión en la nube de App Service en App Service](media/app-service-deploy-ha/04.png)

6. **Conéctese** a la suscripción de Azure Stack que se usará para la instalación y elija la ubicación. 

    ![Conexión a la suscripción a Azure Stack en App Service](media/app-service-deploy-ha/05.png)

7. Seleccione **Usar la red virtual y las subredes existentes** y **Nombre del grupo de recursos** para el grupo de recursos que se usa para implementar la plantilla de alta disponibilidad.<br><br>A continuación, seleccione la red virtual creada como parte de la implementación de la plantilla y, después, seleccione las subredes de rol adecuadas en las opciones de la lista desplegable. 

    ![Selección de red virtual en App Service](media/app-service-deploy-ha/06.png)

8. Proporcione la información de los resultados de la plantilla registrados anteriormente para los parámetros del propietario y la ruta de acceso del recurso compartido de archivos. Cuando termine, haga clic en **Siguiente**.

    ![Información de los resultados del recurso compartido de archivos en App Service](media/app-service-deploy-ha/07.png)

9. Dado que la máquina que se usa para instalar App Service no se encuentra en la misma red virtual que el servidor de archivos utilizado para hospedar el recurso compartido de archivos de App Service, no puede resolver el nombre. **Este error es el comportamiento esperado**.<br><br>Verifique que la información especificada para las cuentas y la ruta de acceso UNC del recurso compartido de archivos es correcta. A continuación,presione **Sí** en el cuadro de diálogo de alerta para continuar con la instalación de App Service.

    ![Cuadro de diálogo del error esperado en App Service](media/app-service-deploy-ha/08.png)

    Si opta por realizar la implementación en una red virtual existente y una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. Esta regla habilita el tráfico SMB entre la subred de trabajo y el servidor de archivos. Vaya a WorkersNsg en el portal del administrador y agregue una regla de seguridad de salida con las siguientes propiedades:
    - Origen: Any
    - Intervalo de puertos de origen: *
    - Destino: Direcciones IP
    - Intervalo de direcciones IP de destino: Intervalo de direcciones IP del servidor de archivos
    - Intervalo de puertos de destino: 445
    - Protocolo: TCP
    - Acción: Allow
    - Prioridad: 700
    - Nombre: Outbound_Allow_SMB445

10. Proporcione el identificador de la aplicación de identidad y la ruta de acceso y las contraseñas de los certificados de identidad y haga clic en **Siguiente**:
    - Certificado de la aplicación de identidad (con el formato de **sso.appservice.local.azurestack.external.pfx**)
    - Certificado raíz de Azure Resource Manager (**AzureStackCertificationAuthority.cer**)

    ![Certificado de la aplicación de identificador y certificado raíz en App Service](media/app-service-deploy-ha/008.png)

11. A continuación, proporcione la información restante necesaria para los certificados siguientes y haga clic en **Siguiente**:
    - Certificado SSL de Azure Stack predeterminado (con el formato de **_.appservice.local.azurestack.external.pfx**)
    - Certificado SSL de la API (con el formato de **api.appservice.local.azurestack.external.pfx**)
    - Certificado del publicador (con el formato de **ftp.appservice.local.azurestack.external.pfx**) 

    ![Certificados de configuración adicionales en App Service](media/app-service-deploy-ha/09.png)

12. Proporcione la información de conexión de SQL Server con la información de conexión de SQL Server de los resultados de la implementación de la plantilla de alta disponibilidad:

    ![Información de conexión de SQL Server en App Service](media/app-service-deploy-ha/10.png)

13. Dado que la máquina que se usa para instalar App Service no se encuentra en la misma red virtual que la instancia de SQL Server utilizada para hospedar las bases de datos de App Service, no puede resolver el nombre.  **Este es el comportamiento esperado.**<br><br>Verifique que la información especificada para las cuentas y el nombre de SQL Server es correcta y presione **Sí** para continuar con la instalación de App Service. Haga clic en **Next**.

    ![Información de conexión de SQL Server en App Service](media/app-service-deploy-ha/11.png)

14. Acepte los valores de configuración de rol predeterminados o cambie a los valores recomendados y haga clic en **Siguiente**.<br><br>Se recomienda que los valores predeterminados de las instancias de rol de la infraestructura de App Service se modifiquen como sigue para configuraciones de alta disponibilidad:

    |Role|Valor predeterminado|Recomendación de alta disponibilidad|
    |-----|-----|-----|
    |Rol de controlador|2|2|
    |Rol de administración|1|3|
    |Rol de publicador|1|3|
    |Rol de FrontEnd|1|3|
    |Rol de trabajo compartido|1|10|
    |     |     |     |

    ![Valores de la instancia de rol de la infraestructura en App Service](media/app-service-deploy-ha/12.png)

    > [!NOTE]
    > Modificar los valores predeterminados por los recomendados en este tutorial aumenta los requisitos de hardware para la instalación de App Service. Se necesitan un total de 26 núcleos y 46 592 MB de RAM para admitir las 21 máquinas virtuales recomendadas en lugar de los 18 núcleos y los 32 256 MB de RAM predeterminados para 15 máquinas virtuales.

15. Seleccione la imagen de la plataforma que va a usar para instalar las máquinas virtuales de la infraestructura de App Service y haga clic en **Siguiente**:

    ![Selección de la imagen de la plataforma en App Service](media/app-service-deploy-ha/13.png)

16. Proporcione la información de las credenciales del rol de la infraestructura de App Service que se va a usar y haga clic en **Siguiente**:

    ![Credenciales del rol de la infraestructura en App Service](media/app-service-deploy-ha/14.png)

17. Revise la información que se usará para implementar App Service y haga clic en **Siguiente** para comenzar la implementación.

    ![Revisión del resumen de instalación en App Service](media/app-service-deploy-ha/15.png)

18. Revise el progreso de la implementación de App Service. Esta implementación puede tardar más de una hora según la configuración específica de la implementación y el hardware. Después de que el instalador finalice correctamente, seleccione **Salir**.

    ![Instalación completada de App Service](media/app-service-deploy-ha/16.png)

## <a name="next-steps"></a>Pasos siguientes

[Agregue las bases de datos appservice_hosting y appservice_metering a un grupo de disponibilidad](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/availability-group-add-a-database) si ha proporcionado al proveedor de recursos de App Service una instancia de SQL Always On. Sincronice las bases de datos para evitar cualquier pérdida de servicio en caso de una conmutación por error de la base de datos. También puede ejecutar un [script](https://blog.sqlauthority.com/2017/11/30/sql-server-alwayson-availability-groups-script-sync-logins-replicas/) para importar los inicios de sesión de AppServices desde el servidor principal original a un servidor de conmutación por error.

[Escalar horizontalmente App Service](azure-stack-app-service-add-worker-roles.md). Puede que necesite agregar trabajos de rol de la infraestructura de App Service adicionales para satisfacer la demanda esperada de la aplicación en el entorno. De forma predeterminada, App Service en Azure Stack es compatible con los niveles de trabajo gratuito y compartido. Para agregar otros niveles de trabajo, debe agregar más roles de trabajo.

[Configuración de orígenes de implementación](azure-stack-app-service-configure-deployment-sources.md). Se requiere una configuración adicional para admitir la implementación a petición de varios proveedores de control de código fuente como GitHub, BitBucket, OneDrive y DropBox.

[Copia de seguridad de App Service](app-service-back-up.md). Una vez haya implementado y configurado correctamente App Service, debe asegurarse de que se haya realizado una copia de seguridad de todos los componentes necesarios para la recuperación ante desastres. Si realiza dicha copia de seguridad de los componentes esenciales, podrá evitar la pérdida de datos y el tiempo de inactividad innecesario del servicio durante las operaciones de recuperación.
