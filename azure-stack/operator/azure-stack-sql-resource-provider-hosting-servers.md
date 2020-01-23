---
title: Incorporación de servidores de hospedaje para el proveedor de recursos SQL
titleSuffix: Azure Stack Hub
description: Aprenda a agregar servidores de hospedaje para el aprovisionamiento mediante el adaptador del proveedor de recursos de SQL.
services: azure-stack
documentationCenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/02/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 10/16/2018
ms.openlocfilehash: 7bdbe25e204ca00b31f1932c16aab6cdb815d5e1
ms.sourcegitcommit: 1185b66f69f28e44481ce96a315ea285ed404b66
ms.translationtype: HT
ms.contentlocale: es-ES
ms.lasthandoff: 01/09/2020
ms.locfileid: "75814565"
---
# <a name="add-hosting-servers-for-the-sql-resource-provider"></a>Incorporación de servidores de hospedaje para el proveedor de recursos SQL

Puede crear servidores que hospeden una base de datos de SQL Server en una máquina virtual en [Azure Stack Hub](azure-stack-overview.md) o en una máquina virtual fuera del entorno de Azure Stack Hub, siempre y cuando el proveedor de recursos de SQL pueda conectarse a la instancia.

> [!NOTE]
> El proveedor de recursos SQL debe crearse en la suscripción de proveedor predeterminada, mientras que los servidores de hospedaje de SQL deben crearse en una suscripción de usuario facturable. El servidor del proveedor de recursos no debe usarse para hospedar bases de datos de usuario.

## <a name="overview"></a>Información general

Antes de agregar un servidor de hospedaje SQL, revise los siguientes requisitos obligatorios y generales.

### <a name="mandatory-requirements"></a>Requisitos obligatorios

* Habilite la autenticación de SQL en la instancia de SQL Server. Dado que la máquina virtual del proveedor de recursos de SQL no está unida a ningún dominio, solo se puede conectar a un servidor de hospedaje mediante la autenticación de SQL.
* Configure las direcciones IP para las instancias de SQL como Públicas cuando se instalen en Azure Stack Hub. El proveedor de recursos y los usuarios como, por ejemplo, las aplicaciones web, se comunican a través de la red del usuario, por lo que se requiere conectividad a la instancia de SQL en esta red.

### <a name="general-requirements"></a>Requisitos generales

* Dedique la instancia de SQL para que la use el proveedor de recursos y las cargas de trabajo de usuario. No se puede utilizar una instancia de SQL que esté siendo utilizada por otro consumidor. Esta restricción también se aplica a App Services.
* Configure una cuenta con los niveles de privilegios adecuados para que la use el proveedor de recursos (descrito a continuación).
* Usted es responsable de administrar las instancias de SQL y sus hosts. Por ejemplo, el proveedor de recursos no aplica actualizaciones, administra copias de seguridad ni controla la rotación de credenciales.

### <a name="sql-server-vm-images"></a>Imágenes de las VM con SQL Server

Las imágenes de máquina virtual IaaS de SQL están disponibles en la característica Administración de Marketplace. Estas imágenes son las mismas que las máquinas virtuales de SQL disponibles en Azure.

Asegúrese de descargar siempre la versión más reciente de la **extensión IaaS de SQL** antes de implementar una máquina virtual de SQL mediante un elemento de Marketplace. La extensión de IaaS y las correspondientes mejoras del portal proporcionan características adicionales tales como la copia de seguridad y la aplicación de revisiones. Para más información acerca de esta extensión, consulte [Automatizar las tareas de administración en Azure Virtual Machines con la extensión del Agente SQL Server](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-sql-server-agent-extension).

> [!NOTE]
> La extensión SQL IaaS es _obligatoria_ para todas las imágenes de SQL en Windows de Marketplace; se produce un error de implementación de la máquina virtual si no se ha descargado la extensión. No se usa con imágenes de máquina virtual de SQL basadas en Linux.

