---
title: Oferta de bases de datos SQL de alta disponibilidad en Azure Stack | Microsoft Docs
description: Obtenga información sobre cómo crear un equipo host proveedor de recursos de SQL Server y bases de datos AlwaysOn SQL de alta disponibilidad con Azure Stack.
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
ms.date: 02/25/2019
ms.author: justinha
ms.reviewer: xiaofmao
ms.lastreviewed: 10/23/2018
ms.openlocfilehash: fa9577bf0a620f8911ee6cf5238b55f460076883
ms.sourcegitcommit: 3f52cf06fb5b3208057cfdc07616cd76f11cdb38
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 06/21/2019
ms.locfileid: "67316322"
---
# <a name="tutorial-offer-highly-available-sql-databases"></a>Tutorial: Oferta de bases de datos SQL de alta disponibilidad

Como operador de Azure Stack, puede configurar máquinas virtuales de servidor para hospedar bases de datos de SQL Server. Una vez que se haya creado correctamente un servidor de hospedaje de SQL y Azure Stack lo administre, los usuarios suscritos a servicios de SQL pueden crear fácilmente bases de datos SQL.

Este tutorial muestra cómo usar una plantilla de inicio rápido de Azure Stack para crear un [grupo de disponibilidad AlwaysOn de SQL Server](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server?view=sql-server-2017), agregarlo como servidor de hospedaje SQL de Azure Stack y, a continuación, crear una base de datos SQL de alta disponibilidad.

Lo qué aprenderá:

> [!div class="checklist"]
> * Creación de un grupos de disponibilidad AlwaysOn de SQL Server a partir de una plantilla
> * Creación de un servidor de hospedaje SQL de Azure Stack
> * Creación de una base de datos SQL de alta disponibilidad

En este tutorial, utilizará los elementos disponibles de Marketplace para Azure Stack para crear y configurar un grupo de disponibilidad AlwaysOn de SQL Server para dos máquinas virtuales. 

Antes de comenzar los pasos descritos en este tutorial, asegúrese de que el [proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md) ha instalado correctamente y ha puesto a disposición de los usuarios los siguientes elementos en el Marketplace para Azure Stack:

> [!IMPORTANT]
> Todos los elementos siguientes son necesarios para utilizar la plantilla de inicio rápido de Azure Stack.

