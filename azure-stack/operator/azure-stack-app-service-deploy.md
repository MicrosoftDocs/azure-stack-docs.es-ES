---
title: Implementación de App Service en Azure Stack Hub
description: Aprenda a implementar App Service en Azure Stack Hub.
author: bryanla
ms.topic: article
ms.date: 10/28/2020
ms.author: bryanla
ms.reviewer: anwestg
ms.lastreviewed: 10/28/2020
zone_pivot_groups: state-connected-disconnected
ms.openlocfilehash: 78457d49b35c49cefc458070dc8c1f41391eb9eb
ms.sourcegitcommit: 695f56237826fce7f5b81319c379c9e2c38f0b88
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 11/12/2020
ms.locfileid: "94544584"
---
# <a name="deploy-app-service-in-azure-stack-hub"></a>Implementación de App Service en Azure Stack Hub

[!INCLUDE [Azure Stack hub update reminder](../includes/app-service-hub-update-banner.md)]

> [!IMPORTANT]
> Antes de ejecutar el instalador del proveedor de recursos, debe completar los pasos que se indican en la sección [Antes de comenzar](azure-stack-app-service-before-you-get-started.md).

::: zone pivot="state-connected"
En este artículo aprenderá a implementar App Service en Azure Stack Hub, lo cual ofrecerá a los usuarios la posibilidad de crear aplicaciones web, de API y Azure Functions. Necesita:

- Agregue el [proveedor de recursos de App Service](azure-stack-app-service-overview.md) a la implementación de Azure Stack Hub mediante los pasos descritos en este artículo.
- Después de instalar el proveedor de recursos de App Service, puede incluirlos en sus planes y ofertas. A continuación, los usuarios pueden suscribirse para obtener el servicio y empezar a crear aplicaciones.

## <a name="run-the-app-service-resource-provider-installer"></a>Ejecución del instalador del proveedor de recursos de App Service

La instalación del proveedor de recursos de App Service tarda al menos una hora. El período de tiempo necesario depende del número de instancias de rol que vaya a implementar. Durante la implementación, el instalador ejecuta las siguientes tareas:

- Registra los proveedores de recursos necesarios en la suscripción del proveedor predeterminado
- Concede acceso de colaborador a la aplicación de identidad de App Service
- Crea un grupo de recursos y una red virtual (si es necesario)
- Crea cuentas de almacenamiento y contenedores para los artefactos de instalación, servicio de uso e hidratación de recursos de App Service
- Descarga artefactos de App Service y los carga en la cuenta de almacenamiento de App Service
- Implementa App Service
- Registra el servicio de uso
- Crea entradas de DNS para App Service
- Registra los proveedores de recursos del administrador y el inquilino de App Service
- Registra elementos de la galería: aplicaciones web, API, aplicación de funciones, plan de App Service, WordPress, DNN, Orchard y Django

Para implementar el proveedor de recursos de App Service, siga estos pasos:

1. Ejecute appservice.exe como administrador desde un equipo que pueda acceder al punto de conexión de administración de recursos de Azure del administrador de Azure Stack Hub.

2. Seleccione **Implementar App Service o actualizar a la última versión**.

    ![Captura de la pantalla principal del instalador de Azure App Service.][1]

3. Revise y acepte los términos de licencia del software de Microsoft y seleccione **Siguiente**.

4. Revise y acepte los términos de licencia de terceros y seleccione **Siguiente**.

5. Asegúrese de que la información de configuración de nube de App Service es correcta. Si usó la configuración predeterminada durante la implementación del ASDK, puede aceptar los valores predeterminados. Sin embargo, si personalizó las opciones cuando implementó el ASDK o va a realizar la implementación en un sistema integrado de Azure Stack Hub, debe editar los valores de esta ventana para reflejar las diferencias.

   Por ejemplo, si se usa el sufijo de dominio mycloud.com, el punto de conexión de Azure Resource Manager del inquilino de Azure Stack Hub debe cambiarse a management.&lt;región&gt;.mycloud.com. Revise estos valores y, a continuación, seleccione **Siguiente** para guardar la configuración.

   ![Captura de la pantalla para especificar los puntos de conexión de ARM para App Service.][2]

6. En la siguiente página del instalador de App Service, se conectará a su instancia de Azure Stack Hub:

    1. Seleccione el método de conexión que quiera usar: **Credencial** o **Entidad de servicio**
 
        - **Credential:**
            - Si usa Azure Active Directory (Azure AD), escriba la cuenta de administrador y la contraseña de Azure AD que proporcionó al implementar Azure Stack Hub. Seleccione **Conectar**.
            - Si se usan los Servicios de federación de Active Directory (AD FS), debe proporcionar la cuenta de administrador. Por ejemplo, cloudadmin@azurestack.local. Escriba la contraseña y, a continuación, seleccione **Conectar**.

        - **Entidad de seguridad de servicio**
            - La entidad de servicio que utilice **debe** tener derechos de **Propietario** en la **suscripción de proveedor predeterminada**.
            - Proporcione el **identificador de entidad de servicio**, el **archivo de certificado** y la **contraseña** y seleccione **Conectar**.

    1. En **Suscripciones de Azure Stack Hub**, seleccione la **suscripción de proveedor predeterminada**.  Azure App Service en Azure Stack Hub **debe** implementarse en la **suscripción de proveedor predeterminada**.

    1. En **Ubicaciones de Azure Stack Hub**, seleccione la ubicación que corresponda a la región en la que se va a implementar. Por ejemplo, seleccione **local** si va a realizar la implementación en el ASDK.

    ![Captura de pantalla que muestra dónde se especifica la información de suscripción de Azure Stack Hub en el instalador de App Service.][3]