Hay otras opciones para implementar máquinas virtuales de SQL, incluidas las plantillas de la [Galería de inicio rápido de Azure Stack Hub](https://github.com/Azure/AzureStack-QuickStart-Templates).

> [!NOTE]
> Cualquier servidor de hospedaje instalado en una instancia de Azure Stack Hub de varios nodos se tiene que crear desde una suscripción de usuario que no sea la suscripción de proveedor predeterminada. Se deben crear desde el portal de usuario o desde una sesión de PowerShell con un inicio de sesión adecuado. Todos los servidores de hospedaje son máquinas virtuales facturables y deben tener las licencias de SQL adecuadas. El administrador del servicio _puede_ ser el propietario de esa suscripción.

### <a name="required-privileges"></a>Privilegios necesarios

Puede crear un usuario administrador con menos privilegios que un administrador del sistema de SQL. El usuario solo necesita permisos para las operaciones siguientes:

* Base de datos: crear, modificar, con contención (solo para Always On), eliminar, copia de seguridad
* Grupo de disponibilidad: alterar, combinar, agregar o quitar base de datos
* Inicio de sesión: crear, seleccionar, alterar, eliminar, revocar
* Operaciones Select: \[master\].\[sys\].\[availability_group_listeners\] (AlwaysOn), sys.availability_replicas (AlwaysOn), sys.databases, \[master\].\[sys\].\[dm_os_sys_memory\], SERVERPROPERTY, \[master\].\[sys\].\[availability_groups\] (AlwaysOn), sys.master_files

### <a name="additional-security-information"></a>Información adicional de seguridad

La siguiente información proporciona una guía de seguridad adicional:

* Todo el almacenamiento de Azure Stack Hub está cifrado con BitLocker, por lo que cualquier instancia de SQL en Azure Stack Hub utilizará almacenamiento de blobs cifrado.
* El proveedor de recursos de SQL es totalmente compatible con TLS 1.2. Asegúrese de que cualquier servidor SQL Server que se administra mediante el proveedor de recursos de SQL está configurado _solo_ para TLS 1.2, y que el proveedor de recursos lo usará de forma predeterminada. Todas las versiones compatibles de SQL Server admiten TLS 1.2. Para más información, consulte [Compatibilidad de TLS 1.2 con Microsoft SQL Server](https://support.microsoft.com/help/3135244/tls-1-2-support-for-microsoft-sql-server).
* Use Administrador de configuración de SQL Server para establecer la opción **ForceEncryption** para garantizar que todas las comunicaciones con SQL server están siempre cifradas. Para más información, consulte [Para configurar el servidor para forzar conexiones cifradas](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine?view=sql-server-2017#to-configure-the-server-to-force-encrypted-connections).
* Asegúrese de que cualquier aplicación cliente también se comunica a través de una conexión cifrada.
* El proveedor de recursos está configurado para confiar en los certificados usados por las instancias de SQL Server.

## <a name="provide-capacity-by-connecting-to-a-standalone-hosting-sql-server"></a>Agregar capacidad a través de la conexión a un servidor SQL de hospedaje independiente

Puede usar servidores SQL (que no sean HA) independientes con cualquier edición de SQL Server 2014 o SQL Server 2016. Asegúrese de que tiene las credenciales de una cuenta con privilegios de administrador del sistema.

Para agregar un servidor de hospedaje independiente que ya se haya configurado, siga estos pasos:

1. Inicie sesión en el portal del administrador de Azure Stack Hub como administrador de servicios.

2. Seleccione **All services** &gt; **ADMINISTRATIVE RESOURCES** &gt; **SQL Hosting Servers** (Todos los servicios > Recursos administrativos > Servidores de hospedaje de SQL).

   ![Servidores de hospedaje de SQL en el portal del administrador de Azure Stack Hub](./media/azure-stack-sql-rp-deploy/sqlhostingservers.png)

   En **SQL Hosting Servers** (Servidores de hospedaje SQL), puede conectar el proveedor de recursos de SQL a instancias de SQL Server que actuarán como back-end del proveedor de recursos.

   ![Panel del adaptador de SQL en el portal del administrador de Azure Stack Hub](./media/azure-stack-sql-rp-deploy/sqlrp-hostingserver.png)

3. En **Agregar**, proporcione los detalles de conexión para la instancia de SQL Server en la hoja **Add a SQL Hosting Server** (Agregar un servidor de hospedaje SQL).

   ![Adición de un servidor de hospedaje de SQL en el portal del administrador de Azure Stack Hub](./media/azure-stack-sql-rp-deploy/sqlrp-newhostingserver.png)

    Tiene la opción de incluir un nombre de instancia y un número de puerto si la instancia no está asignada al puerto predeterminado 1433.

   > [!NOTE]
   > Siempre y cuando el usuario y el administrador en Azure Resource Manager puedan acceder a la instancia de SQL, esta puede ponerse bajo el control del proveedor de recursos. La instancia de SQL __debe__ asignarse exclusivamente al proveedor de recursos.

4. A medida que agregue servidores, debe asignarlos a una SKU existente o crear una nueva. En **Add a Hosting Server** (Agregar un servidor de hospedaje), seleccione **SKU**.

   * Para usar una SKU existente, elija una SKU disponible y, a continuación, seleccione **Crear**.
   * Para crear una SKU, seleccione **+ Create new SKU** (Crear nueva SKU). En **Create SKU** (Crear SKU), introduzca la información necesaria y seleccione **Aceptar**.

     ![Creación de una SKU en el portal del administrador de Azure Stack Hub](./media/azure-stack-sql-rp-deploy/sqlrp-newsku.png)

## <a name="provide-high-availability-using-sql-always-on-availability-groups"></a>Alta disponibilidad mediante grupos de disponibilidad SQL Always On

La configuración de instancias de SQL de Always On requiere pasos adicionales e implica tres máquinas virtuales (o máquinas físicas). En este artículo se da por hecho que ya conoce bien los grupos de disponibilidad Always On. Para más información, consulte los siguientes artículos.

* [Introducción a los grupos de disponibilidad Always On de SQL Server en Azure Virtual Machines](https://docs.microsoft.com/azure/virtual-machines/windows/sql/virtual-machines-windows-portal-sql-availability-group-overview)
* [Grupos de disponibilidad Always On (SQL Server)](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/always-on-availability-groups-sql-server?view=sql-server-2017)

> [!NOTE]
> El proveedor de recursos del adaptador SQL _solo_ admite instancias de SQL 2016 SP1 Enterprise o versiones posteriores para grupos de disponibilidad AlwaysOn. Esta configuración de adaptador requiere nuevas características SQL como la propagación automática.

### <a name="automatic-seeding"></a>Propagación automática

Tiene que habilitar la opción [Propagación automática](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/automatically-initialize-always-on-availability-group) en cada grupo de disponibilidad para cada instancia de SQL Server.

Para habilitar la propagación automática en todas las instancias, edite y ejecute el siguiente comando SQL en la réplica principal para cada instancia secundaria:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>]
      MODIFY REPLICA ON '<secondary_node>'
      WITH (SEEDING_MODE = AUTOMATIC)
  GO
  ```

El grupo de disponibilidad tiene que estar entre corchetes.

En los nodos secundarios, ejecute el siguiente comando SQL:

  ```sql
  ALTER AVAILABILITY GROUP [<availability_group_name>] GRANT CREATE ANY DATABASE
  GO
  ```

### <a name="configure-contained-database-authentication"></a>Configuración de autenticación de base de datos independiente

Antes de agregar una base de datos independiente a un grupo de disponibilidad, asegúrese de que la opción de servidor de autenticación de la base de datos independiente se establece en 1 en cada instancia del servidor que hospeda una réplica de disponibilidad para el grupo de disponibilidad. Para más información, consulte la [opción de configuración del servidor de autenticación de base de datos independiente](https://docs.microsoft.com/sql/database-engine/configure-windows/contained-database-authentication-server-configuration-option?view=sql-server-2017).

Use estos comandos para establecer la opción del servidor de autenticación de base de datos independiente para cada instancia:

  ```sql
  EXEC sp_configure 'contained database authentication', 1
  GO
  RECONFIGURE
  GO
  ```

### <a name="to-add-sql-always-on-hosting-servers"></a>Para agregar servidores de hospedaje SQL Always On

1. Inicie sesión en el portal del administrador de Azure Stack Hub como administrador de servicios.

2. Seleccione **Browse** &gt; **ADMINISTRATIVE RESOURCES** &gt; **SQL Hosting Servers** &gt; **+Add** (Examinar > Recursos administrativos > Servidores de hospedaje de SQL > +Agregar).

   En **SQL Hosting Servers** (Servidores de hospedaje SQL), puede conectar el proveedor de recursos de SQL Server a instancias reales de SQL Server que actúan como back-end del proveedor de recursos.

3. Rellene el formulario con los detalles de conexión de la instancia de SQL Server. Asegúrese de que usa la dirección FQDN del agente de escucha Always On (y un nombre de instancia y número de puerto opcional). Proporcione la información de la cuenta que ha configurado con privilegios de administrador del sistema.

4. Active la casilla Grupos de disponibilidad Always On para habilitar la compatibilidad con instancias de grupos de disponibilidad SQL Always On.

   ![Habilitación de Grupos de disponibilidad AlwaysOn en el portal del administrador de Azure Stack Hub](./media/azure-stack-sql-rp-deploy/AlwaysOn.PNG)

5. Agregue la instancia de SQL Always On a una SKU.

   > [!IMPORTANT]
   > No puede mezclar servidores independientes con instancias de Always On en la misma SKU. Si se intentan combinar tipos después de agregar los primeros resultados del servidor de hospedaje, se produce un error.

## <a name="sku-notes"></a>Notas de la SKU
Use un nombre de SKU que describa las capacidades de los servidores de la SKU, como la capacidad y el rendimiento. El nombre sirve de ayuda para que los usuarios implementen sus bases de datos en la SKU adecuada. Por ejemplo, puede usar nombres de SKU para diferenciar las ofertas de servicio mediante las características siguientes:
  
* alta capacidad
* alto rendimiento
* alta disponibilidad

Como procedimiento recomendado, todos los servidores de hospedaje de una SKU deben tener las mismas características de rendimiento y recursos.

Las SKU no se pueden asignar a usuarios o grupos específicos.

Las SKU pueden tardar hasta una hora en estar visibles en el portal. Los usuarios no pueden crear una base de datos hasta que se cree totalmente la SKU.

Para editar una SKU, vaya a **All services** > **MySQL Adapter** > **SKUs** (Todos los servicios > Adaptador de MySQL > SKU). Seleccione la SKU que quiere modificar, realice los cambios necesarios y haga clic en **Guardar** para guardar los cambios. 

Para eliminar una SKU que ya no es necesaria, vaya a **All services** > **SQL Adapter** > **SKUs** (Todos los servicios > Adaptador de SQL > SKU). Haga clic en el nombre de la SKU y seleccione **Eliminar** para eliminarla.

> [!IMPORTANT]
> Las nuevas SKU pueden tardar hasta cuatro horas en estar disponibles en el portal del usuario.

## <a name="make-sql-databases-available-to-users"></a>Bases de datos SQL disponibles para los usuarios

Cree planes y ofertas para poner las bases de datos SQL a disposición de los usuarios. Agregue el servicio **Microsoft.SqlAdapter** al plan y cree una cuota nueva.

> [!IMPORTANT]
> Pueden pasar hasta dos horas hasta que las nuevas cuotas estén disponibles en el portal del usuario o antes de que se aplique una cuota modificada.

## <a name="next-steps"></a>Pasos siguientes

[Agregar bases de datos](azure-stack-sql-resource-provider-databases.md)