- Imagen de Marketplace de [Windows Server 2016 Datacenter](https://azuremarketplace.microsoft.com/marketplace/apps/MicrosoftWindowsServer.WindowsServer).
- SQL Server 2016 SP1 o SP2 (Developer, Standard o Enterprise) en la imagen del servidor de Windows Server 2016. Este tutorial se usa la imagen de Marketplace [SQL Server 2016 SP2 Enterprise en Windows Server 2016](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoftsqlserver.sql2016sp2-ws2016).
- [Extensión IaaS de SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension), versión 1.2.30 o superior. La extensión IaaS de SQL instala los componentes que son necesarios para los elementos de Marketplace de SQL Server para todas las versiones de Windows. Permite que los parámetros específicos de SQL se configuren en máquinas virtuales de SQL. Si la extensión no está instalada en el Marketplace local, el aprovisionamiento de SQL generará un error.
- [Extensión de script personalizada para Windows](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.CustomScriptExtension), versión 1.9.1 o superior. La extensión de script personalizada es una herramienta que puede usarse para iniciar automáticamente las tareas de personalización de la máquina virtual posteriores a la implementación.
- [Extensión DSC (configuración de estado deseado) de PowerShell](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.DSC-arm), versión 2.76.0.0 o superior. DSC es una plataforma de administración de Windows PowerShell que permite implementar y administrar datos de configuración de servicios de software y administrar el entorno en el que se ejecutan estos servicios.

Para obtener más información acerca de cómo agregar elementos a Marketplace de Azure Stack, consulte [Información general de Azure Stack Marketplace](azure-stack-marketplace.md).

## <a name="create-a-sql-server-alwayson-availability-group"></a>Creación de un grupo de disponibilidad AlwaysOn de SQL Server
Siga los pasos de esta sección para implementar el grupo de disponibilidad AlwaysOn de SQL Server mediante la [plantilla de inicio rápido de Azure Stack sql-2016-alwayson](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/sql-2016-alwayson). Esta plantilla implementa dos instancias de SQL Server Enterprise, Standard o Developer en un grupo de disponibilidad AlwaysOn. Crea estos recursos:

- Un grupo de seguridad de red
- Una red virtual
- Cuatro cuentas de almacenamiento (una para Active Directory [AD], otra para SQL, otra para el testigo de recurso compartido de archivos y otra para el diagnóstico de máquina virtual)
- Cuatro direcciones IP públicas (una para AD, dos para cada VM de SQL y otra para el equilibrador de carga público enlazado al agente de escucha de SQL AlwaysOn)
- Un equilibrador de carga externo para las VM de SQL con una dirección IP pública enlazada al agente de escucha AlwaysOn de SQL
- Una máquina virtual (Windows Server 2016) configurada como controlador de dominio para un nuevo bosque con un único dominio
- Dos máquinas virtuales (Windows Server 2016) configuradas con SQL Server 2016 SP1 o SP2 Enterprise, Standard o Developer Edition y en clúster. Deben ser imágenes de Marketplace.
- Una máquina virtual (Windows Server 2016) configurada como el testigo de recurso compartido de archivos para el clúster
- Un conjunto que disponibilidad que contiene las máquinas virtuales testigo del recurso compartido de archivos y SQL  

1. 
   [!INCLUDE [azs-admin-portal](../includes/azs-admin-portal.md)]

2. Seleccione **\+** **Crear un recurso** > **Personalizar** y, a continuación, **Implementación de plantilla**.

   ![Implementación de plantilla personalizada](media/azure-stack-tutorial-sqlrp/1.png)


3. En la hoja **Implementación personalizada**, seleccione **Editar plantilla** > **Plantilla de inicio rápido** y, a continuación, use la lista desplegable de plantillas personalizadas disponibles para seleccionar la plantilla **sql-2016-alwayson**. Luego, haga clic en **Aceptar** y, a continuación, en **Guardar**.

   [![](media/azure-stack-tutorial-sqlrp/2-sm.PNG "Selección de plantilla de inicio rápido")](media/azure-stack-tutorial-sqlrp/2-lg.PNG#lightbox)

4. En la hoja **Implementación personalizada**, seleccione **Editar parámetros** y revise los valores predeterminados. Modifique los valores según sea necesario para proporcionar toda la información de los parámetros necesaria y, a continuación, haga clic en **Aceptar**.<br><br> Como mínimo:

    - Proporcione contraseñas complejas para los parámetros ADMINPASSWORD, SQLSERVERSERVICEACCOUNTPASSWORD y SQLAUTHPASSWORD.
    - Escriba el sufijo DNS para la búsqueda inversa todo en minúsculas para el parámetro DNSSUFFIX (**azurestack.external** para las instalaciones de ASDK).
    
   [![](media/azure-stack-tutorial-sqlrp/3-sm.PNG "Edición de los parámetros de implementación personalizada")](media/azure-stack-tutorial-sqlrp/3-lg.PNG#lightbox)

5. En la hoja **Implementación personalizada**, seleccione la suscripción de Azure que se usará y cree un nuevo grupo de recursos o seleccione un grupo de recursos existente para la implementación personalizada.<br><br> A continuación, seleccione la ubicación del grupo de recursos (**local** para las instalaciones de ASDK) y, a continuación, haga clic en **Crear**. Se validará la configuración de implementación personalizada y, a continuación, se iniciará la implementación.

    [![](media/azure-stack-tutorial-sqlrp/4-sm.PNG "Creación de implementación personalizada")](media/azure-stack-tutorial-sqlrp/4-lg.PNG#lightbox)


6. En el portal de administración, seleccione **Grupos de recursos** y, a continuación, el nombre del grupo de recursos que creó para la implementación personalizada (**resource-group** en este ejemplo). Vea el estado de la implementación para asegurarse de que todas las implementaciones se han completado correctamente.<br><br>A continuación, revise los elementos del grupo de recursos y seleccione el elemento de la dirección IP pública **SQLPIPsql\<nombre del grupo de recursos\>** . Anote la dirección IP pública y el nombre de dominio completo de la dirección IP pública del equilibrador de carga. Deberá proporcionar esta información al operador de Azure Stack para que pueda crear un servidor de hospedaje SQL aprovechando este grupo de disponibilidad AlwaysOn de SQL.

   > [!NOTE]
   > La implementación de la plantilla tardará varias horas en completarse.

   ![Implementación personalizada completada](./media/azure-stack-tutorial-sqlrp/5.png)

### <a name="enable-automatic-seeding"></a>Habilitación de la propagación automática
Una vez que se ha implementado correctamente la plantilla y se ha configurado el grupo de disponibilidad AlwaysON de SQL, debe habilitar la [propagación automática](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) en cada instancia de SQL Server del grupo de disponibilidad. 

Cuando se crea un grupo de disponibilidad con propagación automática, SQL Server crea automáticamente las réplicas secundarias para cada base de datos en el grupo sin que se requiera más intervención manual para garantizar una alta disponibilidad de bases de datos de AlwaysOn.

Use estos comandos SQL para configurar la propagación automática para el grupo de disponibilidad AlwaysOn. Sustituya \<InstanceName\> por el nombre de la instancia de SQL Server principal y <availability_group_name> por el nombre del grupo de disponibilidad AlwaysOn según sea necesario. 

En la instancia de SQL principal:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<InstanceName>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

>  ![Script de la instancia de SQL principal](./media/azure-stack-tutorial-sqlrp/sql1.png)

En las instancias de SQL secundarias:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```
>  ![Script de la instancia de SQL secundaria](./media/azure-stack-tutorial-sqlrp/sql2.png)

### <a name="configure-contained-database-authentication"></a>Configuración de autenticación de base de datos independiente
Antes de agregar una base de datos independiente a un grupo de disponibilidad, asegúrese de que la opción de servidor de autenticación de la base de datos independiente se establece en 1 en cada instancia del servidor que hospeda una réplica de disponibilidad para el grupo de disponibilidad. Para obtener más información, consulte [contained database authentication (opción de configuración del servidor)](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Use estos comandos para establecer la opción del servidor de autenticación de base de datos independiente para cada instancia de SQL Server del grupo de disponibilidad:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```
>  ![Establecimiento de la autenticación de la base de datos independiente](./media/azure-stack-tutorial-sqlrp/sql3.png)

## <a name="create-an-azure-stack-sql-hosting-server"></a>Creación de un servidor de hospedaje SQL de Azure Stack
Una vez que el grupo de disponibilidad AlwaysOn de SQL Server se ha creado y configurado correctamente, un operador de Azure Stack debe crear un servidor de hospedaje de SQL de Azure Stack para facilitar una mayor capacidad que permita a los usuarios crear bases de datos. 

Asegúrese de utilizar la dirección IP pública o el nombre de dominio completo para la dirección IP pública del equilibrador de carga SQL que anotó anteriormente, cuando creó el grupo de recursos del grupo de disponibilidad AlwaysOn de SQL (**SQLPIPsql\<nombre del grupo de recursos\>** ). Además, tendrá que conocer las credenciales de autenticación de SQL Server utilizadas para tener acceso a las instancias de SQL en el grupo de disponibilidad AlwaysOn.

> [!NOTE]
> Este paso se debe ejecutar desde el portal de administración de Azure Stack por un operador de Azure Stack.

Con la información de inicio de sesión de autenticación de SQL y la dirección IP pública del agente de escucha del equilibrador de carga del grupo de disponibilidad AlwaysOn de SQL, un operador de Azure Stack ahora puede [crear un servidor de hospedaje de SQL mediante el grupo de disponibilidad AlwaysOn de SQL](azure-stack-sql-resource-provider-hosting-servers.md#provide-high-availability-using-sql-always-on-availability-groups). 

Asegúrese también de que ha creado planes y ofertas para que la creación de la base de datos AlwaysOn de SQL esté disponible para los usuarios. El operador deberá agregar el servicio **Microsoft.SqlAdapter** a un plan y crear una nueva cuota específicamente para las bases de datos de alta disponibilidad. Para obtener más información acerca de la creación de planes, consulte [Introducción a los planes, ofertas, cuotas y suscripciones](azure-stack-plan-offer-quota-overview.md).

> [!TIP]
> El servicio **Microsoft.SqlAdapter** no estará disponible para agregarlo a planes hasta que [se haya implementado el proveedor de recursos de SQL Server](azure-stack-sql-resource-provider-deploy.md).

## <a name="create-a-highly-available-sql-database"></a>Creación de una base de datos SQL de alta disponibilidad
Una vez que el grupo de disponibilidad AlwaysOn de SQL se ha creado, configurado y agregado como servidor de hospedaje SQL de Azure Stack por un operador de Azure Stack, un usuario inquilino con una suscripción que tenga capacidades de base de datos SQL Server puede crear bases de datos SQL que admitan la funcionalidad AlwaysOn siguiendo los pasos de esta sección. 

> [!NOTE]
> Ejecute estos pasos desde el portal de usuario de Azure Stack como un usuario inquilino con una suscripción que proporcione funciones de SQL Server (servicio Microsoft.SQLAdapter).

1. 
   [!INCLUDE [azs-user-portal](../includes/azs-user-portal.md)]

2. Seleccione **\+** **Crear un recurso** > **Datos \+y almacenamiento** y, luego, **SQL Database**.<br><br>Proporcione la información de propiedad de base de datos requerida, incluidos el nombre, la intercalación, el tamaño máximo y la suscripción, así como el grupo de recursos y la ubicación que se usará para la implementación. 

   ![Creación de base de datos SQL](./media/azure-stack-tutorial-sqlrp/createdb1.png)

3. Seleccione **SKU** y, a continuación, elija la SKU del servidor de hospedaje de SQL apropiado que se usará. En este ejemplo, el operador de Azure Stack ha creado la SKU **Enterprise-HA** para admitir la alta disponibilidad de grupos de disponibilidad AlwaysOn de SQL.

   ![Selección de SKU](./media/azure-stack-tutorial-sqlrp/createdb2.png)

4. Seleccione **Inicio de sesión** > **Create a new login** (Crear un nuevo inicio de sesión) y, a continuación, proporcione las credenciales de autenticación de SQL que se usarán para la nueva base de datos. Cuando termine, haga clic en **Aceptar** y, a continuación, en **Crear** para comenzar el proceso de implementación de la base de datos.

   ![Creación de inicio de sesión](./media/azure-stack-tutorial-sqlrp/createdb3.png)

5. Cuando la implementación de la base de datos SQL se complete correctamente, revise las propiedades de la base de datos para detectar la cadena de conexión que se usará para conectarse a la nueva base de datos de alta disponibilidad. 

   ![Visualización de la cadena de conexión](./media/azure-stack-tutorial-sqlrp/createdb4.png)




## <a name="next-steps"></a>Pasos siguientes

En este tutorial, ha aprendido cómo:

> [!div class="checklist"]
> * Creación de un grupos de disponibilidad AlwaysOn de SQL Server a partir de una plantilla
> * Creación de un servidor de hospedaje SQL de Azure Stack
> * Creación de una base de datos SQL de alta disponibilidad

Prosiga con el siguiente tutorial para aprender a:
> [!div class="nextstepaction"]
> [Creación de bases de datos MySQL de alta disponibilidad](azure-stack-tutorial-mysql.md)