7. Ahora puede realizar la implementación en una red virtual existente que haya configurado [siguiendo estos pasos](azure-stack-app-service-before-you-get-started.md#virtual-network), o dejar que el instalador de App Service cree una nueva red virtual y subredes. Para crear una red virtual, siga estos pasos:

   a. Seleccione **Crear una red virtual con la configuración predeterminada**, acepte los valores predeterminados y haga clic en **Siguiente**.

   b. O, seleccione **Usar la red virtual y las subredes existentes**. Complete las siguientes acciones:

     - Seleccione el **grupo de recursos** que contiene la red virtual.
     - Elija el nombre de la instancia de **Virtual Network** en la que desea realizar la implementación.
     - Seleccione los valores correctos de **subred** para cada una de las subredes de rol necesarias.
     - Seleccione **Next** (Siguiente).

   ![Captura de pantalla que muestra la pantalla en la que se configura la red virtual en el instalador de App Service.][4]

8. Escriba la información para el recurso compartido de archivos y, a continuación, seleccione **Siguiente**. La dirección del recurso compartido debe usar el nombre de dominio completo (FQDN) o la dirección IP del servidor de archivos. Por ejemplo, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, o \\\10.0.0.1\websites.  Si usa un servidor de archivos que está unido a un dominio, debe proporcionar el nombre de usuario completo, incluido el dominio. Por ejemplo, miDominioDeServidorDeArchivos\propietarioDeRecursoCompartido.

   >[!NOTE]
   >El instalador intenta comprobar la conectividad con el recurso compartido de archivos antes de continuar. Sin embargo, si implementa en una red virtual existente, puede que se produzca un error en esta prueba de conectividad. Se le envía una advertencia y un aviso para continuar. Si la información del recurso compartido de archivos es correcta, continúe con la implementación.

   ![Captura de pantalla que muestra la pantalla en la que se configuran las rutas de acceso y credenciales del servidor de archivos en el instalador de App Service][5]

9. En la página siguiente del instalador de App Service, siga estos pasos:

   a. En el cuadro **Identificador de la aplicación de identidad**, escriba el GUID de la aplicación que creó como parte de los [requisitos previos](azure-stack-app-service-before-you-get-started.md).

   b. En el cuadro **Identity Application certificate file** (Archivo del certificado de la aplicación de identidad) escriba (o vaya a) la ubicación del archivo del certificado.

   c. En el cuadro **Identity Application certificate password** (Contraseña del certificado de la aplicación de identidad), escriba la contraseña para el certificado. Esta contraseña es la que anotó cuando usó el script para crear los certificados.

   d. En el cuadro **Azure Resource Manager root certificate file** (Archivo del certificado raíz de Azure Resource Manager) escriba (vaya a) la ubicación del archivo del certificado.

   e. Seleccione **Next** (Siguiente).

   ![Captura de pantalla que muestra la pantalla en la que se proporcionan los detalles de la aplicación de identidad de AAD/ADFS y el certificado de Resource Manager de Azure Stack, en el instalador de App Service][6]

10. Para cada uno de los tres cuadros de archivo de certificado, seleccione **Examinar** y navegue hasta el archivo de certificado adecuado. Debe proporcionar la contraseña de cada certificado. Estos certificados son los que ha creado en [Requisitos previos para implementar App Service en Azure Stack Hub](azure-stack-app-service-before-you-get-started.md). Seleccione **Siguiente** después de escribir toda la información.

    | Box | Ejemplo de nombre de archivo de certificado |
    | --- | --- |
    | **Archivo de certificado SSL predeterminado de App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **Archivo de certificado SSL de API de App Service** | api.appservice.local.AzureStack.external.pfx |
    | **Archivo de certificado SSL del publicador de App Service** | ftp.appservice.local.AzureStack.external.pfx |

    Si usó un sufijo de dominio diferente cuando creó los certificados, los nombres de archivo de certificado no usan *local.AzureStack.external*. En su lugar, use la información del dominio personalizado.

    ![Captura de pantalla que muestra la pantalla en la que se proporcionan los detalles de los certificados necesarios en el instalador de App Service][7]

11. Escriba los detalles de SQL Server para la instancia de servidor que se usa para hospedar la base de datos del proveedor de recursos de App Service y, después, seleccione **Siguiente**. El instalador valida las propiedades de conexión de SQL.<br><br>El instalador de App Service intenta probar la conectividad con la instancia de SQL Server antes de continuar. Si realiza la implementación en una red virtual existente, puede que se produzca un error en esta prueba de conectividad. Se le envía una advertencia y un aviso para continuar. Si la información de SQL Server es correcta, continúe con la implementación.

    ![Captura de pantalla que muestra la pantalla en la que se proporcionan los detalles de conexión de SQL Server en el instalador de App Service][8]

12. Revise las opciones de SKU y la instancia de rol. Los valores predeterminados se rellenan con el número mínimo de instancias y la SKU mínima de cada rol en una implementación en producción.  En el caso de las implementaciones de ASDK, puede reducir verticalmente las instancias para las SKU inferiores para reducir el uso de los núcleos y la memoria pero experimentará una degradación del rendimiento. Se proporciona un resumen de los requisitos de memoria y vCPU para ayudar a planear la implementación. Después de realizar las selecciones, elija **Siguiente**.

    >[!NOTE]
    >Para las implementaciones de producción, siga las instrucciones que encontrará en [Planeamiento de la capacidad de los roles de servidor de Azure App Service en Azure Stack Hub](azure-stack-app-service-capacity-planning.md).

    | Role | Número mínimo de instancias | SKU mínima | Notas |
    | --- | --- | --- | --- |
    | Controller | 2 | Standard_A4_v2 - (4 núcleos, 8192 MB) | Administra y mantiene el estado de la nube de App Service. |
    | Administración | 1 | Standard_D3_v2 - (4 cores, 14 336 MB) | Administra los puntos de conexión de API y Azure Resource Manager de App Service, las extensiones de portales (portal de Functions, administración e inquilino) y el servicio de datos. Para admitir la conmutación por error, se incrementan las instancias recomendadas a 2. |
    | Publicador | 1 | Standard_A2_v2 - (2 núcleos, 4096 MB) | Publica contenido a través de la implementación de web y FTP. |
    | FrontEnd | 1 | Standard_A4_v2 - (4 núcleos, 8192 MB) | Enruta las solicitudes a las aplicaciones de App Service. |
    | Trabajo compartido | 1 | Standard_A4_v2 - (4 núcleos, 8192 MB) | Hospeda aplicaciones de API o web y aplicaciones de Azure Functions. Puede ser conveniente agregar más instancias. Como operador, puede definir su oferta y elegir cualquier nivel de SKU. Los niveles deben tener una vCPU como mínimo. |

    ![Captura de pantalla que muestra la pantalla en la que se proporciona el número de instancias de rol y su SKU de proceso correspondiente en el instalador de App Service][9]

    > [!NOTE]
    > **Windows Server 2016 Core no es una imagen de plataforma compatible para su uso con Azure App Service en Azure Stack Hub.  No use imágenes de evaluación para las implementaciones de producción.**

13. En el cuadro **Seleccionar imagen de plataforma**, elija su imagen de máquina virtual (VM) Windows Server 2016 de implementación entre las que están disponibles en el proveedor de recursos de procesos para la nube de App Service. Seleccione **Next** (Siguiente).

14. En la página siguiente del instalador de App Service, siga estos pasos:

     a. Escriba el nombre de usuario y la contraseña del administrador de máquinas virtuales con rol de trabajo.

     b. Escriba el nombre de usuario y la contraseña del administrador de máquinas virtuales con otros roles.

     c. Seleccione **Next** (Siguiente).

    ![Captura de pantalla que muestra la pantalla donde se selecciona la imagen de la plataforma Windows que va a usar el instalador de App Service][10]

15. En la página de resumen del instalador de App Service, siga estos pasos:

    a. Compruebe las selecciones realizadas. Para realizar cambios, use los botones **Anterior** para visitar las páginas anteriores.

    b. Si las configuraciones son correctas, active la casilla.

    c. Para iniciar la implementación, seleccione **Siguiente**.

    ![Captura de pantalla que muestra el resumen de las opciones especificadas para su implementación por el instalador de App Service][11]

16. En la página siguiente del instalador de App Service, siga estos pasos:

    a. Realice un seguimiento del progreso de la instalación. La implementación de App Service en Azure Stack Hub puede tardar hasta 240 minutos según las opciones predeterminadas y la antigüedad de la imagen base de Windows 2016 Datacenter.

    b. Después de que el instalador finalice correctamente, seleccione **Salir**.

    ![Captura de pantalla que muestra el progreso de la implementación realizada en el instalador de App Service][12]

## <a name="post-deployment-steps"></a>Pasos posteriores a la implementación

> [!IMPORTANT]
> Si ha proporcionado el proveedor de recursos de App Service con una instancia de SQL Always On, **debe** [agregar las bases de datos appservice_hosting y appservice_metering a un grupo de disponibilidad](/sql/database-engine/availability-groups/windows/availability-group-add-a-database), así como sincronizar dichas bases de datos para evitar la pérdida de servicio en caso de producirse una conmutación por error de base de datos.

Si va a realizar la implementación en una red virtual existente y a usar una dirección IP interna para conectarse al servidor de archivos, debe agregar una regla de seguridad de salida. Esta regla habilita el tráfico SMB entre la subred de trabajo y el servidor de archivos. En el portal del administrador vaya al grupo de seguridad de red WorkersNsg y agregue una regla de seguridad de salida con las siguientes propiedades:

- Origen: Any
- Intervalo de puertos de origen: *
- Destino: Direcciones IP
- Intervalo de direcciones IP de destino: Intervalo de direcciones IP del servidor de archivos
- Intervalo de puertos de destino: 445
- Protocolo: TCP
- Acción: Allow
- Prioridad: 700
- Nombre: Outbound_Allow_SMB445

## <a name="validate-the-app-service-on-azure-stack-hub-installation"></a>Validación de la instalación de App Service en Azure Stack Hub

1. En el portal del administrador de Azure Stack Hub, vaya a **Administration - App Service** (Administración: App Service).

2. En la información general, debajo del estado, compruebe que en **Estado** se muestra el mensaje **Todos los roles están listos**.

    ![Administración de App Service en el portal del administrador de Azure Stack Hub](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-app-service-on-azure-stack-hub"></a>Prueba de App Service en Azure Stack Hub

Después de implementar y registrar el proveedor de recursos de App Service, pruébelo para asegurarse de que los usuarios pueden implementar aplicaciones web y de API.

>[!NOTE]
>Debe crear una oferta que tenga el espacio de nombres Microsoft.Web en el plan. También necesita una suscripción de inquilino que se suscriba a la oferta. Para más información, consulte [Creación de una oferta](azure-stack-create-offer.md) y [Creación de un plan](azure-stack-create-plan.md).
>
>*Debe* tener una suscripción de inquilino para crear aplicaciones que usen App Service en Azure Stack Hub. Las únicas tareas que un administrador del servicio puede completar en el portal del administrador están relacionadas con la administración del proveedor de recursos de App Service. Entre estas tareas se incluyen la adición de capacidad, la configuración de orígenes de implementación y la adición de niveles de trabajo y SKU.
>
>Para crear aplicaciones web, de API y Azure Functions, se debe usar el portal de usuarios y tener una suscripción de inquilino.
>

Para crear una aplicación web de prueba, siga estos pasos:

1. En el portal de usuarios de Azure Stack Hub, seleccione **+ Crear un recurso** > **Web y móvil** > **Aplicación web**.

2. En **Aplicación web**, escriba un nombre en **Aplicación web**.

3. En **Grupo de recursos**, seleccione **Nuevo**. Escriba un nombre para el **grupo de recursos**.

4. Seleccione **Plan de App Service/Ubicación** > **Crear nuevo**.

5. En **plan de App Service**, escriba un nombre para el **plan de App Service**.

6. Seleccione **Plan de tarifa** > **Free-Shared** (Gratis - Compartido) o **Shared-Shared** (Compartido - Compartido) > **Seleccionar** > **Aceptar** > **Crear**.

7. Aparecerá un icono para la nueva aplicación web en el panel. Seleccione el icono.

8. En la hoja **Aplicación web**, seleccione **Examinar** para ver el sitio web predeterminado para esta aplicación.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implementación de un sitio web de WordPress, DNN o Django (opcional)

1. En el portal de usuarios de Azure Stack Hub, seleccione **+** , vaya a Azure Marketplace, implemente un sitio web de Django y, a continuación, espere a que finalice la implementación. La plataforma web de Django usa una base de datos basada en el sistema de archivos. No se necesita ningún proveedor de recursos adicional, como SQL o MySQL.

2. Si también implementó un proveedor de recursos de MySQL, puede implementar un sitio web de WordPress en Marketplace. Cuando se le pidan los parámetros de la base de datos, escriba el nombre de usuario, como *Usario1\@Servidor1*, con el nombre de usuario y el nombre del servidor de su elección.

3. Si también implementó un proveedor de recursos de SQL Server, puede implementar un sitio web de DNN en Marketplace. Cuando se le pidan los parámetros de la base de datos, elija una base de datos del equipo que ejecute la instancia de SQL Server que está conectado a su proveedor de recursos.
::: zone-end



<!----------------------------------------- DISCONNECTED PIVOT -------------------------------------------->
::: zone pivot="state-disconnected"
En este artículo aprenderá a implementar el [proveedor de recursos de Azure App Service](azure-stack-app-service-overview.md) en un entorno de Azure Stack Hub que:
- No esté conectado a Internet.
- Esté protegido por Servicios de federación de Active Directory (AD FS).

Para agregar el proveedor de recursos de Azure App Service a la implementación de Azure Stack Hub sin conexión, debe completar estas tareas de nivel superior:

1. Realice los [pasos previos necesarios](azure-stack-app-service-before-you-get-started.md) (como la compra de certificados, que pueden tardar unos días en recibirse).
2. [Descargue y extraiga los archivos auxiliares y de instalación](azure-stack-app-service-before-you-get-started.md) en una máquina conectada a Internet.
3. Cree un paquete de instalación sin conexión.
4. Ejecute el archivo del instalador appservice.exe.

## <a name="create-an-offline-installation-package"></a>Creación de un paquete de instalación sin conexión

Para implementar Azure App Service en un entorno sin conexión, primero debe crear un paquete de instalación sin conexión en una máquina que esté conectada a Internet.

1. Ejecute el instalador AppService.exe en una máquina que esté conectada a Internet. 

2. Seleccione **Opciones avanzadas** > **Crear paquete de instalación sin conexión**. Este paso tardará varios minutos en completarse.

    ![Creación de un paquete sin conexión en el instalador de Azure App Service][13]

3. El instalador de Azure App Service crea un paquete de instalación sin conexión y muestra su ruta de acceso. Puede seleccionar **Abrir carpeta** para abrir la carpeta en el Explorador de archivos.

    ![El paquete de instalación sin conexión generado correctamente en el instalador de Azure App Service][14]

4. Copie el instalador (AppService.exe) y el paquete de instalación sin conexión en una máquina que tenga conectividad con Azure Stack Hub.

## <a name="complete-the-offline-installation-of-azure-app-service-on-azure-stack-hub"></a>Realización de la instalación sin conexión de Azure App Service en Azure Stack Hub

1. Ejecute appservice.exe como administrador desde un equipo que pueda acceder al punto de conexión de administración de recursos de Azure del administrador de Azure Stack Hub.

1. Seleccione **Opciones avanzadas** > **Completar la instalación sin conexión**.

    ![Realización de la instalación sin conexión en el instalador de Azure App Service][15]

1. Vaya a la ubicación del paquete de instalación sin conexión que creó previamente y seleccione **Siguiente**.

    ![Especificación de la ruta de acceso del paquete de instalación sin conexión en el instalador de Azure App Service][16]

1. Revise y acepte los términos de licencia del software de Microsoft y seleccione **Siguiente**.

1. Revise y acepte los términos de licencia de terceros y seleccione **Siguiente**.


1. Asegúrese de que la información de configuración en la nube de Azure App Service sea correcta. Si ha usado la configuración predeterminada durante la implementación del ASDK, puede aceptar los valores predeterminados. Sin embargo, si personalizó las opciones cuando implementó Azure Stack Hub o va a realizar la implementación en un sistema integrado, debe editar los valores de esta ventana para reflejar esos cambios. Por ejemplo, si usa el sufijo de dominio mycloud.com, el punto de conexión de Azure Resource Manager del inquilino de Azure Stack Hub debe cambiarse por `management.<region>.mycloud.com`. Después de confirmar la información, seleccione **Siguiente**.

    ![Configuración de la nube de Azure App Service en el instalador de Azure App Service][2]

1. En la siguiente página del instalador de App Service, se conectará a su instancia de Azure Stack Hub:

    1. Seleccione el método de conexión que quiera usar: **Credencial** o **Entidad de servicio**
        - **Credential:**
            - Si usa Azure Active Directory (Azure AD), escriba la cuenta de administrador y la contraseña de Azure AD que proporcionó al implementar Azure Stack Hub. Seleccione **Conectar**.
            - Si se usan los Servicios de federación de Active Directory (AD FS), debe proporcionar la cuenta de administrador. Por ejemplo, cloudadmin@azurestack.local. Escriba la contraseña y, a continuación, seleccione **Conectar**.
        - **Entidad de seguridad de servicio**
            - La entidad de servicio que utilice **debe** tener derechos de **Propietario** en la **suscripción de proveedor predeterminada**.
            - Proporcione el **identificador de entidad de servicio**, el **archivo de certificado** y la **contraseña** y seleccione **Conectar**.

    1. En **Suscripciones de Azure Stack Hub**, seleccione la **suscripción de proveedor predeterminada**.  Azure App Service en Azure Stack Hub **debe** implementarse en la **suscripción de proveedor predeterminada**.

    1. En **Ubicaciones de Azure Stack Hub**, seleccione la ubicación que corresponda a la región en la que se va a implementar. Por ejemplo, seleccione **local** si va a realizar la implementación en el ASDK.

1. Puede permitir que el instalador de Azure App Service cree una red virtual y las subredes asociadas. O bien, puede realizar la implementación en una red virtual existente, tal y como se configura mediante [estos pasos](azure-stack-app-service-before-you-get-started.md#virtual-network).
   - Para usar el método del instalador de Azure App Service, seleccione **Crear una red virtual con la configuración predeterminada**, acepte los valores predeterminados y seleccione **Siguiente**.
   - Para realizar la implementación en una red existente, seleccione **Usar la red virtual y las subredes existentes**, y después:
       1. Seleccione la opción **Grupo de recursos** que contiene la red virtual.
       2. Elija el nombre de la instancia de **Virtual Network** en la que desea realizar la implementación.
       3. Seleccione los valores correctos de **subred** para cada una de las subredes de rol necesarias.
       4. Seleccione **Next** (Siguiente).

      ![Información de red virtual y subred en el instalador de Azure App Service][4]

1. Escriba la información para el recurso compartido de archivos y, a continuación, seleccione **Siguiente**. La dirección del recurso compartido de archivos debe usar el nombre de dominio completo (FQDN) o la dirección IP del servidor de archivos. Por ejemplo, \\\appservicefileserver.local.cloudapp.azurestack.external\websites, o \\\10.0.0.1\websites.  Si usa un servidor de archivos que está unido a un dominio, debe proporcionar el nombre de usuario completo, incluido el dominio. Por ejemplo: `<myfileserverdomain>\<FileShareOwner>`.

    > [!NOTE]
    > El instalador intenta comprobar la conectividad con el recurso compartido de archivos antes de continuar. Sin embargo, si ha elegido realizar la implementación en una red virtual existente, puede que el instalador no pueda conectarse al recurso compartido de archivos y muestre una advertencia, que le pregunta si desea continuar. Compruebe la información del recurso compartido de archivos y continúe si es correcta.

   ![Información del recurso compartido de archivos en el instalador de Azure App Service][5]

1. En la página siguiente:
    1. En el cuadro **Identificador de la aplicación de identidad**, escriba el GUID de la aplicación que creó como parte de los [requisitos previos](azure-stack-app-service-before-you-get-started.md).
    1. En el cuadro **Identity Application certificate file** (Archivo del certificado de la aplicación de identidad) escriba (o vaya a) la ubicación del archivo del certificado.
    1. En el cuadro **Identity Application certificate password** (Contraseña del certificado de la aplicación de identidad), escriba la contraseña para el certificado. Esta contraseña es la que anotó cuando usó el script para crear los certificados.
    1. En el cuadro **Azure Resource Manager root certificate file** (Archivo del certificado raíz de Azure Resource Manager) escriba (vaya a) la ubicación del archivo del certificado.
    1. Seleccione **Next** (Siguiente).

    ![Especificación del identificador de la aplicación y la información del certificado en el instalador de Azure App Service][6]

1. Para cada uno de los tres cuadros de archivo de certificado, seleccione **Examinar** y navegue hasta el archivo de certificado adecuado. Debe proporcionar la contraseña de cada certificado. Estos certificados son los que ha creado en [Requisitos previos para implementar App Service en Azure Stack Hub](azure-stack-app-service-before-you-get-started.md). Seleccione **Siguiente** después de escribir toda la información.

    | Box | Ejemplo de nombre de archivo de certificado |
    | --- | --- |
    | **Archivo de certificado SSL predeterminado de App Service** | \_.appservice.local.AzureStack.external.pfx |
    | **Archivo de certificado SSL de API de App Service** | api.appservice.local.AzureStack.external.pfx |
    | **Archivo de certificado SSL del publicador de App Service** | ftp.appservice.local.AzureStack.external.pfx |

    Si usó un sufijo de dominio diferente cuando creó los certificados, los nombres de archivo de certificado no usan *local.AzureStack.external*. En su lugar, use la información del dominio personalizado.

    ![Especificación de la información del certificado SSL en el instalador de Azure App Service][7]

1. Escriba los detalles de SQL Server de la instancia de servidor que se usa para hospedar las bases de datos del proveedor de recursos de Azure App Service y, después, seleccione **Siguiente**. El instalador valida las propiedades de conexión de SQL. **Debe** escribir la dirección IP interna o el nombre de dominio completo para el nombre de SQL Server.

    > [!NOTE]
    > El instalador intenta comprobar la conectividad con el equipo que ejecuta la instancia de SQL Server antes de continuar. Sin embargo, si ha elegido realizar la implementación en una red virtual existente, puede que el instalador no pueda conectarse al equipo que ejecuta la instancia de SQL Server y muestre una advertencia, que le pregunta si desea continuar. Compruebe la información de SQL Server y continúe si es correcta.
    >
    > A partir de Azure App Service en Azure Stack Hub 1.3 en adelante, el instalador comprueba que el equipo que ejecuta la instancia de SQL Server tenga la contención de base de datos habilitada en el nivel de SQL Server. Si no es así, se le indicará la siguiente excepción:
    > ```sql
    >    Enable contained database authentication for SQL server by running below command on SQL server (Ctrl+C to copy)
    >    ***********************************************************
    >    sp_configure 'contained database authentication', 1;
    >    GO
    >    RECONFIGURE;
    >    GO
    >    ***********************************************************
    > ```
    > Para más información, consulte las [notas de la versión de Azure App Service en Azure Stack Hub 1.3](azure-stack-app-service-release-notes-update-three.md).

    ![Especificación de la información de SQL Server en el instalador de Azure App Service][8]

1. Revise las opciones de SKU y la instancia de rol. Los valores predeterminados se rellenan con el número mínimo de instancias y la SKU mínima de cada rol en una implementación en producción.  En el caso de las implementaciones de ASDK, puede reducir verticalmente las instancias para las SKU inferiores para reducir el uso de los núcleos y la memoria pero experimentará una degradación del rendimiento.  Se proporciona un resumen de los requisitos de memoria y vCPU para ayudar a planear la implementación. Después de realizar las selecciones, elija **Siguiente**.

     > [!NOTE]
     > En las implementaciones de producción, siga las instrucciones que se indican en [Planeamiento de la capacidad de los roles de servidor de App Service en Azure Stack Hub](azure-stack-app-service-capacity-planning.md).
     >
     >

    
    | Role | Número mínimo de instancias | SKU mínima | Notas |
    | --- | --- | --- | --- |
    | Controller | 2 | Standard_A4_v2 - (4 núcleos, 8192 MB) | Administra y mantiene el estado de la nube de App Service. |
    | Administración | 1 | Standard_D3_v2 - (4 cores, 14 336 MB) | Administra los puntos de conexión de API y Azure Resource Manager de App Service, las extensiones de portales (portal de Functions, administración e inquilino) y el servicio de datos. Para admitir la conmutación por error, se incrementan las instancias recomendadas a 2. |
    | Publicador | 1 | Standard_A2_v2 - (2 núcleos, 4096 MB) | Publica contenido a través de la implementación de web y FTP. |
    | FrontEnd | 1 | Standard_A4_v2 - (4 núcleos, 8192 MB) | Enruta las solicitudes a las aplicaciones de App Service. |
    | Trabajo compartido | 1 | Standard_A4_v2 - (4 núcleos, 8192 MB) | Hospeda aplicaciones de API o web y aplicaciones de Azure Functions. Puede ser conveniente agregar más instancias. Como operador, puede definir su oferta y elegir cualquier nivel de SKU. Los niveles deben tener una vCPU como mínimo. |

    ![Establecimiento de los niveles de rol y las opciones de SKU en el instalador de Azure App Service][9]

1. En el cuadro **Seleccionar imagen de plataforma**, elija su imagen de implementación de máquina virtual Windows Server 2016 entre las que están disponibles en el proveedor de recursos de proceso para la nube de Azure App Service. Seleccione **Next** (Siguiente).

    > [!NOTE]
    > Windows Server 2016 Core *no* es una imagen de plataforma compatible para su uso con Azure App Service en Azure Stack Hub.  No use imágenes de evaluación para las implementaciones de producción. Azure App Service en Azure Stack Hub requiere que Microsoft.Net 3.5.1 SP1 se active en la imagen utilizada para la implementación. Las imágenes de Windows Server 2016 distribuidas en Marketplace no tienen habilitada esta característica. Por lo tanto, debe crear y usar una imagen de Windows Server 2016 con esta característica habilitada previamente.
    >
    > Consulte [Adición de una imagen de máquina virtual personalizada a Azure Stack Hub](azure-stack-add-vm-image.md) para más información sobre cómo crear una imagen personalizada y agregarla a Marketplace. Asegúrese de especificar lo siguiente al agregar la imagen a Marketplace:
    >
    >- Publicador = MicrosoftWindowsServer
    >- Oferta = WindowsServer
    >- SKU = 2016-Datacenter
    >- Versión = especifique la versión más "reciente"

1. En la página siguiente:
     1. Escriba el nombre de usuario y la contraseña del administrador de máquinas virtuales con rol de trabajo.
     2. Escriba el nombre de usuario y la contraseña del administrador de máquinas virtuales con otros roles.
     3. Seleccione **Next** (Siguiente).

    ![Especificación de administradores de máquina virtual de rol en el instalador de Azure App Service][10]

1. En la página de resumen:
    1. Compruebe las selecciones realizadas. Para realizar cambios, use los botones **Anterior** para visitar las páginas anteriores.
    2. Si las configuraciones son correctas, active la casilla.
    3. Para iniciar la implementación, seleccione **Siguiente**.

    ![Resumen de las selecciones realizadas en el instalador de Azure App Service][11]

1. En la página siguiente:
    1. Realice un seguimiento del progreso de la instalación. La implementación de App Service en Azure Stack Hub puede tardar hasta 240 minutos según las opciones predeterminadas y la antigüedad de la imagen base de Windows 2016 Datacenter.

    2. Cuando el instalador termine de ejecutarse, seleccione **Salir**.

    ![Seguimiento del proceso de instalación en el instalador de Azure App Service][12]

## <a name="post-deployment-steps"></a>Pasos posteriores a la implementación

> [!IMPORTANT]
> Si ha proporcionado al proveedor de recursos de Azure App Service una instancia de SQL Always On, **debe**[agregar las bases de datos appservice_hosting y appservice_metering a un grupo de disponibilidad](/sql/database-engine/availability-groups/windows/availability-group-add-a-database). También debe sincronizar las bases de datos para evitar cualquier pérdida de servicio en caso de una conmutación por error de la base de datos.

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

## <a name="validate-the-azure-app-service-on-azure-stack-hub-installation"></a>Validación de la instalación de Azure App Service en Azure Stack Hub

1. En el portal del administrador de Azure Stack Hub, vaya a **Administration - App Service** (Administración: App Service).

1. En la información general, debajo del estado, compruebe que en **Estado** se muestra el mensaje **Todos los roles están listos**.

    ![Introducción a la administración de Azure App Service](media/azure-stack-app-service-deploy/image12.png)

## <a name="test-drive-azure-app-service-on-azure-stack-hub"></a>Prueba de Azure App Service en Azure Stack Hub

Después de implementar y registrar el proveedor de recursos de Azure App Service, pruébelo para asegurarse de que los usuarios pueden implementar aplicaciones web y de API.

> [!NOTE]
> Debe crear una oferta que tenga el espacio de nombres Microsoft.Web dentro del plan. A continuación, debe tener una suscripción de inquilino que suscriba esta oferta. Para más información, consulte [Creación de una oferta](azure-stack-create-offer.md) y [Creación de un plan](azure-stack-create-plan.md).
>
> *Debe* tener una suscripción de inquilino para crear aplicaciones que usen Azure App Service en Azure Stack Hub. Las únicas funcionalidades que un administrador del servicio puede realizar en el portal del administrador están relacionadas con la administración del proveedor de recursos de Azure App Service. Entre estas funcionalidades se incluyen la adición de capacidad, la configuración de orígenes de implementación y la adición de niveles de trabajo y SKU.
>
> A partir de la tercera versión preliminar técnica, para crear aplicaciones web, de API y de Azure Functions, se debe usar el portal de usuarios y tener una suscripción de inquilino.

1. En el portal de usuarios de Azure Stack Hub, seleccione **+ Crear un recurso** > **Web y móvil** > **Aplicación web**.

1. En la hoja **Aplicación web**, escriba un nombre en el cuadro **Aplicación web**.

1. En **Grupo de recursos**, seleccione **Nuevo**. Escriba un nombre en el cuadro **Grupo de recursos**.

1. Seleccione **Plan de App Service/Ubicación** > **Crear nuevo**.

1. En la hoja **Plan de App Service**, escriba un nombre en el cuadro **Plan de App Service**.

1. Seleccione **Plan de tarifa** > **Free-Shared** (Gratis - Compartido) o **Shared-Shared** (Compartido - Compartido) > **Seleccionar** > **Aceptar** > **Crear**.

1. En menos de un minuto, aparecerá un icono para la nueva aplicación web en el panel. Seleccione el icono.

1. En la hoja **Aplicación web**, seleccione **Examinar** para ver el sitio web predeterminado para esta aplicación.

## <a name="deploy-a-wordpress-dnn-or-django-website-optional"></a>Implementación de un sitio web de WordPress, DNN o Django (opcional)

1. En el portal de usuarios de Azure Stack Hub, seleccione **+** , vaya a Azure Marketplace, implemente un sitio web de Django y espere a que se complete correctamente. La plataforma web de Django usa una base de datos basada en el sistema de archivos. No se necesita ningún proveedor de recursos adicional, como SQL o MySQL.

1. Si también implementó un proveedor de recursos de MySQL, puede implementar un sitio web de WordPress en Azure Marketplace. Cuando se le pidan los parámetros de la base de datos, escriba el nombre de usuario, como *Usario1\@Servidor1*, con el nombre de usuario y el nombre del servidor de su elección.

1. Si también implementó un proveedor de recursos de SQL Server, puede implementar un sitio web de DNN en Azure Marketplace. Cuando se le pidan los parámetros de la base de datos, elija una base de datos del equipo que ejecute la instancia de SQL Server que esté conectada a su proveedor de recursos.

::: zone-end

## <a name="next-steps"></a>Pasos siguientes

Prepárese para realizar operaciones de administración adicionales para App Service en Azure Stack Hub:

- [Planeamiento de capacidad](azure-stack-app-service-capacity-planning.md)
- [Configuración de orígenes de implementación](azure-stack-app-service-configure-deployment-sources.md)

<!-- Connected image references-->
[1]: ./media/azure-stack-app-service-deploy/app-service-installer.png
[2]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-arm-endpoints.png
[3]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-subscription-information.png
[4]: ./media/azure-stack-app-service-deploy/app-service-default-VNET-config.png
[5]: ./media/azure-stack-app-service-deploy/app-service-fileshare-configuration.png
[6]: ./media/azure-stack-app-service-deploy/app-service-identity-app.png
[7]: ./media/azure-stack-app-service-deploy/app-service-certificates.png
[8]: ./media/azure-stack-app-service-deploy/app-service-sql-configuration.png
[9]: ./media/azure-stack-app-service-deploy/app-service-cloud-quantities.png
[10]: ./media/azure-stack-app-service-deploy/app-service-role-credentials.png
[11]: ./media/azure-stack-app-service-deploy/app-service-azure-stack-deployment-summary.png
[12]: ./media/azure-stack-app-service-deploy/app-service-deployment-progress.png

<!-- Disconnected image references-->
[13]: ./media/azure-stack-app-service-deploy/app-service-exe-advanced-create-package.png
[14]: ./media/azure-stack-app-service-deploy/app-service-exe-advanced-create-package-complete.png
[15]: ./media/azure-stack-app-service-deploy/app-service-exe-advanced-complete-offline.png
[16]: ./media/azure-stack-app-service-deploy/app-service-exe-advanced-complete-offline-package-browse.png